---
title: Discrete-Time Nonlinear HJB Solution Using Approximate Dynamic Programming Convergence Proof
katex: true
categories: 
- ADP
tags:
- 论文笔记
- 仿真
- algorithm
---
# Discrete-Time Nonlinear HJB Solution Using Approximate Dynamic Programming: Convergence Proof ，2008, Asma Al-Tamimi; Frank L. Lewis; Murad Abu-Khalaf  IEEE Transactions on Systems

## 主要研究内容 
对离散非线性系统的整定问题，给出基于值迭代的HDP算法和收敛性证明，HDP收敛于最优控制和最优值函数，求解无限时域离散时间非线性系统中最优控制出现的HJB方程。给出值和动作的更新方程。评价网络近似值函数，动作网络近似最优控制，模型网络逼近实际系统。迭代值函数单调不减、有界，收敛到最优值。HDP算法实现不需要系统的内部动力学。对LQR问题，动作为线性的，值函数为二次型，神经网络没有近似误差。以往文章种仅有一个评价网络。

给出动态规划（DP）求解DARE是向后计算的，而HDP求解是向前计算的。HDP算法作为VI算法，不需要初始的稳定增益。

![动态规划（DP）求解DARE](https://s2.loli.net/2024/04/02/ZsfdC2X8EBaNg67.png)

![HDP增量优化](https://s2.loli.net/2024/04/02/sIuqwoG7ChX3SYJ.png)

Lemma1给出任意控制序列下，辅助值函数与迭代值函数的不等式

Lemma2系统稳定时，值函数存在上界；若最优控制可求解时

$$V^{*}(x_k)<Y(x_k)$$
$$0≤V_i(x_k)≤V^{*}(x_k)≤Y(x_k)$$


![HDP结构图](https://s2.loli.net/2024/04/03/DHYyrNxcvzw4IP2.png)

Theorem1给出迭代值函数序列是单调不减，当迭代次数趋于无穷时，值函数收敛到最优，迭代控制收敛到最优。

给出激活函数的条件，使得初始值函数满足条件。调整评价网络权重，根据近似值函数和状态二次型构造残差。采用加权残差法求解最小二乘解。

![残差](https://s2.loli.net/2024/04/03/PmBf5aZp8y4URS6.png)

HDP 算法流程框图

![HDP 算法流程框图](https://s2.loli.net/2024/04/03/tmya7dSJMQNbuwZ.png)

给出评价网络权重更新和动作网络权重更新，由于动作网络权重隐式，难以求解，故根据随机逼近得到。
$f(x_k)$不需要，即内部动力学不需要。但需要输入耦合项g(x)。

![随机逼近更新评价网络权重](https://s2.loli.net/2024/04/03/cQABhWJYbG6z3FC.png)

Assumption1给出PE条件

仿真：基于值迭代 HDP算法。LQR，在未知系统矩阵A下，使用两个神经网络实现HDP算法。

## matlab实现(linear system)
### 神经网络工具箱
生成训练的数据
```matlab
%-------------------------- generate training data ------------------------
clear; close all; clc;


A = [0,0.1; 0.3, -1];
B = [0;0.5]; % 定义一个状态转移矩阵 A、控制输入矩阵 B
Q = 1*eye(2); R = 1; % 状态权重矩阵 Q 和控制输入权重 R
control_dim = 1;
state_dim = 2;
x0 = [1;-1];
% 这些矩阵用于定义系统的动力学和性能指标。

x_train = zeros(state_dim,1)
u_train = zeros(control_dim,1); % 设定初始的状态和控制矩阵，用于存放训练的数据

for i = 1:50 %循环用于生成训练数据。
    x_train = [x_train, zeros(state_dim,1)];
    x_train = [x_train,4*(rand(state_dim,1)-0.5)];   % [-2 2]
    x_train = [x_train,2*(rand(state_dim,1)-0.5)];   % [-1 1]
    x_train = [x_train,1*(rand(state_dim,1)-0.5)];   % [-0.5 0.5]
    x_train = [x_train,0.2*(rand(state_dim,1)-0.5)]; % [-0.1 0.1]
    u_train = [u_train,zeros(control_dim,1)];
    u_train = [u_train,4*(rand(control_dim,1)-0.5)];   % [-2 2]
    u_train = [u_train,2*(rand(control_dim,1)-0.5)];   % [-1 1]
    u_train = [u_train,1*(rand(control_dim,1)-0.5)];   % [-0.5 0.5]
    u_train = [u_train,0.2*(rand(control_dim,1)-0.5)]; % [-0.1 0.1]
end
% 将一些随机生成的状态和控制输入添加到 x_train 和 u_train 中。
% 生成的随机数是在一定范围内均匀分布的。

r = randperm(size(x_train,2));   % 随机选取状态数据的某列
x_train = x_train(:,r); % 得到随机的两行训练数据,x1和x2      
[~,n] = find(x_train == zeros(state_dim,1)); % 返回训练数据中为0元素的索引

zeros_index = [];
for i = 1:size(n,1)/state_dim
    zeros_index = [zeros_index, n(state_dim*i)];
end
% 对 x_train 进行随机重排，以打乱数据的顺序。
% 同时，记录了 x_train 中等于零的状态的索引，并保存在 zeros_index 变量中。

save training_data/state_data x_train zeros_index x0 control_dim state_dim  A B Q R
% 生成的训练数据 x_train、零状态索引 zeros_index、初始状态 x0、控制输入维度 control_dim、状态维度 state_dim、
% 系统动力学矩阵 A、控制输入矩阵 B、状态权重矩阵 Q 和控制输入权重矩阵 R 保存。
```

ADP中的值迭代算法(VI_Algorithm)
```matlab
function vi_algorithm
% This demo checks the feasibility of value iteration adaptive dynamic programming 

%-------------------------------- start -----------------------------------
clear; close all; clc;

% information of system & cost function
global A; 
global B;
global Q; global R;

load training_data/state_data.mat

% action network
actor_middle_num = 8;
actor_epoch = 20000;
actor_err_goal = 1e-7;
actor_lr = 0.05;
actor = newff(minmax(x_train), [actor_middle_num control_dim], {'tansig' 'purelin'},'trainlm'); 
% 构造动作网络，输入数据为训练数据每一行的最小值和最大值，隐藏层的神经元个数为8，输出层神经元个数为1
actor.trainParam.epochs = actor_epoch; % max epochs
actor.trainParam.goal = actor_err_goal; % tolerance error 训练精度
actor.trainParam.show = 10;  % interval 显示训练迭代过程
actor.trainParam.lr = actor_lr; % learning rate - traingd,traingdm
actor.biasConnect = [1;0];% bias 

% critic network
critic_middle_num = 8;
critic_epoch = 10000;
critic_err_goal = 1e-7;
critic_lr = 0.01;
critic = newff(minmax(x_train), [critic_middle_num 1], {'tansig' 'purelin'},'trainlm');
% 构造动作网络，输入数据为训练数据每一行的最小值和最大值，隐藏层的神经元个数为8，输出层神经元个数为1
critic.trainParam.epochs = critic_epoch;
critic.trainParam.goal = critic_err_goal; 
critic.trainParam.show = 10;  
critic.trainParam.lr = critic_lr; 
critic.biasConnect = [1;0];
critic_last = critic;

epoch = 50; % Actor和Critic共有的迭代次数
tol = 1e-9; % 容许误差
performance_index = zeros(1,epoch + 1); %性能指标迭代的下标

critic_set = cell(1,epoch); %评价网络集合，一维，预留有50个存储单元
actor_set = cell(1,epoch);

figure(1),plot(0,0,'*') % 初始0点坐标
set(gca,'FontName','Times New Roman','FontSize',14,'linewidth',1); % 设置坐标轴的属性
grid on;
hold on;
h = waitbar(0,'please wait');

for i = 1:epoch
    % update action network
    % 根据当前状态和控制策略，通过最小化目标函数来计算新的控制策略。
    actor_target = zeros(1,size(x_train,2)); 
    if i ~= 1 % 第一次评价网络更新后，动作网络更新
        for j = 1:size(x_train,2) % 对所有训练数据迭代
            x = x_train(:,j); % 取每一组训练数据
            if x == zeros(state_dim,1)
               ut = zeros(control_dim,1);
            else
                objective = @(u) cost_function(x,u) + critic(controlled_system(x,u)); %构造目标函数
                u0 = actor(x);% 输入初始状态。返回控制策略
                ut = fminunc(objective,u0); % 求解关于目标函数取最小时的控制策略
                actor_target(j) = ut;% 动作网络目标值
            end
        end
    end

    actor = train(actor, x_train, actor_target); % 训练动作网络 

    % update critic network。
    if i == 1 % 第一次迭代评价网络更新
        critic_target = cost_function(x_train,0); % 初始评价网络
    else % 后续评价网络更新
        x_next = controlled_system(x_train,actor(x_train)); % 将动作网络更新的动作代入控制系统更新下一个状态
        critic_target = cost_function(x_train,(actor(x_train))) + critic_last(x_next); % 目标评价网络更新 
    end
    for j = 1:size(zeros_index,2)
        critic_target(:,zeros_index(j)) = zeros(1,1); % 目标评价网络输出值初始化为0
    end
    critic = train(critic,[x_train,-x_train],[critic_target,critic_target]); % 评价网络训练
    
    % 如果不是第一次迭代，判断当前的价值函数网络和上一次迭代的价值函数网络在训练数据上的均方误差是否小于容许误差 tol。
    % 如果是，则结束迭代。
    if i ~= 1 && mse(critic(x_train),critic_last(x_train)) <= tol 
        critic_set{i} = critic;
        actor_set{i} = actor;
        break;
    end
    
    critic_last = critic;
    
    critic_set{i} = critic;
    actor_set{i} = actor;
    
    % 包含迭代过程中性能指标的数组。
    % 每次迭代后，将当前迭代的性能指标值存储在 performance_index(i+1) 中，
    performance_index(i+1) = critic(x0);
    figure(1),plot(i,performance_index(i+1),'*')
    waitbar(i/epoch,h,['Training controller...',num2str(i/epoch*100),'%']);
end
close(h);

figure(1),
xlabel('Iterations');
ylabel('Iterative $V(x_0)$','Interpreter','latex');
set(gca,'FontName','Times New Roman','FontSize',14,'linewidth',1);
grid on;
hold off;

save training_results/actor_critic critic_set actor_set critic actor
% 保存训练网络后的数据
end

%--------------------------- outpout of system ----------------------------
function y = controlled_system(x,u)
% system matrices
 global B;global A;
 y = A*x + B*u;  % dot product should be adopt in nonlinear systems
end
%----------------------------- cost function ------------------------------
function y = cost_function(x,u)
global Q; global R;
y = (diag(x'*Q*x) + diag(u'*R*u))';
end
```

对经过值迭代算法后训练数据结果进行验证，比较真实代价函数、评价网络输出(近似值函数)和最优值函数
```matlab
function vi_validation
%-------------- validate training results --------------
clear; close all; clc;

% 加载系统和成本函数的信息（A、B、Q、R）以及训练过程中保存的状态数据和训练结果。
% information of system & cost function
global A; global B; global Q; global R;

load training_data/state_data.mat % 训练数据
load training_results/actor_critic.mat % 训练后的网络的数据

% 用最优控制器的增益矩阵 Kopt 和最优值函数的矩阵(离散系统黎卡提方程的解) Popt 进行初始化。
[Kopt, Popt] = dlqr(A,B,Q,R);

Fsamples = 50; 
% 设置参数，包括采样数 Fsamples 和初始状态 x0。
x0 = [1;-1];
x = x0;
x_net = x0;
xx = x.*ones(length(x0),Fsamples+1);
xx_net = x_net.*ones(length(x0),Fsamples+1);
uu_opt = zeros(control_dim,Fsamples);
uu_net = zeros(control_dim,Fsamples);

Jreal = 0;

% 进行循环迭代，根据最优控制器和动态系统更新状态，并记录控制输入和状态。
for k = 1:Fsamples
    u_opt = -Kopt*x;
    x = controlled_system(x,u_opt);
    uu_opt(:,k) = u_opt;
    xx(:,k+1) = x;
    
    u_net = actor(x_net); % 将初始状态代入动作网络输出控制
    Jreal = Jreal + x_net'*Q*x_net + u_net'*R*u_net;
    x_net = controlled_system(x_net,u_net); % 代入控制系统输出
    uu_net(:,k) = u_net;
    xx_net(:,k+1) = x_net;
end

% 计算最优值函数 Jopt、神经网络的值函数 Jnet 和真实的代价函数 Jreal。
Jopt = x0'*Popt*x0
Jnet = critic(x0) % 将初始状态代入评价网络输出代价函数
Jreal

% 绘制状态和控制输入的图形，比较最优控制器和神经网络的性能。
figure(1)
subplot(2,1,1)
plot(0:Fsamples,xx(1,:),'r',0:Fsamples,xx_net(1,:),'b--','linewidth',1)
legend('Optimal','Action network');
ylabel('$x_1$','Interpreter','latex');
set(gca,'FontName','Times New Roman','FontSize',14,'linewidth',1);
grid on;
subplot(2,1,2)
plot(0:Fsamples,xx(2,:),'r',0:Fsamples,xx_net(2,:),'b--','linewidth',1)
legend('Optimal','Action network');
xlabel('Time steps');
ylabel('$x_2$','Interpreter','latex');
set(gca,'FontName','Times New Roman','FontSize',14,'linewidth',1);
grid on;

figure(2)
plot(0:Fsamples-1,uu_opt,'r',0:Fsamples-1,uu_net,'b--','linewidth',1)
legend('Optimal','Action network');
xlabel('Time steps');
ylabel('Controls');
set(gca,'FontName','Times New Roman','FontSize',14,'linewidth',1);
grid on;

end

%--------------------------- outpout of system ----------------------------
function y = controlled_system(x,u)
% system matrices
global A; global B;
y = A*x + B*u;  % dot product should be adopt in nolinear systems
end
```


### 自构建网络权值和激活函数
生成训练数据
```matlab
%-------------------------- generate training data ------------------------
clear; close all; clc;

A = [0,0.1; 0.3, -1]; B = [0;0.5];
Q = 1*eye(2); R = 1;
control_dim = 1;
state_dim = 2;
x0 = [1;-1];

Jcompression = 1; % ？ 
ucompression = 1;

x_train = zeros(state_dim,1);
u_train = zeros(control_dim,1);

for i = 1:30
    x_train = [x_train, zeros(state_dim,1)];
    x_train = [x_train,4*(rand(state_dim,1)-0.5)];   % [-2 2]
    x_train = [x_train,2*(rand(state_dim,1)-0.5)];   % [-1 1]
    x_train = [x_train,1*(rand(state_dim,1)-0.5)];   % [-0.5 0.5]
    x_train = [x_train,0.2*(rand(state_dim,1)-0.5)]; % [-0.1 0.1]
    u_train = [u_train,zeros(control_dim,1)];
    u_train = [u_train,4*(rand(control_dim,1)-0.5)];   % [-2 2]
    u_train = [u_train,2*(rand(control_dim,1)-0.5)];   % [-1 1]
    u_train = [u_train,1*(rand(control_dim,1)-0.5)];   % [-0.5 0.5]
    u_train = [u_train,0.2*(rand(control_dim,1)-0.5)]; % [-0.1 0.1]
end

r = randperm(size(x_train,2));   % 随机选取状态数据的某列
x_train = x_train(:,r); % 得到随机的两行训练数据,x1和x2      
[~,n] = find(x_train == zeros(state_dim,1)); % 返回训练数据x1和x2中为0元素的索引

zeros_index = [];
for i = 1:size(n,1)/state_dim % 62/2
    zeros_index = [zeros_index, n(state_dim*i)]; % 0元素索引
end

ru = randperm(size(u_train,2));   % 随机选取状态数据的某列
u_train = u_train(:,ru);         % 设定初始的状态和控制矩阵，用于存放训练的数据

xu_train = [x_train; u_train]; % 状态和控制输入训练数据
x_next_train = A*x_train + B*u_train; % 代入系统，得下一次的训练数据

save state_data x_train u_train xu_train x_next_train zeros_index x0 control_dim state_dim A B Q R

```
VI算法
```matlab
% This demo checks the feasibility value iteration adaptive dynamic 
% programming using an arbitrary positive semi-definite function as
% the initial value function
%-------------------------------- start -----------------------------------
clear; close all;    clc;

% utilize vi_state_data.m to generate training data and then run this file
load state_data

% the value function of linear systems can be approximated using quadratic
% form and eigenvalues of its corresponding matrix are 3.9645 and 11.0355

weights_critic_ini = [0;0;0]; % 评价网络的任意初始权重
% 线性系统的值函数可近似为二次型
% eigenvalues can be computed by 计算初始的值函数权函数的特征值
P_ini = [ weights_critic_ini(1) weights_critic_ini(2)/2; weights_critic_ini(2)/2 weights_critic_ini(3) ]
eig(P_ini)

weights_critic = weights_critic_ini; 
weights_actor = zeros(2,1); 

% 选取值函数和控制策略的输入
xV = vi_pf_critic(x_train);
xV_minus = vi_pf_critic(-x_train);
xu = vi_pf_actor(x_train);


epoch = 20;
performance_index = zeros(1,epoch + 1);

performance_index(1) = weights_critic'*vi_pf_critic(x0); % 第一次迭代值函数(初始值函数为半正定)

% 存放评价网络和动作网络更新数据
weights_critic_set = weights_critic;
weights_actor_set = [];


figure(1),plot(0,performance_index(1),'*'),hold on;
h = waitbar(0,'please wait');
for i = 1:epoch
    % update action network
    % by optimal tool
    if i ~= 1 % 当非第1次迭代
        actor_target = zeros(control_dim,size(x_train,2));
        for j = 1:size(x_train,2)
            x = x_train(:,j);
            if x == zeros(state_dim,1)
                ut = zeros(control_dim,1); % 离线数据为0时，控制输入为0
            else
                objective = @(u) vi_cost_function(x,u,Q,R) + ...
                    weights_critic'*vi_pf_critic(vi_controlled_system(x,u,A,B)); % 构造性能指标函数的更新
                u0 = weights_actor'*vi_pf_actor(x); % 计算迭代控制策略
                ut = fminunc(objective,u0); % 根据性能指标求最优控制策略
            end
            actor_target(:,j) = ut; % 更新最优控制序列
        end
        weights_actor = xu'\actor_target'; % 更新动作网络的权重
    end
    
    % update critic network
    if i == 1 
        critic_target = vi_cost_function(x_train,0,Q,R)'; % 第一次迭代得到目标值函数V_0
    else
        x_next = vi_controlled_system(x_train,weights_actor'*xu,A,B); 
        critic_target = vi_cost_function(x_train,weights_actor'*xu,Q,R)'+ ...
                        (weights_critic'*vi_pf_critic(x_next))'; % 第2次迭代开始计算迭代值函数
    end
    weights_critic = [ xV xV_minus]'\[ critic_target; critic_target]; % 评价网络的权重更新（正负）
    
    weights_actor_set = [weights_actor_set weights_actor]; % 记录动作网络的权重
    weights_critic_set = [weights_critic_set weights_critic]; % 记录评价网络的权重
    performance_index(i+1) = weights_critic'*vi_pf_critic(x0); % 性能指标或值函数更新
    figure(1),plot(i,performance_index(i+1),'*'),hold on;
    waitbar(i/epoch,h,['Training controller...',num2str(i/epoch*100),'%']);
end
close(h);


save actor_critic weights_actor_set weights_actor weights_critic_set weights_critic


figure(1),
xlabel('Iterations');
ylabel('Iterative $V(x_0)$','Interpreter','latex');
set(gca,'FontName','Times New Roman','FontSize',14,'linewidth',1);
grid on;
hold off;

figure(2),
plot((1:length(weights_critic_set))-1,weights_critic_set,'linewidth',1)
xlabel('Iterations');
ylabel('Critic NN weights'); 
set(gca,'FontName','Times New Roman','FontSize',14,'linewidth',1);
grid on;

figure(3),
plot((1:length(weights_actor_set))-1,weights_actor_set,'linewidth',1)
xlabel('Iterations');
ylabel('Action NN weights'); 
set(gca,'FontName','Times New Roman','FontSize',14,'linewidth',1);
grid on;

```
验证
```matlab
% check results
clear; close all; clc;
% 加载系统和成本函数的信息（A、B、Q、R）以及训练过程中保存的状态数据和训练结果。
% information of system & cost function

load state_data.mat % 训练数据
load actor_critic.mat % 训练后的网络的数据

[Kopt, Popt] = dlqr(A,B,Q,R) % 最优增益和最优值函数矩阵（黎卡提方程的解）

Fsamples = 50; 
% 设置参数，包括采样数 Fsamples 和初始状态 x0。

x = x0;
x_net = x0;
xx = x.*ones(length(x0),Fsamples+1);
xx_net = x_net.*ones(length(x0),Fsamples+1);
uu_opt = zeros(control_dim,Fsamples);
uu_net = zeros(control_dim,Fsamples);
Jreal = 0;

for k = 1:Fsamples
    u_opt = -Kopt*x; % 最优控制
    x = vi_controlled_system(x,u_opt,A,B);
    uu_opt(:,k) = u_opt;
    xx(:,k+1) = x;
    
    u_net = weights_actor'*vi_pf_actor(x_net); % 将初始状态代入动作网络输出控制
    Jreal = Jreal + x_net'*Q*x_net + u_net'*R*u_net;
    x_net = vi_controlled_system(x_net,u_net,A,B); % 系统状态
    uu_net(:,k) = u_net;
    xx_net(:,k+1) = x_net;
end


Jopt = x0'*Popt*x0 
Jnet = weights_critic'*vi_pf_critic(x0) % 将初始状态代入评价网络输出值函数
Jreal % 动作网络更新的迭代控制代入代价函数


figure(1)
subplot(2,1,1)
plot(0:Fsamples,xx(1,:),'r',0:Fsamples,xx_net(1,:),'b--','linewidth',1)
legend('Optimal','Action network');
ylabel('$x_1$','Interpreter','latex');
set(gca,'FontName','Times New Roman','FontSize',14,'linewidth',1);
grid on;
subplot(2,1,2)
plot(0:Fsamples,xx(2,:),'r',0:Fsamples,xx_net(2,:),'b--','linewidth',1)
legend('Optimal','Action network');
xlabel('Time steps');
ylabel('$x_2$','Interpreter','latex');
set(gca,'FontName','Times New Roman','FontSize',14,'linewidth',1);
grid on;


figure(2)
plot(0:Fsamples-1,uu_opt,'r',0:Fsamples-1,uu_net,'b--','linewidth',1)
legend('Optimal','Action network');
xlabel('Time steps');
ylabel('Controls');
set(gca,'FontName','Times New Roman','FontSize',14,'linewidth',1);
grid on;

% P_ini = [ weights_critic_ini(1) weights_critic_ini(2)/2; weights_critic_ini(2)/2 weights_critic_ini(3) ]
% K_final = -weights_actor' % 迭代控制增益
% P_final = [ weights_critic(1) weights_critic(2)/2; weights_critic(2)/2 weights_critic(3) ] % 迭代值函数

```

缺点不足：
1. 值迭代在无穷次终止，而实际系统是在有限次迭代下。且实际系统必须在有限次迭代下找到有效的控制策略，值迭代中的控制策略可容许性不能得到保证，迭代控制策略不能保证系统稳定，只能通过预训练的数据**离线**执行。需要完整的系统动力学模型。
2. 当为得到最优控制，需要求得最优值函数，其通过VI算法的值函数迭代到收敛。而根据初始值函数必须为0，以保证满足其收敛性，但这条件比较苛刻。
3. 未考虑神经网络或者函数近似器的近似误差，会影响VI算法的有效性