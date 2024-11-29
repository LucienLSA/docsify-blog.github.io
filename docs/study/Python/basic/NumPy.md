

Numpy提供一个数组对象ndarray，比python自带的列表更快。Numpy的数组被存储在内存中的一个连续位置。

# 数组
## 数组维数

### 一维数组

### 二维数组

### 三维数组

### 检查维数
arr.ndim

### 更高维数
arr3 = np.array([1,2,3,4], ndmin=5)

## 数组索引
访问数组元素，通过索引号访问数组索引。以0开头，第一个元素的索引为0，第二个元素的索引为1

### 访问数组元素

### 访问二维数组

### 访问三维数组

### 负索引
print("第二维中的最后一个元素", arr1[1, -1])

## 数组裁切
裁切将元素从一个给定的索引带到另一个给定的索引。[start:end]
定义步长[start:end:step]
1. 不传递start，将其视为0
2. 不传递end，将其视为该维度内数组的长度
3. 不传递step，将其视为1
print(arr[1:5])
print(arr[4:])
print(arr[:4])

### 负裁切
使用减号运算符从末尾开始引用索引

### 二维数组裁切
```python
arr1 = np.array([[1,2,3,4,5],[6,7,8,9,10]])
print(arr1)
# 从第二个元素开始，对从索引1到索引4（不包括）的元素进行切片
print(arr1[1, 1:4])
# 从两个元素中返回索引2的元素
print(arr1[0:2, 2])
# 从两个元素裁切索引1到索引4（不包括），返回一个二维数组
print(arr1[0:2,  1:4])
```


# 数据类型
## Numpy数据类型
i-整数；b-布尔；u-无符号整数；f-浮点；c-复合浮点数；m-timedelta；M-datatime；O-对象；S-字符串；U-unicode；V-固定的其他类型的内存块

### 检查数组的数据类型
arr.dtype

### 已定义的数据类型创建数组
arr3 = np.array([1,2,3,4], dtype='S')

### 值无法转换情况
不能强制转换元素的类型，则Numpy引发ValueError:如果传递给函数的参数类型是非预期或错误

arr9 = np.array(['a','2','1'],dtype='i')

### 转换已有数组的数据类型
astype()方法复制该数组。astype()函数创建数组的副本，允许将数据类型指定为参数
- 将数据类型从浮点型转换为整型
- 将数据类型从整型转换为布尔值

### 副本与视图
副本是一个新数组，视图只是原始数组的视图。
- 副本拥有数据，对副本的任何更改不会影响原始数组；对原始数组的任何更改不会影响副本。
- 视图不拥有数据，对视图的任何更改都会影响原始数组，而对原始数组的任何更改会影响视图

### 检查数组是否拥有数据
每个Numpy数组都有一个属性base，该数组拥有数据，base属性返回None，否则base属性将引用原始对象

# 数组形状
数组的形状是每个维中元素的数量

## 获取数组形状
Numpy的shape属性，该属性返回一个元组，每个索引具有相应元素的数量
arr.shape

## 数组的重塑
更改数组的形状，增加或删除。
```python
arr1 = np.array([1,2,3,4,4,5,6,7,8,3,5,6])
newarr1 = arr1.reshape(4,3)
```

### 不能重塑成任何形状
如可将8个元素的一维数组重塑为二维数组中2行的4个元素，但不能将其重塑为二维数组中的3行3个元素

### reshape后返回的是视图

### 未知维度
使用未知维度，不必再reshape中维度指定确切的数字，传递-1作为值

### 展平数组
将多维数组转换为一维数组，reshape(-1)

## 数组迭代
逐一遍历每个元素

### 迭代一维数组

### 迭代二维数组

# 逐一遍历二维数组
```python
arr2 = np.array([[1,2,4],[4,5,6]])
for x in arr:
    print(x)
```
### 迭代三维数组
nditer()辅助函数，对高维数组更加方便
```python
arr6 = np.array([[[1,2],[3,4]],[[5,6],[7,8]]])
for x in np.nditer(arr6):
    print(x)
```

### 迭代不同数据类型的数组
op_dtypes参数，传递期望的数据类型，迭代时更改元素的数据类型

Numpy不会更改元素的数据类型，因此需要其他空间执行，额外空间称为buffer。在nditer()中启用它，传参flags=['buffered']

### 不同步长迭代

### 枚举迭代
ndenumerate()，迭代时需要元素的相应索引。输出索引与值
```python
arr3 = np.array([[1,2,3],[4,5,6]])
for idx ,x in np.ndenumerate(arr3):
    print(idx, x)
```

## 数组连接

将多个数组的内容放在单个数组中。Numpy中按轴连接数组。

沿着行(axis=1)连接两个二维数组

### 堆栈函数连接数组

堆栈与连接，不同在堆栈沿着新轴完成。可以沿着第二个轴连接两个一维数组，导致彼此重叠，堆叠。
```python
# 堆栈函数连接数组
arr1 = np.array([[1,2],[3,4]])
arr2 = np.array([[5,6],[7,8]])
arr = np.stack((arr1, arr2), axis=0)
print(arr, arr.shape)

arr = np.stack((arr1, arr2), axis=1)
print(arr, arr.shape)
```

### 沿着列堆叠
arr = np.vstack((arr1, arr2))

### 沿着行堆叠
arr = np.hstack((arr1, arr2))

### 沿着高度堆叠
arr = np.dstack((arr1, arr2))



## 数组拆分
拆分将一个数组拆分为多个，array_split()分割数组 

### 分割二维数组

np.array_split(arr, num,axis=)

## 数组搜索

数组中搜索某个值，返回获得匹配的索引（位置）。where(arr==1)

### 搜索排序

searchsorted()，二进制搜索，返回将在其中指定值以维持搜索顺序的索引

x = np.searchsorted(arr, 9) # 返回第一个索引，9不再大于下一个值

x = np.searchsorted(arr, 9, side='right')，9不再小于下一个值

### 多个值搜索

## 数组排序

sort()函数，指定数组进行排序

### 二维数组排序

## 数组过滤

从现有数组取出一些元素并从中创建新数组称为过滤

布尔索引列表进行过滤。布尔索引列表是与数组中索引相对应的布尔值列表

索引处值为True，元素包含在过滤后的数组中；索引处值为Flase，元素将从过滤后的数组中排除

### 创建过滤器数组

对True和Flase值进行硬编码，根据条件创建过滤器数组

for循环条件进行过滤

```python
# 过滤器数组
arr = np.array([61,62,63,64,65])
filter_arr = []
for element in arr:
    if element > 62:
        filter_arr.append(True)
    else:
        filter_arr.append(False)
newarr = arr[filter_arr]
print(filter_arr)
print(newarr)
```

### 直接从数组创建过滤器
```python
arr = np.array([61,62,63,64,65])
filter_arr = arr > 62
# filter_arr = arr % 2 == 0
newarr = arr[filter_arr]
print(filter_arr)
print(newarr)
```

# 通用函数
unfuncs在Numpy实现矢量化，迭代更快。

where定义操作发生位置

dtype定义元素的返回类型

out输出数组

矢量化：将迭代语句转换为矢量的操作

## 算术
有条件：定义操作发生条件，包含where参数，指定条件

### 加法
np.add()
- 轴上求和 np.sum([arr1, arr2], axis=1)
- 累加 np.cunsum(arr)

### 减法
np.subtract(arr1,arr2)

### 乘法
np.multipy(arr1,arr2)

### 乘积
np.prod(arr) # 一个数组中各元素的乘积

np.prod([arr1, arr2]) # 两个数组元素的乘积

- 累乘 np.cunprod(arr) # 部分乘积

### 除法
np.divide(arr1,arr2)

### 幂
np.power(arr1,arr2)

### 余数
np.mod(arr1, arr2) # 返回第一个数组的数值对应第二个数组的数组的余数，并在一个新的数组中返回结果

np.remainder(arr1, arr2)

### 商和余数
np.divmod(arr1,arr2) # 第一个数组返回商，第二个数组返回余数

### 绝对值
np.abs(arr1,arr2)

## 四舍五入

### 截断
去掉小数，返回最接近零的浮点数

np.trunc()

np.fix()

### 四舍五入
np.around() 

### 下限
np.floor() # 四舍五入到最接近的小整数

### 上限
np.ceil() # 四舍五入到最接近的大整数

## 对数

### 基数为2的对数
np.log2(arr1)

### 自然对数，基数为e的对数
np.log(arr)

### 任意基数下的对数
```python
from math import log
nplog = np.frompyfunc(log,2,1)
print(nplog(100,15))
```

### 创建自己的ufunc
定义一个函数，frompyfunc()方法添加到numpy的ufunc库
```python
def myadd(x,y):
    return x+y
myadd = np.frompyfunc(myadd,2,1)
print(myadd([1,2,3,4],[5,6,7,8]))
```

### 检查一个函数是否ufunc
是则返回<class 'numpy.ufunc'>
```python
print(type(np.add))
```
不是则返回其它类型
```python
print(type(np.concatenate)) # function内置函数
```


### 差分
离散的差意味减去两个连续的元素

np.diff(arr)

#### 二阶差分
np.diff(arr, n=2)

## LCM最小公倍数

### 寻找最小公倍数
x = np.lcm(num1, num2)

### 寻找数组中的最小公倍数
找到一个数组中所有数值的最小公倍数，reduce()，reduce()方法将对每个元素使用ufunc

x = np.lcm.reduce(arr)
 
## GCD最大公约数

### 两个数最大公约数
x = np.gcd(num1 , num2)

### 数组中的最大公约数
y = np.gcd.reduce(arr)

## 三角函数
np.sin(np.pi/2)

## 角度转换为弧度
np.deg2rad(arr)

## 寻找角度
从正弦、余弦、正切值中寻找角度

np.arcsin(1.0)

## 斜边(勾股定理)
base = 3
perp = 4
np.hypot(base, perp)

## 双曲函数
sinh() cosh() tanh() 以弧度为单位取值并产生相应的sinh cosh和tanh值

np.sinh(np.pi/2)

np.cosh(arr)

np.arcsinh(1.0)

## 集合
集合用于频繁相交、联合和差异操作

### 创建集合
unique()从任何数组找到唯一元素，只能是一维数组

arr = np.array([1,1,1,2,3,4,5,5,6,7])

x = np.unique(arr)

### 寻找联合
两个数组的唯一值

np.union1d(arr1, arr2)

### 寻找相同值
两个数组中都有的值，intersect1d()。需要一个可选参数assume_unique

np.intersect1d(arr1, arr2, assume_unique=True)

## 寻找差异
找到第一组中不存在于第二组中的数值

np.setdiff1d(arr1, arr2, assume_unique=True)

## 寻找异或值
找到两个集合的不相等值
np.setxor1d(set1, set2, assume_unique=True)

# 数据分布
所有可能值得列表，每个值出现频率。

## 随机数

### 伪随机和真随机
通过生成算法生成的随机数称为伪随机数，通过某个外部来源获取随机数据

### 生成随机数
以下0到100内的随机整数

from numpy import random
x = random.randint(100)

### 生成随机浮点数
以下0到1之间的随机浮点数

random.rand()

### 生成随机数组
- 生成5个元素的数组，0-100内的随机整数

random.randint(100,size=(5))

- 生成三行五列的数组

random.randint(100, size(3,5))

- 生成0到1之间的随机浮点数的整数

random.rand(5)

- 生成三行五列的数组

random.rand(3,5)

### 数组生成随机数
- choice()将数组作为参数，并随机返回其中一个值

random.choice([3,5,7,9])

- 二维数组

random.choice([3,5,7,9], size(3,5))


## Seaborn分布的可视化
分布图，将一个数组作为输入，并绘制出数组中的点分布相对应的曲线
```python
import seaborn as sns
import matplotlib.pyplot as plt
sns.displot([0,1,2,3,4,5])
plt.show()
```

## 随机分布
遵循某种概率密度函数的随机数。概率密度函数，一个描述连续概率的函数。即一个数组中所有值的概率。choice()方法根据定义的概率生成随机数

x = random.choice([3,4,7,9],p=[0.1,0.3,0.6,0], size=(100))

x = random.choice([3,4,7,9],p=[0.1,0.3,0.6,0], size=(3,5)) # 二维数组

## 随机排列组合
### 洗牌数组
改变元素的排列，数组本身

arr = np.array([1,2,3,4,5])
random.shuffle(arr)

### 数组排列组合
permutation()返回一个重新排列的数组（并使原始数组不发生变化）

```python
arr = np.array([1,2,3,4,5])
newarr = random.permutation(arr)
print(newarr)
print(newarr.base)
```

### 正态分布
高斯分布，loc-均值（峰值）；scale-标准偏差（图形分布应该有多平坦）；size-返回数组形状

```python
x = random.normal(size=(2,3)) # 二维数组，两行三列

x = random.normal(loc=1, scale=2, size=(2,3)) # 均值为1，标准差为2

x = random.normal(size=1000)
sns.distplot(x,hist=False)
plt.show()
```

### 二项式分布
离散分布，n-试验的数量，p-每个试验出现的概率，size-返回数组

```python
x = random.binomial(n=10,p=0.5,size=100)
print(x)
sns.distplot(x, hist=True, kde=False)
plt.show()
```

#### 正态分布与二项分布的区别
正态分布是连续的，二项分布是离散的，如果有足够多数据点，与正态分布类似

```python
sns.distplot(random.normal(loc=50,scale=5, size=1000), hist=False, label='normal')
sns.distplot(random.binomial(n=100,p=0.5,size=1000), hist=False, label='binormal')
plt.show()
```

### 泊松分布
离散分布，lam-速率或已知的发生次数；size-返回数组的形状
```python
x = random.poisson(lam=2, size=10)
sns.distplot(random.poisson(lam=2, size=100),kde=False)
plt.show()
```

#### 正态分布和泊松分布区别
正态分布是连续的，泊松分布是离散的。足够大的泊松分布，变得与正态分布相似，具有一定的标准差和平均数

#### 泊松分布和二项分布的区别
二项分布对离散试验，泊松分布对连续试验。对于非常大的n和接近于零的p来说，二项分布与泊松分布几乎相同，n*p约等于lam

### 均匀分布
每个事件发生的机会均等的概率
a-下限，默认为0.0； b-上限，默认为1.0

x = random.uniform(size(2,3))
sns.distplot(random.uniform(size=100), hist=False)
plt.show()

### Logistic分布
描述增长，广泛用于机器学习、神经网络
loc-平均值（峰值），默认为0；scale-标准差，分布的平坦程度，默认为1；size形状

x = random.logistic(loc=1, scale=2,size=(2,3))
sns.distplot(random.logistic(size=100), hist=False)
plt.show()

#### logistic分布和正态分布的区别
几乎相同，但logistic分布的尾部面积更大，表示离平均值更远的事件发生的可能性更大

### 多项式分布
多种情况下，为二项式的概况。
n-可能的结果数；pvals-结果的概率列表；size形状

### 指数分布
描述下一个事件的时间
scale-速率的导数，默认为1.0；size为数组形状

x = random.exponential(scale=2, size=(2,3))
sns.distplot(random.exponential(size=100),hist=False)
plt.show()

### 泊松分布与指数分布之间关系
泊松分布是一个时间段内发生的事件和数量，指数分布是这些事件之间的时间

### 智方分布
用来作为验证假设的基础
df-自由度，size-返回数组的形状

x = random.chisquare(df=2,size=(2,3))
sns.distplot(random.chisquare(df=1,size=100),hist=False)
plt.show()

### 雷利分布
信号处理中，scale-标准偏差决定的平坦程度，默认为1.0，size为形状

x = random.rayleigh(scale=2, size=(2,3))
sns.distplot(random.rayleigh(size=100),hist=False)
plt.show()


#### 雷利分布与智方分布的相似性
在一个标准差和两个自由度，雷利分布与智方分布代表相同的分布

### 帕累托分布
帕累托法则，即80-20分布（20%的因素导致80%的结果）
a-形状参数；size-数组形状

```python
x = random.pareto(a=2,size=(2,3))
print(x)
sns.distplot(random.pareto(a=2,size=100),kde=False)
plt.show()
```

### 拉兹夫分布
根据Zipf定理对数据进行抽样。一个集合中，第n个常用词是最常用词的1/n
a-分布参数；size-数组形状

x = random.zipf(a=2,size=100)
sns.distplot(x[x<10],kde=False)








