
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

# randperm()随机函数

p = randperm(n)   返回一行包含从1到n的整数。

p = randperm(n,k) 返回一行从1到n的整数中的k个，而且这k个数也是不相同的。

# num2str()使用数值为绘图添加标签和标题

若想要画一个滤波器的不同反馈系数的频率响应曲线，在设置条例内容时，需要手动输入反馈系数K的数组用以标记不同曲线的含义，不利于调试更改K的系数，造成无谓的工作量。

![原增加标签和标题方法](https://s2.loli.net/2024/04/08/DivjnpT74o9Ghf6.png)

对于类似于上文中图例内容与数组变量有关的legend图例撰写，可使用写法 **legend(num2str(K))** 即可使 **图例随变量变化** ，不需要手动修改，方便且规范。

![num2str()](https://s2.loli.net/2024/04/08/W8mtKcxg6jHGadY.png)

# sub2ind()函数

对矩阵索引号检索的函数。索引就是把整个向量或矩阵重拍成一维向量后所处的位置。按列

```matlab
A=[17 24 1 8;2 22 7 14;4 6 13 20]

A =

    17    24     1     8
     2    22     7    14
     4     6    13    20

>> b=sub2ind(size(A),2,2)

b =

     5

>> sub2ind(size(A),1,1)

ans =

     1

>> sub2ind(size(A),3,3)

ans =

     9
```

可以发现 sub2ind是找到矩阵A对应位置的索引号

第一个参数size（A)是A的维数。

# find()函数

## 查找非零元素的索引和值

k = find(X,n) 返回与 X 中的非零元素对应的前 n 个索引。
[row,col] = find(___) 使用前面语法中的任何输入参数返回数组 X 中每个非零元素的行和列下标。

# newff函数

net = newff(minmax(p),[隐层的神经元的个数，输出层的神经元的个数],{隐层神经元的传输函数，输出层的传输函数｝,'反向传播的训练函数'),其中p为输入数据，t为输出数据

# minmax()函数

用于获取数组中每一行的最小值和最大值

# cell元胞数组

cell数组的每个单元可以储存不同的数据类型，可以是数值，字符或矩阵或元胞数组等，类似于学过的c语言里的结构体。

1. cell数组的创建

可以直接通过{}类似于矩阵的直接赋值：a={'winter',123,'coming','哈哈'};

也可以通过cell函数预分配内存，再赋值：a=cell(1,4);a={'winter',123,'coming','哈哈'};

2. cell数组的访问

与普通矩阵，数组的主要区别在于：通过（）访问cell数组时访问到的是cell单元,通过{}访问cell数组时访问到的是cell单元储存的内容，举例如下图所示，其他操作基本一样。

# waitbar()

打开或者更新进度条

matlab添加进度条（waitbar）

百分比形式显示处理进程

显示处理时间（tic与toc）

```matlab
tic;                                % tic;与toc;配合使用能够返回程序运行时间
bar = waitbar(0,'读取数据中...');    % waitbar显示进度条
A = randn(1000,1);                  % 随机生成1000行1列数据
len = length(A);                    % 读取A矩阵长度
for i = 1:len                       % 循环1000次
    B(i) = i^2;                     % 求平方，无意义，示例函数
    str=['计算中...',num2str(100*i/len),'%'];    % 百分比形式显示处理进程,不需要删掉这行代码就行
    waitbar(i/len,bar,str)                       % 更新进度条bar，配合bar使用
end
%close(bar)                % 循环结束可以关闭进度条，个人一般留着不关闭
toc;                       % tic;与toc;配合使用能够返回程序运行时间

```

# @是用于定义函数句柄的操作符

用法1：@(输入参数)表达式

场景：有时需要定义一个函数来计算某个表达式的值，可以直接用语法:@(输入参数)表达式，来创建一个匿名函数，返回该函数句柄。然后就可以用该句柄来计算了

用法2：既是一种变量，可以用于传参和赋值

比如说 变量名=@+一个函数名

f=@sin%那么它就可以传参，用法和sin函数一样，把它当作函数名使用

# 神经网络train( )函数使用说明

函数的语法格式如下：

[net, tr]=train(net, P, T, Pi, Ai)；

train( )函数用于训练创建好的感知器网络，事实上，train( )函数可以训练所有神经网络（径向基函数网络等不需要训练除外）。
输入参数：

 net：需要训练的神经网络，对于感知器，net是newp函数的输出。train根据net.trainFcn和net.trainParam进行训练。

 P：网络输入。P是R×Q的输入矩阵，每一列是一个输入向量；R为神经网络的输入节点个数，共有Q个训练输入向量。也即共有Q个训练样本，每个样本有R个特征。

 T：网络期望输出。该参数可选，对于无监督学习，不需要期望输出。T是S×Q的期望矩阵，每一列是一个输出向量，S是输出节点个数，共有Q个输出，Q值应与输入向量的个数相等。T的默认值为0。

 Pi：初始输入延迟，默认值为0。

 Ai：初始的层延迟，默认值为0。

输出参数

 net：训练好的神经网络。

 tr：训练记录，包括训练的步数epoch和性能perf。

对于没有输入延迟或层延迟的网络，Pi、Ai、Pf和Af参数是不需要的。

```matlab
net.performFcn= 'mse'; 
%性能评价指标，我觉得无伤大雅，按照默认的mse就行。除此之外，还有mae（平均绝对误差）、sae（绝对值和误差）、sse（平方和误差）、crossentropy（交叉熵）。
 net.trainParam.show=1;
%这个是负责图像刷新频率的，每多少个数据刷新一次。
 net.trainParam.lr=0.2;
%学习率，改不改无所谓，有些下降方法是自适应学习率的，改了也没用。
 net.trainParam.max_fail=100;
%当出现多少次下降失败时停止训练，这里是100次。
 net.trainParam.epochs=1e3;
%最大迭代次数
 net.trainParam.goal=0;
%当训练性能评价指标达到多少时停止训练
 net.trainParam.min_grad=0;
%当下降梯度达到多少时停止训练
```

# lqr和dlqr

### lqr线性二次调节器 (LQR) 设计

[K,S,P] = lqr(sys,Q,R,N)

计算连续时间或离散时间状态空间模型 sys 的最优增益矩阵 K、相关联代数黎卡提方程的解 S 以及闭环极点 P。Q 和 R 分别是状态和输入的权重矩阵。

### dlqr离散时间状态空间系统的线性二次 (LQ) 状态反馈调节器

[K,S,e] = dlqr(A,B,Q,R,N)

A为系统的状态矩阵；B为系统的输出矩阵；Q为给定的半正定实对称常数矩阵；R为给定的正定实对称常数矩阵；N代表更一般化性能指标中交叉乘积项的加权矩阵；K为最优反馈增益矩阵；S为对应Riccati方程的唯一正定解P（若矩阵A-BK是稳定矩阵，则总有正定解P存在）；E为矩阵A-BK的特征值。

# DARE

[LQR线性二次型调节器](https://blog.csdn.net/weixin_40857506/article/details/125684303)

![dare()函数使用](https://s2.loli.net/2024/04/08/lghrmiTxPatzBoC.png)

离散系统中，函数dare()和dlqr()计算的结果相等，K,P,r完全相同

公式求解的k和dlqr()函数、dare()函数求解的k结果相同

函数dare()和dlqr()都可以直接求解Riccati方程的解P、以及最优反馈增益矩阵K，且他们求解的P和dlyap()函数求解的P不同

# norm 范数

n = norm(v) 返回向量 v 的欧几里德范数。此范数也称为 2-范数、向量模或欧几里德长度。

n = norm(v,p) 返回广义向量 p 范数。

n = norm(X) 返回矩阵 X 的 2-范数或最大奇异值，该值近似于 max(svd(X))。

n = norm(X,p) 返回矩阵 X 的 p-范数，其中 p 为 1、2 或 Inf：

如果 p = 1，则 n 是矩阵的最大绝对列之和。

如果 p = 2，则 n 近似于 max(svd(X))。此值等效于 norm(X)。

如果 p = Inf，则 n 是矩阵的最大绝对行之和。

# ode45

求解非刚性微分方程 - 中阶方法

[t,y] = ode45(odefun,tspan,y0)

[t,y] = ode45(odefun,tspan,y0,options)

[t,y,te,ye,ie] = ode45(odefun,tspan,y0,options)

sol = ode45(___)

[t,y] = ode45(odefun,tspan,y0)（其中 tspan = [t0 tf]）求微分方程组 y′=f(t,y) 从 t0 到 tf 的积分，初始条件为 y0。解数组 y 中的每一行都与列向量 t 中返回的值相对应。

# nargin

针对当前正在执行的函数，返回函数调用中给定函数输入参数的数目。该语法仅可在函数体内使用

# pinv

使用伪逆求解器 pinv 是为了处理可能存在的奇异矩阵或病态问题。它能够找到最接近的解，即使矩阵不是满秩的或不可逆。

对于方阵A，如果为非奇异方阵，则存在逆矩阵inv(A)

对于奇异矩阵或者非方阵，并不存在逆矩阵，但可以使用pinv(A)求其伪逆

# kronecker乘积

![kron函数](https://s2.loli.net/2024/04/08/yPtwXcmsJrAU2Cd.png)

# int 函数

nt(F,x,a,b)。

a表示定积分的下限；

b表示定积分的上限；

上式表示，被积函数F在区间 [a,b]上的定积分。a 和 b 可以是两个具体的数，也可以是一个符号表达式，还可以是无穷inf(极限)。

当函数F关于变量x在闭区间[a,b]上可积时，函数返回一个定积分结果。当a,b中有一个是inf 时，函数返回一个广义积分；当a、b 中有一个有符号表达式时，函数返回一个符号函数。

# Matlab图片处理

[matlab在大图中加入小图_matlab怎么在大图中放小图-CSDN博客](https://blog.csdn.net/qq_36262908/article/details/128993776)

[Matlab 批量fig转png（并按原文件名保存）_matlab 批量将fig文件变成png-CSDN博客](https://blog.csdn.net/weixin_44170261/article/details/123332987)

[Matlab导出eps图片的两种方法-CSDN博客](https://blog.csdn.net/weixin_44145191/article/details/122310596)

[MATLAB作图时值为0的点不画出来_matlab plot 没有零点-CSDN博客](https://blog.csdn.net/m0_50305149/article/details/124691119)

[Matlab_求最大特征值和特征向量_matlab互反矩阵最大特征值-CSDN博客](https://blog.csdn.net/qq_32095939/article/details/77714130)

[matlab添加进度条（waitbar）（百分比显示处理进程以及处理时间）_matlab 进度条-CSDN博客](https://blog.csdn.net/weixin_43465015/article/details/89294079)

[plot](https://ww2.mathworks.cn/help/matlab/ref/plot.html?searchHighlight=plot&s_tid=srchtitle_support_results_1_plot)
