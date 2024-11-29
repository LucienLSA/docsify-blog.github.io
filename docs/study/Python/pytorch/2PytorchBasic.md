
## pytorch学习资源来源

b站
https://www.bilibili.com/video/BV17X4y1H7dK?p=3&vd_source=917ef87e48a267f0acc88f766dea0a6e

## tensor简介

深度学习中，Tensor实际上就是一个多维数组（multidimensional array）。

而Tensor的目的是能够创造更高维度的矩阵、向量。

# 基本操作

## 创建Tensor

### list创建

创建一行两列和两行两列的tensor数据

```python
torch.tensor([1, 2]), torch.tensor([[1, 2], [3, 4]])
```

### numpy创建

```python
torch.from_numpy(np.array([1, 2]))
```

### 初始化的不同方法

两行三列

```python
#全1填充
print(torch.ones(2, 3))

#全0填充
print(torch.zeros(2, 3))

#full填充
print(torch.full([2, 3], 7))

#对角矩阵
print(torch.eye(3, 3))

#创建但不初始化 (内存随意划一段区域)
print(torch.empty(2, 3))

#标准正态分布
print(torch.randn(2, 3))

#0-1均匀分布
print(torch.rand(2, 3))

#1-9的整数均匀分布
print(torch.randint(1, 10, [2, 3]))

#自增数列,0到9,步长是2
print(torch.arange(0, 10, 2))

#等差数列,0到10,4个数
print(torch.linspace(0, 10, 4))
```

### 指定类型的创建

32位的浮点数和64位的整数

```python
torch.FloatTensor([1, 2]), torch.LongTensor([1, 2])
```

获取数据类型

```python
torch.randn(2,3).dtype
```

转换数据类型

```python
torch.randn(2, 3).to(torch.float64).dtype
```

## 索引tensor

```python
#创建测试数据
#4张图片,3通道,28*28像素
a = torch.randn(4, 3, 28, 28)
a.shape
```

### 直接索引

```python
#查看第0张图片
print(a[0].shape)

#查看第0张图片的第0个通道
print(a[0, 0].shape)

#查看第0张图片的第0个通道的第2行
print(a[0, 0, 2].shape)

#查看第0张图片的第0个通道的第2行第4列
print(a[0, 0, 2, 4].shape)
```

### 切片

在冒号前表示为0；
冒号前后都不写数据，则取维度上的所有数据

```python
#查看0-1张图片
print(a[:2].shape)

#查看0-1张图片的0-1通道
print(a[:2, :2].shape)

#查看0-1张图片的所有通道的倒数5-正数24行
print(a[:2, :, -5:25].shape)

#有间隔的索引（每两列取一列数据）
print(a[:, :, :, ::2].shape)

#用...表示多个被省略的:

#取所有图片的第0-2列
print(a[..., :2].shape)

#取所有图片的第0-2行
print(a[..., :2, :].shape)

#取第2张图片
print(a[2, ...].shape)
```

## tensor维度变换

### view,reshape维度变换

```python
#4张图片,单通道,28*28像素
a = torch.randn(4, 1, 28, 28)
print(a.shape)

#转换为(4,784)维度,四个维度转换为两个维度，相当于打平了
print(a.reshape(4, 784).shape)

#转换为(4,28,28)维度,相当于重新展开
print((a.reshape(4, 784)).reshape(4, 28, 28).shape)

#view的用法和reshape是完全一样的')
print(a.view(4, 784).shape)
```

### unsqueeze插入维度

```python
#2*2的tensor
a = torch.randn(2, 2)
print(a.shape)

#插入维度在第0维
print(a.unsqueeze(0).shape)

#插入维度在倒数第1维
print(a.unsqueeze(-1).shape)
```

### squeeze删除维度,只能删除为1的维度

```python
#1*2*2*1的tensor
a = torch.randn(1, 2, 2, 1)
print(a.shape)

#删除第0维
print(a.squeeze(0).shape)

#删除倒数第1维
print(a.squeeze(-1).shape)

#删除所有为1的维度
print(a.squeeze().shape)
```

### repeat复制维度

```python
#分别复制2次和3次
print(torch.randn(2, 2).repeat(2, 3).shape)
```

### 维度交换

三维数组的下标为[z, y, x]， transpose()对其的默认编号为0=z, y=1, x=2。

```python
#t转置,只能操作2维tensor
print(torch.randn(1, 2).t().shape)

#transpose维度交换,指定要转换的维度,只能两两交换
print(torch.randn(1, 2, 3).transpose(0, 1).shape)

#permute维度交换,输入维度的顺序
print(torch.rand(1, 2, 3).permute(2, 1, 0).shape)
```

## broadcast

```python
a = torch.randn(2, 3)
b = torch.randn(1, 3)
c = torch.randn(1)

print((a + b).shape)
print((a + c).shape)

#手动boradcast
print(b.expand_as(a).shape)
print(c.expand_as(a).shape)
```

## tensor拼接与拆分

### cat拼接

```python
a = torch.rand(4, 32, 8)
b = torch.rand(5, 32, 8)

#cat拼接,dim=0是指定要拼接的维度
torch.cat([a, b], dim=0).shape
```

a = torch.rand(4, 32, 8)
b = torch.rand(4, 32, 8)

### stack组合

会创建一个新的维度,用以区分组合后的两个tensor

```python
torch.stack([a, b], dim=0).shape
```

### split拆分

```python
a = torch.rand(4, 32, 8)

#split拆分,在0维度上拆分,每2个元素1拆
_1, _2 = a.split(2, dim=0)
print(_1.shape)
print(_2.shape)

#split拆分,在0维度上拆分,拆分后长度分别为1,2,1
_1, _2, _3 = a.split([1, 2, 1], dim=0)
print(_1.shape)
print(_2.shape)
print(_3.shape)
```

### chunk拆分

```python
a = torch.rand(4, 32, 8)

#chunk拆分,在0维度上拆分,拆成2个
_1, _2 = a.chunk(2, dim=0)
print(_1.shape)
print(_2.shape)
```

## 数学运算

```python
#测试数据
a = torch.FloatTensor([[0, 1, 2], [3, 4, 5]])
b = torch.FloatTensor([0, 1, 2])
print(a,b)
```

```python
#四则运算
print(a + b)
print(a - b)
print(a * b)
print(a / b)

#矩阵乘法
print(a @ b)
print(a.matmul(b))

#计算过程
0 * 0 + 1 * 1 + 2 * 2, 0 * 3 + 1 * 4 + 2 * 5
```

```python
#指数,对数运算

#求指数
print(a**2)

#开根号
print(a**0.5)

#求e的n次方
print(a.exp())

#以e为底,求对数
print(a.log())

#以2为底,求对数
print(a.log2())
```

```python
#逻辑运算

#大于
print(a > b)

#小于
print(a < b)

#等于
print(a == b)

#不等于
print(a != b)
```

```python
#裁剪,限制数据的上下限
a.clamp(2, 4)
```

### 四舍五入

```python
c = torch.FloatTensor([3.14])

#向下取整
print(c.floor())

#向上取整
print(c.ceil())

#四舍五入
print(c.round())
```

### 属性统计

```python
#测试数据
a = torch.FloatTensor([[0., 1., 2.], [3., 4., 5.]])
```

```python
#求最小
print(a.min())
#求最大
print(a.max())
#求平均
print(a.mean())
#求积
print(a.prod())
#求和
print(a.sum())
#求最大值下标
print(a.argmax())
#求最小值下标
print(a.argmin())
```

```python
#分维度求最大
a.max(dim=0)

#分维度求最大值下标
a.argmax(dim=0)

#求1范数
print(a.norm(1))
print(a.norm(1, dim=0))

#求2范数
print(a.norm(2))
print(a.norm(2, dim=0))

#求前2个最小值
a.topk(2, dim=1, largest=False)

#求第2个小的值
a.kthvalue(2, dim=1)
```
