
# Scientific Python
优化、统计和信号处理。Scipy基于NumPy

# 常数
## 常量
```python
from scipy import constants
print(constants.pi)
```
## 常量单位
dir()函数可看到常量模块下所有单位的列表

## 单位类别
公制长度、二进制、质量、角度、时间、体积、压力、速度、温度、力

### 公制长度
以米为单位
print(constants.kilo)  # 千米

### 二进制单位
以字节为单位
print(constants.kibi) # 千米二进制

### 质量
以公斤为单位
print(constants.gram) # 克

### 角度
以弧度为单位
print(constants.degree)  # 弧度

### 时间
以秒为单位
print(constants.minute) # 1分钟，60秒

### 英制长度

### 压力
以帕斯卡为单位

### 面积
以平方米为单位

### 体积
以立方米为单位
print(constants.liter)

### 速度
以米/秒 为单位
print(constants.kmh)

### 温度
以指定开尔文单位

### 能量
以指定焦耳单位

### 功率
以瓦为单位

### 力
以指定牛顿单位

# 优化器
## 优化函数
复杂方差下将给定数据的目标函数最优化

## 方程的根
非线性方程的根，optimiza.root
fun-一个代表方程的函数
x0-一个根的初始猜测

该函数返回一个包含解的信息的对象

```python
from scipy.optimize import root
from math import cos
def eqn(x):
    return x + cos(x)

myroot = root(eqn, 0)
print(myroot.x)
print(root) # 打印所有解的信息
```

## 最小化一个函数
minimize()函数，fun-方程的函数，x0-根的初始猜测，method-使用方法的名称，'CG' 'BFGS' '牛顿-CG' 'TNC'，callback回调函数-每次优化迭代后的函数，options-定义额外参数的字典
```python
from scipy.optimize import minimize
def eqn1(x):
    return x**2 + x + 2

mymin = minimize(eqn, 0 ,method='BFGS')
print(mymin)
```

# 稀疏数据
稀疏数据指大部分没有使用的元素（不携带任何信息的元素）的数据

稀疏数组 [1,0,2,0,0,3,0,0,0,0,0] 数据集，其中大部分项目的值为零

密集数组 大部分数值不为零

## 处理稀疏数据
scipy.sparse 提供函数

CSC-压缩稀疏列，快速的列切分

CSR-压缩稀疏行，快速的行切分，更快矩阵向量乘积

## CSR矩阵
scipy.sparse.csr_matrix()传递array创建CSR矩阵
```python
import numpy as np
from scipy.sparse import csr_matrix
arr = np.array([0,0,0,0,1,1,0,2])
print(csr_matrix(arr))
```

## 稀疏矩阵方法

```python
arr = np.array([[0,0,0],[0,0,1],[1,0,2]])
print(csr_matrix(arr).data) # 用数据属性查看存储的数据。

print(csr_matrix(arr).count_nonzero()) # 计算非零的数量
mat = csr_matrix(arr)
mat.eliminate_zeros() # 去除矩阵中的零条目
print(mat)

mat = csr_matrix(arr) 
mat.sum_duplicates() # 消除重复的条目
print(mat)

newarr = csr_matrix(arr).tocsc() # 从csr转换到csc
print(newarr)
```

# 图论








