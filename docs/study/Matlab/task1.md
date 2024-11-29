
# Matlab学习记录

## 1 读取：通过循环方式一次性读取所有csv文件（注：不能逐个输入文件名）

1. pathname变量为文件绝对路径
2. filenames变量获取所有csv文件名，f = fullfile(filepart1,filepart2,…,filepartN)
   输出：将各个输入用"\"拼接起来。
3. dir()函数用于获得指定文件夹下的所有子文件夹和文件，并存放在一种文件结构体数组中。

* name	文件名
* folder	文件夹
* date	修改日期 （格式：日-月-年 时-分-秒）
* bytes	文件大小（单位：字节）
* isdir	目录标识符，1-目录，0-文件
* datenum	MATLAB中特定的修改日期

1. length()函数返回 X 中最大数组维度的长度，这里用来获取文件的数量
2. cell()函数创建一个元胞数组，类似于C语言中的结构体。每个单元可以储存不同的数据类型，可以是数值，字符或矩阵或元胞数组等。具体效果可查看matlab的工作区变量值
3. for循环中同样用fullfile()函数，将csv文件名拼接起来，存储在变量csvname中。
4. 读取csv中数据

* readmatrix(filename) 通过从文件中读取列向数据来创建数组。将对应的文件个数(本程序中有8个文件)，分别存储在元胞数组中
* importdata(filename)函数可直接将将数据加载到数组 A
* readable('xxx.xlsx','Sheet','1996','Range','A1:E11')或readable('xxx.csv','Sheet','1996','Range','A1:E11')
  将电子表格数据读入到表中，需要结合 A = table2array(T) 将表 T 转换为同构数组 A，将数值数据的表转换为数组
* csvread(filename)

  注意：csvread函数只适用与用逗号分隔的纯数字文件
  第一种：M = CSVREAD(‘FILENAME’) ，直接读取csv文件的数据，并返回给M
  第二种：M = CSVREAD(‘FILENAME’,R,C) ，读取csv文件中从第R-1行，第C-1列的数据开始的数据，这对带有头文件说明的csv文件(如示波器等采集		的文件)的读取是很重要的。
  第三种：M = CSVREAD(‘FILENAME’,R,C,RNG)，其中 RNG = [R1 C1 R2 C2]，读取左上角为索引为(R1,C1) ,右下角索引为(R2,C2)的矩阵中的数据。
  注意：matlab认为CSV第1行第1列的单元格坐标为（0,0）

## 2 FFT变换：一个数据的FFT，获得数据的频谱信息（包括幅值谱、功率谱、相位谱）

### 幅频图

1. 定义信号的采样周期、时间、采样点数、采样频率等信息。
2. fft()函数，返回矩阵或向量的傅里叶变换。
3. subplot(m,n,p)函数。m表示是图排成m行，n表示图排成n列。如代码中subplot(5,1,1)，一共5行，1张图，从左到右从上到下的第一个位置。
4. 绘制原信号图和傅里叶变换之后的图，注意需要对其取绝对值，fft后的结果是复数，取复数的绝对值，实部或幅角实数进行作图。
5. Y = fftshift(X) 通过将零频分量移动到数组中心，重新排列傅里叶变换 X。见图可以理解
6. 第四张图将保留频率为正的部分，第五章截取结果的一部分（这两张图片不是终点）

见官方文档

```matlab
first_col = f1(:,1); % 取第一列数据
second_col = f2(:,1); % 取第二列数据
% x = table2array(first_col); % 表转化为数值
% y = table2array(second_col);
x = first_col; % 表转化为数值
y = second_col;
N = 250; % 采样点数
t_s = 0.0000000004; %采样间隔
f_s = 1/t_s; %采样频率
data_length = size(x,1);%获取数据长度（行数）   
L = data_length;      % 信号数据个数
t = (0:L-1)*t_s;        % 时间向量

% 原始信号图
subplot(4,1,1);
plot(x,y);title('原始信号图');xlabel('time (s)');  

%  幅频图
x_f = fft(x);  % 傅里叶变换
subplot(4,1,2);
plot(f_s/L*(0:L-1),abs(x_f),'LineWidth',1);
title('傅里叶变换幅频图');
xlabel('f (Hz)');
ylabel('|fft(x)|');
subplot(4,1,3);
plot(f_s/L*(-L/2:L/2-1),abs(fftshift(x_f)),'LineWidth',1);
title('傅里叶变换双边频谱');
xlabel('f (Hz)');
ylabel('|fft(x)|');
```

### 相频图

theta = angle(z) 为复数数组 z 的每个元素返回区间 [-π,π] 中的相位角

```matlab
phase=angle(x_f);
plot(f_s*linspace(0,1,length(x_f)), phase);
title('傅里叶变换相位谱图');
xlabel('f (Hz)');
ylabel('angle(x)');
```

### 功率谱图

1. 直接法，直接将信号的采样数据x(n)进行Fourier变换求取功率谱密度估计。

- boxcar(N)，N个长度大学的矩形窗。
- [Pxx,f] = periodogram(x,window,nfft,fs)函数用来计算功率谱密度，参数X：所求功率谱密度的信号；window：所使用的窗口，默认是boxcar，其长度必须与x的长度一致；nfft：采样点数；fs：采样频率。

2. 间接法，求功率谱(先估计出自相关函数，再傅里叶变化求功率谱)

- r = xcorr(x) 返回 x 的自相关序列，描述了两个信号之间的相似性。

  - r = xcorr(___,maxlag) 范围限制为从 -maxlag 到 maxlag。
  - r = xcorr(___,scaleopt) 为互相关或自相关指定归一化项。

![归一化项](https://s2.loli.net/2024/05/11/Ou4EoAUm9bwiIRg.png)

- round(X) 将 X 的每个元素四舍五入为最近的整数。
- 绘制功率谱图，10*log10就是用db做单位。

## 3 滤波：对原始波形进行低通滤波消除高频毛刺，给出滤波器响应（幅频，相频），理解滤波器阶数的作用，通过程序理解filtfilt与filter函数的区别、理解窗函数的作用、理解不同种类滤波器的区别

1. 给定原始信号信及其他信息，绘制傅里叶变换后频谱图
2. filter函数为一维的数字滤波器。移动平均滤波器用于对含噪数据进行平滑处理

- 移动平均值滤波器沿数据移动长度为windowSize的窗口，并计算每个窗口中包含的数据的平均值。差分方程定义向量 x 的移动平均值滤波器：

![移动平均值滤波器](https://s2.loli.net/2024/05/11/xfWYmtdP12lOFUC.png)

y = filter(b,a,x)，计算有理传递函数的分子系数b和分母系数a

3. 滤波器的阶数，就是指过滤谐波的次数，一般来讲，同样的滤波器，其阶数越高，滤波效果就越好，但是，阶数越高，成本也就越高。

滤波器的阶数是指在滤波器的传递函数中有几个极点。阶数同时也决定了转折区的下降速度，一般每增加一阶(一个极点)，就会增加一20dBDec(一20dB每十倍频程)。 滤波器特性可以用其频率响应来描述，按其特性的不同，

## 4 曲线拟合：对原始波形进行曲线拟合，拟合得到类似雷电的双指数波形

由于提供文件中未知有类似雷电的双指数波形。本例子数组为自定义的数据。

### fittype函数的具体表达形式

```matlab
- f=fittype('公式具体表达','independent','自变量名','coefficients',{'待定参数1','待定参数2'});

- [cfun,函数输出设置]=fit(x,y,f,'函数输入设置1',输入设置1具体定义,'函数输入设置2',输入设置2具体定义,...,'函数输入设置n',输入设置n具体定义)
```

双指数函数的拟合模型

```matlab
f = fittype('a1*exp(b1*x) - a2*exp(b2*x) ');
```

例子

```matlab
close all;
clear;
clc;
% 准备数据
f1=csvread('xk1-20-daofang-007_Ch1.csv',0,3,[0 3 999 3]);        %将.csv文件中的指定数据存入F1
f2=csvread('xk1-20-daofang-007_Ch1.csv',0,4,[0 4 999 4]);         %将.csv文件中的指定数据存入F2
first_col = f1(:,1);
second_col = f2(:,1);
x = first_col;
y = second_col;
% 构建模型
myfittype = fittype("a1*exp(b1*x) + a2*exp(b2*x) ",...
    coefficients=["a1" "b1" "a2" "b2"]);
% 拟合数据的初始点
start_point = [-0.0000000000372528952, -0.0000001,-0.00160000015,-0.0000000984];
% 拟合参数选项
fit_options = fitoptions('Method', 'NonlinearLeastSquares', 'StartPoint', start_point); 
% 进行拟合
fit_result = fit(x, y, myfittype, fit_options); 
% 拟合结果
coefficients = coeffvalues(fit_result);
a1 = coefficients(1);
b1 = coefficients(2);
a2 = coefficients(3);
b2 = coefficients(4);
disp(fit_result);
% 绘制拟合曲线
x_fit = linspace(min(x),max(x),100);
% y_fit = a1*exp(b1*x_fit) + a2*exp(b2*x_fit);
y_fit = fit_result(x_fit);
plot(x,y,'.',x_fit,y_fit);
legend('原始数据','拟合数据');
% 保存数据和加载数据
save('Seventh.mat');
load('Seventh.mat')
```

雷电波形函数图

![雷电波形函数图](https://s2.loli.net/2024/05/11/Csb5LrzoIDq1hUg.png)

### save和load的使用

（1）将m文件中生成的所有变量保存到当前工作目录
在example.m文件中输入：
save example 或 save("example.mat");
所有数据即保存到example.mat文件中

（2）将m文件中指定变量保存到当前目录
如果要保存example.m文件中的变量x、y、z
在example.m文件中输入：
save example x y z
x、y、z数据即保存到example.mat文件中

（3）将m文件中所有变量保存到指定文件夹中
在example.m文件中输入：
save(‘D:\\example.mat’)

（4）将m文件中指定变量保存到指定文件夹中
如果要保存example.m文件中的变量x到D:\\example.mat中
在example.m文件中输入：
save(‘D:\\example.mat’, ‘x’)

## 5 插值与重采样：理解interp与resample函数，对数据分别进行插值与重采样处理

### resample函数

resample函数用于重新采样信号或数据序列。它可以改变信号的采样率，从而改变信号的持续时间或时间分辨率。

### interp函数

1. interp1()，一维数据内插值

- yi= interp1(x,y,xi,'method')

其中x，y为插值点，yi为在被插值点xi处的插值结果；x,y为向量

'method'表示采用的插值方法，MATLAB提供的插值方法有几种：

- 'nearest'是最邻近插值;
- 'linear'线性插值;
- 'spline'三次样条插值;
- 'pchip'立方插值;
- 缺省时表示线性插值

2. interp2()，二维数据内插值

```matlab
ZI = interp2(X,Y,Z,XI,YI,method)
返回矩阵ZI，其元素包含对应于参量XI 与YI（可以是向量、或同型矩阵） 的元素
```

- meshgrid函数是用来生成网格的，

```matlab
[X,Y] = meshgrid(x,y)
```

- Peaks函数表达式

![Peaks函数](https://s2.loli.net/2024/05/11/Xv9Em3WByxbdi6h.png)

- surfl(X,Y,Z) 创建一个带光源高光的三维曲面图

  该函数将矩阵 Z 中的值绘制为由 X 和 Y 定义的 x-y 平面中的网格上方的高度
- shading函数是阴影函数控制曲面和图形对象的颜色着色，即用来处理色彩效果的，包括以下三种形式:

  - shading faceted：默认模式，在曲面或图形对象上叠加黑色的网格线；
  - shading flat：是在shading faceted的基础上去掉图上的网格线；
  - shading interp：对曲面或图形对象的颜色着色进行色彩的插值处理，使色彩平滑过渡

## 6 双Y轴画图：在一张图上分别呈现Ch1和Ch4波形，只要一组就可以。理解xlim，axis，xlabel，semilog，loglog，legend，通过get和set设置横纵坐标ticket。

1. plotyy（X1,Y1,X2,Y2）
   以左、右不同纵轴绘制X1-Y1、X2-Y2两条曲线。
2. plotyy（X1,Y1,X2,Y2,FUN1）
   以左、右不同纵轴把X1-Y1、X2-Y2两条曲线绘制成FUN1指定形式的两条曲线。
3. plotyy（X1,Y1,X2,Y2,FUN1，FUN2）
   以左、右不同纵轴把X1-Y1、X2-Y2两条曲线绘制成FUN1、FUN2指定的不同形式的两条曲线。其中fun的绘制方式包括plot，semilogx，semilogy，loglog等。

   - plot(X,Y) 在 x 轴和 y 轴上应用线性刻度
   - semilogy(X,Y) 在 x 轴上使用线性刻度、在 y 轴上使用以 10 为底的对数刻度来绘制 x 和 y 坐标
   - loglog(X,Y) 在 x 轴和 y 轴上应用以 10 为底的对数刻度来绘制 x 和 y 坐标
4. set设置图形对象属性。系统默认的坐标轴范围以及间隔有时候并不是很合适，使用set语句可以设置。
5. get(h) 在命令行窗口中显示指定的图形对象 h 的属性和属性值。
6. xlim和ylim更改 x 轴线和 y 轴线的显示位置
7. legend()函数为每个绘制的数据序列创建一个带有描述性标签的图例。

   - legend(label1,...,labelN) 设置图例标签。以字符向量或字符串列表形式指定标签
8. xlabel(txt) 对当前坐标区或独立可视化的 x 轴加标签
9. axis——设置坐标轴

   - axis( [xmin xmax ymin ymax] ) 设置当前二维图形对象的 x 轴 和 y 轴的取值范围。向量参数[xmin xmax ymin ymax] 中的元素分别表示 x 轴最小值、x 轴最大值、y 轴最小值和 y 轴最大值。（主要）
   - 其他：axis off 去掉坐标轴；axis on 显示坐标轴；axis square 坐标轴呈方形；axis equal 设置屏幕高宽比，使得每个坐标轴的具有均匀的刻度间隔

```matlab
close all;
clear;
clc;
% 准备数据
f1=csvread('pg1-20-daofang-60kV-002_Ch3.csv',0,3,[0 3 999 3]);        %将.csv文件中的指定数据存入F1
f2=csvread('pg1-20-daofang-60kV-002_Ch1.csv',0,4,[0 4 999 4]);         %将.csv文件中的指定数据存入F2
f3=csvread('pg1-20-daofang-60kV-002_Ch3.csv',0,4,[0 4 999 4]);         %将.csv文件中的指定数据存入F2
x = f1(:,1);
y1 = f2(:,1);
y2 = f3(:,1);
x_s = x;
y1_s = y1;
y2_s = y2;
t_s = 0.0000000004; %采样间隔
f_s = 1/t_s; %采样频率
% [AX,H1,H2] = plotyy(x_s,y1_s,x_s,y2_s,'plot'); % 按FUN1指定形式的两条曲线
[AX,H1,H2] = plotyy(x_s,y1_s,x_s,y2_s,'semilogx');
% [AX,H1,H2] = plotyy(x_s,y1_s,x_s,y2_s,'loglog');
% xlim([0 10]);
% 信号颜色
set(AX(1),'XColor','k','YColor','b');
set(AX(2),'XColor','k','YColor','r');
% 左右两轴y轴
HH1=get(AX(1),'Ylabel');
set(HH1,'String','Left Y-axis');
set(HH1,'color','b');
HH2=get(AX(2),'Ylabel');
set(HH2,'String','Right Y-axis');
set(HH2,'color','r');
% 信号轨迹性质
set(H1,'LineStyle','-.');
set(H1,'color','b');
set(H2,'LineStyle',':');
set(H2,'color','r');
legend([H1,H2],{'ch1';'ch3'});
xlabel('time(s)');
% axis([0 5 -100 100 ]) % 
title('Labeling plotyy'); % 图像的标题
```
