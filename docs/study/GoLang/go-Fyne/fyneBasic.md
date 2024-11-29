---
title: Fyne-GoGUI学习
katex: true
categories: 
- golang
tags:
- Fyne
---

# 资源来自
[https://www.bilibili.com/video/BV1u142187Ps?p=1&vd_source=917ef87e48a267f0acc88f766dea0a6e](Go语言+Fyne快速上手教程[Go GUI应用开发|零基础Go实战速成])

## GUI APPlication
GUI 图形用户界面，指使用图形界面与用户交互的软件应用程序

### Fyne优势与劣势
1. 轻量
2. 性能
3. 资源消耗
4. 多平台一致
5. 生态不够成熟

#### 热重载（Hot Reload）
定义：允许开发者在不重新启动整个应用程序情况下立即看到代码更改的效果。Fyne框架本身不直接内置热重载功能


### 基本包
```go
	"fyne.io/fyne/v2/app"
	"fyne.io/fyne/v2/widget"
```

### 多窗口问题，Fyne容器 
```go
	w2 := a.NewWindow("Window 2")
	w3 := a.NewWindow("Window 3")
    w2.SetContent(container.NewVBox(widget.NewLabel("Test02")))
	w3.SetContent(container.NewVBox(widget.NewLabel("Test03")))
    w2.Show()
	w3.Show()
	a.Run()
```

###  使用容器制作UI界面
实现文本框输入和正整数的自增功能  按钮的外观设置  引入结构体

```go
func main() {
	a := app.New()
	w4 := a.NewWindow("Window 4")

	output, entry, btn, count, incrementbtn := makeUI()

	w4.SetContent(container.NewVBox(output, entry, btn, count, incrementbtn))
	w4.Resize(fyne.Size{Width: 1000, Height: 800})
	w4.Show()
	a.Run()

	fmt.Println("closed")
}

func makeUI() (*widget.Label, *widget.Entry, *widget.Button, *widget.Label, *widget.Button) {
	output := widget.NewLabel("Hello World!")
	entry := widget.NewEntry()
	btn := widget.NewButton("click!", func() {
		output.SetText(entry.Text)
	})
    btn.Importance = widget.HighImportance
	number := 0
	count := widget.NewLabel(fmt.Sprintf("Current Number :%d", number))
	incrementbtn := widget.NewButton("click again!", func() {
		number++
		count.SetText(fmt.Sprintf("Current Number :%d", number))
	})
    incrementbtn.Importance = widget.DangerImportance
	return output, entry, btn, count, incrementbtn
}
```
对结构体定义， 并进行实例化
```go
type App struct {
    output *widget.Label
}

var myApp App
```

### 解决中文的乱码问题
导入.ttf字体文件及相关的主题文件

## 案例markdown编辑器

### URI URL解析
1. URI是一个用于标识资源的字符串，资源的名称、位置或两者结合，URI概念为广义的，包含URL和URN

2. URL（统一资源定位符），URL是URI的一个子集，用于指定资源的位置，URL不仅标识资源，还提供定位资源的方法

### 主程序
(main.go)
1. 结构体定义
2. 过滤器设置
3. 自定义字体主题
4. UI设置，初始标题，菜单栏

### 窗口
（ui.go）
#### 文本编辑区

#### 富文本解析

#### 菜单栏创建三类

（config.go）
1. 另存为
2. 保存
3. 打开


### 筛选器和窗口修复
只打开.md后缀的文件 ， 窗口文件名保存

### 单元测试程序

### 打包fyne程序


## 案例检测是否开机

### 非阻塞的并发编程
每一个并发执行活动称为goroutine，一个程序启动时，只有一个goroutine调用main函数，称为主goroutine

对一个程序加入 go func(){}() 的并发


