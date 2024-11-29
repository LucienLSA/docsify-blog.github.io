---
title: 事件触发控制
katex: true
categories: 
- ADP
tags:
- 论文笔记
- 仿真
- algorithm
- 事件触发
---

## 事件触发控制学习
$\dot x=Ax+Bu$
假设采取控制策略

$u = Kx(t_k)，t_k\leq t \leq t_{k+1}$

$t_k$时刻采样系统状态x(t_k)，并计算控制信号u，实施控制信号直到下一次采样控制

目标是确定何时需要执行下一次采样和控制，保持系统稳定，A-BK必须稳定

假设采用连续控制系统模式，$u=Kx$，李雅普诺夫函数可表达为(P、Q为对称矩阵)

$\dot x = (A+BK)x=A_cx$

$V(x)=x^{T}Px, A_cP+PA_c^{T}=-Q$
对其时间进行求导
$$\frac{d\big(V(x)\big)}{dt}=\frac{d\big(V(x)\big)}{dx}\frac{dx}{dt}=x^T(A_cP+PA_c^T)x=-x^TQx$$

k和k+1采样时刻之间，实际的控制策略为

$u=Kx(t_k)=K(x(t)+e),$

实际系统动态方程为

$\dot{x}=(A+BK)x+BKe$

实际对以上的李雅普诺夫函数求导

$$\frac{d\left(V(x)\right)}{dt}=\frac{d\left(V(x)\right)}{dx}\frac{dx}{dt}=-x^TQx+2x^TPBKe$$

在采样时刻tk时，可以获得准确地系统状态x(tk)，并计算获得对应的李雅普诺夫函数的值V(x(tk))。如果在接下来直到下一次采样时刻tk+1的时间里，实际的李雅普诺夫函数对时间的导数均小于连续控制的李雅普诺夫函数对时间的导数，由于连续控制的系统稳定，即李雅普诺夫函数的值收敛到零，则实际的李雅普诺夫函数的值也收敛到零（且比连续控制的李雅普诺夫函数收敛的更快）。



## 例子
```matlab
clc;        clear;      close all;
%%%%%%%%%%%%%%%%%%%%%%%%%%%
%%%  event-triggered control
%%% Event-Triggered Real-Time Scheduling of Stabilizing Control Tasks
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%

%% system define
A = [0 1; -2 3;];
B = [0; 1];
K = [1 -4];
P = [1 0.25; 0.25 1 ];
Q = [0.5 0.25; 0.25 1.5];
x0 = [0.5 0.5]';


%% event triggered controller
sigma = 0.05;

%% simulation
x = x0;         xBuf = [];
u = K*x0;    uBuf = [];
dt = 0.001;
Ad = expm(A*dt);
syms s
Bd = int(expm(A*s),0,dt)*B;
Bd = eval(Bd);
V = 0;          vBuf = []

for step = 1:10000
    e = x-x0;
    if norm(e) >= sigma*norm(x)
        x0 = x;
        u = K*x0;
    end
    x = Ad*x + Bd*u;
    
    V = norm(e);
    
    vBuf = [vBuf V];
    xBuf = [xBuf x];
    uBuf = [uBuf u];
end

%% plot
figure(1)
hold on
plot(xBuf');
plot(uBuf)
figure(2)
plot(vBuf)
```

```python
import numpy as np
import matplotlib.pyplot as plt
from scipy.linalg import expm

# system define
A = np.array([[0, 1], [-2, 3]])
B = np.array([[0], [1]])
K = np.array([[1, -4]])
P = np.array([[1, 0.25], [0.25, 1]])
Q = np.array([[0.5, 0.25], [0.25, 1.5]])
x0 = np.array([[0.5], [0.5]])


# event triggered controller
sigma = 0.1

# simulation
x = x0
xBuf = []
u = np.dot(K, x0)
uBuf = []
dt = 0.001
Ad = expm(A * dt)
Bd = np.dot(np.linalg.inv(A), (Ad - np.eye(2))).dot(B)
V = 0
vBuf = []

for step in range(10000):
    e = x - x0
    if np.linalg.norm(e) >= sigma * np.linalg.norm(x):
        x0 = x
        u = np.dot(K, x0)
    x = np.dot(Ad, x) + np.dot(Bd, u)

    V = np.linalg.norm(e)

    vBuf.append(V)
    xBuf.append(x)
    uBuf.append(u)

# plot
xBuf = np.array(xBuf).squeeze().T
uBuf = np.array(uBuf).squeeze().T
vBuf = np.array(vBuf).squeeze()

plt.figure(1)
plt.plot(xBuf)
plt.plot(uBuf)
plt.figure(2)
plt.plot(vBuf)
plt.show()
```