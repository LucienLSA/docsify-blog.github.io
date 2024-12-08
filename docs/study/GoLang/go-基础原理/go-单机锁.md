

## 学习资源
[https://www.bilibili.com/video/BV1kv4y157wj?p=1&vd_source=917ef87e48a267f0acc88f766dea0a6e](一次搞懂golang单机锁实现原理)

##  Introduction
1. 不同的goroutine之间希望在全局的秩序之下相互协作、共同的推进系统中任务的执行（context、channel、线程池），做到不同goroutine间的团结协作，全局并发的掌控
2. 多个并发访问全局的共享的状态数据同时，需要注意数据前后的一致性问题，避免逻辑等一些问题，状态数据产生偏差，引起严重逻辑错误。原生数据结构不支持并发读写的操作。如：map使用间接性扩容，基于并发读写时，底层会出现严重错误。

并发访问，共享数据，使用锁实现在关键节点和临界资源面前，并发退化为串行，遵守一定秩序，一次一个goroutine对状态数据的读写

## Sync.Mutex互斥锁
基于互斥锁，对map进行保护，保证能够并发安全的访问


在入口处，设置关卡，保证每次真正接触到临界资源之前，做收口，一次只有一个goroutine能访问到对应的邻接资源，
```go
// 并发、自由
m.Mutex.Lock

// 保证一次只有一个goroutine

m.Mutex.Unlock
// 并发、自由
```
#### 读写锁（升级）
写操作：涉及修改、创建、更新，更具有侵入性、更重。

并发读（查阅）并不会产生问题；读+写； 写+写，后两者会对数据进行修改。RWMutex保证并发读情况下，不需要额外加锁的成本。即在读操作情况下（无侵入性操作）加入读锁


### Mutex的核心

#### 上锁和解锁
通过Mutex内的一个状态值标识锁的状态。上锁：将0改为1；解锁：将1置为0；

1. 锁是独占的，只有显式将锁的状态从0改为1，才视为加锁成功
2. 尝试上锁时，先去读取锁的值，将锁从0改为1时，若锁的值被其它角色后发先至，提前被改为1了，破坏了整个流程的原子性。误以为加锁成功，状态数据不一致，回滚。需要保证原子性

#### 自旋到阻塞的升级
针对goroutine加锁，锁已经被抢占，有以下两种策略
1. 阻塞/唤醒：将当前goroutine阻塞挂起，直到锁被释放后，以回调方式，将阻塞goroutine重新唤醒进行锁争夺
精准打击、不浪费CPU时间片，需要挂起协程，进行上下文切换，适用并发竞争激烈场景

2. 自旋+CAS：基于自旋结合CAS，重新校验锁的状态并尝试获取锁，始终把主动权掌握手里
无需阻塞协程，短期操作较轻，长时间争而不得，浪费CPU时间片，并发竞争强度低

设计具备探测并适应环境，从而采取不同对策因地制宜的能力

自旋模式转为阻塞模式的具体条件拆解
1. 自旋累积4次仍未取到结果
2. CPU单核或仅有单个P调度器；其它goroutine根本没机会释放锁
3. 当前P的执行队列仍有待执行的G；避免自旋影响到GMP调度效率


##### sync.Mutex运行过程中存在两种模式
1. 正常模式/非饥饿模式
默认，当有goroutine从阻塞队列被唤醒时，会和此时先进入抢锁流程的goroutine进行锁资源的争夺，假如抢锁失败，会重新回到阻塞队列头部。（新goroutine可能存在多个，已经在占用CPU时间片，从而形成多对一的优势，对老goroutine不利）

2. 饥饿模式
饥饿：Mutex阻塞队列中存在goroutine长时间取不到锁，陷入饥荒状态
饥饿模式：当Mutex阻塞队列中存在处于饥饿态的goroutine时，进入模式，将抢锁流程由非公平制转为公平机制，为拯救老goroutine，锁的所有权按照阻塞队列的顺序依次传递，新goroutine进行流程不得抢锁，而是进入队列尾部排队

##### 模式转换条件
默认为正常；

正常模式-》饥饿模式；阻塞队列存在goroutine等锁超过1ms而不得，进入饥饿状态

饥饿模式-》正常模式；阻塞队列已清空；或取得锁的goroutine等锁时间已低于1ms时，回到正常模式

#####
这两种模式切换，体现sync.Mutex为公平与性能之间作出的调整与权衡

#### 数据结构
源码分析，state字段描述，不同bit位的不同标识

#### Mutex.Lock()
首先进行一轮CAS(无锁算法)操作，假如当前未上锁且锁内不存在阻塞协程，则直接CAS抢锁成功返回

第一轮初探失败，则进入lockSlow流程

#### Mutex.lockSlow()
局部变量

自旋空转

预设新值

