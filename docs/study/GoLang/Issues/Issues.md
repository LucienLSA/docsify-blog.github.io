# 相关问题及链接

[【已解决】--go_out: protoc-gen-go: Plugin failed with status code 1.-CSDN博客](https://blog.csdn.net/js010111/article/details/125392266)

[[系列] Gin 框架 - 使用 logrus 进行日志记录 - 程序员新亮 - 博客园](https://www.cnblogs.com/xinliangcoder/p/11212573.html)

[windows下使用endless报错：undefined: syscall.SIGUSR1_endless undefined: syscall.sigusr1-CSDN博客](https://blog.csdn.net/qq_28466271/article/details/116521955)

[windows下使用endless报错：undefined: syscall.SIGUSR1 | Go 技术论坛](https://learnku.com/articles/51696)

[Go语言之使用 swaggo 一键生成 API 文档 -](https://www.lixueduan.com/posts/go/swagger/)

[go clean -modcache命令清理缓存_gomodcache-CSDN博客](https://blog.csdn.net/a772304419/article/details/139283012)

[gin框架swagger的使用的坑_failed to load spec.-CSDN博客](https://blog.csdn.net/weixin_43249914/article/details/103035711)

[go mod tidy 报错：XXXX found，but does not contain package XXXX - 简书](https://www.jianshu.com/p/937eff8e48c7)

[go 结构体赋值 invalid memory address or nil pointer dereference-CSDN博客](https://blog.csdn.net/m0_37929803/article/details/107322805)

[Golang程序 获取当前工作目录|极客教程](https://geek-docs.com/go-tutorials/go-articles/t_golang-program-to-get-current-working-directory.html)

[Golang错误解析&#34;runtime error: invalid memory address or nil pointer dereference&#34;-CSDN博客](https://blog.csdn.net/m0_37422289/article/details/102677610)

[在 Go 中将时间转换为字符串 | D栈 - Delft Stack](https://www.delftstack.com/zh/howto/go/convert-time-to-string-in-go/)

[golang使用strconv包string/int/int64类型转换_strconv int64-CSDN博客](https://blog.csdn.net/cnwyt/article/details/95386285)

[Go语言实现将[]string转化为[]byte_go string to byte-CSDN博客](https://blog.csdn.net/john_f_lau/article/details/51475995)

[调用接口时不时出现 Error: socket hang up](https://blog.csdn.net/fang0604631023/article/details/136175538)

[go语言web开发系列之二十一:用go-qrcode库生成二维码](https://blog.csdn.net/weixin_43881017/article/details/112790066)

[手把手教你如何高效发布自己的Go语言库到GitHub](https://www.oryoy.com/news/golang-shi-zhan-shou-ba-shou-jiao-ni-ru-he-gao-xiao-fa-bu-zi-ji-de-go-yu-yan-ku-dao-github.html)

# 问题描述

1. 关于空指针
**panic: runtime error: invalid memory address or nil pointer dereference**

##### 示例
main.go
```go
package main

import (
	"context"
	"database/sql"
	"fmt"
	"log"
	// 数据库驱动， _ 自我注册init 会自动调用。把包变成_ , 不会直接使用。
	//没有变量，不影响代码里面的逻辑
	_ "github.com/denisenkom/go-mssqldb"
)

var db *sql.DB // 指向数据库
func main() {
	// 连接字符串
	connStr := fmt.Sprintf("server=%s;user id = %s; "+
		""+
		"password=%s;port=%d;database=%s;",
		server, user, password, port, database)
	fmt.Println(connStr)

	var err error
	// 这个db 需要是全局的，不能用:= 
	db, err = sql.Open("sqlserver", connStr)
	if err != nil {
		log.Fatalln(err.Error())
	}
	ctx := context.Background()

	err = db.PingContext(ctx)
	if err != nil {
		log.Fatalln(err.Error())
	}
	// 测试是否连接数据库
	fmt.Println("connected!")

	// 查询
	one, err := getOne(103)
	if err != nil {
		log.Fatalln(err.Error())
	}
	fmt.Println(one)
}

const (
	server   = "xxxx.xxx"
	port     = 1433
	user     = "xxx"
	password = "123"
	database = "go-db"
)
```
model.go 对应数据库的结构
```go
package main

// 对应数据库的结构
type app struct {
	ID     int
	name   string
	status int
	level  int
	order  int
}
```
service.go 执行sql语句
```go
package main
func getOne(id int) (a app, err error) {
    a = app{}
    // [order] 是sql中的关键字，需加上[]
    err = db.QueryRow("select id, name, status,"+
        "level, [order] from dbo.App").Scan(
        &a.ID, &a.name, &a.status, &a.level, &a.order)
	return
}
```
问题分析：db在赋值的时候:=，声明新的变量
```go
db, err := sql.Open("sqlserver", connStr)
```
改为
```go
var err error
db, err = sql.Open("sqlserver", connStr)
```