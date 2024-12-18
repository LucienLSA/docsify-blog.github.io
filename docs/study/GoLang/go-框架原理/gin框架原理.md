# gin框架原理
• 支持中间件操作（ handlersChain 机制 ）
• 更方便的使用（ gin.Context ）
• 更强大的路由解析能力（ radix tree 路由树 ）

## Gin 与 net/http 的关系
![Gin 与 net/http](gin-images/1.png)
在 net/http 的既定框架下，gin 所做的是提供了一个 gin.Engine 对象作为 Handler 注入其中，从而实现路由注册/匹配、请求处理链路的优化.

## 注册handler的流程

### 核心数据结构

![核心数据结构](gin-images/2.png)

#### （1）gin.Engine
```go
type Engine struct {
   // 路由组
    RouterGroup
    // ...
    // context 对象池
    pool             sync.Pool
    // 方法路由树
    trees            methodTrees
    // ...
}
```

```go
// net/http 包下的 Handler interface
type Handler interface {
    ServeHTTP(ResponseWriter, *Request)
}


func (engine *Engine) ServeHTTP(w http.ResponseWriter, req *http.Request) {
    // ...
}
```
Engine 为 Gin 中构建的 HTTP Handler，其实现了 net/http 包下 Handler interface 的抽象方法： Handler.ServeHTTP，因此可以作为 Handler 注入到 net/http 的 Server 当中.

+ 路由组 RouterGroup：
+ Context 对象池 pool：基于 sync.Pool 实现，作为复用 gin.Context 实例的缓冲池.
+ 路由树数组 trees：共有 9 棵路由树，对应于 9 种 http 方法. 路由树基于压缩前缀树实现，

#### （2）RouterGroup
```go
type RouterGroup struct {
    Handlers HandlersChain
    basePath string
    engine *Engine
    root bool
}
```
+ Handlers：路由组共同的 handler 处理函数链. 组下的节点将拼接 RouterGroup 的公用 handlers 和自己的 handlers，组成最终使用的 handlers 链
+ basePath：路由组的基础路径. 组下的节点将拼接 RouterGroup 的 basePath 和自己的 path，组成最终使用的 absolutePath
+ engine：指向路由组从属的 Engine
+ root：标识路由组是否位于 Engine 的根节点. 当用户基于 RouterGroup.Group 方法创建子路由组后，该标识为 false


#### （3）HandlersChain
```go
type HandlersChain []HandlerFunc
type HandlerFunc func(*Context)
```
HandlersChain 是由多个路由处理函数 HandlerFunc 构成的处理函数链. 在使用的时候，会按照索引的先后顺序依次调用 HandlerFunc.

### 流程入口
```go
func main() {
    // 创建一个 gin Engine，本质上是一个 http Handler
    mux := gin.Default()
    // 注册中间件
    mux.Use(myMiddleWare)
    // 注册一个 path 为 /ping 的处理函数
    mux.POST("/ping", func(c *gin.Context) {
        c.JSON(http.StatusOK, "pone")
    })
    // ...
}
```

### 初始化Engine
方法调用：gin.Default -> gin.New

+ 创建一个 gin.Engine 实例
+ 创建 Enging 的首个 RouterGroup，对应的处理函数链 Handlers 为 nil，基础路径 basePath 为 "/"，root 标识为 true
+ 构造了 9 棵方法路由树，对应于 9 种 http 方法
+ 创建了 gin.Context 的对象池

```go
func Default() *Engine {
    engine := New()
    // ...
    return engine
}

func New() *Engine {
    // ...
    // 创建 gin Engine 实例
    engine := &Engine{
        // 路由组实例
        RouterGroup: RouterGroup{
            Handlers: nil,
            basePath: "/",
            root:     true,
        },
        // ...
        // 9 棵路由压缩前缀树，对应 9 种 http 方法
        trees:                  make(methodTrees, 0, 9),
        // ...
    }
    engine.RouterGroup.engine = engine     
    // gin.Context 对象池   
    engine.pool.New = func() any {
        return engine.allocateContext(engine.maxParams)
    }
    return engine
}
```

### 注册 middleware
通过 Engine.Use 方法可以实现中间件的注册，会将注册的 middlewares 添加到 RouterGroup.Handlers 中. 后续 RouterGroup 下新注册的 handler 都会在前缀中拼上这部分 group 公共的 handlers.

![注册 middleware](gin-images/3.png)
以 http post 为例，注册 handler 方法调用顺序为 RouterGroup.POST-> RouterGroup.handle，接下来会完成三个步骤：

+ 拼接出待注册方法的完整路径 absolutePath
+ 拼接出代注册方法的完整处理函数链 handlers
+ 以 absolutePath 和 handlers 组成 kv 对添加到路由树中

```go
func (group *RouterGroup) POST(relativePath string, handlers ...HandlerFunc) IRoutes {
    return group.handle(http.MethodPost, relativePath, handlers)
}
func (group *RouterGroup) handle(httpMethod, relativePath string, handlers HandlersChain) IRoutes {
    absolutePath := group.calculateAbsolutePath(relativePath)
    handlers = group.combineHandlers(handlers)
    group.engine.addRoute(httpMethod, absolutePath, handlers)
    return group.returnObj()
}
```
#### （1）完整路径拼接
结合 RouterGroup 中的 basePath 和注册时传入的 relativePath，组成 absolutePath

```go
func (group *RouterGroup) calculateAbsolutePath(relativePath string) string {
    return joinPaths(group.basePath, relativePath)
}
 
func joinPaths(absolutePath, relativePath string) string {
    if relativePath == "" {
        return absolutePath
    }


    finalPath := path.Join(absolutePath, relativePath)
    if lastChar(relativePath) == '/' && lastChar(finalPath) != '/' {
        return finalPath + "/"
    }
    return finalPath
}
```
#### （2）完整 handlers 生成
深拷贝 RouterGroup 中 handlers 和注册传入的 handlers，生成新的 handlers 数组并返回
```go
func (group *RouterGroup) combineHandlers(handlers HandlersChain) HandlersChain {
    finalSize := len(group.Handlers) + len(handlers)
    assert1(finalSize < int(abortIndex), "too many handlers")
    mergedHandlers := make(HandlersChain, finalSize)
    copy(mergedHandlers, group.Handlers)
    copy(mergedHandlers[len(group.Handlers):], handlers)
    return mergedHandlers
}
```
#### （3）注册 handler 到路由树
+ 获取 http method 对应的 methodTree
+ 将 absolutePath 和对应的 handlers 注册到 methodTree 中
```go
func (engine *Engine) addRoute(method, path string, handlers HandlersChain) {
    // ...
    root := engine.trees.get(method)
    if root == nil {
        root = new(node)
        root.fullPath = "/"
        engine.trees = append(engine.trees, methodTree{method: method, root: root})
    }
    root.addRoute(path, handlers)
    // ...
}
```

## 启动服务流程
### 流程入口
```go
func main() {
    // 创建一个 gin Engine，本质上是一个 http Handler
    mux := gin.Default()
    
    // 一键启动 http 服务
    if err := mux.Run(); err != nil{
        panic(err)
    }
}
```
### 启动服务
一键启动 Engine.Run 方法后，底层会将 gin.Engine 本身作为 net/http 包下 Handler interface 的实现类，并调用 http.ListenAndServe 方法启动服务.
```go
func (engine *Engine) Run(addr ...string) (err error) {
    // ...
    err = http.ListenAndServe(address, engine.Handler())
    return
}
```
ListenerAndServe 方法本身会基于主动轮询 + IO 多路复用的方式运行，因此程序在正常运行时，会始终阻塞于 Engine.Run 方法，不会返回

![主动轮询 + IO 多路复用](gin-images/4.png)

### 处理请求

在服务端接收到 http 请求时，会通过 Handler.ServeHTTP 方法进行处理. 而此处的 Handler 正是 gin.Engine，其处理请求的核心步骤如下：

+ 对于每笔 http 请求，会为其分配一个 gin.Context，在 handlers 链路中持续向下传递
+ 调用 Engine.handleHTTPRequest 方法，从路由树中获取 handlers 链，然后遍历调用
+ 处理完 http 请求后，会将 gin.Context 进行回收. 整个回收复用的流程基于对象池管理

```go
func (engine *Engine) ServeHTTP(w http.ResponseWriter, req *http.Request) {
    // 从对象池中获取一个 context
    c := engine.pool.Get().(*Context)
    
    // 重置/初始化 context
    c.writermem.reset(w)
    c.Request = req
    c.reset()
    
    // 处理 http 请求
    engine.handleHTTPRequest(c)


    // 把 context 放回对象池
    engine.pool.Put(c)
}
```
![处理请求](gin-images/5.png)
Engine.handleHTTPRequest 方法核心步骤分为三步：

+ 根据 http method 取得对应的 methodTree
+ 根据 path 从 methodTree 中找到对应的 handlers 链
+ 将 handlers 链注入到 gin.Context 中，通过 Context.Next 方法按照顺序遍历调用 handler

```go
func (engine *Engine) handleHTTPRequest(c *Context) {
    httpMethod := c.Request.Method
    rPath := c.Request.URL.Path
    
    // ...
    t := engine.trees
    for i, tl := 0, len(t); i < tl; i++ {
        // 获取对应的方法树
        if t[i].method != httpMethod {
            continue
        }
        root := t[i].root
        // 从路由树中寻找路由
        value := root.getValue(rPath, c.params, c.skippedNodes, unescape)
        if value.params != nil {
            c.Params = *value.params
        }
        if value.handlers != nil {
            c.handlers = value.handlers
            c.fullPath = value.fullPath
            c.Next()
            c.writermem.WriteHeaderNow()
            return
        }
        // ...
        break
    }
    // ...
}
```

## Gin的路由树
###  策略与原理
![策略与原理](gin-images/6.png)
#### （1）前缀树
前缀树又称 trie 树，是一种基于字符串公共前缀构建索引的树状结构，核心点包括：

+ 除根节点之外，每个节点对应一个字符
+ 从根节点到某一节点，路径上经过的字符串联起来，即为该节点对应的字符串
+ 尽可能复用公共前缀，如无必要不分配新的节点
tries 树在 leetcode 上的题号为 208

#### （2）压缩前缀树
压缩前缀树又称基数树或 radix 树，是对前缀树的改良版本，优化点主要在于空间的节省，核心策略体现在：

倘若某个子节点是其父节点的唯一孩子，则与父节点进行合并


在 gin 框架中，使用的正是压缩前缀树的数据结构.

#### （3）为什么使用压缩前缀树
与压缩前缀树相对的就是使用 hashmap，以 path 为 key，handlers 为 value 进行映射关联，这里选择了前者的原因在于：

+ path 匹配时不是完全精确匹配，比如末尾 ‘/’ 符号的增减、全匹配符号 '*' 的处理等，map 无法胜任（模糊匹配部分的代码于本文中并未体现，大家可以深入源码中加以佐证）
+ 路由的数量相对有限，对应数量级下 map 的性能优势体现不明显，在小数据量的前提下，map 性能甚至要弱于前缀树
+ path 串通常存在基于分组分类的公共前缀，适合使用前缀树进行管理，可以节省存储空间

#### （4）补偿策略
在 Gin 路由树中还使用一种补偿策略，在组装路由树时，会将注册路由句柄数量更多的 child node 摆放在 children 数组更靠前的位置.（把挂载路径数量越多的子节点优先往左侧靠）

这是因为某个链路注册的 handlers 句柄数量越多，一次匹配操作所需要花费的时间就越长，且被匹配命中的概率就越大，因此应该被优先处理.

![补偿策略](gin-images/7.png)

### 核心数据结构

![核心数据结构](gin-images/8.png)
```go
type methodTree struct {
    method string
    root   *node
}
```
node 是 radix tree 中的节点，对应节点含义如下：

+ path：节点的相对路径，拼接上 RouterGroup 中的 basePath 作为前缀后才能拿到完整的路由 path
+ indices：由各个子节点 path 首字母组成的字符串，子节点顺序会按照途径的路由数量 priority进行排序
+ priority：途径本节点的路由数量，反映出本节点在父节点中被检索的优先级
+ children：子节点列表
+ handlers：当前节点对应的处理函数链

```go
type node struct {
    // 节点的相对路径
    path string
    // 每个 indice 字符对应一个孩子节点的 path 首字母
    indices string
    // ...
    // 后继节点数量
    priority uint32
    // 孩子节点列表
    children []*node 
    // 处理函数链
    handlers HandlersChain
    // path 拼接上前缀后的完整路径
    fullPath string
}
```
###  注册到路由树
从路由树中匹配 path 对应 handler 的详细过程
```go
// 插入新路由
func (n *node) addRoute(path string, handlers HandlersChain) {
    fullPath := path
    // 每有一个新路由经过此节点，priority 都要加 1
    n.priority++


    // 加入当前节点为 root 且未注册过子节点，则直接插入由并返回
    if len(n.path) == 0 && len(n.children) == 0 {
        n.insertChild(path, fullPath, handlers)
        n.nType = root
        return
    }
    // 如果之间有过路径的注册

// 外层 for 循环断点
walk:
    for {
        // 获取 node.path 和待插入路由 path 的最长公共前缀长度
        i := longestCommonPrefix(path, n.path)
    
        // 倘若最长公共前缀长度小于 node.path 的长度，代表 node 需要分裂
        // 举例而言：node.path = search，此时要插入的 path 为 see
        // 最长公共前缀长度就是 2，len(n.path) = 6
        // 需要分裂为  se -> arch
                       // -> e    
        if i < len(n.path) {
        // 原节点分裂后的后半部分，对应于上述例子的 arch 部分
            child := node{
                path:      n.path[i:],
                // 原本 search 对应的参数都要托付给 arch
                indices:   n.indices,
                children: n.children,              
                handlers:  n.handlers,
                // 新路由 see 进入时，先将 search 的 priority 加 1 了，此时需要扣除 1 并赋给 arch
                priority:  n.priority - 1,
                fullPath:  n.fullPath,
            }


            // 先建立 search -> arch 的数据结构，后续调整 search 为 se
            n.children = []*node{&child}
            // 设置 se 的 indice 首字母为 a
            n.indices = bytesconv.BytesToString([]byte{n.path[i]})
            // 调整 search 为 se
            n.path = path[:i]
            // search 的 handlers 都托付给 arch 了，se 本身没有 handlers
            n.handlers = nil           
            // ...
        }


        // 最长公共前缀长度小于 path，正如 se 之于 see
        if i < len(path) {
            // path see 扣除公共前缀 se，剩余 e
            path = path[i:]
            c := path[0]            


            // 根据 node.indices，辅助判断，其子节点中是否与当前 path 还存在公共前缀       
            for i, max := 0, len(n.indices); i < max; i++ {
               // 倘若 node 子节点还与 path 有公共前缀，则令 node = child，并调到外层 for 循环 walk 位置开始新一轮处理
                if c == n.indices[i] {                   
                    i = n.incrementChildPrio(i)
                    n = n.children[i]
                    continue walk
                }
            }
            
            // node 已经不存在和 path 再有公共前缀的子节点了，则需要将 path 包装成一个新 child node 进行插入      
            // node 的 indices 新增 path 的首字母    
            n.indices += bytesconv.BytesToString([]byte{c})
            // 把新路由包装成一个 child node，对应的 path 和 handlers 会在 node.insertChild 中赋值
            child := &node{
                fullPath: fullPath,
            }
            // 新 child node append 到 node.children 数组中
            n.addChild(child)
            n.incrementChildPrio(len(n.indices) - 1)
            // 令 node 指向新插入的 child，并在 node.insertChild 方法中进行 path 和 handlers 的赋值操作
            n = child          
            n.insertChild(path, fullPath, handlers)
            return
        }
        // 此处的分支是，path 恰好是其与 node.path 的公共前缀，则直接复制 handlers 即可
        // 例如 se 之于 search
        if n.handlers != nil {
            panic("handlers are already registered for path '" + fullPath + "'")
        }
        n.handlers = handlers
        // ...
        return
}
}
```
下面这段代码体现了，在每个 node 的 children 数组中，child node 在会依据 priority 有序排列，保证 priority 更高的 child node 会排在数组前列，被优先匹配.
```go
func (n *node) incrementChildPrio(pos int) int {
    cs := n.children
    cs[pos].priority++
    prio := cs[pos].priority

    // Adjust position (move to front)
    newPos := pos
    for ; newPos > 0 && cs[newPos-1].priority < prio; newPos-- {
        // Swap node positions
        cs[newPos-1], cs[newPos] = cs[newPos], cs[newPos-1]
    }

    // Build new index char string
    if newPos != pos {
        n.indices = n.indices[:newPos] + // Unchanged prefix, might be empty
            n.indices[pos:pos+1] + // The index char we move
            n.indices[newPos:pos] + n.indices[pos+1:] // Rest without char at 'pos'
    }

    return newPos
}
```
### 检索路由树
从路由树中匹配 path 对应 handler 的详细过程
```go
type nodeValue struct {
    // 处理函数链
    handlers HandlersChain
    // ...
}
```
```go
// 从路由树中获取 path 对应的 handlers 
func (n *node) getValue(path string, params *Params, skippedNodes *[]skippedNode, unescape bool) (value nodeValue) {
    var globalParamsCount int16

// 外层 for 循环断点
walk: 
    for {
        prefix := n.path
        // 待匹配 path 长度大于 node.path
        if len(path) > len(prefix) {
            // node.path 长度 < path，且前缀匹配上
            if path[:len(prefix)] == prefix {
                // path 取为后半部分
                path = path[len(prefix):]
                // 遍历当前 node.indices，找到可能和 path 后半部分可能匹配到的 child node
                idxc := path[0]
                for i, c := range []byte(n.indices) {
                    // 找到了首字母匹配的 child node
                    if c == idxc {
                        // 将 n 指向 child node，调到 walk 断点开始下一轮处理
                        n = n.children[i]
                        continue walk
                    }
                }


                // ...
            }
        }

        // 倘若 path 正好等于 node.path，说明已经找到目标
        if path == prefix {
            // ...
            // 取出对应的 handlers 进行返回 
            if value.handlers = n.handlers; value.handlers != nil {
                value.fullPath = n.fullPath
                return
            }


            // ...           
        }

        // 倘若 path 与 node.path 已经没有公共前缀，说明匹配失败，会尝试重定向，此处不展开
        // ...
 }  
}
```

## gin.Context
### 核心数据结构
![核心数据结构](gin-images/9.png)

gin.Context 的定位是对应于一次 http 请求，贯穿于整条 handlersChain 调用链路的上下文，其中包含了如下核心字段：

• Request/Writer：http 请求和响应的 reader、writer 入口
• handlers：本次 http 请求对应的处理函数链
• index：当前的处理进度，即处理链路处于函数链的索引位置
• engine：Engine 的指针
• mu：用于保护 map 的读写互斥锁
• Keys：缓存 handlers 链上共享数据的 map

### 复用策略
![复用策略](gin-images/10.png)

gin.Context 作为处理 http 请求的通用数据结构，不可避免地会被频繁创建和销毁. 为了缓解 GC 压力，gin 中采用对象池 sync.Pool 进行 Context 的缓存复用，处理流程如下：
+ http 请求到达时，从 pool 中获取 Context，倘若池子已空，通过 pool.New 方法构造新的 Context 补上空缺
+ http 请求处理完成后，将 Context 放回 pool 中，用以后续复用

sync.Pool 并不是真正意义上的缓存，将其称为回收站或许更加合适，放入其中的数据在逻辑意义上都是已经被删除的，但在物理意义上数据是仍然存在的，这些数据可以存活两轮 GC 的时间，在此期间倘若有被获取的需求，则可以被重新复用.

对象池中,针对每一个p会放有一个私有的凹槽,存放一个唯一的sync.Pool的实例; sharedList中会存放大量gin.Context实例.当某一个goroutine在某一个p下面调度运行的时候,先尝试去拿当前的这个p所私有的凹槽,看有没有可复用的gin.Context实例,有的话不需要加锁,不会有其他的p去介入,不会有并发安全问题;如果凹槽是空的,则会尝试去访问自己的sharedList,需要进行加锁.可多个p之间进行访问;如果自己的sharedList对应的为空.则会尝试从其他p的sharedList中去获取对应的实例,如果还没有获取到,则会有一个轮次交替.兜底情况会当所有情况下都没有可复用的context实例,调用在sync.Pool中兜底的构造方法,分配内存构造新的context实例,

```go
type Engine struct {
    // context 对象池
    pool             sync.Pool
}
 
func New() *Engine {
    // ...
    engine.pool.New = func() any {
        return engine.allocateContext(engine.maxParams)
    }
    return engine
}
 
func (engine *Engine) allocateContext(maxParams uint16) *Context {
    v := make(Params, 0, maxParams)
   // ...
    return &Context{engine: engine, params: &v, skippedNodes: &skippedNodes}
}
```

### 分配与回收时机
![分配与回收时机](gin-images/11.png)

gin.Context 分配与回收的时机是在 gin.Engine 处理 http 请求的前后，位于 Engine.ServeHTTP 方法当中：

+ 从池中获取 Context
+ 重置 Context 的内容，使其成为一个空白的上下文
+ 调用 Engine.handleHTTPRequest 方法处理 http 请求
+ 请求处理完成后，将 Context 放回池中

```go
func (engine *Engine) ServeHTTP(w http.ResponseWriter, req *http.Request) {
    // 从对象池中获取一个 context
    c := engine.pool.Get().(*Context)
    // 重置/初始化 context
    c.writermem.reset(w)
    c.Request = req
    c.reset()
    // 处理 http 请求
    engine.handleHTTPRequest(c)
    
    // 把 context 放回对象池
    engine.pool.Put(c)
}
```

### gin.Context的使用时机
#### （1）handlesChain 入口
在 Engine.handleHTTPRequest 方法处理请求时，会通过 path 从 methodTree 中获取到对应的 handlers 链，然后将 handlers 注入到 Context.handlers 中，然后启动 Context.Next 方法开启 handlers 链的遍历调用流程.

```go
func (engine *Engine) handleHTTPRequest(c *Context) {
    // ...
    t := engine.trees
    for i, tl := 0, len(t); i < tl; i++ {
        if t[i].method != httpMethod {
            continue
        }
        root := t[i].root        
        value := root.getValue(rPath, c.params, c.skippedNodes, unescape)
        // ...
        if value.handlers != nil {
            c.handlers = value.handlers
            c.fullPath = value.fullPath
            c.Next()
            c.writermem.WriteHeaderNow()
            return
        }
        // ...
    }
    // ...
}
```
#### （2）handlersChain 调用
![handlersChain 调用](gin-images/12.png)
推进 handlers 链调用进度的方法正是 Context.Next. 可以看到其中以 Context.index 为索引，通过 for 循环依次调用 handlers 链中的 handler.
```go
func (c *Context) Next() {
    c.index++
    for c.index < int8(len(c.handlers)) {
        c.handlers[c.index](c)
        c.index++
    }
}
```
Context 本身会暴露于调用链路中，因此用户可以在某个 handler 中通过手动调用 Context.Next 的方式来打断当前 handler 的执行流程，提前进入下一个 handler 的处理中.

本质上是一个方法压栈调用的行为，因此在后置位 handlers 链全部处理完成后，最终会回到压栈前的位置，执行当前 handler 剩余部分的代码逻辑.
![压栈调用](gin-images/13.png)


用户可以在某个 handler 中，于调用 Context.Next 方法的前后分别声明前处理逻辑和后处理逻辑，这里的“前”和“后”相对的是后置位的所有 handler 而言

```go
func myHandleFunc(c *gin.Context){
    // 前处理
    preHandle()  
    c.Next()
    // 后处理
    postHandle()
}
```

用户可以在某个 handler 中通过调用 Context.Abort 方法实现 handlers 链路的提前熔断.


其实现原理是将 Context.index 设置为一个过载值 63，导致 Next 流程直接终止. 这是因为 handlers 链的长度必须小于 63，否则在注册时就会直接 panic. 因此在 Context.Next 方法中，一旦 index 被设为 63，则必然大于整条 handlers 链的长度，for 循环便会提前终止.

```go
const abortIndex int8 = 63

func (c *Context) Abort() {
    c.index = abortIndex
}
```

用户还可以通过 Context.IsAbort 方法检测当前 handlerChain 是出于正常调用，还是已经被熔断.

注册 handlers，倘若 handlers 链长度达到 63，则会 panic
```go
func (group *RouterGroup) combineHandlers(handlers HandlersChain) HandlersChain {
    finalSize := len(group.Handlers) + len(handlers)
    // 断言 handlers 链长度必须小于 63
    assert1(finalSize < int(abortIndex), "too many handlers")
    // ...
}
```

#### （3）共享数据存取
![共享数据存取](gin-images/14.png)
gin.Context 作为 handlers 链的上下文，还提供对外暴露的 Get 和 Set 接口向用户提供了共享数据的存取服务，相关操作都在读写锁的保护之下，能够保证并发安全.

```go
type Context struct {
    // ...
    // 读写锁，保证并发安全
    mu sync.RWMutex

    // key value 对存储 map
    Keys map[string]any
}
```

```go
func (c *Context) Get(key string) (value any, exists bool) {
    c.mu.RLock()
    defer c.mu.RUnlock()
    value, exists = c.Keys[key]
    return
}
 
func (c *Context) Set(key string, value any) {
    c.mu.Lock()
    defer c.mu.Unlock()
    if c.Keys == nil {
        c.Keys = make(map[string]any)
    }


    c.Keys[key] = value
}
```

## 总结
+ gin 将 Engine 作为 http.Handler 的实现类进行注入，从而融入 Golang net/http 标准库的框架之内
+ gin 中基于 handler 链的方式实现中间件和处理函数的协调使用
+ gin 中基于压缩前缀树的方式作为路由树的数据结构，对应于 9 种 http 方法共有 9 棵树
+ gin 中基于 gin.Context 作为一次 http 请求贯穿整条 handler chain 的核心数据结构
+ gin.Context 是一种会被频繁创建销毁的资源对象，因此使用对象池 sync.Pool 进行缓存复用











