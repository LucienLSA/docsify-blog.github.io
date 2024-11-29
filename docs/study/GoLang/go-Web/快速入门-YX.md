# Go Web 快速入门
# 介绍
来着b站软件工艺师视频教程

(Go Web 编程快速入门【Golang/Go语言】)[https://www.bilibili.com/video/BV1Xv411k7Xn?vd_source=917ef87e48a267f0acc88f766dea0a6e&spm_id_from=333.788.videopod.episodes]

## 小demo
仅用两行代码创建一个web
```go
func main() {
	// 调用适配器处理函数，两个参数，一个http地址，一个是hangler函数
	http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
		w.Write([]byte("hello go"))
	})

	//设置web 服务器,俩个参数，一个监听地址port,一个handler,默认是nil， 采用多路复用mux
	http.ListenAndServe("localhost:8080", nil)

}
```

## 处理handle请求
Handler处理进来的HTTP请求，go语言会创建一个goroutine

### http.ListenAndServe()
+ 第一个参数是网络地址
    + 如何为""，网络接口在80端口
+ 第二个参数是handler
    - 可以是nil，即DefaultServeMux

### DefaultServeMux
一个multiplexer 多路复用器，它也是一个Handler

```go
	http.ListenAndServe("localhost:8080", nil)
```

http.Server是一个struct，也可以自定义server，以下为常用的方法

```go
	server := http.Server{
		Addr: ("localhost:8080"),
		Handler: nil,
	}
	server.ListenAndServe()
```

### Handler
Handler是一个接口，定义了ServeHTTP方法

```go
type Handler interface {
    ServeHTTP(ResponseWriter, *Request)
}
```

也可自定义Handler，实现ServeHTTP方法

+ 多个Handler情况，处理不同的路径 http.Handle()
    + 调用http.Handler，实际上调用的是DefaultServeMux的Handle方法 

#### 第一个函数http.Handle
```go
type helloHandler struct{}

func (h helloHandler) ServeHTTP(w http.ResponseWriter, r *http.Request) {
    w.Write([]byte("hello go"))
}

type aboutHandler struct{}

func (a aboutHandler) ServeHTTP(w http.ResponseWriter, r *http.Request) {
    w.Write([]byte("about go"))
}

func main() {
    mh := helloHandler{}
    ah := aboutHandler{}
    server := http.Server{
        Addr: ("localhost:8080"),
        Handler: nil,
    }
    http.Handle("/hello", &mh)
    http.Handle("/about", &ah)
    server.ListenAndServe()
}
```

#### 第二个函数http.HandleFunc

将某个具有适当签名的函数，适配成一个Handler，这个Handler具有方法

```go
func welcome(w http.ResponseWriter, r *http.Request) {
    w.Write([]byte("welcome to go web"))
}

func main(){
    http.HandleFunc("/home",func(w http.ResponseWriter, r *http.Request){
        w.Write([]byte("home page"))
    })
    http.HandlerFunc("/welcome", welcome)
    http.Handle("/welcome", http.HandlerFunc(welcome)) // http.HandlerFunc为一个函数结构，实现也是serveHTTP方法 符合Handler接口，即可以将Handler函数转化为Handler
}
```

## 内置的Handlers

### http.NotFoundHandler