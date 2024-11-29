

# 加载前端静态文件

## 前端报错

### runtime error: invalid memory address or nil pointer dereference

读取路径不对，查找.htmpl文件
```go
// 告诉gin框架取哪里找模板文件
r.LoadHTMLGlob("templates/*")
```

### 
[GIN] 2024/08/06 - 08:57:26 | 404 |            0s |       127.0.0.1 | GET      "/static/css/app.8eeeaf31.css"
[GIN] 2024/08/06 - 08:57:26 | 404 |            0s |       127.0.0.1 | GET      "/static/css/chunk-vendors.57db8905.css"
[GIN] 2024/08/06 - 08:57:26 | 404 |            0s |       127.0.0.1 | GET      "/static/js/app.0007f9690.js"
[GIN] 2024/08/06 - 08:57:26 | 404 |            0s |       127.0.0.1 | GET      "/static/js/chunk-vendors.ddcb6f91.js"
[GIN] 2024/08/06 - 08:57:26 | 404 |            0s |       127.0.0.1 | GET      "/favicon.ico"   

没有加载引用的前端静态文件
```go
// 告诉gin框架模板文件引用的静态文件去哪里找
r.Static("/static", "static")
```

### 
127.0.0.1/9090/#/

其中#是因为由前端Vue框架，修改路由的配置


# 初步功能实现
## 构建模型

## 连接数据库
测试连通性

## 模型绑定数据库中表

[GIN] 2024/08/06 - 09:03:35 | 404 |            0s |       127.0.0.1 | POST     "/v1/todo"

处理请求，将待办事项作为事务

## gin框架
```go
r := gin.Default()
	// 告诉gin框架模板文件引用的静态文件去哪里找
	r.Static("/static", "static")
	// 告诉gin框架取哪里找模板文件
	r.LoadHTMLGlob("templates/*")
	r.GET("/", func(ctx *gin.Context) {
		ctx.HTML(http.StatusOK, "index.html", nil)
	})
	//v1 路由组
	v1Group := r.Group("v1")
	{
		// 待办事项
		// 添加
		v1Group.POST("/todo", func(ctx *gin.Context) {
			// 前端页面填写待办事项，点击提交，发送请求到这里
			// 1. 从请求中把数据拿出
			var todo Todo
			ctx.BindJSON(&todo)
			// 2. 存入数据库
			err = DB.Create(&todo).Error

			// 3. 返回响应
			if err != nil {
				ctx.JSON(http.StatusOK, gin.H{"error": err.Error()})
			} else {
				// ctx.JSON(http.StatusOK, todo)
				ctx.JSON(http.StatusOK, gin.H{
					"code": 2000,
					"msg":  "success",
					"data": todo,
				})
			}
		})
		// 查看
		// 查看所有的待办事项
		v1Group.GET("/todo", func(ctx *gin.Context) {
			var todoList []Todo
			err = DB.Find(&todoList).Error
			if err != nil {
				ctx.JSON(http.StatusOK, gin.H{"error": err.Error()})
			} else {
				ctx.JSON(http.StatusOK, todoList)
			}
		})
		// 查看某一个待办事项
		v1Group.GET("/todo/:id", func(ctx *gin.Context) {

		})
		// 修改状态
		v1Group.PUT("/todo/:id", func(ctx *gin.Context) {
			// 获取id
			id, ok := ctx.Params.Get("id")
			if !ok {
				ctx.JSON(http.StatusOK, gin.H{"error": "无效id"})
				return
			}
			var todo Todo
			err = DB.Where("id=?", id).First(&todo).Error
			if err != nil {
				ctx.JSON(http.StatusOK, gin.H{"error": err.Error()})
				return
			}
			ctx.BindJSON(&todo) // json字段绑定到to
			// Save将数据库的字段修改
			if err = DB.Save(&todo).Error; err != nil {
				ctx.JSON(http.StatusOK, gin.H{"error": err.Error()})
			} else {
				ctx.JSON(http.StatusOK, todo)
			}

		})
		// 删除
		v1Group.DELETE("/todo/:id", func(ctx *gin.Context) {
			id, ok := ctx.Params.Get("id")
			if !ok {
				ctx.JSON(http.StatusOK, gin.H{"error": "无效id"})
				return
			}
			if err = DB.Where("id=?", id).Delete(Todo{}).Error; err != nil {
				ctx.JSON(http.StatusOK, gin.H{"error": err.Error()})
			} else {
				ctx.JSON(http.StatusOK, gin.H{id: "deleted"})
			}
		})
	}
```

### 可使用postman或者apifox，模拟发送各种请求，修改html返回数据

## 项目结构划分
url   --> controller --> logic --> models
请求   --> 控制器     -->业务逻辑 --> 模型层的增删改查


### 控制器controllers
路由执行的函数，请求和响应控制，负责前后端交互，接收前端请求，调用service层，接收service层返回的数据，返回具体页面和数据到客户端

```go
package controller

import (
	"bubble/models"
	"net/http"

	"github.com/gin-gonic/gin"
)

func IndexHandler(c *gin.Context) {
	c.HTML(http.StatusOK, "index.html", nil)
}

func CreateTodo(ctx *gin.Context) {
	// 前端页面填写待办事项，点击提交，发送请求到这里
	// 1. 从请求中把数据拿出
	var todo models.Todo
	ctx.BindJSON(&todo)
	// 2. 存入数据库
	err := models.CreateATodo(&todo)
	// 3. 返回响应
	if err != nil {
		ctx.JSON(http.StatusOK, gin.H{"error": err.Error()})
	} else {
		// ctx.JSON(http.StatusOK, todo)
		ctx.JSON(http.StatusOK, gin.H{
			"code": 2000,
			"msg":  "success",
			"data": todo,
		})
	}
}
func GetTodoList(ctx *gin.Context) {
	todoList, err := models.GetAllTodo()
	if err != nil {
		ctx.JSON(http.StatusOK, gin.H{"error": err.Error()})
	} else {
		ctx.JSON(http.StatusOK, todoList)
	}
}

func UpdateATodo(ctx *gin.Context) {
	// 获取id
	id, ok := ctx.Params.Get("id")
	if !ok {
		ctx.JSON(http.StatusOK, gin.H{"error": "无效id"})
		return
	}
	todo, err := models.GetATodo(id)
	if err != nil {
		ctx.JSON(http.StatusOK, gin.H{"error": err.Error()})
	}
	ctx.BindJSON(&todo) // json字段绑定到to
	// Save将数据库的字段修改
	if err = models.UpdateATodo(todo); err != nil {
		ctx.JSON(http.StatusOK, gin.H{"error": err.Error()})
	} else {
		ctx.JSON(http.StatusOK, todo)
	}

}

func DeleteATodo(ctx *gin.Context) {
	id, ok := ctx.Params.Get("id")
	if !ok {
		ctx.JSON(http.StatusOK, gin.H{"error": "无效id"})
		return
	}
	if err := models.DeleteATodo(id); err != nil {
		ctx.JSON(http.StatusOK, gin.H{"error": err.Error()})
	} else {
		ctx.JSON(http.StatusOK, gin.H{id: "deleted"})
	}
}
```

### 数据访问层DAO (mapper层)
Dao的作用是把访问数据库的代码封装起来，封装对数据库的访问：增删改查，不涉及业务逻辑，只是达到按某个条件获得指定数据的要求。

```go
package dao

import (
	"github.com/jinzhu/gorm"
	_ "github.com/jinzhu/gorm/dialects/mysql"
)

var (
	DB *gorm.DB
)

// 连接数据库
func InitMySQL() (err error) {
	dsn := "root:123456@tcp(127.0.0.1:3306)/bubble?charset=utf8&parseTime=True&loc=Local"
	DB, err = gorm.Open("mysql", dsn)
	if err != nil {
		return
	}
	//测试连通性
	return DB.DB().Ping()
}

func Close() {
	DB.Close()
}
```


### 数据库实体层(model层)
数据库一张表对应一个实体类，类属性同表字段一一对应
```go
package models

import (
	"bubble/dao"
)

// To Model
type Todo struct {
	ID     int    `json:"id"`
	Title  string `json:"title"`
	Status bool   `json:"status"`
}

/*
Todo这个Model的增删改查操作都放在这里
*/

// Create 创建todo
func CreateATodo(todo *Todo) (err error) {
	err = dao.DB.Create(&todo).Error
	return
}

func GetAllTodo() (todoList []*Todo, err error) {
	err = dao.DB.Find(&todoList).Error
	if err != nil {
		return nil, err
	}
	return
}

func GetATodo(id string) (todo *Todo, err error) {
	todo = new(Todo)
	err = dao.DB.Where("id=?", id).First(todo).Error
	if err != nil {

		return nil, err
	}
	return
}

func UpdateATodo(todo *Todo) (err error) {
	err = dao.DB.Save(todo).Error
	return
}

func DeleteATodo(id string) (err error) {
	err = dao.DB.Where("id=?", id).Delete(&Todo{}).Error
	return
}
```



<!-- ### service层
业务逻辑层，调用dao层接口，接收dao层返回数据 -->


### 路由层
配置路由，gin框架相关前端部分
```go
package routers

import (
	"bubble/controller"

	"github.com/gin-gonic/gin"
)

func SetupRouter() *gin.Engine {
	r := gin.Default()
	// 告诉gin框架模板文件引用的静态文件去哪里找
	r.Static("/static", "static")
	// 告诉gin框架取哪里找模板文件
	r.LoadHTMLGlob("templates/*")
	r.GET("/", controller.IndexHandler)
	//v1 路由组
	v1Group := r.Group("v1")
	{
		// 待办事项
		// 添加
		v1Group.POST("/todo", controller.CreateTodo)
		// 查看
		// 查看所有的待办事项
		v1Group.GET("/todo", controller.GetTodoList)
		// 查看某一个待办事项
		v1Group.GET("/todo/:id", func(ctx *gin.Context) {

		})
		// 修改状态
		v1Group.PUT("/todo/:id", controller.UpdateATodo)
		// 删除
		v1Group.DELETE("/todo/:id", controller.DeleteATodo)
	}
	return r
}
```


### static
css fonts js前端文件

### templats
html模板文件