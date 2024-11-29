

# 绘图

## 绘制x和y点
图表中绘制点（标记），默认情况从点到点绘制一条线

## 无线绘图
绘制标记，字符串符号参数"o"
```python
xpoints = np.array([0, 6])
ypoints = np.array([0,250])

plt.plot(xpoints, ypoints, 'o')
plt.show()
```

## 多点
绘制任意数量的点，确保两个轴上的点数相同
```python
xpoints = np.array([1,2,6,8])
ypoints = np.array([3,8,1,10])
plt.plot(xpoints, ypoints)
plt.show()
```

## 默认X点
不指定x轴上的点，获得默认值0,1,2,3...取决于y点的数量
```python
ypoints = np.array([3,8,1,10,5,7])
plt.plot( ypoints)
plt.show()
```

# 标记
marker强调每个点
plt.plot(ypoints,marker='o')

## 标记、线、颜色

## 格式化字符串fmt
快捷字符串符号参数指定标记
```python
ypoints = np.array([3,8,1,10])
plt.plot(ypoints,'o:r')
plt.show()
```

## 标记尺寸
plt.plot(ypoints,'o:r',ms=20)

## 标记颜色
### 标记边缘颜色
plt.plot(ypoints,'o:r',ms=20, mec='r)

### 标记边缘内颜色
plt.plot(ypoints,'o:r',ms=20, mfc='r)

### 十六进制颜色值


# 线型
## 线样式
linestyle或ls更改绘制线的样式
```python
plt.plot(ypoints,linestyle='dotted')
```

- linestyle ls
- dotted : 
- dashed --

## 线颜色
```python
plt.plot(ypoints,color='r')
```

## 线宽度
```python
plt.plot(ypoints,linewidth='20')
```

## 多条直线
只指定y轴上的点，x轴上点为默认值(0 1 2 3 ...)
```python
x1 = np.array([0,1,2,3])
y1 = np.array([3,8,1,10])
x2 = np.array([0,1,2,3])
y2 = np.array([6,2,7,11])
plt.plot(x1,y1,x2,y2)
plt.show()
```

# 标签和标题
## 标签
xlabel()和ylabel()函数为x轴和y轴设置标签

## 标题
title()

## 设置字体属性
font1 = {'family':'serif':'color':'blue','size':20}
font2 = {'family':'serif':'color':'darked','size':15}

plt.title("123", fontdict=font1)
plt.xlabel("x", fontdict=font2)
plt.ylabel("y", fontdict=font2)

## 定位标题
title()的loc参数定位标题，合法值："left"、"right"、默认值为"center"

# 网格
## 绘图添加网格线
grid()函数将网格线添加到绘制图

plt.grid()

## 指定显示的网格线
使用grid()轴参数指定显示的网格线

plt.grid(axis='x')

## 设置网格的线属性
grid(color='green', linestyle='--', linewidth=1.5)

## 显示多图 
### subplot()
subplot(1,2,1)

subplot(1,2,2)

### 添加标题
同plt.plot(x,y)

### 超级标题
作为多个图上的大标题

## 散点图
plt.scatter(x,y)

### 颜色
plt.scatter(x,y,color='hotpink')

### 每个点上色
颜色数组作为c参数的值，为每个点设置特定的颜色，不能使用color参数，只使用c参数

plt.scatter(x, y, c=colors)

## 颜色图
关键字参数cmap指定颜色图，viridis。必须创建一个包含值的数组，散点图每个点有一个值

colors = np.array([0,10,20,30,40,45,50,55,60,65,70,80,90,100])
plt.scatter(x,y c=colors, cmap='viridis')
plt.colorbar()

### 尺寸
s参数更改点的大小，确保大小数组的长度与x和y轴的数组长度相同

sizes = np.array([20,50,100,200,500,1000,60,90,10,300,600,800,75])

plt.scatter(x,y,s=sizes)

### 透明度

plt.scatter(x,y,s=sizes,alpha=0.5)

## 柱状图
第一个和第二个参数表示为数组

plt.bar(x,y)

### 水平柱状图
plt.barh(x,y)

### 柱状图颜色
plt.bar(x,y,color="red")

plt.bar(x,y,color = "hotpink")

### 十六进制

### 条形宽度
plt.bar(x,y,width=0.1)

### 条形高度
plt.barh(x,y,height=0.1)

## 直方图
hist()函数，将一个数字数组创建直方图，

x = np.random.normal(170,10,250)
print(x)
plt.hist(x)
plt.show()

## 饼图
y=np.array([35,25,25,15])
plt.pie(y)
plt.show()

### 标签
label参数必须是一个数组

y=np.array([35,25,25,15])
mylabels=["Apples","Bananas","Cherries","Dates"]
plt.pie(y, labels=mylabels)

### 起始角度
默认起始角度位于x轴，startangle参数更改起始角度，以度为单位，默认角度为0

plt.pie(y, labels=mylabels, startangle=90)

### Explode
让其中一个部分凸显，指定explode参数，必须是一个数组，数组中的值表示每个部分离中心有多远

### 阴影
shadows参数设置为True，为饼图添加阴影

plt.pie(y, labels=mylabels, explode=myexplode, shadow=True)

### 颜色
mycolors=["black","hotpink","b","#4CAF50"]

plt.pie(y, labels=mylabels, explode=myexplode, shadow=True, colors=mycolors)

### 图例
plt.legend() # 添加解释列表

plt.legend(title="Four Fruits") # 带标题的图例
