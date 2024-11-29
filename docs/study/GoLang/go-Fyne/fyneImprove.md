

## 案例检测是否开机
进阶学习

### air热重载工具

#### 安装下载

air init

### 断言 

```go
func main() {
	var i interface{} = "123"
	s, ok := i.(int)
	if ok {
		fmt.Println("断言成功，值为", s)
	} else {
		fmt.Println("断言失败，值为", s)
	}
}
```

### panic 
类型断言失败，若没有使用ok检查，直接导致panic，程序会中止执行

#### 处理方法
defer 写在panic之前，否则无法捕获到panic

panic能在当前Goroutine中递归调用执行调用方(caller)的defer

panic只会触发当前goroutine的defer

### unsafe.Pointer 和 uintptr

#### unsafe.Pointer
```go
num := 14
numPointer := &num
fmt.Println(numPointer)
numb := (*float32)(unsafe.Pointer(numPointer))
fmt.Println(numb)
```

#### uintptr
一个整型,unsafe 包支持了这些函数来完成到uintptr的转换
```go
func Sizeof(x ArbitraryType) uintptr
func Offsetof(x ArbitraryType) uintptr
func Alignof(x ArbitraryType) uintptr
```

```go
func main() {
 arr := []int{10, 20, 30, 40, 50}
 ptr := unsafe.Pointer(&arr[0])
 ptr = unsafe.Pointer(uintptr(ptr) + unsafe.Sizeof(arr[0]))
 *(*int)(ptr) = 25
 fmt.Println(arr) // 输出 [10, 25, 30, 40, 50]
}
```

```go
func main() {
 var x = 114

 p := &x
 up := unsafe.Pointer(p) 
 uptr := uintptr(up)   
 fmt.Printf("pointer: %p;十进制为：%d\n", p, p)
// pointer: 0xc00000a0c8; 十进制为：824633761992(Up案例)
 fmt.Printf("uintptr: %d\n", uptr) // 地址的十进制表示
// uintptr: 824633761992
}
```

### 接口满足检查
```go
var _ fyne.Theme = (*myTheme)(nil)
```
_作为空标识符，使编译错误更接近定义类型的地方，无用变量的占位符，可接收任何类型的值，无害地丢弃

当不需要所有值，使用_进行忽略

### 主题背景颜色处理
按官方文档写
```go
func (m *myTheme) Color(n fyne.ThemeColorName, v fyne.ThemeVariant) color.Color {
	switch n {
	case theme.ColorNameBackground:
	return color.RGBA{190, 233, 255, 1}
	case theme.ColorNameButton:
	return color.RGBA{0, 122, 255, 255}
	case theme.ColorNameDisabledButton:
	return color.RGBA{142, 142, 147, 255}
	case theme.ColorNameHover:
	return color.RGBA{230, 230, 230, 255}
	case theme.ColorNameFocus:
	return color.RGBA{255, 165, 0, 255}
	case theme.ColorNameShadow:
	return color.RGBA{0, 0, 0, 50}
	default:
	return theme.DefaultTheme().Color(n, v)
 }
}
```

### bundling resources
捆绑资源
#### fyne bundle工具
1. 将外部资源打包成go
2. 生成go文件包含资源的字节数据，资源文件可与应用程序一起分发，不需要单独的资源文件

#### theme主题的自定义图标
```sh
fyne bundle -o bundled.go icon.jpeg
```

### 字体主题官方文档的不足
```go
type myTheme struct {
    regular   fyne.Resource
    bold      fyne.Resource
    italic    fyne.Resource
    monospace fyne.Resource
}
func (m *myTheme) Font(s fyne.TextStyle) fyne.Resource {
    if s.Monospace {
        return m.monospace
    }
    if s.Bold {
        return m.bold
    }
    if s.Italic {
        return m.italic
    }
    return m.regular
}
```

### 日志输出
输出日期和时间，自动换行

```go
log.Printf("Student name: %s, age: %d", u.Name, u.Age)
log.Panicf("%s is sleeping", u.Name)
log.Fatalf("%s is sleeping", u.Name) // 调用完退出程序
```

### 大小主题
```go
func (m *myTheme) Size(n fyne.ThemeSizeName) float32 {
	if n == theme.SizeNameText {
		return 114
	}
	return theme.DefaultTheme().Size(n)
}
```

### 对话框和表单
