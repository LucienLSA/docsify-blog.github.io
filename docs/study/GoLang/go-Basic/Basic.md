
# golang介绍

## 优势

### 部署快

1. 直接编译成机器码
2. 不依赖其他库
3. 直接运行可部署

### 静态类型

编译时检查出隐藏的大多数问题

### 语言层面并发

1. 并发
2. 多核

### 标准库

1. runtime系统调度机制
2. 高效的回收
3. 丰富标准库

## 强项

云计算基础设施(docker/kubernetes/CDN)，后端软件，微服务(micro/bilibili)，互联网基础设施

## 缺点

1. 包管理大部分在github
2. exception都用error来处理

# go配置与基础使用

## 不需要配置GOPATH和GOROOT

### 有必要时可修改

参考博客
[https://blog.csdn.net/weixin_63860405/article/details/139732041?spm=1001.2101.3001.6650.4&amp;utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7EYuanLiJiHua%7EPosition-4-139732041-blog-78759542.235%5Ev43%5Epc_blog_bottom_relevance_base6&amp;depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7EYuanLiJiHua%7EPosition-4-139732041-blog-78759542.235%5Ev43%5Epc_blog_bottom_relevance_base6&amp;utm_relevant_index=9](2024年go安装教程（Windows）包括配置GOPATH)

[https://blog.csdn.net/m0_73537205/article/details/140078383](Go语言--工程管理、临时/永久设置GOPATH、main函数以及init函数)

[https://blog.csdn.net/qq_46027425/article/details/139924867?utm_medium=distribute.pc_relevant.none-task-blog-2~default~baidujs_baidulandingword~default-9-139924867-blog-78759542.235^v43^pc_blog_bottom_relevance_base6&spm=1001.2101.3001.4242.6&utm_relevant_index=12](Windows安装 Go 环境并配置环境变量)

修改后需要重启电脑

## 配置GOPROXY

修改GOPROXY

```sh
go env -w GOPROXY=https://goproxy.cn,direct
```

## 安装Go语言开发工具包

vscode输入框>go:install，全选

## 配置代码片段快捷键

vscode输入框>snippets，首选项：配置用户代码片段，go

```json
{
	"println":{
		"prefix": "pln",
		"body":"fmt.Println($0)",
		"description": "println"
	},
	"printf":{
		"prefix": "plf",
		"body": "fmt.Printf(\"$0\")",
		"description": "printf"
	}
}
```

## 初始化golang项目

在新建文件夹hello的终端"ctrl `"，打开，路径为新建文件夹hello

### 通过go mod init初始化项目

```sh
go mod init github.com/项目名称
go mod init hello
```

生成.mod文件
可自己编写go文件

## golang 使用包 package 来管理定义模块，可以使用 import 关键字来导入使用

1. 如果导入的是 go 自带的包，则会去安装目录 $GOROOT/src 按包路径加载，如 fmt、encoding/json包
2. 如果是我们 go get 安装或自定义的包，则会去 $GOPATH/src 下加载

### 跨平台编译

在windows下编译一个linux下可执行文件，指定目标操作系统平台和处理器，如windows平台cmd指定环境变量

```sh
SET CGO_ENABLED=0 // 禁用CGO
SET GOOS=linux // 目标平台为linux
SET GOARCH=amd64 // 目标处理器框架为amd64
```

## 引入github上第三方包运行

```sh
go mod init demo1
go get -u github.com/gin-gonic/gin
```

编写demo1文件，一个小的web服务

```go
package main // 声明 main 包，表明当前是一个可执行程序
import "github.com/gin-gonic/gin"

func main() { // main函数，是程序执行的入口 函数的花括号和函数名是在同一行，否则编译错误，分号;可以加或不加
	r := gin.Default()
	r.GET("/", func(c *gin.Context) {
		c.String(200, "liwenzhou.com")
	})
	r.Run()
}
```

```sh
go build 
demo1.exe
```

### go mod 操作

1. Go mod 删除错误或者不使用的modules
   go mod tidy
2. Go mod 初始化
   go mod init 模块名
3. Go mod 清理本地Cache
   go clean -modcache

## 变量声明

数据类型查看

打印输出

```go
fmt.Println("a = ", a)
fmt.Printf("cc = %s, type of cc = %T\n", cc, cc)
```

### 默认为0

```go
var a int
```

### 初始化值

```go
var b int = 100
```

### 初始化省去数据类型，值自动匹配当前的变量数据类型

```go
var c = 122
```

### 省去var关键字，直接自动匹配(最常用)

:= 但是只能在函数体内声明，不能声明全局变量

```go
e := 100
```

有符号和无符号的区别，int8 范围-128-127，uint8范围0-255

当一个变量被声明后，系统自动赋予它类型的零值

int 0, float 0.0, bool false, string 空字符串, 指针 nil

### 多变量声明

```go
	var xx, yy int = 122, 344
	var ii, te = 123, "asd"
	var (
		vv int = 222
		jj bool = true
	)
```

## 常量

const 定义枚举类型，常量不允许修改
const() 添加一个关键字iota，每行的iota都累加1，第一行iota默认值为0

**iota 只能配合const的枚举实现**

但可读性不高

```go
const (
	// BEIJING=0
	// SHANGHAI = 1
	// SHENZHEN = 2
	BEIJING = iota // iota = 0
	SHANGHAI // iota = 1
	SHENZHEN
)
	const length int = 10
const (
	a, b = iota + 1, iota + 2
	c, d
	e, f

	g, h = iota * 2, iota * 3
	i, k
)
```

## 函数

### 返回一个匿名值

```go
func foo1(a string, b int) int {
	fmt.Println("a = ", a)
	fmt.Println("b = ", b)
	c := 100
	return c
}
	c := foo1("abd", 555)
	fmt.Println("c = ", c)
	fmt.Print("\n")
```

### 返回多个匿名值

```go
func foo2(a string, b int) (int, int) {
	fmt.Println("a = ", a)
	fmt.Println("b = ", b)

	return 666, 777
}
	ret1, ret2 := foo2("haha", 999)
	fmt.Println("ret1= ", ret1, "ret2= ", ret2)
```

### 返回多个值，有形参名称

```go
func foo3(a string, b int) (r1 int, r2 int) {
	fmt.Println("---foo3---")
	fmt.Println("a= ", a)
	fmt.Println("b= ", b)
	// 给有名称的返回值变量赋值
	r1 = 1000
	r2 = 2000
	return
}
	ret1, ret2 = foo3("foo3", 333)
	fmt.Println("ret1= ", ret1, "ret2= ", ret2)
```

### 当输出变量未进行赋值时，默认初始值0（因为已经定义了）

r1 r2为函数{}内的所有作用域空间

```go
func foo3(a string, b int) (r1 int, r2 int) {
	fmt.Println("---foo3---")
	fmt.Println("a= ", a)
	fmt.Println("b= ", b)
	// r1 r2属于foo3的形参
	fmt.Println("r1= ", r1)
	fmt.Println("r1= ", r1)
	// 给有名称的返回值变量赋值
	r1 = 1000
	r2 = 2000
	return
}
```

## 报错main redeclared in this block，other declaration of main

同一个目录下面不能有个多 package main
*但还是能够执行相应的go文件*

### 解决方案

多套一个文件夹

## init函数与import调用包（未在[go/src/项目]的路径下

在init项目文件夹下创建main.go和两个lib文件夹，其中各有lib.go

### 调用包的函数

函数名必须以大写开头，才能被外部调用

### import 本地包出现的问题

#### 参考教程

[https://www.liwenzhou.com/posts/Go/import-local-package/](go module导入本地包)

#### 同一个项目下

只需在项目文件下执行 `go mod init 项目名称`

#### 不同项目下

第一个主项目文件夹moduledemo，有main.go，执行调用第二个项目文件夹study，有4init/lib1下的函数Lib1Test()，两个项目都进行go mod init

main.go

```go
package main

import (
	"study/4init/lib1"
)

func main() {
	lib1.Lib1Test()
}
```

在moduledemo主项目的go.mod中添加依赖

```mod
module moduledemo

go 1.22.5

require "study" v0.0.0
replace "study" => "../study"
```

最后在路径moduledemo项目下执行go run main.go

**如果在$GOPATH根目录下的项目，不用进行go module,，即go mod init**

### 导入包，但不使用，进行匿名

起别名

```go
import (
	_ "study/4init/lib1"
	mylib2 "study/4init/lib2"
)

func main() {
	// lib1.Lib1Test()
	mylib2.Lib2Test()
}
```

直接使用包的函数，但该方法容易引起歧义，不同包有相同的函数

```go
import (
	. "study/4init/lib2"
)

func main() {
	Lib2Test()
}
```

## 指针

### 值传递

```go
func swap1(a int, b int) {
	var temp int
	temp = a
	a = b
	b = temp
}
```

### 地址传递

```go
func swap2(pa *int, pb *int) {
	var temp int
	temp = *pa
	*pa = *pb
	*pb = temp
}
```

### 二级指针

## defer关键字

类似c++中的析构函数

### defer执行顺序

栈

```go
func func1() {
	fmt.Println("A")
}
func func2() {
	fmt.Println("B")
}
func func3() {
	fmt.Println("C")
}

func main() {
	defer func1()
	defer func2()
	defer func3()
}
```

### defer和return，先执行return

defer皆在当前的函数周期结束之后，才会出栈

```go
func returnAndDefer() int {
	defer deferFunc()
	return returnFunc()
}
```

## 切片slice

### 数组

```go
// 定义打印数组函数
func printArray(myArray [10]int) {
	// 值拷贝(固定数组长度为10)，严格匹配数组类型
	for index, value := range myArray {
		fmt.Println("index = ", index, "value = ", value)
	}
}

func main() {
	// 固定长度的数组，默认初始值为0
	var myArray1 [10]int
	// 数组赋值，其它默认为0
	myArray2 := [10]int{1, 2, 3, 4}

	for i := 0; i < len(myArray1); i++ {
		fmt.Println(myArray1[i])
	}

	for index, value := range myArray2 {
		fmt.Println("index = ", index, "value = ", value)
	}
	// 查看数组的数据类型
	fmt.Printf("myArray1 type = %T\n", myArray1)
	fmt.Printf("myArray2 type = %T\n", myArray2)

	printArray(myArray1)
}
```

### slice(动态数组)

可变长度的同类型元素序列

```go
func printArray1(myArray []int) {
	// 引用传递，传的是指针，数组长度任意且形参一致
	// _ 表示匿名的变量
	for _, value := range myArray {
		fmt.Println("value = ", value)
	}
	myArray[0] = 100
}

func main() {
	myArray := []int{1, 2, 3, 4}
	fmt.Println("myArray type is %T\n", myArray)
	printArray1(myArray)

	fmt.Println(" === ")

	for _, value := range myArray {
		fmt.Println("value = ", value)
	}
}
```

### slice声明方式

```go
func main() {
	// 声明slice1是一个切片，初始化，默认值为1，2，3
	slice1 := []int{1, 2, 3}

	// 声明slice2是一个切片，但没有给slice分配空间
	var slice2 []int
	slice2 = make([]int, 3) // 开辟3个空间，默认值为0
	slice2[0] = 100

	// 声明slice3是一个切片，同时给slice分配空间，3个空间，初始值为0
	var slice3 []int = make([]int, 3)
	// 声明slice4是一个切片，同时给slice分配空间，3个空间，初始值为0，通过:=推导slice是切片
	slice4 := make([]int, 3)

	fmt.Printf("len = %d, slice1 = %v\n", len(slice1), slice1)
	fmt.Printf("len = %d, slice2 = %v\n", len(slice2), slice2)
	fmt.Printf("len = %d, slice3 = %v\n", len(slice3), slice3)
	fmt.Printf("len = %d, slice4 = %v\n", len(slice4), slice4)

	// 判断slice是否为0
	if slice1 == nil {
		fmt.Println("slice1是一个空切片")
	} else {
		fmt.Println("slice1是有空间的")
	}
}
```

### slice切片追加

长度是实际容纳的数量(左指针至右指针之间的距离)，容量是可以容纳的元素数量（左指针至底层数组末尾的距离）

```go
func main() {
	var numbers = make([]int, 3, 5)
	fmt.Println("len = %d, cap = %d, slice = %v\n", len(numbers), cap(numbers), numbers)

	// 向numbers切片追加一个元素1，numbers len =4, [0,0,0,1], cap=5
	numbers = append(numbers, 1)
	fmt.Println("len = %d, cap = %d, slice = %v\n", len(numbers), cap(numbers), numbers)
	numbers = append(numbers, 3)
	fmt.Println("len = %d, cap = %d, slice = %v\n", len(numbers), cap(numbers), numbers)

	// 向容量cap已经满的slice追加元素，扩增当前slice容量的一倍
	numbers = append(numbers, 5)
	fmt.Println("len = %d, cap = %d, slice = %v\n", len(numbers), cap(numbers), numbers)
}
```

### slcie截取

类似python的数组切片
**注意理解切片和拷贝，对于底层数组影响**

```go
func main() {
	s := []int{1, 2, 3}

	s1 := s[0:2]

	fmt.Println(s1)

	s1[0] = 100 //s1和s指向底层的地址是一致的

	fmt.Println(s)
	fmt.Println(s1) // 都会被修改

	// copy将底层数组的slice一起拷贝
	s2 := make([]int, 3)

	// 将s中的值依次拷贝到s2中
	copy(s2, s)
	fmt.Println(s2) // s2指向新的地址空间
}
```

## map

key是string类型，value是string类型

```go
func main() {
	// 声明myMap1是一种map类型，key是string类型，value是string类型
	var myMap1 map[string]string
	if myMap1 == nil {
		fmt.Println("myMap1 是一个空的map")
	}

	// 使用map前，需要先用make给map分配数据空间
	myMap1 = make(map[string]string, 10)

	myMap1["one"] = "java"
	myMap1["two"] = "c++"
	myMap1["three"] = "python"

	fmt.Println(myMap1)

	// 声明:=
	myMap2 := make(map[int]string)
	myMap2[1] = "java"
	myMap2[2] = "c++"
	myMap2[3] = "python"

	fmt.Println(myMap2)

	// 声明:=直接map
	myMap3 := map[string]string{
		"one":   "php",
		"two":   "c++",
		"three": "python",
	}
	fmt.Println(myMap3)
}
```

### map使用方式

```go
func printMap(cityMap map[string]string) {
	// cityMap是一个引用传递
	for key, value := range cityMap {
		fmt.Println("key = ", key)
		fmt.Println("value = ", value)
	}
}

func ChangeValue(cityMap map[string]string) {
	cityMap["England"] = "London"
}

func main() {
	cityMap := make(map[string]string)

	// 添加
	cityMap["China"] = "Beijing"
	cityMap["Japan"] = "Tokyo"
	cityMap["USA"] = "NewYork"

	// 遍历
	// for key, value := range cityMap {
	// 	fmt.Println("key = ", key)
	// 	fmt.Println("value = ", value)
	// }
	printMap(cityMap)

	// 删除
	delete(cityMap, "China")

	// 修改
	cityMap["USA"] = "DC"
	ChangeValue(cityMap)
	fmt.Println("-----")

	// 遍历
	printMap(cityMap)
}
```

## 面向对象编程（类/继承/多态）

### struct结构体

```go
// 声明一种行的数据类型 myint，对int的一个别名
type myint int

type Book struct {
	title string
	auth  string
}

func changeBook(book Book) {
	// 传递一个book的副本，值传递
	book.auth = "qwe"
}

func changeBook2(book *Book) {
	// 指针传递
	book.auth = "sdf"
}

func main() {
	var a myint = 10
	fmt.Println("a = ", a)
	fmt.Printf("type of a = %T\n", a)

	var book1 Book
	book1.title = "Golang"
	book1.auth = "lisi"

	fmt.Printf("%v\n", book1)

	changeBook(book1)

	fmt.Printf("%v\n", book1)

	changeBook2(&book1)

	fmt.Printf("%v\n", book1)
}
```

### class类

类名大写，其他包能够访问；类的方法名、属性名大写，该属性是外部可访问的（但区分c++中的公有与私有）

```go
// 如果类名首字母大写，其他包也能够访问
type Hero struct {
	// 如果类的属性首字母大写，表示该属性是对外能够访问的，否则只能够类的内部访问
	Name  string
	Ad    int
	Level int
}

/*
func (this Hero) ShowName() {
	fmt.Println("Name = ", this.Name)
	fmt.Println("Ad = ", this.Ad)
	fmt.Println("Level = ", this.Level)
}

func (this Hero) GetName() string {
	return this.Name
}

func (this Hero) SetName(newName string) {
	// this 是调用该方法的对象的一个副本（拷贝），即值传递
	this.Name = newName
}
*/
// 取地址，引用传递
func (this *Hero) ShowName() {
	fmt.Println("Name = ", this.Name)
	fmt.Println("Ad = ", this.Ad)
	fmt.Println("Level = ", this.Level)
}

func (this *Hero) GetName() string {
	return this.Name
}

func (this *Hero) SetName(newName string) {
	this.Name = newName
}

func main() {
	// 创建一个对象
	hero := Hero{Name: "zhang3", Ad: 100, Level: 1}

	hero.ShowName()

	hero.SetName("li4")

	hero.ShowName()
}
```

### 继承

#### go本身没有继承

组合与继承本身区别就不大，区别有两个：继承有直接访问子类成员的语法糖，继承可以实现多态，而前者是后者的基础

```go
type Human struct {
	name string
	sex  string
}

func (this *Human) Eat() {
	fmt.Println("Human.Eat()...")
}
func (this *Human) Walk() {
	fmt.Println("Human.Walk()...")
}

//============

type SuperMan struct {
	Human // SuperMan类继承Human类的方法
	level int
}

// 重定义父类的方法Eat()
func (this *SuperMan) Eat() {
	fmt.Println("SuperMan.Eat()...")
}

// 定义子类的新方法
func (this *SuperMan) Fly() {
	fmt.Println("SuperMan.Fly()...")
}
func (this *SuperMan) Print() {
	fmt.Println("name = ", this.name)
	fmt.Println("sex = ", this.sex)
	fmt.Println("level = ", this.level)
}

func main() {
	h := Human{"zhang3", "female"}
	h.Eat()
	h.Walk()
	// 定义子类对象
	// s := SuperMan{Human{"li4", "male"}, 88}
	var s SuperMan
	s.name = "li4"
	s.sex = "male"
	s.level = 99
	s.Eat()
	s.Fly()
	s.Walk()
	s.Print()
}
```

### 多态

1. 有一个父类（有接口）
2. 有子类（实现了父类的全部接口方法）
3. 父类类型的变量（指针） 指向（引用） 子类的具体数据变量

```go
// 本质是一个指针
type AnimalIF interface { // 接口，以下三个方法
	Sleep()
	GetColor() string // 获得动物的颜色
	GetType() string  // 获取动物的种类
}

// 具体的类
type Cat struct {
	color string
}

func (this *Cat) Sleep() {
	fmt.Println("Cat is Sleep")
}

func (this *Cat) GetColor() string {
	return this.color
}

func (this *Cat) GetType() string {
	return "Cat"
}

// 接口中的方法必须完全重写，才可实现接口，接口的指针能指向具体类
//  类似c++中的抽象类和虚函数

type Dog struct {
	color string
}

func (this *Dog) Sleep() {
	fmt.Println("Dog is Sleep")
}

func (this *Dog) GetColor() string {
	return this.color
}

func (this *Dog) GetType() string {
	return "Dog"
}

func showAnimal(animal AnimalIF) {
	animal.Sleep() // 多态，传入对象
	fmt.Println("color = ", animal.GetColor())
	fmt.Println("kind = ", animal.GetType())
}

func main() {
	// 多态

	var animal AnimalIF // 接口的数据类型，父类指针
	animal = &Cat{"Green"}
	animal.Sleep() // 调用的Cat的Sleep()方法
	animal = &Dog{"Yellow"}
	animal.Sleep() // 调用的Dog的Sleep()方法

	fmt.Println("----------")

	cat := Cat{"Green"}
	dog := Dog{"Yellow"}

	showAnimal(&cat)
	showAnimal(&dog)
}
```

### 通用万能类型

interface{}

1. 空接口
2. 通用的万能数据类型，int/float64/string/struct，引用任意的数据类型
3. interface{}该如何区分，引用的底层数据类型是什么？给interface{}提供“类型断言”的机制

```go
func myfunc(arg interface{}) {
	fmt.Println("myfunc is called:")
	fmt.Println(arg)
	// 增加断言机制，判断底层的数据类型
	value, ok := arg.(string)
	if !ok {
		fmt.Println("myfunc is not a string type")
	} else {
		fmt.Println("string type, value = ", value)
		fmt.Printf("value type is %T\n", value)
	}
}

type Book1 struct {
	auth string
}

func main() {
	book := Book1{"Golang"}
	myfunc(book)
	myfunc(100)
	myfunc("abc")
	myfunc(3.14)
}
```

接口类型的嵌套是将一个接口类型作为另一个接口类型的一部分，嵌套的接口类型可以继承被嵌套接口类型的所有方法，并可以添加新的方法。

接口类型的扩展是在一个接口类型的基础上新增方法，被扩展的接口类型的实现类也必须实现新添加的方法。

嵌套和扩展的区别在于，嵌套是将接口类型作为另一个接口类型的一部分，而扩展是在接口类型的基础上新增方法。嵌套是一种组合关系，扩展是一种继承关系。

## 反射

### 变量的pair结构

1. 变量由type和value。type分为static type(int/string...) 和 concrete type(interface指向的具体数据类型)

```go
func main() {
	// pair<statictype:string, value:"aeee"
	var a string
	a = "aeee"
	fmt.Println(a)

	// pair<type:string, value:"aeee"
	var allType interface{}
	allType = a

	str, _ := allType.(string)
	fmt.Println("allType is ", str)
}
```

2. 保持pair是连续不变的

```go
import (
	"fmt"
	"io"
	"os"
)

func main() {
	// tty: pair<type:*os.File, value:" "文件描述符>
	tty, err := os.OpenFile("23.txt", os.O_RDWR, 0)

	if err != nil {
		fmt.Println("Open file error!", err)
		return
	}

	var r io.Reader // io.Reader为interface{}
	// r: pair<type:*os.File, value:""文件描述符>
	r = tty

	var w io.Writer // io.Writer为interface{}
	// w: pair<type:*os.File, value:" "文件描述符>
	w = r.(io.Writer) // r强制转换，赋值给w，pair不变
	w.Write([]byte("Hello lucien!\n"))
}
```

断言有两步：得到动态类型 type，判断 type 是否实现了目标接口。这里断言成功是因为 type 是 Book，而 Book 实现了 Writer 接口，pair是不变的

断言成功有两种：1、两种类型是相同的 2、断言接口值(也就是r)实现了目标接口的所有方法所以才能断言成功，像这里的b、r应该都是Book*的数据类型

```go
type Reader interface {
	ReadBook()
}

type Writer interface {
	WriteBook()
}

// 具体类型
type Book struct {
}

func (this *Book) ReadBook() {
	fmt.Println("Read a Book")
}

func (this *Book) WriteBook() {
	fmt.Println("Write a Book")
}
func main() {
	// b: pair<type:Book, value:book{}地址>
	b := &Book{}

	// r: pair<type:Book, value:Book{}地址>
	var r Reader
	r = b
	r.ReadBook()

	var w Writer
	// w: pair<type:Book, value:Book{}地址>
	w = r.(Writer) // 断言成功，因为w和r具体的type是一致的
	w.WriteBook()
}
```

### reflect包的使用

反射机制的使用可能会带来一定的性能损失，因此应该尽量避免过度使用反射。在编写代码时，应该尽可能地使用静态类型检查，避免在运行时进行类型检查和转换

```go
reflect.TypeOf(arg) // 获取输入参数接口中值的类型，若接口为空则返回nil
reflect.ValueOf(arg) // 获取输入参数接口中的数据的值，若接口为空则返回0
```

```go
type User struct {
	Id   int
	Name string
	Age  int
}

func (this User) Call() {
	fmt.Println("user is called ...")
	fmt.Printf("%v\n", this)
}

func main() {
	user := User{1, "Aee", 18}
	DoFiledAndMethod(user)
}

func DoFiledAndMethod(input interface{}) {
	// 获取input的type
	inputType := reflect.TypeOf(input)
	fmt.Println("inputType is :", inputType.Name())
	// 获取input的value
	inputValue := reflect.ValueOf(input)
	fmt.Println("inputValue is :", inputValue)
	// 通过type 获取里面的字段
	// 1. 获取interface的reflect.Type，通过Type得到NumField，进行遍历
	// 2. 得到的每个field，数据类型
	// 3. 通过field有一个Interface()方法得到对应的value
	for i := 0; i < inputType.NumField(); i++ {
		field := inputType.Field(i)
		value := inputValue.Field(i).Interface()
		fmt.Printf("%s: %v = %v\n", field.Name, field.Type, value)
	}
	// 通过type 获取里面的方法、调用
	for i := 0; i < inputType.NumMethod(); i++ {
		m := inputType.Method(i)
		fmt.Printf("%s: %v\n", m.Name, m.Type)
	}
}
```

### 结构体标签

```go
type resume struct {
	Name string `info:"name" doc:"我的名字"` // 结构体标签说明
	Sex  string `info:"sex"`
}

func findTag(str interface{}) {
	t := reflect.TypeOf(str).Elem()

	for i := 0; i < t.NumField(); i++ {
		// tagstring := t.Field(i).Tag.Get("info")
		// fmt.Println("info: ", tagstring)

		taginfo := t.Field(i).Tag.Get("info")
		tagdoc := t.Field(i).Tag.Get("doc")
		fmt.Println("info: ", taginfo, " doc: ", tagdoc)
	}
}

func main() {
	var re resume
	findTag(&re)
}
```

### 结构体在标签的应用

```go
package main

import (
	"encoding/json"
	"fmt"
)

type Movie struct {
	// Title  string   `json:"title"`
	// Year   int      `json:"year"`
	// Price  int      `json:"rmb"`
	// Actors []string `json:"actors"`
	Title  string // 也可自动获取标签
	Year   int
	Price  int
	Actors []string
}

func main() {
	movie := Movie{"喜剧之王", 2000, 10, []string{"周星驰", "张柏芝"}}

	// 编码 结构体转化为json
	jsonStr, err := json.Marshal(movie)
	if err != nil {
		fmt.Println("json marshal error", err)
		return
	}
	fmt.Printf("jsonStr = %s\n", jsonStr)

	// 解码的过程 jsonstr转化为结构体
	// jsonStr = {"title":"喜剧之王","year":2000,"rmb":10,"actors":["周星驰","张柏芝"]}
	myMovie := Movie{}
	err = json.Unmarshal(jsonStr, &myMovie)
	if err != nil {
		fmt.Println("json unmarshal error", err)
		return
	}
	fmt.Printf("%v\n", myMovie)
}
```

## new和make

### new

返回一个指向新变量的指针，其变量的值未其类型的零值，new用于创建变量的指针，在堆上分配内存空间，返回的指针指向在堆上分配的变量，即在函数调用结束后仍然存在。

```go
func new(Type) *Type
```

new只能创建变量的指针，而不能创建变量本身。new只分配了变量所需的内存空间

### make

make用于创建特定类型的数据结构，如slice、map、channel

```go
func make(t Type, size ...IntegerType) Type
```

make函数创建的数据结构分配在堆上，返回一个引用，即指向数据结构的指针。make分配了变量所需的内存空间，并初始化变量的其他属性

#### 创建slice

make([]T, length, capacity)

#### 创建map

make(map[T]U, capacity)

#### 创建channel

make(chan T, capacity)

var关键字用于声明变量，而new和make内置函数用于分配内存。var声明值类型的变量时，默认分配内存空间，并赋该类型的零值。

指针类型或引用类型的变量，不会被分配内存，默认为nil，若直接使用会异常

## 值传递和引用传递

### 值传递

将参数的值复制一份，然后将复制的值传递给函数，函数对参数的修改不会影响到原始的值。常见的值类型如 int、float、bool 等都是值类型

### 引用传递

将参数的地址复制一份，然后将复制的地址传递给函数，函数对参数的修改会影响到原始的值。常见的引用类型如 Slice、Map、Channel、指针等都是引用类型

在 Golang 中数组虽然是引用类型，但是它的传递却是值传递。这是因为 Golang 的数组长度是固定的，数组的值复制时会将整个数组的元素都复制一遍，因此传递数组时的开销较大，而且数组的长度也不可变，因此将数组的地址复制一份也无法修改原数组的长度

**Go语言特点**

引用类型和引用传递是 2 个概念，切记！！

Go语言中所有的传参都是值传递（传值），都是一个副本，一个拷贝。

参数如果是非引用类型（int、string、struct 等这些），这样就在函数中就无法修改原内容数据；如果是引用类型（指针、map、slice、chan等这些），这样就可以修改原内容数据。

是否可以修改原内容数据，和传值、传引用没有必然的关系。在 C++ 中，传引用肯定是可以修改原内容数据的，在 Go 语言里，虽然只有传值，但是我们也可以修改原内容数据，因为参数是引用类型。

Go 语言是没有引用传递的，在 C++ 中，函数参数的传递方式有引用传递。

## goroutine基本模型和调度设计策略

### 单进程时代的两个问题

1. 单一执行流程、计算机只能一个任务处理
2. 进程阻塞所带来的cpu浪费时间

### 宏观执行多个任务（并发执行）

多进程/多线程解决阻塞问题
![image.png](https://s2.loli.net/2024/07/10/IyrY2Z18sXeaPb9.png)

进程/线程的数量越多，切换成本就越大，越是浪费
![image.png](https://s2.loli.net/2024/07/10/iFGhclVsXSw96pm.png)

多线程有同步竞争（锁、竞争资源冲突等），开发设计越来越复杂，高消耗调度cpu，进程和线程占用内存导致高内存的占用

将一个线程切分成用户空间和内核空间（协程[co-route]和线程[thread]，互相绑定）

![image.png](https://s2.loli.net/2024/07/10/iUOkzI2wqme5MgD.png)

#### golang对协程处理

协程(co-routine)改为Goroutine，内存几KB和灵活调度（常切换）

GMP（goroutine协程/processor处理器/thread线程）

![image.png](https://s2.loli.net/2024/07/10/fqz2yjaDbEBQUFW.png)

Goroutine 可以通过 go 关键字启动，它会在一个独立的栈空间中执行相应的函数，可以在函数中执行阻塞和非阻塞操作。

要停止 Goroutine，需要使用 Go 语言提供的通道（channel）机制。可以在 Goroutine 中使用一个通道来接收停止信号，当主线程需要停止 Goroutine 时，向该通道发送一个信号即可。Goroutine 在执行任务的同时需要不断检测该通道是否有信号，如果有，则立即退出任务

#### 调度器的设计策略

1. 复用线程
   work stealing机制   hand off机制
2. 利用并行
   GOMAXPROCS 限定P的个数 = CPU核数/2
3. 抢占
4. 全局G队列

### 创建goroutine

```go
// 副goroutine
func newTask() {
	i := 0
	for {
		i++
		fmt.Printf("new Goroutine : i = %d\n", i)
		time.Sleep(1 * time.Second)
	}
}

// 主goroutine
func main() {
	// 创建一个go进程，执行newTask()流程
	go newTask()

	fmt.Println("main goroutine exit")
	i := 0
	for {
		i++
		fmt.Printf("main goroutine: i = %d\n", i)
		time.Sleep(1 * time.Second)
	}
}
```

### goexit

1. 外函数退出

```go
package main

import (
	"fmt"
	"runtime"
	"time"
)

func main() {
	// go创建承载一个形参为空，返回值为空的一个函数
	go func() {
		defer fmt.Println("A.defer")
		return //退出外函数

		// 子函数
		func() {
			defer fmt.Println("B.defer")
			fmt.Println("B")
		}()

		fmt.Println("A")
	}()

	// 死循环
	for {
		time.Sleep(1 * time.Second)
	}
}
```

2. 子函数先退出，再外函数退出

```go
package main

import (
	"fmt"
	"runtime"
	"time"
)

func main() {
	// go创建承载一个形参为空，返回值为空的一个函数
	go func() {
		defer fmt.Println("A.defer")

		// 子函数
		func() {
			defer fmt.Println("B.defer")
			// 退出当前的goroutine
			runtime.Goexit()
			fmt.Println("B")
		}()

		fmt.Println("A")
	}()

	// 死循环
	for {
		time.Sleep(1 * time.Second)
	}
}
```

3. 有参

```go
package main

import (
	"fmt"
	"time"
)

func main() {
	// 主go程得到当前go程的值，两个go程之间进行通信，则需要引入channl管道，不支持返回值
	go func(a int, b int) bool {
		fmt.Println("a = ", a, ", b = ", b)
		return true
	}(10, 20)

	// 死循环
	for {
		time.Sleep(1 * time.Second)
	}
}
```

## cap函数

1. 数组：cap() 函数返回数组的长度，因为数组的长度在创建时就已经确定了。
2. 切片：cap() 函数返回切片的容量，即切片底层数组的长度，它可能大于或等于切片的长度。切片容量的大小是在切片创建时自动分配的，也可以通过对切片使用 append() 函数来改变容量大小。
3. Channel：cap() 函数返回 Channel 的容量，即 Channel 缓冲区的大小，只有在创建带缓冲的 Channel 时，才有意义。

注意：cap() 函数只能作用于包含指针的数据类型，如数组、切片和 Channel，而不能用于值类型的数据类型，如整型、浮点型等。

## channel

### 定义使用channel

channel能够同步两个不同go程，主要采用阻塞

```go
package main

import "fmt"

func main() {
	// 定义一个channel
	c := make(chan int)

	go func() {
		defer fmt.Println("goroutine结束")
		fmt.Println("goroutine 正在运行...")

		c <- 666 // 将666发送给c
	}()

	num := <-c // 从c中接受数据，并赋值给num

	fmt.Println("num = ", num)
	fmt.Println("main goroutine 结束...")
}
```

运行结果

```
goroutine 正在运行...
goroutine结束
num =  666
main goroutine 结束...
```

### 带缓冲和无缓冲的channel

1. 到达通道，还没开始执行发送或接收
2. 左侧goroutine向通道发送数据的行为，goroutine在通道中被锁住，直到交换完成
3. 右侧goroutine从通道里接收数据，goroutine也会在通道中被锁住，直到交换完成
4. 交换后，两个goroutine从通道里出来，被锁住的goroutine得到释放，两个goroutine可以去做其他事情

### 有缓冲的channel

1. 右侧的goroutine正从通道中接收一个值
2. 右侧的goroutine独立完成接收值的动作，左侧goroutine正发送一个新值到通道里
3. 左侧的goroutine向通道发送新值，右侧的goroutine正在从通道接收另一个值，操作既不是同步，也不会相互阻塞
4. 所有发送和接收都完成，通道里还有几个值，也有一些空间存更多的值

```go
package main

import (
	"fmt"
	"time"
)

func main() {
	c := make(chan int, 3) //带缓冲的channel
	fmt.Println("len(c) = ", len(c), "cap(c) = ", cap(c))

	go func() {
		defer fmt.Println("子go程结束")
		for i := 0; i < 3; i++ { // for i := 0; i < 4; i++
			// 当i为4时，channel已满发生阻塞，需等待channel中的空间
			c <- i
			fmt.Println("子go程正在运行: len(c) = ", len(c), ", 发送的元素 = ", i, ", cap(c) = ", cap(c))
		}
	}()

	time.Sleep(2 * time.Second)

	// 主go程
	for i := 0; i < 3; i++ {
		num := <-c // 从c中接收数据，并赋值给num
		fmt.Println("num = ", num)
	}

	fmt.Println("main 结束")
}
```

channel已满，再向里面写数据，就会阻塞
channel为空，从里面取数据也会阻塞

### 关闭channel

如果不进行关闭，出现死锁。子go程执行完，主go程依然在等待数据，但没有数据进行传入，导致永远阻塞

```go
package main

import "fmt"

func main() {
	c := make(chan int)

	go func() {
		for i := 0; i < 5; i++ {
			c <- i
		}
		// close 可关闭一个channel
		close(c)
	}()
	// 主go程
	for {
		// ok如果为true表示channel没有关闭，如果false表示channel已经关闭
		if data, ok := <-c; ok {
			fmt.Println(data)
		} else {
			break
		}
	}
	fmt.Println("Main Finished...")
}
```

1. channel不需要经常关闭，只有当确实没有任何发送数据，显式结束range循环之类的，才关闭channel
2. 关闭channel后，无法向channel再发送数据（引发panic错误后，导致接收立即返回零值），如果发完一个数据，就进行close，则报panic错误
3. 关闭channel后，可以继续从channel接收数据
4. 对nil channel，无论收发都会被阻塞

### channel与range

```go
package main

import "fmt"

func main() {
	c := make(chan int)

	go func() {
		for i := 0; i < 5; i++ {
			c <- i
		}
		// close 可关闭一个channel
		close(c)
	}()
	// 主go程
	// 用go程来迭代不断操作channel
	for data := range c {
		fmt.Println(data)
	}
	fmt.Println("Main Finished...")
}
```

### channel与select

单个流程下一个go只能监控一个channel状态，select可以完成监控多个channel状态

```go
select {
	case <- chan1:
		// 如果chan1成功读到数据，则进行该case语句
	case chan2 <- 1:
		// 如果成功chan2写入数据，则进行case语句
	default:
		// 如果上面都没有成功，则进入default处理流程
}
```

```go
package main

import "fmt"

func fibonacii(c chan int, quit chan int) {
	x, y := 0, 1

	for {
		select {
		case c <- x:
			// 如果c可写，则case会进来
			x = y
			y = x + y
		case <-quit:
			fmt.Println("quit")
			return
		}
	}
}

func main() {
	c := make(chan int)
	quit := make(chan int)

	// sub go
	go func() {
		for i := 0; i < 6; i++ {
			fmt.Println(<-c) // c读数据，并打印出来
		}

		quit <- 0 // 发送数字
	}()

	// 监控两个状态
	// main go
	fibonacii(c, quit)
}
```

### 同步锁

同步锁是通过 sync 包提供的 Mutex 类型

1. 互斥性：同一时刻只能有一个 Goroutine 持有锁，其他 Goroutine 需要等待锁的释放才能获取锁。
2. 阻塞性：如果一个 Goroutine 尝试获取一个已经被其他 Goroutine 持有的锁，它会被阻塞，直到锁被释放。
3. 公平性：锁的获取是公平的，即锁会按照申请的先后顺序分配给等待的 Goroutine，避免某些 Goroutine 永远无法获取锁的情况。

同步锁的作用是保护共享数据，避免多个 Goroutine 同时对共享数据进行修改而导致的竞争条件和数据竞争问题。在需要保证共享数据的安全访问时，可以使用同步锁来对临界区进行加锁，以避免并发修改数据产生的问题。在使用同步锁时，需要注意避免死锁和饥饿等问题的发生，同时需要合理地设计锁的粒度和作用域，避免锁的竞争导致性能下降

## GOPATH的工作模式

### GOPATH弊端

1. 无版本控制概念
2. 无法同步一致第三方版本号
3. 无法指定当前项目引用的第三方版本号

### go mod 环境变量

#### go modules 模式

设置go111MODULE 高版本开启go modules模式

#### GOPROXY

项目第三方依赖库的下载源地址

国内的地址  阿里云或者七牛云

direct 指示go回溯到模块版本的源地址抓取

#### GOSUMDB

### go mod 命令

#### go modules模块

1. 保证GO111MODULE=on

```sh
go env -w GO111MODULE=on
```

2. 初始化项目
   任意文件夹创建项目，不要求在$GOPATH/src

创建go.mod文件，同时写当前项目模块名称

生成go mod 文件

编写源代码 go mod tidy

go mod 文件添加一行新代码

会生成一个go.sum文件，当前项目直接或者间接的依赖所有模块版本，保证今后项目依赖的版本不会篡改

hash校验

### 模块之间的依赖关系

修改go.mod文件的模块依赖
