
# 载入matlab的数据

读取matlab数据文件，我们需要使用Python中的科学计算库numpy和读取mat文件的库scipy.io。首先需要安装这两个库，可以使用pip命令进行安装。

```bash
pip install numpy scipy
```

读取matlab文件时我们需要知道文件所在的完整路径，以及文件中所包含的数据的变量名称

```python
import scipy.io as sio

mat_file = sio.loadmat('example.mat') # 加载数据文件
data = mat_file['data'] # 获得名为'data'的变量
```

其中sio.loadmat()函数将mat文件中的数据以python字典的形式存储到了变量mat_file中，我们可以通过字典的键值对方式获取到文件中的数据。我们使用mat_file['data']获取到了名为'data'的变量

## 数据可视化

```python
import matplotlib.pyplot as plt

plt.plot(data)
plt.show()
```

## 读取mat文件中的向量并绘制在坐标系

```python
import numpy as np
import scipy.io as sio
import matplotlib.pyplot as plt

data = sio.loadmat('example.mat')
x = data['x'] # 获得名为'x'的变量

fig = plt.figure()
ax = fig.add_subplot(1,1,1)
ax.plot(x)
ax.set_xlabel("Samples")
ax.set_ylabel("Amplitude")
ax.set_title("Signal Curve")
plt.show()
```

通过loadmat函数将文件example.mat中的请求载入，并赋值给data变量。data变量是一个Python字典，其中包含了所有在Matlab文件中的变量名及其对应的值。我们可以使用data['x']语句获取名为'x'的变量，进而绘制出对应的图像。

figure函数新建一个画布，并使用add_subplot函数添加一个子图。然后使用ax.plot(x)函数绘制x中的数据，ax.set_xlabel/set_ylabel/set_title分别设置x轴、y轴以及图表标题等属性，最后使用plt.show()展示图象。

## 举例代码

```python
import matplotlib.pyplot as plt
import numpy as np
import scipy.io
plt.rcParams['axes.unicode_minus'] = False #显示负号


data_snr = scipy.io.loadmat(r'C:\Users\Administrator\Desktop\mat1\angle1.mat')  # 横坐标数据，读取mat文件
data_RMSE1 = scipy.io.loadmat(r'C:\Users\Administrator\Desktop\mat1\Pmusic1.mat')  # 纵坐标数据一，读取mat文件
data_RMSE2 = scipy.io.loadmat(r'C:\Users\Administrator\Desktop\mat1\Pmusic2.mat')  # 纵坐标数据二，读取mat文件
data_RMSE3 = scipy.io.loadmat(r'C:\Users\Administrator\Desktop\mat1\Pmusic3.mat')  # 纵坐标数据二，读取mat文件
#print(data_snr.keys())   # 查看mat文件中的所有变量
snr=data_snr['angle1']
print(snr)
R1list=data_RMSE1['Pmusic1'].tolist()
R2list=data_RMSE2['Pmusic2'].tolist()
R3list=data_RMSE3['Pmusic3'].tolist()
snrlist = snr.tolist()
snrlist = snrlist[0]

print(snrlist[-1]) #横坐标最右值
d=30 #横坐标间隔

list1 = R1list[0]
list2 = R2list[0]
list3 = R3list[0]

plt.rcParams['font.sans-serif'] = ['SimHei']  # 用来正常显示中文标签
plt.xlabel("Angle of incidence/(degree)")
plt.ylabel("Spatial spectrum/(dB)")
x = snrlist
#x[0] = -90
my_x_ticks = np.arange(-90, snrlist[-1]+1, d)
plt.xticks(my_x_ticks)
# 添加label设置图例名称
plt.plot(x, list1, label='Basic_MUSIC',color="k", linestyle='--',linewidth=1)  
plt.plot(x, list2, label='TOP_Denoise',color="k", linestyle='-',linewidth=0.6)  
plt.plot(x, list3, label='TOP_Iter',color="k", linestyle='-.',linewidth=0.6) 
plt.legend()
plt.show()
```

# Axes3D函数绘制三维图形

```python
import numpy as np
import matplotlib.pyplot as plt
from mpl_toolkits.mplot3d import Axes3D    #这里只是用Axes3D函数，所以只导入了Axes3D
np.seterr(divide='ignore',invalid='ignore')

# 3D图
plt.rcParams['axes.unicode_minus']=False
# 创建窗口
fig=plt.figure()
# 在该窗口中创建3d绘图对象
ax=Axes3D(fig)
fig.add_axes(ax)
# 创建点的x和y坐标数组
x=np.arange(-3,3,0.2)
y=np.arange(-3,3,0.2)
# 网格化处理
x,y=np.meshgrid(x,y)
# 计算某个点到原点的距离为半径
r=np.sqrt(x**2+y**2)
# 以半径为基准，求它的正弦值为点的z轴坐标
z=np.sin(r)
# 绘制图像
ax.plot_surface(x,y,z,rstride=3,cstride=1,cmap="hot")

# 底部的投影
ax.contour(x,y,z, zdir = 'z', offset = -1, cmap = plt.get_cmap('rainbow'))
# 设置z轴的维度，x,y类似
ax.set_zlim(-2, 2)
plt.show()
```

# python调用matlab

## 安装

查询matlab支持的python版本：到网址 https://www.mathworks.com/help/releases/R2021b/matlab/matlab_external/system-requirements-for-matlab-engine-for-python.html

MATLAB中用命令 matlabroot查询 matlab 根目录。

```bash
%如果使用的是python虚拟环境，在Python虚拟环境中执行
cd "E:\Matlab\R2021b\extern\engines\python" %切换到根目录下的文件夹中
python setup.py install                                   %需要管理员权限
```

## 问题

https://blog.csdn.net/weixin_46115959/article/details/129961443

Python中安装matlab.engine出现UserWarning: The version specified (‘Rxxxx‘) is an invalid version...的解决方案

原因是setuptools版本过高。setuptools在58.0版本以后弃用了一些方法，导致matlab.engine中的一些命令无法运行。在cmd中输入如下命令将setuptools降版本至58.0版本即可

```bash
pip install setuptools==58.0

python setup.py install 
```

## 使用

将Python参数传递给 Matlab 需要将参数 变为double 计算回来的也是double

```python
import matlab.engine    #导入包

eng = matlab.engine.start_matlab()   #启动MATLAB，这个需要一些时间
a = matlab.double([1,4,9,16,25])  #MATLAB只能调用double型的数据
traindata.tolist()			#如果数据是numpy,则需要讲数据转化为list才能转化为double
# 调用matlab自带函数
b = eng.sqrt(a)
print(b)
# 调用用户自己的函数
c=eng.myfunc(a,b)    #自己的函数需要编写在当前目录下，或者是在matlab目录中
# stop
eng.quit()

V,theta1,W = eng.test(nargout=3)  #调用返回值是多个，需要指定参数nargout个数。
```

# python scipy中的odeint函数求解常微分方程

## 函数及其参数

scipy.integrate.odeint(func, y0, t)

参数

1. func 自定义求解的微分方程函数
2. y0 代表求解微分方程的初值
3. t 求解微分方程中的自变量，一个连续的序列值

转载学习
https://blog.csdn.net/qq_38981614/article/details/113915904?utm_source=csdn_ai_ada_ask_robot

### 一阶微分方程

```python
initial = 100				# 定义原方程初始值
def f1(y,x):
    return 12*x**2
x = np.linspace(0,10,100) 	#定义自变量
result1 = odeint(f1,initial,x) #调用求解
plt.plot(x,result1)				#求解值
plt.plot(x,4*x**3+initial,'--k') #真实值
```

### 二阶微分方程

```python
initial = 100	#原初值
derivative1 = 0 #一阶初值
def f2(y,x):
    y1,w = y
    return np.array([w,24*x]) #返回值列阵 先一阶 再 二阶 由低到高
x = np.linspace(0,10,100)		#定义自变量
result2 = odeint(f2,(initial,derivative1),x)  #初值对应 先原方程 再 一阶 由低到高
plt.plot(x,result2)
plt.plot(x,4*x**3+initial,'--k')
```

# python读取文件路径

常见的报错问题来自文件路径错误，即未打开或者获取到某个文件内容。

## 处理方法

1. 检查文件路径是否正确，是否包含中文（未实践是否存在影响）
2. 更改用绝对路径或相对路径
3. 如果文件路径对不上，则需要将其修改和复制文件

# pyhon的matplotlib绘图问题

## 出现x轴和y轴上出现方框

[关于Python数据可视化中文部分显示方块的解决方法_如果可视化中需要用到中文,则在显示中文的时候中文呈方块状显示,思考如何解决这一-CSDN博客](https://blog.csdn.net/qq_36556893/article/details/90145177)
