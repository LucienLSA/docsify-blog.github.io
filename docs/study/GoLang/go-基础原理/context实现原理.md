# go context的底层原理
在异步场景中实现并发协调，对goroutine的生命周期控制。还兼有数据存储能力

作为调用链路的上下文，

## context.Context 

![context.Context](context-images/1.png)

Context 为 interface，定义了四个核心 api：

1. （1）Deadline：返回 context 的过期时间；

2. （2）Done：返回 context 中的 channel；
返回一个只读的channel，可作为信号发送器，时时刻刻监听channel动静，表示context上下文的生命周期是否已结束。

3. （3）Err：返回错误；

4. （4）Value：返回 context 中的对应 key 的值.

### 标准error

## emptyCtx
### 类的实现
```go
// emptyCtx 是一个空的 context，本质上类型为一个整型；
type emptyCtx int

// Deadline 方法会返回一个公元元年时间以及 false 的 flag，标识当前 context 不存在过期时间；
func (*emptyCtx) Deadline() (deadline time.Time, ok bool) {
    return
}

// Done 方法返回一个 nil 值，用户无论往 nil 中写入或者读取数据，均会陷入阻塞；
func (*emptyCtx) Done() <-chan struct{} {
    return nil
}

func (*emptyCtx) Err() error {
    return nil
}

func (*emptyCtx) Value(key any) any {
    return
}
```

###  context.Background() & context.TODO()
context.Background() 和 context.TODO() 方法返回的均是 emptyCtx 类型的一个实例
```go
var (
    background = new(emptyCtx)
    todo       = new(emptyCtx)
)


func Background() Context {
    return background
}


func TODO() Context {
    return todo
}
```

## cancelCtx

### 引入
1. 并发调用链路
![并发调用链路](context-images/3.png)

+ 同步调用
串行化、有条链，父方法和子方法会形成一个压栈。保证子方法协程完成之后，父方法拿到响应的结果，才能串行化往下执行

+ 异步调用
父协程与子协程是松耦合的状况，父协程对子协程的感知较弱，父协程可异步创建多个子协程。创建完成之后便可执行后续的业务，与子协程没有阻塞、同步过程。

2. 并发的特点
如果创建一个异步协程或线程，不知道什么时候会终止，则不应该去创建。即不可滥用并发，做好并发控制。否则可能会造成协程泄漏和安全问题

![父子并发关系](context-images/4.png)
每一个子context有唯一指向的父context，父context可创建多个不同的子context。context形成上图多叉树，根节点一定是emptyCtx

+ 生命周期的终止事件传递的单向性，用来作父子协程的并发控制。根据父子之间联动，单向传递，终止特性的方式

### cancelCtx 数据结构
![cancelCtx 数据结构](context-images/2.png)

```go
type cancelCtx struct {
    Context

    mu       sync.Mutex            // protects following fields
    done     atomic.Value          // of chan struct{}, created lazily, closed by first cancel call
    children map[canceler]struct{} // set to nil by the first cancel call
    err      error                 // set to non-nil by the first cancel call
}

type canceler interface {
	cancel(removeFromParent bool, err error)
	Done() <-chan struct{}
}
```

+ （1）embed 了一个 context 作为其父 context. 可见，cancelCtx 必然为某个 context 的子 context；

+ （2）内置了一把锁，用以协调并发场景下的资源获取；互斥锁以进行并发保护

+ （3）done：实际类型为 chan struct{}，即用以反映 cancelCtx 生命周期的通道；

+ （4）children：一个 set，指向 cancelCtx 的所有子 context；只关心到子context中的cancel和Done两个方法。职责内聚、边界分明

+ （5）err：记录了当前 cancelCtx 的错误. 必然为某个 context 的子 context；

### Deadline 方法
cancelCtx 未实现该方法，仅是 embed 了一个带有 Deadline 方法的 Context interface，因此倘若直接调用会报错. 调用父context的 Deadline 方法，如果父context为空context，则直接返回一个初始的时间和false

### Done 方法
![Done 方法](context-images/5.png)
```go
func (c *cancelCtx) Done() <-chan struct{} {
	d := c.done.Load()
	if d != nil {
		return d.(chan struct{})
	}
	c.mu.Lock()
	defer c.mu.Unlock()
	d = c.done.Load()
	if d == nil {
		d = make(chan struct{})
		c.done.Store(d)
	}
	return d.(chan struct{})
}
```
+ （1）基于 atomic 包，读取 cancelCtx 中的 chan；倘若已存在，则直接返回；

+ （2）加锁后，在此检查 chan 是否存在，若存在则返回；（double check）

+ （3）初始化 chan 存储到 aotmic.Value 当中，并返回.（懒加载机制）

### Err 方法
```go
func (c *cancelCtx) Err() error {
	c.mu.Lock()
	err := c.err
	c.mu.Unlock()
	return err
}
```

### Value 方法
```go
func (c *cancelCtx) Value(key any) any {
	if key == &cancelCtxKey {
		return c
	}
	return value(c.Context, key)
}
```
cancelCtxKey 全局整型变量 不可导出类型，不可能是外部用户传入的key，应该是内部某个环节调用

### context.WithCancel()
#### context.WithCancel()
![context.WithCancel()](context-images/6.png)
```go
func WithCancel(parent Context) (ctx Context, cancel CancelFunc) {
	if parent == nil {
		panic("cannot create context from nil parent")
	}
	c := newCancelCtx(parent)
	propagateCancel(parent, &c)
	return &c, func() { c.cancel(true, Canceled) }
}
```
+ （1）校验父 context 非空；

+ （2）注入父 context 构造好一个新的 cancelCtx；

+ （3）在 propagateCancel 方法内启动一个守护协程，以保证父 context 终止时，该 cancelCtx 也会被终止；

+ （4）将 cancelCtx 返回，连带返回一个用以终止该 cancelCtx 的闭包函数.


#### newCancelCtx

```go
func newCancelCtx(parent Context) cancelCtx {
	return cancelCtx{Context: parent}
}
```
+ （1）注入父 context 后，返回一个新的 cancelCtx.

#### propagateCancel
```go
func propagateCancel(parent Context, child canceler) {
	done := parent.Done()
	if done == nil {
		return // parent is never canceled
	}

	select {
	case <-done:
		// parent is already canceled
		child.cancel(false, parent.Err())
		return
	default:
	}

	if p, ok := parentCancelCtx(parent); ok {
		p.mu.Lock()
		if p.err != nil {
			// parent has already been canceled
			child.cancel(false, p.err)
		} else {
			if p.children == nil {
				p.children = make(map[canceler]struct{})
			}
			p.children[child] = struct{}{}
		}
		p.mu.Unlock()
	} else {
		atomic.AddInt32(&goroutines, +1)
		go func() {
			select {
			case <-parent.Done():
				child.cancel(false, parent.Err())
			case <-child.Done():
			}
		}()
	}
}
```
+ （1）倘若 parent 是不会被 cancel 的类型（如 emptyCtx），则直接返回；

+ （2）倘若 parent 已经被 cancel，则直接终止子 context，并以 parent 的 err 作为子 context 的 err；

+ （3）假如 parent 是 cancelCtx 的类型，则加锁，并将子 context 添加到 parent 的 children set 当中；

+ （4）假如 parent 不是 cancelCtx 类型，但又存在 cancel 的能力（比如用户自定义实现的 context），则启动一个守护协程，通过select多路复用的方式监控 parent 状态，倘若其终止，则同时终止子 context，并透传 parent 的 err；如果子conxtex终止，则不做任何处理，不影响到parent的生命周期；

##### parentCancelCtx
parentCancelCtx 是如何校验 parent 是否为 cancelCtx 的类型：
```go
func parentCancelCtx(parent Context) (*cancelCtx, bool) {
	done := parent.Done()
	if done == closedchan || done == nil {
		return nil, false
	}
	p, ok := parent.Value(&cancelCtxKey).(*cancelCtx)
	if !ok {
		return nil, false
	}
	pdone, _ := p.done.Load().(chan struct{})
	if pdone != done {
		return nil, false
	}
	return p, true
}
```
+ （1）倘若 parent 的 channel 已关闭或者是不会被 cancel 的类型，则返回 false；

+ （2）倘若以特定的 cancelCtxKey 从 parent 中取值，取得的 value 是 parent 本身，则返回 true. （基于 cancelCtxKey 为 key 取值时返回 cancelCtx 自身，是 cancelCtx 特有的协议


#### cancelCtx.cancel
![cancelCtx.cancel](context-images/7.png)
```go
func (c *cancelCtx) cancel(removeFromParent bool, err error) {
	if err == nil {
		panic("context: internal error: missing cancel error")
	}
	c.mu.Lock()
	if c.err != nil {
		c.mu.Unlock()
		return // already canceled
	}
	c.err = err
	d, _ := c.done.Load().(chan struct{})
	if d == nil {
		c.done.Store(closedchan)
	} else {
		close(d)
	}
	for child := range c.children {
		// NOTE: acquiring the child's lock while holding parent's lock.
		child.cancel(false, err)
	}
	c.children = nil
	c.mu.Unlock()

	if removeFromParent {
		removeChild(c.Context, c)
	}
}
```
+ 1）cancelCtx.cancel 方法有两个入参，第一个 removeFromParent 是一个 bool 值，表示当前 context 是否需要从父 context 的 children set 中删除；第二个 err 则是 cancel 后需要展示的错误；

+ （2）进入方法主体，首先校验传入的 err 是否为空，若为空则 panic；

+ （3）加锁；

+ （4）校验 cancelCtx 自带的 err 是否已经非空，若非空说明已被 cancel，则解锁返回；

+ （5）将传入的 err 赋给 cancelCtx.err；

+ （6）处理 cancelCtx 的 channel，若 channel 此前未初始化，则直接注入一个 closedChan，否则关闭该 channel；

+ （7）遍历当前 cancelCtx 的 children set，依次将 children context 都进行 cancel；

+ （8）解锁.

+ （9）根据传入的 removeFromParent flag 判断是否需要手动把 cancelCtx 从 parent 的 children set 中移除.

#### timerCtx
![timerCtx](context-images/8.png)
##### 类
timerCtx 在 cancelCtx 基础上又做了一层封装，除了继承 cancelCtx 的能力之外，新增了一个 time.Timer 用于定时终止 context；另外新增了一个 deadline 字段用于字段 timerCtx 的过期时间.

##### timerCtx.Deadline()
```go
func (c *timerCtx) Deadline() (deadline time.Time, ok bool) {
	return c.deadline, true
}
```

##### timerCtx.cancel
重写cancelCtx 的 cancel 能力，进行 cancel 处理；
```go
func (c *timerCtx) cancel(removeFromParent bool, err error) {
	c.cancelCtx.cancel(false, err)
	if removeFromParent {
		removeChild(c.cancelCtx.Context, c)
	}
	c.mu.Lock()
	if c.timer != nil {
		c.timer.Stop()
		c.timer = nil
	}
	c.mu.Unlock()
}
```
+ （2）判断是否需要手动从 parent 的 children set 中移除，若是则进行处理

+ （3）加锁；

+ （4）停止 time.Timer

+（5）解锁返回.

#### context.WithTimeout & context.WithDeadline
构造一个 timerCtx， 相对时间转为绝对时间
```go
func WithDeadline(parent Context, d time.Time) (Context, CancelFunc) {
	if parent == nil {
		panic("cannot create context from nil parent")
	}
	if cur, ok := parent.Deadline(); ok && cur.Before(d) {
		// The current deadline is already sooner than the new one.
		return WithCancel(parent)
	}
	c := &timerCtx{
		cancelCtx: newCancelCtx(parent),
		deadline:  d,
	}
	propagateCancel(parent, c)
	dur := time.Until(d)
	if dur <= 0 {
		c.cancel(true, DeadlineExceeded) // deadline has already passed
		return c, func() { c.cancel(false, Canceled) }
	}
	c.mu.Lock()
	defer c.mu.Unlock()
	if c.err == nil {
		c.timer = time.AfterFunc(dur, func() {
			c.cancel(true, DeadlineExceeded)
		})
	}
	return c, func() { c.cancel(true, Canceled) }
}
```

+ （1）校验 parent context 非空；

+ （2）校验 parent 的过期时间是否早于自己，若是，则构造一个 cancelCtx 返回即可；

+ （3）构造出一个新的 timerCtx；

+ （4）启动守护方法，同步 parent 的 cancel 事件到子 context；

+ （5）判断过期时间是否已到，若是，直接 cancel timerCtx，并返回 DeadlineExceeded 的错误；

+ （6）加锁；

+ （7）启动 time.Timer，设定一个延时时间，即达到过期时间后会终止该 timerCtx，并返回 DeadlineExceeded 的错误；

+ （8）解锁；

+ （9）返回 timerCtx，已经一个封装了 cancel 逻辑的闭包 cancel 函数.

#### valueCtx
协调控制，数据存储的容器
![valueCtx](context-images/9.png)
+ （1）valueCtx 同样继承了一个 parent context；

+ （2）一个 valueCtx 中仅有一组 kv 对.

##### valueCtx.Value()
![valueCtx.Value()](context-images/10.png)
```go
func (c *valueCtx) Value(key any) any {
	if c.key == key {
		return c.val
	}
	return value(c.Context, key)
}
```
+ （1）假如当前 valueCtx 的 key 等于用户传入的 key，则直接返回其 value；

+ （2）假如不等，则从 parent context 中依次向上寻找.
```go
func value(c Context, key any) any {
	for {
		switch ctx := c.(type) {
		case *valueCtx:
			if key == ctx.key {
				return ctx.val
			}
			c = ctx.Context
		case *cancelCtx:
			if key == &cancelCtxKey {
				return c
			}
			c = ctx.Context
		case *timerCtx:
			if key == &cancelCtxKey {
				return &ctx.cancelCtx
			}
			c = ctx.Context
		case *emptyCtx:
			return nil
		default:
			return c.Value(key)
		}
	}
}
```
+ （1）启动一个 for 循环，由下而上，由子及父，依次对 key 进行匹配；
	- 在查询数据的时候，时间复杂度不是像map是常数级的，而是遍历的过程（链表形式）

+ （2）其中 cancelCtx、timerCtx、emptyCtx 类型会有特殊的处理方式；

+ （3）找到匹配的 key，则将该组 value 进行返回.

##### context.WithValue()
```go
func WithValue(parent Context, key, val any) Context {
	if parent == nil {
		panic("cannot create context from nil parent")
	}
	if key == nil {
		panic("nil key")
	}
	if !reflectlite.TypeOf(key).Comparable() {
		panic("key is not comparable")
	}
	return &valueCtx{parent, key, val}
}
```
+ （1）倘若 parent context 为空，panic；

+ （2）倘若 key 为空 panic；

+ （3）倘若 key 的类型不可比较，panic；

+ （4）包括 parent context 以及 kv对，返回一个新的 valueCtx的struct实例.


如果context中两次放入的key相同，无去重的机制，形成两个不同的子context，通过key寻找value时取决于调用context.Value的起点位置

#### valueCtx 小结
valueCtx 不适合视为存储介质，存放大量的 kv 数据，原因有三：

+ （1）一个 valueCtx 实例只能存一个 kv 对，因此 n 个 kv 对会嵌套 n 个 valueCtx，造成空间浪费；

+ （2）基于 k 寻找 v 的过程是线性的，时间复杂度 O(N)；

+ （3）不支持基于 k 的去重，相同 k 可能重复存在，并基于起点的不同，返回不同的 v.

由此得知，valueContext 的定位类似于请求头，只适合存放少量作用域较大的全局 meta 数据.









