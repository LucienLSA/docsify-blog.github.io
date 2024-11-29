
[[go-每日一库]golang-gorm实现关联查询(四) - 进击的davis - 博客园](https://www.cnblogs.com/davis12/p/16365294.html)

[Json: struct field readyReplicas has json tag but is not exported-CSDN博客](https://blog.csdn.net/baobaoxiannv/article/details/118809413)

[【Golang】GORM中实现分页的方法(附详细代码及注释)_gorm 分页-CSDN博客](https://blog.csdn.net/qq_42346574/article/details/110823914)

[高级主题 - 创建插件 - 《GORM 2.0 使用教程(中文文档)》 - 书栈网 · BookStack](https://www.bookstack.cn/read/gorm-2.0/docs-write_plugins.md)

## ORM（对象 关系 映射）

程序中对象或实例 映射到 关系型数据库 中的数据

### 特点

提高开发效率，牺牲执行性能，牺牲灵活性。在项目中使用sql语句可实现高性能查询等

## gorm与MySQL数据库

导入gorm包和mysql的驱动包

数据库查询管理可使用Navicat

### 基本建表

```go
package main

import (
	"fmt"

	"github.com/jinzhu/gorm"
	_ "github.com/jinzhu/gorm/dialects/mysql"
)

// UserInfor --> 数据表
type UserInfo struct {
	ID     int
	Name   string
	Gender string
	Hobby  string
}

func main() {
	// 连接数据库
	db, err := gorm.Open("mysql", "root:123456@(127.0.0.1:3306)/db1?charset=utf8&parseTime=True&loc=Local")
	if err != nil {
		panic(err)
	}
	defer db.Close()

	// 创建表 自动迁移（把结构体和数据表进行对应）
	db.AutoMigrate(&UserInfo{})

	// // 创建数据行
	// u1 := UserInfo{ID: 1, Name: "lucien", Gender: "男", Hobby: "篮球"}
	// db.Create(&u1)

	// 查询
	var u UserInfo
	db.First(&u) // 查询表中第一条数据保存到u
	fmt.Printf("u:%#v\n", u)

	// 更新
	db.Model(&u).Update("hobby", "乒乓球")

	// 删除
	db.Delete(&u)
}
```

### gorm model定义（数据库表最好不用go来建立）

定义模型与数据库中的数据表进行映射

- 定义模型
- 连接数据库
- 将模型与数据库表相对应
- 创建模型对象（判断是否为空）

gorm内置一个gorm.Model结构体

### 主键

gorm会默认使用ID字段作为表的主键，也可通过结构体的tag设定主键

```go
// 使用`AnimalID`作为主键
type Animal struct {
	AnimalID int64 `gorm:"primary_key"`
	Name     string
	Age      int64
}
```

### 表名

默认结构体名称的复数，也可禁用复数

```go
db.SingularTable(true)
```

可指定表名

```go
db.Table("home").CreateTable(&User{})
```

修改默认的表名前缀规则

```go
gorm.DefaultTableNameHandler = func(db *gorm.DB, defaultTableName string) string {
    return "SMS_" + defaultTableName
}
```

### 列名

列名由字段名称进行下划线分割来生成

也可使用tag对列名进行修改

```go
type Animal struct {
  AnimalId    int64     `gorm:"column:beast_id"`         // set column name to `beast_id`
  Birthday    time.Time `gorm:"column:day_of_the_beast"` // set column name to `day_of_the_beast`
  Age         int64     `gorm:"column:age_of_the_beast"` // set column name to `age_of_the_beast`
}
```

不会对数据表中内容原来修改的删除

### 时间戳跟踪

#### CreatedAt

初次创建记录的时间

#### UpdatedAt

每次更新记录的时间

#### DeletedAt（软删除）

删除具有DeletedAt字段的记录，设置Delete字段为当前时间，而不是直接从数据库中删除

## CRUD增删改查

### 增

#### 默认值

对创建的结构体，其一个变量没有传入值。如果想要传入默认值，在创建结构体时，加tag设定默认值

```go
// Debug()可将执行的sql语句打印出来
db.Debug().Create(&u) 
```

所有字段的零值。0 "" false 或者其他零值，都不会保存到数据库中，但会使用它们的默认值，避免这种情况，使用指针或者Scanner/Valuer

```go
type User struct {
	Id   int64
	Name *string `gorm:"default:'小王子'"`
	Age  int64
}
// 创建以空字符
u := User{Name: new(string), Age:25}
```

```go
type User struct {
	Id   int64
	Name sql.NullString `gorm:"default:'小王子'"`
	Age  int64
}
// 创建以空字符
u := User{Name: sql.NullString{String:"", Valid:true}, Age:25} 
```

#### 扩展创建

实现合并插入，有则更新，无则插入

### 查询

#### 一般查询

```go
	// var user User // 声明模型结构体类型变量user
	// 根据主键查询第一条记录
	user := new(User)
	db.First(&user) // 传地址（解决拷贝情况）
	fmt.Printf("user:%#v\n", user)

	var users []User
	db.Debug().Find(&users)
	fmt.Printf("user:%#v\n", users)
```

#### Where 条件

指定条件

1. sql语句
2. Struct & Map 查询
   结构体查询，GORM将只通过非零值字段查询，字段为0/false或者零值，不会被用于构建查询条件

处理该问题，同样可使用Scanner/Valuer接口

```go
// Struct
db.Where(&User{Name: "jinzhu", Age: 20}).First(&user)
//// SELECT * FROM users WHERE name = "jinzhu" AND age = 20 LIMIT 1;

// Map
db.Where(map[string]interface{}{"name": "jinzhu", "age": 20}).Find(&users)
//// SELECT * FROM users WHERE name = "jinzhu" AND age = 20;

// 主键的切片
db.Where([]int64{20, 21, 22}).Find(&users)
//// SELECT * FROM users WHERE id IN (20, 21, 22);

```

#### Not条件

排除某些条件

#### Or条件

#### 内联条件

内联条件与多个立即执行方法一起使用，不会传递给后面立即执行方法

```go
// 根据主键获取记录 (只适用于整形主键)
db.First(&user, 23)
//// SELECT * FROM users WHERE id = 23 LIMIT 1;
// 根据主键获取记录, 如果它是一个非整形主键
db.First(&user, "id = ?", "string_primary_key")
//// SELECT * FROM users WHERE id = 'string_primary_key' LIMIT 1;

// Plain SQL
db.Find(&user, "name = ?", "jinzhu")
//// SELECT * FROM users WHERE name = "jin
```

#### 额外的SQL操作

```go
db.Set("gorm:query_option", "FOR UPDATE").First(&user, 10)
//// SELECT * FROM users WHERE id = 10 FOR UPDATE;
```

#### FirstOrInit

获取匹配的第一条记录，否则根据给定的条件初始化一个新的对象 (仅支持 struct 和 map 条件)

```go
// 未找到
db.FirstOrInit(&user, User{Name: "non_existing"})
//// user -> User{Name: "non_existing"}

// 找到
db.Where(User{Name: "Jinzhu"}).FirstOrInit(&user)
//// user -> User{Id: 111, Name: "Jinzhu", Age: 20}
db.FirstOrInit(&user, map[string]interface{}{"name": "jinzhu"})
//// user -> User{Id: 111, Name: "Jinzhu", Age: 20}
```

1. Attrs
   记录未找到，使用参数初始化struct

```go
// 未找到
db.Where(User{Name: "non_existing"}).Attrs(User{Age: 20}).FirstOrInit(&user)
//// SELECT * FROM USERS WHERE name = 'non_existing';
//// user -> User{Name: "non_existing", Age: 20}
```

2. Assign
   不管记录是否找到，都将参数赋值给struct

```go
// 未找到
db.Where(User{Name: "non_existing"}).Assign(User{Age: 20}).FirstOrInit(&user)
//// user -> User{Name: "non_existing", Age: 20}

// 找到
db.Where(User{Name: "Jinzhu"}).Assign(User{Age: 30}).FirstOrInit(&user)
//// SELECT * FROM USERS WHERE name = jinzhu';
//// user -> User{Id: 111, Name: "Jinzhu", Age: 30}
```

#### FirstOrCreate

获取匹配的第一条记录, 否则根据给定的条件创建一个新的记录 (仅支持 struct 和 map 条件)

同样有Attrs和Assign

### 高级查询

### Pluck

### 扫描

#### Group & Having

使用rows返回结果，使用Scan将多条结果扫描进实现准备的结构体切片中

## 链式操作

创建函数，使用相关逻辑

### 立即执行

### 范围

在链式操作基础上创建函数

### 多个立即执行

后一个立即执行会复用前一个立即执行， 但内联条件不会复用

## 更新

### 更新所有字段

Save() 默认更新该对象的所有字段

```go
user.Name = "weewe"
user.Age = 67
db.Debug().Save(&user)
```

### 更新修改字段

Update

```go
	m1 := map[string]interface{}{
		"name":   "kkk",
		"age":    23,
		"active": true,
	}
	db.Model(&user).Updates(m1)              //m1列出来的所有字段都会更新
```

### 更新选定字段

Select Omit

```go
db.Model(&user).Select("age").Update(m1) // 只更新age字段
```

```go
db.Debug().Model(&user).Omit("active").Updates(m1) // 排除m1字段
```

### 无Hook更新

以上更新操作会自动运行model的BeforeUpdate/AfterUpdate方法，更新Update时间戳，更新时保存其Associations

使用UpdateColumn/UpdateColumns

```go
db.Debug().Model(&user).UpdateColumn("age", 33)
```

### 批量更新

Hooks钩子函数不会执行

使用struct 更新只会更新非零值字段，若需要更新所有字段，使用map[string]interface{}

使用RowsAffected 获取更新记录总数

```go
	rowsNum := db.Model(User{}).Updates(User{Name: "hello", Age: 19}).RowsAffected
	fmt.Println(rowsNum)
```

### 使用SQL表达式更新

比如让users表中的所有用户的年龄在原来基础上+2，也可做多个更新。并加入其他条件

```go
db.Debug().Model(&User{}).Update("age", gorm.Expr("age+?", 2))
```

### 修改Hooks中的值

使用scope.SetColumn可修改BeforeUpdate和BeforeSave等Hooks中更新的值

## 删除

### 删除记录

**删除记录时，请确保主键字段有值，GORM 会通过主键去删除记录，如果主键为空，GORM 会删除该 model 的所有记录**

```go
var u = User{}
u.ID = 1
db.Debug().Delete(&u)
```

### 批量删除

删除全部匹配记录

```go
db.Debug().Where("name=?", "lucien").Delete(User{})
db.Delete(User{}, "age=?", 18)
```

### 软删除

如果一个 model 有 DeletedAt 字段，他将自动获得软删除的功能！ 当调用 Delete 方法时， 记录不会真正的从数据库中被删除， 只会将DeletedAt 字段的值会被设置为当前时间

Upscoped()可查询被软删除的记录

```go
	var u1 []User
	db.Debug().Unscoped().Where("name=?", "lucien").Find(&u1)
	fmt.Println(&u1)
```

### 物理删除

嵌入内置的gorm.Model

```go
db.Debug().Unscoped().Where("name=?", "lucien").Delete(User{})
```

如果不嵌入内置的gorm.Model，则自定义ID（主键），执行普通删除时，不能实现软删除（因为没有delete_at字段）

```go
db.Debug().Where("name=?", "lucien").Delete(User{})
```
