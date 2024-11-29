

## Web
Web基于HTTP协议交互的应用网络，使用浏览器或APP访问各种资源

一个请求一个响应


### 简单的网页web开发

html和js创建，配置服务端程序

```go
import (
	"fmt"
	"io/ioutil"
	"net/http"
)

func sayHello(w http.ResponseWriter, r *http.Request) {
	// _, _ = fmt.Fprintln(w, "<h1>hello golang!<h1><h2>how are you?<h2>")
	b, _ := ioutil .ReadFile("./hello. txt")
	_, _ = fmt.Fprintln(w, string(b))
}

func main() {
	http.HandleFunc("/hello", sayHello)
	err := http.ListenAndServe(":9090", nil)
	if err != nil {
		fmt.Printf("http server failed, err:%v\n", err)
		return
	}
}
```
前后端分离，后端只返回数据，读取html和json文件

#### Restful API开发
HTTP的请求，将代码中加入以下四种常用的请求，并在请求测试网站上测试
[https://app.apifox.com/project/4897433](apifox)
1. GET获取资源
浏览器对服务器发送请求，获取信息

2. POST新建资源
注册、创建，发送给服务器

3. PUT更新资源
修改数据信息

4. DELETE删除资源
注销删除

## gin渲染
### HTML渲染
在前后端不分离的Web框架中，后端将一些数据渲染到HTML文档中实现动态网页效果

### .tmpl文件Associations .html
[https://blog.csdn.net/u010168781/article/details/106758948](在vscode中添加对模板文件tmpl的html语法自动补全的支持)

### template
模板文件必须使用UTF8编码，用{ . }传入数据，而复杂数据可用{ { . } }访问结构体字段。

1. 定义模板文件
2. 解析模板文件
3. 模板渲染

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <title>Hello</title>
</head>
<body>
    <p>Hello { { . } } </p>
</body>
</html>
```

*渲染的过程即在渲染部分中的变量去替换模板定义好的占位符或者字段*

```go
package main

import (
	"fmt"
	"html/template"
	"net/http"
)

func sayHello(w http.ResponseWriter, r *http.Request) {
	// 2. 解析模板
	t, err := template.ParseFiles("./hello.tmpl")
	if err != nil {
		fmt.Printf("Parse template failed, err:%v", err)
		return
	}
	// 3. 渲染模板
	name := "小王子"
	err = t.Execute(w, name)
	if err != nil {
		fmt.Printf("render template failed, err:%v", err)
		return
	}
}
func main() {
	http.HandleFunc("/", sayHello)
	err := http.ListenAndServe(":9000", nil)
	if err != nil {
		fmt.Printf("HTTP server start failed, err:%v", err)
		return
	}
}
```

#### 结构体变量
成员变量需要首字母大小写被访问
```html
    <p>Hello { {.Name} }</p>
    <p>年龄 { {.Age} }</p>
    <p>性别 { {.Gender} }</p>
```
```go
	u1 := User{
		Name:   "小王子",
		Gender: "男",
		Age:    18,
	}
	err = t.Execute(w, u1)
	if err != nil {
		fmt.Printf("render template failed, err:%v", err)
		return
	}
```
#### map变量(键值)或传入多个变量
```html
<p>u1</p>
    <p>Hello { { .u1.Name } }</p>
    <p>年龄 { { .u1.Age } }</p>
    <p>性别 { { .u1.Gender } }</p>
<p>m1</p> 
<p>{ { .m1.name } }</p> 
<p>{ { .m1.age } }</p>
<p>{ { .m1.gender } }</p>
```
```go
	u1 := User{
		Name:   "小王子",
		Gender: "男",
		Age:    18,
	}
	m1 := map[string]interface{}{
		"name":   "小王子",
		"gender": "男",
		"age":    18,
	}
	err = t.Execute(w, map[string]interface{}{
		"u1": u1,
		"m1": m1,
	})
```
#### 条件判断

#### with作用域
```html
{ {with .m1 } }
    <p>Hello { { .Name } }</p>
    <p>年龄 { { .Age } }</p>
    <p>性别 { { .Gender } }</p>
{ {end} }
```

#### index取索引值

#### 自定义模板

```go
func f1(w http.ResponseWriter, r *http.Request) {
	// 定义一个函数kua
	// 一个返回值或者两个返回值，第二个返回值必须为error类型
	k := func(name string) (string, error) {
		return name + "真帅", nil
	}

	t := template.New("f.tmpl") // 创建一个f的模板对象，名字与模板对应
	// 告诉模板，加入一个自定义函数kua
	t.Funcs(template.FuncMap{
		"kua11": k,
	})
	_, err := t.ParseFiles("./f.tmpl") // 解析的模板
	if err != nil {
		fmt.Printf("parse template failed, err:%v\n", err)
		return
	}
	name := "小王子"

	t.Execute(w, name)
}
```
```html
<body>
{ { kua11 . } }
</body>
```

#### 嵌套模板
```html
<body>
    
    <h1>测试嵌套template语法</h1>
    <hr>
    <!--嵌套另外一个单独的模板文件-->>
    { {template "ul.tmpl"} }
    <hr>
    <!--嵌套另外一个define定义的模板-->>
    { {template "ol.tmpl"} }
    <div>你好，{ { . } }</div>
</body>
</html>

<!--define定义一个模板-->>
{ { define "ol.tmpl"} }
<ol>
    <li>吃饭</li>
    <li>睡觉</li>
    <li>打豆豆</li>
</ol>
```

```go
func demo1(w http.ResponseWriter, r *http.Request) {
	// 定义模板
	// 解析模板
	// 先写t.tmpl，为父；后写ul.tmpl为子，嵌套到t中的模板
	t, err := template.ParseFiles("./t.tmpl", "./ul.tmpl")
	if err != nil {
		fmt.Printf("parse template failed,err:%v\n", err)
		return
	}
	name := "小王子"
	t.Execute(w, name)
	// 渲染模板
}
```

#### block模板继承
定义一组根模板，再重新定义块模板

创建一个文件夹templates，定义一个base.tmpl模板。创建一个index.tmpl，继承根模板，重新定义块
```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <title>Go Templates</title>
</head>
<body>
<div class="container-fluid">
    { {block "content" . } } { {end} }
</div>
</body>
</html>
```

```html
<!--继承根模板-->
{ {template "base.tmpl" . } }
<!--重新定义块模板-->
{ {define "content"} }
<h1>
    这是home页面
</h1>
<p>Hello { { . } }</p>
{ {end} }
```

```html
<!--继承根模板-->
{ {template "base.tmpl" . } }
<!--重新定义块模板-->
{ {define "content"} }
<h1>
    这是index页面
</h1>
<p>Hello { { . } }</p>
{ {end} }
```

```go
func index(w http.ResponseWriter, r *http.Request) {
	// 先写根模板，再写继承模板
	t, err := template.ParseFiles("./templates/base.tmpl", "./templates/index.tmpl")
	if err != nil {
		fmt.Printf("parse template failed,err:%v\n", err)
		return
	}
	name := "小王子 index"
	// 指定模板
	t.ExecuteTemplate(w, "index.tmpl", name)
	// 渲染模板
}
func home(w http.ResponseWriter, r *http.Request) {
	// 先写根模板，再写继承模板
	t, err := template.ParseFiles("./templates/base.tmpl", "./templates/home.tmpl")
	if err != nil {
		fmt.Printf("parse template failed,err:%v\n", err)
		return
	}
	name := "小王子 home"
	t.ExecuteTemplate(w, "home.tmpl", name)
	// 渲染模板
}
```


#### 修改模板中的默认标识符
解析模板之前，定义标识符
```go
t. err := template.New("index.tmpl").Delims("{[", "]}").ParseFiles("./index.tmpl")
if err != nil {
	fmt.Printf("parse template failed, err: %v\n", err)
	return
}
name := "小王子"
err = t.Execute(w, name)
if err != nil {
	fmt.Printf("excute template failed, err: %v\n", err)
	return
}
```

#### text/template与html/template区别
html/template对有风险内容进行转义，防止跨站脚本攻击（将用户的输入作为代码运行）

比如用户再网站上的评论是：<script>alert(123)</script> ，使得网页出错

如果相信用户内容，不进行转义。编写safe函数，返回template.HTML类型内容

```go
func xss(w http.ResponseWriter, r *http.Request) {
	// 在解析模板之前，自定义函数safe
	t, err := template.New("xss.tmpl").Funcs(template.FuncMap{
		"safe": func(str string) template.HTML {
			return template.HTML(str)
		},
	}).ParseFiles("./xss.tmpl")
	if err != nil {
		fmt.Printf("parse template failed,err:%v\n", err)
		return
	}
	// 渲染模板
	str1 := "<script>alert(123);</script>"
	str2 := "<a href='http://lucienlsa.github.io'>lucien的博客"
	t.Execute(w, map[string]string{
		"str1": str1,
		"str2": str2,
	})
}
```

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Hello</title>
</head>
<body>
用户的评论是：{ { .str1 } }
用户的评论是：{ { .str2 | safe} }
</body>
</html>
```

### gin框架下HTML渲染
创建templates文件夹，再分别创建posts和users文件夹的index.tmpl，并定义文件
```html
{{define "posts/index.tmpl"}}
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>posts/index</title>
</head>
<body>
    {{.title}}
</body>
</html>
{{end}}
```
```go
func main() {
	r := gin.Default()
	// gin框架中添加自定义函数
	r.SetFuncMap(template.FuncMap{
		"safe": func(str string) template.HTML {
			return template.HTML(str)
		},
	})
	// 模板解析
	// r.LoadHTMLFiles("./templates/posts/index.tmpl", "./templates/users/index.tmpl")
	// 全局加载模板文件
	r.LoadHTMLGlob("templates/**/*")
	// 创建两个路由
	r.GET("/posts/index", func(c *gin.Context) {
		// http请求的状态码
		// c.HTML(200)
		// 模板渲染
		c.HTML(http.StatusOK, "posts/index.tmpl", gin.H{ // H is a shortcut for map[string]any
			"title": "lucien.github.io",
		})
	})
	r.GET("/users/index", func(c *gin.Context) {
		// http请求的状态码
		// c.HTML(200)
		// 模板渲染
		c.HTML(http.StatusOK, "users/index.tmpl", gin.H{ // H is a shortcut for map[string]any
			"title": "<a href='https://lucienlis.github.io'>lucien的博客</a>",
		})
	})
	// 启动server
	r.Run(":9090")
}
```


#### 报错 html/template: “xxxxxxxx“ is undefined

在模板开头添加
{ {define “name”} }
在模板最后添加{ {end} }
注意：此处的name需要与main.go中的渲染模板处的name相对应，否则仍然找不到模板

**如果没有define起名字，则默认的html为_.html**

#### 静态文件
创建statics文件夹，再创建index.css文件以及index.js文件

浏览器发两次请求，渲染时额外需要.css文件，加载静态文件时找目录中对应文件，将其发送给浏览器

```css
body {
    background-color: cadetblue;
}
```

```js
alert(123);
```

在tmpl中的head处引用css
```html
<link rel="stylesheet" href="/xxx/index.css">
```

在tmpl中的body处引用js
```js
<script src="/xxx/index.js"></script>
```

模板解析前加入
```go
r.Static("/xxx", "./statics")
```

#### 使用前端模板网站上的模板

将static文件导入，.html文件放在templates或者其他根目录下

在main.go文件中添加
```go
	// 返回在网上下载的模板
	r.GET("/home", func(c *gin.Context) {
		c.HTML(http.StatusOK, "home.html", nil)
	})
```

由于在复制导入的html文件中，默认使用的/home，在对应的名字也为默认"home.html"。

导入css和js文件时，需要对应好html文件中的，比如
```html
<link rel="stylesheet" href="static/css/icofont.min.css">
```
同理对于css、js、picture

则在main.go中，加载静态文件，在./statics文件夹下查找文件
```go
r.Static("/static", "./statics")
```

### JSON渲染
返回JSON格式数据，前端与后端数据交互的格式

#### map或者结构体两个方法
结构体定义的变量名首字母需要大写，因为JSON，反射取值读取结构体字段

如果需要得到小写首字母，使用tag

```go
func main() {
	r := gin.Default()
	// 方法1：使用map
	r.GET("/json", func(c *gin.Context) {
		// data := map[string]interface{}{
		// 	"name":    "小王子",
		// 	"message": "hello world",
		// 	"age":     18,
		// }
		//  gin内置方法
		data := gin.H{"name": "小王子", "message": "hello world", "age": 18}
		c.JSON(http.StatusOK, data)
	})
	// 方法2: 结构体
	type msg struct {
		Name    string `json:"name"`
		Message string
		Age     int
	}
	r.GET("/anotherjson", func(c *gin.Context) {
		data := msg{
			"小王子",
			"hello golang",
			18,
		}
		c.JSON(http.StatusOK, data) // json序列化
	})
	r.Run(":9090")
}
```

### gin获取querystring参数
比如在搜索网站，sogou.com/web?query=你好

GET请求 URL ? 后面是querystring参数。 key=value格式，多个key-value用 & 链接
```
127.0.0.1:9090/web?query=小王子&age=18
```

```go
func main() {
	r := gin.Default()

	r.GET("/web", func(c *gin.Context) {
		// 获取浏览器发的请求，携带的query string参数
		name := c.Query("query") // 通过Query获取请求query string参数
		age := c.Query("age")
		// name := c.DefaultQuery("query", "somebody") // 查询不到query，则指定默认值
		// name, ok := c.GetQuery("query") // 取到返回(值, true)，取不到ok返回("",false)
		// if !ok {
		// 	// 取不到，可设置默认参数
		// 	name = "somebody"
		// }
		c.JSON(http.StatusOK, gin.H{
			"name": name,
			"age":  age,
		})
	})

	r.Run(":9090")
}
```

### gin获取form参数
请求的数据通过form表单提交， 通常用post方法

一个请求访问的地址可以相同，但是一次请求对应一个响应，login加载渲染为一次GET请求，输入用户账户和密码后为一次POST请求
/login
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title> LOGIN</title>
</head>
<body>
    <form action="/login" method="post" novalidate autocomplete="off">
    <div>
        <label for="username">username:</label>
        <input type="text" name="username" id="username" >
    </div>
    <div>
        <label for="password">password</label>
        <input type="password" name="password" id="password" >
    </div>
    <div>
        <input type="submit" value="登录" >
    </div>
    </form>
</body>
</html>
```

```go
func main() {
	r := gin.Default()
	r.LoadHTMLFiles("./login.html", "./index.html")
	// /login 的get请求
	r.GET("/login", func(c *gin.Context) {
		c.HTML(http.StatusOK, "login.html", nil)
	})
	// /login 的post请求
	r.POST("/login", func(c *gin.Context) {
		// 获取form表单提交的数据
		// username := c.PostForm("username")
		// password := c.PostForm("password") // 取到返回值，取不到返回空字符串
		// 未取到为默认值
		// username := c.DefaultPostForm("username", "somebody")
		// password := c.DefaultPostForm("password", "****")
		username, ok := c.GetPostForm("username")
		if !ok {
			username = "sb"
		}
		password, ok := c.GetPostForm("password")
		if !ok {
			password = "*****"
		}
		c.HTML(http.StatusOK, "index.html", gin.H{
			"Name":     username,
			"Password": password,
		})
	})
	r.Run(":9090")
}
```

### gin获取url路径参数
```go
func main() {
	r := gin.Default()
	r.GET("/:name/:age", func(c *gin.Context) {
		// 获取路径参数
		name := c.Param("name")
		age := c.Param("age")
		c.JSON(http.StatusOK, gin.H{
			"name": name,
			"age":  age,
		})
	})
	r.GET("/blog/:year/:month", func(c *gin.Context) {
		year := c.Param("year")
		month := c.Param("month")
		c.JSON(http.StatusOK, gin.H{
			"year":  year,
			"month": month,
		})
	})
	r.Run(":9090")
}
```

### gin参数绑定
为方便获取请求的相关参数，基于content-type识别的请求数据类型并利用反射机制自动提前请求中的QueryString/form表单/JSON/XML等参数到结构体中

#### form表单参数
设置标签
```go
type UserInfo struct {
	Username string `form:"username" json:"usr"`
	Password string `form:"password" json:"pwd"`
}
```

```go
	r.POST("/form", func(c *gin.Context) {
		var u UserInfo          // 声明一个UserInfo类型的变量u
		err := c.ShouldBind(&u) // 将请求的参数返回，函数方法都是值传递，要使修改其本身的值应该传地址
		if err != nil {
			c.JSON(http.StatusOK, gin.H{
				"error": err.Error(),
			})
		} else {
			fmt.Printf("%#v\n", u) // 先打印结构体名字，再打印结构体
			c.JSON(http.StatusOK, gin.H{
				"status": "ok",
			})
		}
	})
```

提示：通过apifox或者postman网站，模拟给url发送GET/POST/DELETE等请求。或者自己写另外一个url使其作出action给指定的url，发送请求，即使用浏览器发送请求

#### JSON格式数据
可使用apifox模拟给url发送请求，返回JSON格式数据
```go
	r.POST("/json", func(c *gin.Context) {
		var u UserInfo          // 声明一个UserInfo类型的变量u
		err := c.ShouldBind(&u) // 将请求的参数返回，函数方法都是值传递，要使修改其本身的值应该传地址
		if err != nil {
			c.JSON(http.StatusOK, gin.H{
				"error": err.Error(),
			})
		} else {
			fmt.Printf("%#v\n", u) // 先打印结构体名字，再打印结构体
			c.JSON(http.StatusOK, gin.H{
				"status": "ok",
			})
		}
	})
```

ShouldBind 会按下面顺序解析请求中的数据完成绑定
1. GET请求，只使用Form绑定引擎
2. POST请求，检查content-type是否为JSON或XML，然后再使用Form(form-data)


### gin文件上传
从/index发出post请求上传文件，到/upload，从请求中读取文件，将文件参数进行保存
```go
func main() {
	r := gin.Default()
	// 使用multipart forms提交文件的默认内存限制是32MiB
	r.MaxMultipartMemory = 8 << 20 // 设置提交文件内存
	r.LoadHTMLFiles("./index.html")
	r.GET("/index", func(ctx *gin.Context) {
		ctx.HTML(http.StatusOK, "index.html", nil)
	})
	r.POST("/upload", func(ctx *gin.Context) {
		// 从请求中读取文件
		f, err := ctx.FormFile("f1") // 从请求中获取携带的参数
		if err != nil {
			ctx.JSON(http.StatusBadRequest, gin.H{
				"error": err.Error(),
			})
		} else {
			// 将读取到的文件保存在本地（服务端本地）
		}
		// dst := fmt.Sprint("./%s", f.Filename)
		dst := path.Join("./", f.Filename)
		ctx.SaveUploadedFile(f, dst)

		ctx.JSON(http.StatusOK, gin.H{
			"status": "OK",
		})
	})
	r.Run(":9090")
}
```
同理如果是多个文件上传，使用for 循环


### gin请求重定向
访问网站，需要跳转到其他页面，再跳回来

#### HTTP重定向，内部和外部都支持
```go
	r.GET("/index", func(ctx *gin.Context) {
		// ctx.JSON(http.StatusOK, gin.H{
		// 	"status": "ok",
		// })
		// 跳转到baidu
		ctx.Redirect(http.StatusMovedPermanently, "http://www.baidu.com")
	})
```

#### 路由重定向
```go
	r.GET("/a", func(ctx *gin.Context) {
		// 跳转到/b对应的路由处理函数
		ctx.Request.URL.Path = "/b" // 请求的URL修改
		r.HandleContext(ctx)
	})
	r.GET("/b", func(ctx *gin.Context) {
		ctx.JSON(http.StatusOK, gin.H{
			"message": "bbbbb",
		})
	})
```

### gin路由
#### 普通路由
使用any
```go
	r.Any("/user", func(ctx *gin.Context) {
		switch ctx.Request.Method {
		case "GET":
			ctx.JSON(http.StatusOK, gin.H{"method": "GET"})
		case http.MethodPost:
			ctx.JSON(http.StatusOK, gin.H{"method": "POST"})
		}	
	})
```
为没有配置处理函数的路由添加处理程序，默认情况下返回404代码
```go
	r.NoRoute(func(ctx *gin.Context) {
		ctx.JSON(http.StatusNotFound, gin.H{
			"msg": "hhhhhhhhh",
		})
	})
```

#### 路由组
减少冗余，使拥有共同URL前缀的路由划分一个路由组
```go
	videoGroup := r.Group("/video")
	{
		videoGroup.GET("/index", func(ctx *gin.Context) {
			ctx.JSON(http.StatusOK, gin.H{"msg": "/video/index"})
		})
		videoGroup.GET("/xxx", func(ctx *gin.Context) {
			ctx.JSON(http.StatusOK, gin.H{"msg": "/video/xxx"})
		})
		videoGroup.GET("/ooo", func(ctx *gin.Context) {
			ctx.JSON(http.StatusOK, gin.H{"msg": "/video/ooo"})
		})
	}
```
路由组可支持嵌套


### gin中间件
在处理请求过程中，加入用户自己的钩子(Hook)函数，被称为中间件，如登录认证、权限校验、数据分页、记录日志和耗时统计等

#### 
中间件必须是一个gin.HandlerFunc类型

#### 注册全局中间件
```go
// Handerfunc
func indexHandler(c *gin.Context) {
	fmt.Println("index111")
	c.JSON(http.StatusOK, gin.H{
		"msg": "index111",
	})
}
```
```go
// 定义一个中间件，统计耗时
func m1(c *gin.Context) {
	fmt.Println("m1 in ...")
	// 计时
	start := time.Now()
	c.Next() // 调用后续的处理函数
	// c.Abort() // 阻止调用后续的处理函数
	cost := time.Since(start)
	fmt.Printf("cost:%v\n", cost)
	fmt.Println("m1 out ...")
}

func m2(c *gin.Context) {
	fmt.Println("m2 in ...")
	c.Next()
	fmt.Println("m2 out ...")
}
```
#### 多个中间件处理方式
类似于入栈出栈
```go
func main() {
	r := gin.Default()
	r.Use(m1, m2) // 全局注册中间件函数m1
	// r.GET("/index111", m1, indexHandler)
	r.GET("/index111", indexHandler)
	r.GET("/shop", func(ctx *gin.Context) {
		ctx.JSON(http.StatusOK, gin.H{
			"msg": "shop",
		})
	})
	r.GET("/user", func(ctx *gin.Context) {
		ctx.JSON(http.StatusOK, gin.H{
			"msg": "user",
		})
	})
	r.Run(":9090")
}
```
c.Abort()  阻止调用后续的处理函数

return 直接在该位置返回，不执行此时的函数

####
```go
// 通常写闭包
func authMiddleware(doCheak bool) gin.HandlerFunc {
	// 连接数据库
	return func(c *gin.Context) {
		if doCheak {
			// 存放具体逻辑
			// 是否登录判断
			// 如果是登录用户
			// c.Next()
			// 不是
			// c.Abort()
		} else {
			c.Next()
		}
	}
}
```

#### 路由组注册中间件
```go
路由组注册中间件方法1
xxGroup := r.Group("/xx", authMiddleware(true))
{
	xxGroup.GET("/index", func(ctx *gin.Context) {
		ctx.JSON(http.StatusOK, gin.H{
			"msg": "xxGroup",
		})
	})
}
路由组注册中间件方法2
xx2Group := r.Group("/xx2")
xx2Group.Use(authMiddleware(true))
{
	xx2Group.GET("/index", func(ctx *gin.Context) {
		ctx.JSON(http.StatusOK, gin.H{
			"msg": "xx2Group",
		})
	})
}
```

#### set 跨中间件存取值
```go
// Handerfunc
func indexHandler(c *gin.Context) {
	fmt.Println("index111")
	name, ok := c.Get("name") // 从上下文取值（跨中间件存取值）
	if !ok {
		name = "匿名用户"
	}
	c.JSON(http.StatusOK, gin.H{
		"msg": name,
	})
}

// 定义一个中间件，统计耗时
func m1(c *gin.Context) {
	fmt.Println("m1 in ...")
	// 计时
	start := time.Now()
	c.Next() // 调用后续的处理函数
	// c.Abort() // 阻止调用后续的处理函数
	cost := time.Since(start)
	fmt.Printf("cost:%v\n", cost)
	fmt.Println("m1 out ...")
}

func m2(c *gin.Context) {
	fmt.Println("m2 in ...")
	c.Set("name", "lucien") // 上下文中处理值
	// c.Abort() // 阻止调用后续的处理函数
	// return
	fmt.Println("m2 out ...")
}
```

#### gin默认中间件
1. gin.Default()默认使用Logger和Recovery中间件
- Logger中间件将日志写入gin.DefaultWritter，即使配置GIN_mode=release
- Recovery中间件会recover任何panic，有panic写入500响应码

如不使用上面默认的中间件，可使用gin.New()新建一个没有任何默认中间件的路由

#### gin中间件使用goroutine
当中间件或handler启动新的goroutine，不能使用原始的上下文(ctx *gin.Context)，而必须使用ctx的拷贝 ctx.Copy()