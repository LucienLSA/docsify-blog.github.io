---
title: golang-gin-gorm-mysql的最基础的CRUD项目
katex: true
categories: 
- golang
tags:
- gin
- gorm
- mysql
---


## 学习资源
[https://www.bilibili.com/video/BV1aB4y1k7xM/?p=2&spm_id_from=pageDriver](GO + Gin + GORM + MySql 实现最基础的 CRUD)

## 数据库连接
根据gorm官网的mysql数据库连接，[https://gorm.io/zh_CN/docs/connecting_to_the_database.html](数据库连接)

使用接口和端口号


## 主键丢失和表名的复数问题
在定义表的结构体中，增加
```go
gorm.Model  
```

以下的config中增加配置项，解决查表自动添加复数的问题
```go
	db, err := gorm.Open(mysql.Open(dsn), &gorm.Config{
		NamingStrategy: schema.NamingStrategy{
			// 解决查表自动添加复数的问题
			SingularTable: true,
		},
	})
```

## 表结构体的优化
### 定义标签 反射
结构体变量后加入约束
```go
Name       string `gorm:"type:varchar(20); not null" json:"name" binding:"required"`
```
1. 结构体变量对外可见，首字符大写
2. gorm指定类型，json表示接受时的名称，binding required 表示必须传入

## 增加接口（增删改查）
### 增
创建一条数据
```go
	r.POST("/user/add", func(ctx *gin.Context) {
		var data List
		err := ctx.ShouldBindJSON(&data)

		// 判断绑定是否有误
		if err != nil {
			ctx.JSON(200, gin.H{
				"msg":  "添加失败",
				"data": gin.H{},
				"code": 400,
			})
		} else {
			// 请求成功，数据库操作
			db.Create(&data) // 创建一条数据

			ctx.JSON(200, gin.H{
				"msg":  "添加失败",
				"data": data,
				"code": 200,
			})
		}
	})
```

在postman或apifox中，新建请求，发送JSON格式，得到响应

### 删
1. 找到对应的id所对应的条目
2. 判断id是否存在
3. 从数据库中删除，或不存在id未找到

restful 编码规范

```go
	r.DELETE("/user/delete/:id", func(ctx *gin.Context) {
		var data []List
		// 接受id
		id := ctx.Param("id")
		// 接受以 "/user/delete/name=name"的字符
		// name := ctx.Query("name")

		// 判断id是否存在
		db.Where("id = ?", id).Find(&data)

		// 存在删除，不存在保存
		if len(data) == 0 {
			ctx.JSON(200, gin.H{
				"msg":  "id未找到,删除失败",
				"code": 400,
			})
		} else {
			// 操作数据库删除
			db.Where("id = ?", id).Delete(&data)

			ctx.JSON(200, gin.H{
				"msg":  "删除成功",
				"code": 200,
			})
		}
	})
```

### 改
1. 找到对应的id所对应的条目
2. 判断id是否存在
3. 修改对应条目，或不存在id未找到

```go
r.PUT("/user/update/:id", func(ctx *gin.Context) {
		var data List
		// 接受id
		id := ctx.Param("id")
		// 判断id是否存在
		db.Select("id").Where("id = ?", id).Find(&data)

		if data.ID == 0 {
			ctx.JSON(200, gin.H{
				"msg":  "用户id没有找到",
				"code": 400,
			})
		} else {
			err := ctx.ShouldBind(&data)
			if err != nil { // 所有JSON格式的变量都传入时，才可进行修改
				ctx.JSON(200, gin.H{
					"msg":  "修改失败",
					"code": 400,
				})
			} else {
				// 修改数据库内容
				db.Where("id = ?", id).Updates(&data)

				ctx.JSON(200, gin.H{
					"msg":  "修改成功",
					"code": 400,
				})
			}
		}

	})
```
通过apifox，模拟请求，发送，传入json格式数据

## 查询

### 条件查询
```go
r.GET("/user/search/:name", func(ctx *gin.Context) {
		// 获取路径参数
		name := ctx.Param("name")

		var dataList []List

		// 查询数据库
		db.Where("name = ?", name).Find(&dataList)

		// 判断是否查询到数据

		if len(dataList) == 0 {
			ctx.JSON(200, gin.H{
				"msg":  "未查询到数据",
				"code": 400,
				"data": gin.H{},
			})
		} else {
			ctx.JSON(200, gin.H{
				"msg":  "查询成功",
				"code": 200,
				"data": dataList,
			})
		}

	})
```
### 全部查询 分页查询
```go
	r.GET("/user/search", func(ctx *gin.Context) {
		var dataList []List
		// 1. 查询全部数据 分页数据
		pageNum, _ := strconv.Atoi(ctx.Query("pageNum"))
		pageSize, _ := strconv.Atoi(ctx.Query("pageSize"))
		// fmt.Println(pageNum)
		// fmt.Println(pageSize)

		// 判断是否需要分页
		if pageSize == 0 {
			pageSize = -1
		}
		if pageNum == 0 {
			pageNum = -1
		}
		offsetVal := (pageNum - 1) * pageSize

		if pageNum == -1 && pageSize == -1 {
			offsetVal = -1
		}

		// 返回总数
		var total int64
		db.Model(dataList).Count(&total).Limit(pageSize).Offset(offsetVal).Find(&dataList)

		if len(dataList) == 0 {
			ctx.JSON(200, gin.H{
				"msg":  "未查询到数据",
				"code": 400,
				"data": gin.H{},
			})
		} else {
			ctx.JSON(200, gin.H{
				"msg":  "查询成功",
				"code": 200,
				"data": gin.H{
					"list":     dataList,
					"total":    total,
					"pageNum":  pageNum,
					"pageSize": pageSize,
				},
			})
		}
	})
```

### 根据bubble案例项目改进和完善

使用查询接口时，分别对id和name进行查询，出现的问题
#### gin 路由冲突
panic: ':name' in new path '/user/search/:name' conflicts with existing wildcard ':id' in existing prefix '/user/search/:id'
#### 解决方案
[https://segmentfault.com/q/1010000042183820](go+gin 静态资源路由与后端api路由冲突如何解决？
)

[https://wu.run/posts/solve-gin-router-path-conflict/](解决 gin 路由冲突问题)
