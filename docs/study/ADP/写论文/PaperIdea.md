---
title: 撰写论文思路和想法总结
katex: true
categories: 
- ADP
tags:
- 论文撰写
- 仿真
password: lsa0723
abstract: 这里有东西被加密喽, 请输入密码查看呢！
message: hello! 这里是需要密码才能解锁噢！
theme: xray
wrong_pass_message: 抱歉, 这个密码看着不太对, 请再试试呢！
wrong_hash_message: 抱歉, 这个文章不能被校验, 不过你还是能看看解密后的内容！
---
# 小论文撰写想法和思路
## 论文写作的第一步
### 模仿框架，学习方法
记录一些经典的句子。基于师兄的文章和代码，对文章的学习是先推导后仿真。对最新的文章的创新点，使用之前所了解的文章的框架和方法(事件触发的ADP类方法)，解决最优跟踪控制问题（控制输入受限），套入到最新的idea中

### 模仿很难超越
### 论文是改出来的
摘要中包括知识点框架，进行学习。引言中末尾的通用模板，如主要贡献和文章的章节介绍

### 跟导师沟通
将自己的思路跟老师说清楚，然后给出方案或者问题，询问老师是否可行。将自己的想法表达出来

### 写作过程
作图-结果-讨论-方法-引言-结论-摘要

### 随时附上参考文献
### 润色

## 根据所阅读文献进行思路整理（自己思考）
### 针对什么问题，现有的解决方法有哪些？
1. 未知系统动力学的非线性系统的最优跟踪问题

   增广系统将最优跟踪控制问题转化为最优调节（整定）问题

   基于GrHDP事件触发的最优控制问题

2. **未知系统动力学的多智能体系统的最优跟踪问题**
   multi-player and multiagent

   - multi-player:多个控制器并共享整个系统状态，最优跟踪控制视为非零和博弈问题，目标控制策略使得每个player值函数最小，系统轨迹跟踪期望轨迹达到Nash equilibrium
   - multi-agent:多个控制器有多个局部系统状态，目标使每个智能体跟随领导者

3. off-policy对比on-policy算法的优势：将策略提升的阶段放在了获取策略评估值的状态上，使得算法的策略提升和被提升的状态一致，可直接将被提升的控制策略运用到系统中，提高算法的收敛效果与速度。

4. 相关PI算法需要初始的可容许控制，能使得局部性能指标有界，系统稳定


### 指出现有文章存在的问题和缺点

1. 需要系统的动力学模型(model-based)
2. 本身方法或框架存在的不足
3. 算法的收敛性和稳定性，算法收敛的快慢（值迭代和策略迭代的权衡）
4. 讨论系统在理想情况下，未引入输入控制受限和扰动。比如对实际系统未考虑输入输出上限问题，实际系统可能会存在不确定噪声和扰动。对于存在扰动控制性能最差，存在最优控制使性能最优，即求解$H_\infty$最优控制律使得代价函数获得最优。
5. 神经网络近似，对近似误差（一致最终有界）的处理与证明

#### 多智能体

RL/ADP算法解决多智能体的最优控制问题，如跟踪控制、图论、双边一致性、协同输出整定、编队控制和鲁棒滑膜控制问题。

1. 一致性同步控制研究大部分没有考虑总能量的最优性和状态的收敛速度。困难在于智能体的控制策略只能从自身以及连接的邻居智能体中获取信息
2. 多智能体的时滞问题。大多数时滞系统通过增广矩阵转化为无时滞系统。结合博弈、跟踪误差（坐标变换），将多智能体最优一致跟踪问题转换为多玩家博弈的纳什平衡点问题。
3. （不建议？）多智能体博弈问题。当系统动力学完全未知或者部分未知时，ADP无法求解具有多时滞的多人博弈问题。通过合适坐标变换，使用基于数据ADP算法，可估计很多情况下的延迟阶数。

### 如何进行改进或优化，或者如何组合创新

1. 事件触发仅在违反被定义的触发条件时控制动作的更新，节省通信资源和减少控制的输入
   - multiagent，每个智能体设计自己事件触发条件，当智能体自身事件触发条件满足时，该智能体被触发与邻居进行数据交互，获得邻居的状态$x_j(k)$以及控制输入信号$u_j(k)$，自身采样的数据$x_i(k)$以及交互得到的数据经控制器生产控制信号，并将控制信号通过零阶保持器生成连续的控制信号$u_i(t)$，控制信号输入给被控系统。当事件触发没有满足时，智能体保存原有采样数据和交互数据不变。
2. 事件触发条件的设置：基于增广状态和触发增广状态的误差，对比传统基于误差的事件触发条件和基于性能指标函数预先上界
3. 无模型的方法(model-free)
4. ADP框架的改进，如actor-critic---->GrHDP
5. off-policy和on-policy
6. 对实际系统中不确定扰动和噪声，引入神经网络自适应进行补偿
7. 折扣因子对不同系统(model-free/model-based方法)稳定性和神经网络收敛性的影响
8. 可容许控制策略（获取的难度）：一般而言，PI算法需要Admissible Control，VI算法任意初始控制策略

### 目前自己缺乏的知识和能力

1. 本身存在的仿真代码能力
2. 未对所了解的文章框架进行总结，形成自己的思路，主要是对整个框架的理解
3. 对文章中的证明(后续可模仿)
4. 相关知识点
   - 图论
   - PE条件、concurrent learning(并行学习)，也被称为experience replay(经验重放)
   - 纳什均衡
   - 勒贝格积分
   - off-policy和on-policy
   - model-free(如Q-learning)和model-based


## 文献整理 
### 控制受限事件触发ADP最优整定问题
#### 控制受限制下，引入非二次型的性能指标，基于事件触发的HDP算法，求解最优整定控制问题
```
M. Ha, D. Wang and D. Liu, "Event-Triggered Adaptive Critic Control Design for Discrete-Time Constrained Nonlinear Systems," in IEEE Transactions on Systems, Man, and Cybernetics: Systems, vol. 50, no. 9, pp. 3158-3168, Sept. 2020, doi: 10.1109/TSMC.2018.2868510. 
```
引入假设，值函数V(x(k))为Lyapunov function，输入状态稳定(ISS)； 所给出的事件触发条件关于系统动态，其中控制受限。证明控制受限的事件触发控制是渐近稳定

算法在达到一定的迭代次数后停止

该文章最后没有利用Lyapunov理论分析闭环系统的稳定性，即系统状态和神经网络权值误差的分析（一致最终有界或在原点稳定）

#### 控制受限，以GHDP框架构建神经网络，同样引入二次型的性能指标
```
Mu, C., Liao, K. & Wang, K. Event-triggered design for discrete-time nonlinear systems with control constraints. Nonlinear Dyn 103, 2645–2657 (2021). https://doi.org/10.1007/s11071-021-06218-4
```
评价网络学习代价函数和代价函数导数的信息，能够学习更多信息。而且文章末尾部分给出迭代值函数/代价函数的终止条件，即最优值函数难以确定，迭代次数不可能无穷，所以设定一个很小的常数（收敛因子）。下一次迭代的值函数-当前迭代的值函数<=收敛因子

但是，同样对于神经网络权重误差和系统状态的稳定性，没有给出分析

####  控制输入受限，事件触发ADDHP框架构建神经网络，引入非二次型效用函数/性能指标
```
Yuanyuan Cheng, Yuan Li. A novel event-triggered constrained control for nonlinear discrete-time systems[J]. AIMS Mathematics, 2023, 8(9): 20530-20545. doi: 10.3934/math.20231046
```
事件触发条件以参数C和α，资源利用率。ADDHP框架对比HDP和DHP框架能够学习更多系统的信息，具有更高近似精度和更快收敛性


### HDP(lambda)
#### 未知非线性离散系统，事件触发HDP(λ)方法，解决最优整定控制，引入长期预测参数lambda
```
T. Li, D. Yang, X. Xie and H. Zhang, "Event-Triggered Control of Nonlinear Discrete-Time System With Unknown Dynamics Based on HDP(λ)," in IEEE Transactions on Cybernetics, vol. 52, no. 7, pp. 6046-6058, July 2022, doi: 10.1109/TCYB.2020.3044595.
```
(思路来源于蒙特卡洛法向时间差分法的改进)，考虑N-step-return更多关于未来回报的影响

以往的最优代价函数为short-term 为one-step TD 或者 HDP(0)，给出bellman equation的i-step-return，计算n-step-return的平均值-记为TD(lambda)的lambda-return值，该文章通过TD(lambda)时刻t(R_t^{lambda})的lambda-return值，推导HDP(lambda)的目标值J;one-step-retrn值近似值函数J

相比3，4两篇文章，以同样的思路证明在触发条件和输入状态稳定的Lyapunov function满足一定条件时事件触发控制系统的渐近稳定性，但本文章提出利用Lyapunov理论证明系统状态和神经网络权值误差的一致最终有界(UUB)，



#### 无模型的仿射非线性离散系统，事件触发的GrHDP方法求解最优控制问题
```
Wang, J., Wang, Y. & Ji, Z. Model-free event-triggered optimal control with performance guarantees via goal representation heuristic dynamic programming. Nonlinear Dyn 108, 3711–3726 (2022). https://doi.org/10.1007/s11071-022-07438-y
```
预先给定性能指标的上界，证明实际性能指标小于该定义的上界，以最优性能指标/值函数和内部增强信号的关系作为事件触发条件。其中参数beta作为调整系统性能和通信资源之间的权衡

提出在触发条件下可确定实际性能指标的上界，以及系统稳定性。但未给出算法的收敛性

#### T-GrHDP方法求解非仿射非线性系统的最优整定问题
```
Wang, Ding, X. Li, Lingzhi Hu and Jun-Li Qiao. “Data-driven tracking control design with reinforcement learning involving a wastewater treatment application.” Eng. Appl. Artif. Intell. 123 (2023): 106242.
```
GrHDP结构和HDP结构的改进，内部增强信号包含更多未来的信息，便于对后续的控制策略评估和更新

T-GrHDP算法收敛性证明，迭代值函数单调性，迭代值函数和增强信号的上界，以及收敛到最优值函数和最优控制策略。Lyapunov函数证明神经网络的权重误差在一定条件是一致最终有界(UUB)

### 广义值迭代算法（引入加速因子）
```
Wang, Ding, Mingming Zhao, Mingming Ha and Lingzhi Hu. “Adaptive-critic-based hybrid intelligent optimal tracking for a class of nonlinear discrete-time systems.” Eng. Appl. Artif. Intell. 105 (2021): 104443.
```
对传统的值迭代算法收敛较慢，需要初始代价函数为0->带加速因子的广义值迭代算法



### 动态事件触发，对比静态事件触发间隔更大
```
Z. Ming, H. Zhang, Y. Yan and J. Zhang, "Tracking Control of Discrete-Time System With Dynamic Event-Based Adaptive Dynamic Programming," in IEEE Transactions on Circuits and Systems II: Express Briefs, vol. 69, no. 8, pp. 3570-3574, Aug. 2022, doi: 10.1109/TCSII.2022.3168428. 
```
值函数定义中将控制输入引入到跟踪误差，不考虑控制输入的二次型

分别Lyapunov理论证明静态事件触发和动态事件触发，触发时刻与未触发时刻，系统状态和网络权重误差是一致最终有界的







## 陈凯天师兄与王老师分享文章
```
Ming, Zhong, Huaguang Zhang, Juan Zhang and Xiangpeng Xie. “A novel actor-critic-identifier architecture for nonlinear multiagent systems with gradient descent method.” Autom. 155 (2023): 111128.
```
对一般（非仿射）非线性连续未知动态系统提出在线的ADP算法，解决最优一致性问题，网络结构为Actor-Critic-Identifier

### 主要内容
1. identifier/critic/actor设计
2. 模型网络，当满足函数重构误差及其导数/激活函数有界于正常数，辨识误差收敛到原点可调整的邻域内
3. 误差系统，控制策略和三层网络权重调整策略下，局部误差/网络权重近似误差是一致最终有界（UUB）

### 创新点
未知动态系统设计actor-cirtic-identifier NN，ACI网络结构简单，梯度下降法并简化NN调整的方法，使得NN学习连续和同时的

### 难点
Lyapunov理论证明


### 思考问题
1. 性能指标与代价函数，代价函数与值函数之间的关系。
对多智能体最优一致性问题，性能指标由智能体与邻居的局部误差和控制策略；而值函数由智能体于邻居的局部误差。其类似对比为值函数和Q函数的区别

2. 找到一篇文章看是否能加入事件触发机制（包括动态事件触发和静态事件触发），对智能体的局部一致误差，构造事件触发误差。

3. 选择以actor-critic-identifier作为adp类方法的主要结构

事件触发以离散的事件序列作为输入，而actor-critic-identifier结构一般处理连续信号。事件触发依赖于事件的因果关系，而ACI结构依赖于反馈信号来优化控制策略

4. 网络权重更新需要PE条件，通过增加对控制输入的探测噪声，得到更多系统信息 

5. 加入攻击（如FDI），处理方法为引入安全预选器，过滤掉受到攻击的信号

6. Lyapunov证明方法；关键是讨论在触发时刻与未触发间隔，一致性误差、系统状态和网络权重向量的一致最终有界


### 单体未知非线性连续系统，事件触发和actor-critic-identifier框架最优控制
```
三区 Robust and Nonlinear Control Liu, Ning, Kun Zhang and Xiangpeng Xie. “Optimal control of unknown nonlinear system under event‐triggered mechanism and identifier‐critic‐actor architecture.” International Journal of Robust and Nonlinear Control 34 (2023): 530 - 550.
```
identifier辨识非仿射系统，梯度下降调整identifier权重，辨识状态的误差动力学收敛到原点的可调整的邻域内

ACI三层网络权重更新，证明在事件触发条件下系统状态和权重估计误差是一致有界的

#### 该篇文章进行模仿和改写
加入执行器饱和的输入受限，给出在非仿射系统下的不带折扣因子的值函数，推出哈密顿函数，其中效用函数为二次型。（想法不行），处理受限的控制按以往文章，都以非二次型处理


### 第一篇
对非仿射系统，在控制输入受限，网络辨识器通过输入数据将未知系统模型和动态进行辨识为仿射系统，事件触发
```
二区 Neurocomputing Event-triggered control for input constrained non-affine nonlinear systems based on neuro-dynamic programming  Shunchao Zhang , Bo Zhao , Yongwei Zhang 
```
以这篇文章的推导为基础，引入动态事件触发机制，将neuro-dynamic programming，描述成Identifier-Critic结构网络
```
四区 Optimal control of partially unknown constrained-input systems: A dynamic event-triggered-based approach
```
将其扩展为完全未知系统动态的非仿射系统，通过一层identifier网络辨识系统动态和模型，还有一层评价网络，近似最优值函数，根据已辨识的系统动态，解析可直接求解出受限下的最优控制，引入动态事件触发机制以大大减少通信资源和计算成本。

```
二区 Neurocomputing Dynamic event-triggered-based single-network ADP optimal tracking control for the unknown nonlinear system with constrained input
```
加入动态事件触发，以及放松PE条件和初始可容许控制策略。评价网络权重更新规则中加入经验重放和放松初始可容许控制策略的额外项

### 代码仿真想法
给定一个非线性系统动态，构建identifier网络，初始化参数，网络训练拟合该非线性系统动态。实际的非仿射非线性系统动力学，神经网络构建一个仿射非线性动力学\dotx=a0x+a1tanh(x)+a2u+a3，其中给定初始的权重a0,a1,a2,a3，且维度分别为2*2,2*2,2*1,2*1



```
self.nextstate[:, 0] = 0.5 * self.state[:, 1] - 0.5 * torch.sin(self.state[:, 0])
self.nextstate[:, 1] = -1 * torch.cos(self.state[:, 1]) * torch.sin(self.state[:, 0]) - 0.5 * u[:,0] * u[:,0] * u[:,0] -0.3 * torch.tanh(u[:,0])


next_x(:, 1) = 0.5 * x(:, 2) - 0.5 * sin(x(:, 1));
next_x(:, 2) = -1 * cos(x(:, 2)) * sin(x(:, 1)) - 0.5 * u^3 - 0.3 * tanh(u);
```
next_x(:,i) = a0*x + a1*tanh(x) + a2*u + a3

如何将一个非仿射非线性系统next_x(:, 1) = 0.5 * x(:, 2) - 0.5 * sin(x(:, 1));
next_x(:, 2) = -cos(x(:, 2)) .* sin(x(:, 1)) - 0.5 * u.^3 - 0.3 * tanh(u);
通过辨识网络，得到一个近似仿射的非线性系统next_x(:,i) = a0*x + a1*tanh(x) + a2*u + a3，其中a0为22，a1为22，a2为21，a3为2*1的权重参数。要求控制输入受限在[-0.6,0.6]，给定训练数据下，每次训练得到的权重参数a0，a1，a2，a3最终收敛的值是确定的

根据辨识后的系统，构造评价网络激活函数为[x1^2,x1*x2,x2^2]，权重参数为[wc1,wc2,wc3]，评价网络学习率为ac=0.3，选取初始状态x0=[-1,1]，得到权重收敛

静态事件触发条件与动态事件触发条件

对权重网络更新的学习率采取动态变化

调整代码使得权重参数wc收敛到不为零最优值，控制输入u逐渐趋近0，状态变量逐渐趋近于0，值函数V收敛到不为0最优值

根据TSMC2008VI算法框架结构修改，尝试，得到得到更新的值函数（性能指标函数）criticValue，后保存数据，将更新后的状态和控制进行验证，并引入事件触发机制

#### 整体仿真思路和想法

1. 阅读二区的仿真代码，Model-free event-triggered optimal control with performance guarantees via goal representation heuristic dynamic programming ，非线性系统基于无模型的事件触发GrHDP最优控制，事件触发条件是基于性能指标构建。 采用复杂的GrHDP网络，与实际仿真需求不符

之后尝试自己开始写identifier NN，将21年 Event-triggered control for input constrained non-affine nonlinear systems based on neuro-dynamic programming 的非线性系统 next_x(:, 1) = 0.5 * x(:, 2) - 0.5 * sin(x(:, 1));
next_x(:, 2) = -cos(x(:, 2)) .* sin(x(:, 1)) - 0.5 * u.^3 - 0.3 * tanh(u);构造类似仿射的系统，辨识权重参数a0 a1 a2 a3，先用matlab以梯度下降法实现，代码简单，由于离线数据产生的随机性，鲁棒性太差。之后采用matlab的神经网络工具箱，效果仍不佳，在给定初值情况下，进一步迭代使用优化器，如梯度下降调整identifier的权重参数，每次权重参数不同，最终收敛的结果也不同。总结了以下几点问题：

a. 学习率，在神经网络权重调节过程中，学习率的大小需要按照系统和迭代次数设计，通过绘图观测不同学习率的结果，需要不断调
b. 初始权重初值，随机初始化可能不能达到预期效果
c. 系统状态初值，给定描述的非线性系统，构建离线数据集，系统状态初值，也会影响神经网络的收敛结果

之后学习pytorch深度学习框架，同样针对以上的非线性系统，给定随机种子，构造前向神经网络的identifier模型，梯度下降训练模型，最终使得训练损失降到最小值收敛，对比原非线性实际系统于identifier NN之后的系统产生系统状态的误差，该误差是逐渐收敛至零的，保存训练得到的权重值a0 a1 a2 a3。由于随机种子不同和初始权重，当然所得的的收敛值不同。

2. 首先是完成无事件触发机制下的情况，求解最优控制策略，使得性能指标函数最小化，根据identifier NN得到收敛的权重参数，作为类仿射系统，进一步构建评价网络。程序初步同样以梯度下降法，更新评价网络的权重参数，迭代更新值函数。按照文章中公式，更新受限控制输入，同样预期的结果需要评价网络的权重是最终收敛的，迭代性能指标函数收敛，其控制输入逐渐收敛到零

程序框图表示整个算法过程

要保证在identifier NN训练后的辨识权重参数下，在评价网络训练之后，评价网络权重是收敛到最优值，并将其保存下来。同时需要保证其更新后的评价网络权重能使得控制输入更新，保证控制输入在代入到性能指标中，性能指标函数最小化，则得到最优控制策略。对系统进行应用时，将前两个过程得到的参数和权重代入，对最优控制正定问题，系统状态变量逐渐趋近于零，控制输入逐渐趋近于零。


3. 收集阅读有关值迭代和策略迭代的代码，阅读一区仿真代码，Discrete-Time Nonlinear HJB Solution Using Approximate Dynamic Programming: Convergence Proof，其框架结构比较清晰。

由于identifier NN采用离线的训练数据，故根据以上文献的代码，修改仿真非线性系统，生成更多的训练数据，输入到identifier NN中。 同时改进了identifier NN的整体框架，使收敛更加稳定，鲁棒性更好，得到辨识仿射系统使得与原系统的误差逐渐趋近于零。

Event-triggered single-network ADP method for constrained optimal tracking control of continuous-time non-linear systems
Optimal control of partially unknown constrained‐input systems: A dynamic event‐triggered‐based approach
Optimal tracking control for unknown nonlinear systems with uncertain input saturation: A dynamic event-triggered ADP algorithm
Self-learning robust optimal control for continuous-time nonlinear systems with mismatched disturbances


4. 同样根据以上代码，设计评价网络更新过程，不使用matlab神经网络工具箱，而使用自己构建评价网络激活函数和权重函数，实现评价网络权重的更新，根据推导的文献，在以梯度下降的权重更新过程中，加入了经验重放和放宽可容许控制的条件。出现权重发散的情况，除了之前的三个问题，还有两点问题

a. 构建神经网络本身鲁棒性差的问题
b. 激活函数

5. 不断调整评价网络更新的代码，代入推导的公式验证输出近似评价网络权重的收敛性和性能指标函数的收敛性。将最终收敛的权重代入控制中进行更新，最后验证系统是否解决最优调节问题。系统状态变量和控制输入逐渐趋近于零。


6. 修改了评价网络更新的迭代方式，最终出现的问题在生成训练数据时的系统表达式，调整之后，经过identifier NN，得到的权重参数

a0 = 0.081482060	0.32611328
0.41745630	-0.13388215

a1 = -0.65151274	0.25265476
-1.2301277	0.18640280

a2 = -0.0076132487	-0.38903463

a3 = 0.0010302463	-0.0042948546

最终按照之前很多次修改的critic NN 并更新权重和控制输入，实现状态变量和控制输入逐渐收敛到零

6. 加入事件触发控制机制，静态事件触发条件

根据文献定义的事件触发条件，设计静态触发条件，选取预设定的参数，使得仅在满足事件触发条件时，对系统状态进行采样，更新控制策略

动态事件触发条件

根据文献定义辅助变量，修改原静态事件触发条件，引入辅助动态变量，使得进一步减少通信负担和计算成本

大致完成，后续修改绘图和采取可用的数据

7. 绘图内容
辨识网络训练损失、辨识仿射系统与原非仿射系统的状态近似误差、性能指标函数迭代图、评价网络权重；验证时间触发与动态、静态事件触发控制输入，状态变量，事件触发误差和触发阈值，时间触发与事件触发采样次数；动态变量变化

最小触发时间间隔，理论按23年那篇文章表示出来，具体计算按照21年那篇

整理程序给出对比图

8. 所得的曲线图不够理想，如仿真参考文献对比，曲线不够平滑，即在更快时间内结束迭代。原因可能是网络权重参数少，近似参数少。
修改后，对于评价网络权重参数的个数没有对最终验证产生影响，后续再考虑其他仿真文献的代码程序

根据连续时间的系统方程，参考最新一篇二区的文献仿真，修改了获取系统微分方程的表达式，以近似求积分的方式计算系统状态变量，最后得到的状态变量和控制输入的曲线效果比较好。进一步调整了绘制对比图的程序，给出时间触发、静态事件触发和动态事件触发下的控制输入的曲线变化图

后续将程序整理后，完成论文撰写和修改，对应将仿真调整，具体内容

RNN
https://blog.csdn.net/hlayumi1234567/article/details/128635001


### 第二篇

#### FDI攻击多智能体事件触发简化ADP(SNAC)
```
一区 IEEE TRANSACTIONS ON SYSTEMS, MAN, AND CYBERNETICS: SYSTEMS 2023 Simplified ADP for Event-Triggered Control of Multiagent Systems Against FDI Attacks Yuanyuan Xu , Tieshan Li , Senior Member, IEEE, Yue Yang, Student Member, IEEE, Shaocheng Tong , Senior Member, IEEE, and C. L. Philip Chen , Fellow, IEEE
```

FDI存在稀疏攻击下，构造预选器，对攻击信号过滤，获取安全的输出数据。

状态观测器辨识系统状态，证明该观测器下近似误差是一致最终有界的

采用经验重放方法，评价网络权重更新时放宽PE条件

事件触发HJB方程，神经网络近似下事件触发控制策略及其评价网络权重更新，事件触发条件满足时，保证估计误差和系统有界。

#### 多智能体仿射非线性系统，事件触发简化ADP的PI算法和经验重放
```
二区 Nonlinear Dynamics 2021 Online event-triggered optimal control for multi-agent systems using simplified ADP and experience replay technique Yuanyuan Xu . Tieshan Li . Weiwei Bai . Qihe Shan . Liang’en Yuan . Yue Wu
```
事件触发条件1，局部一致性误差渐近稳定，且局部代价函数等价于值函数。

采用PI算法近似求解事件触发HJB方程，评价网络权重更新，放宽PE条件（经验重放），迭代控制策略和迭代值函数收敛到最优值。

事件触发条件2，经验重放下评价网络权重调整，系统是一致最终有界的。Lyapounv理论分别证明在不触发间隔和触发时刻下的
```
GPT 润色![1727336605354](image/PaperIdea/1727336605354.png)![1727336607435](image/PaperIdea/1727336607435.png)

Hello ChatGPT, I would like to request your help in
correcting my research paper in the area of video captioning (请注意替
换论⽂的研究领域). I have already written the paper, but I need
assistance in improving the language, grammar, spelling, and
punctuation. My paper is intended for submission to a scholarly
journal, and I need it to be error-free and polished to the highest
standards: 



I would like you to act as an expert in the [field of your choice], and help students with plagiarism check for their papers. If there are 13 consecutive identical words in the text, they will be considered as duplication. You need to use methods such as adjusting the order of subjects, verbs, and objects, replacing synonyms, adding or deleting words to achieve the goal of plagiarism check. Please modify the following paragraph:
                        
原文链接：https://blog.csdn.net/CCfz566/article/details/134545437
```

初步想法，在第一篇的Identifier-Critic结构基础上，将非仿射非线性的多智能体模型辨识成类似仿射的模型，

Optimal consensus model-free control for multi-agent systems subject to input delays and switching topologies

多篇有关切换拓扑的文献 或者引入时延

看多智能体系统包含控制相关的论文，更改思路，尝试做最优包含控制(放弃)，回归多体的最优一致性问题，找到一个合适的多体拓扑系统，满足一致性要求


结合文章
```
Simplified optimized finite-time containment control for a class of multi-agent systems with actuator faults

Neural network-based adaptive optimal containment control for non-affine nonlinear multi-agent systems within an identifier-actor-critic framework
```


代码仿真修改来自以下的文章
```
Optimal Tracking Control of Nonlinear Multiagent Systems Using Internal Reinforce Q-Learning

Internal reinforcement adaptive dynamic programming for optimal containment control of unknown continuous-time multi-agent systems
```
多智能体的包含控制问题(放弃包含控制，或者考虑到第三篇)，仍考虑最优一致性问题

对于时滞问题，需针对离散系统进行讨论


#### 仿真过程
1. 原单体的离线数据训练生成，将一个领导者和三个跟随者的训练状态生成，并保存到对应的.mat文件数据中。再修改identifierNN的代码，对所有智能体非仿射非线性系统进行辨识，得到辨识后近仿射非线性的系统，可绘制出邻导者和跟随者的辨识误差

2. 结束identifier NN部分，进一步对CriticNN进行初始化，根据以下automatic论文的仿真，选取系统模型和参数。论文主要理论需要进一步推导
```
A novel actor–critic–identifier architecture for nonlinear multiagent systems with gradient descent method
```

由于需要identifier NN / critic NN / actor NN 结合训练， 所参考的文献仿真并不与第一篇的思想一致，所以对其尝试复现存在问题。

新的思考，搜集查看有关时滞或者时延的文章，对未知系统描述的数据，存在不确定性与时滞性，除了对系统进行辨识，再考虑测量信号和采集信号的延迟，input delay  离散

时滞相关代码
```matlab
        % 模拟通信延迟：对于延迟情况，使用上一个时刻的数据进行优化
        max_delay = 10; % 最大延迟时间为10s
        delay = 10 - randi([0, 10]);
        delayed_wind_speed_wf1 = noise_wind_speed_wf1; % 初始化延迟风速数据
        for t = i + delay
            if rand < 0.2  % 20%的概率发生延迟
                delayed_wind_speed_wf1(:, t) = delayed_wind_speed_wf1(:, t-max_delay);
            end
        end
        delayed_Pout_wf1 = noise_Pout_wf1; % 初始化延迟风速数据
        for t = i + delay![1728376493449](image/PaperIdea/1728376493449.png)![1728376495202](image/PaperIdea/1728376495202.png)![1728376500273](image/PaperIdea/1728376500273.png)
            if rand < 0.2  % 20%的概率发生延迟
                delayed_Pout_wf1(:, t) = delayed_Pout_wf1(:, t-max_delay);
            end
        end
        delayed_pitch_wf1 = noise_pitch_wf1; % 初始化延迟风速数据
        for t = i + delay
            if rand < 0.2  % 20%的概率发生延迟
                delayed_pitch_wf1(:, t) = delayed_pitch_wf1(:, t-max_delay);
            end
        end
        delayed_omega_r_wf1 = noise_omega_r_wf1; % 初始化延迟风速数据
        for t = i + delay
            if rand < 0.2  % 20%的概率发生延迟
                delayed_omega_r_wf1(:, t) = delayed_omega_r_wf1(:, t-max_delay);
            end
        end
        delayed_power_ref = noise_power_ref; % 初始化延迟风速数据
        for t = i + delay
            if rand < 0.2  % 20%的概率发生延迟
                delayed_power_ref(:, t) = delayed_power_ref(:, t-max_delay);
            end
        end
```


控制受限的未知非线性多智能体，求解事件触发最优一致性控制问题
```
Data‐driven optimal event‐triggered consensus control for unknown nonlinear multiagent systems with control constraints
```

思考后想法
```
Simplified ADP for Event-Triggered Control of Multiagent Systems Against FDI Attacks

Ming, Zhong, Huaguang Zhang, Juan Zhang and Xiangpeng Xie. “A novel actor-critic-identifier architecture for nonlinear multiagent systems with gradient descent method.” Autom. 155 (2023): 111128.

Data-driven optimal event-triggered consensus control for unknown nonlinear multiagent systems with control constraints
```
对以上文章考虑在控制饱和(受约束)的事件触发机制下 identifier critic结构框架 解决对于未知系统动态和输入动态的非线性多智能体系统的最优控制问题

一个领导者，五个跟随者的通信拓扑，通过在第一篇基础上增加多个智能体和领导者，辨识网络后满足要求，领导者不加入控制输入，训练结果表明，所有智能体的辨识误差能趋近于0

在评价网络设计中，对5个跟随者智能体分别进行评价网络权重更新，分别收敛到最优值，看上去似乎可行，但在性能指标函数体现上，又存在代价函数小于零，并不满足要求。设计触发控制器，同样在第一篇的基础上修改，主要是在一致性误差的分析，将大部分的代码修改。 但是仿真的结果是发散的 或者 一致性误差是混乱的， 系统状态不趋于0。 认为后续调整修改的地方是

将5个跟随者智能体的评价网络权重同时更新，即单个控制器的更新仅与评价网络权重的一个参数有关，而更新一致性误差，使受约束最优控制器收敛到0。 另外，重新整理和推导文章，理清思路再进行下一步。

重新推导后，主要是之前对于一致性误差公式的理解有误，按照理论重新构建公式。在原来的框架上，引入多体的分析，主要算法使用的是VI值迭代算法。

多次调试评价网络权重发散，多数情况是来源于公式输入有误，太多重复的看错。修改了一致性误差迭代方法和过程，考虑是不是原系统的问题，再进一步修改评价网络激活函数等，对三种系统分别讨论一致性问题，主要修改的地方是误差更新

最终修改完的， 仍然按照第一篇文章的非仿射系统，分别采用的拓扑图为(无向图、有向图)
```
Simplified ADP for Event-Triggered Control of Multiagent Systems Against FDI Attacks
A novel actor–critic–identifier architecture for nonlinear multiagent systems with gradient descent method
```
结果一致性误差、系统状态、控制输入大致满足要求，完成时间触发下一般的最优跟踪一致性问题。进一步讨论在静态事件触发和动态事件触发下的问题


引入时滞调整系统动态和参数（暂不适用时滞）

引入PI算法对原算法改进，需要更新整个框架 根据文章修改框架
```
Optimal Tracking Control of Nonlinear Multiagent Systems Using Internal Reinforce Q-Learning
```

采用评价网络权重更新 与推导的理论一致 考虑系统仍为第一篇中的非仿射系统，并使用RNN辨识网络进行近似



### 第三篇
#### (有待修改)
如何将其讨论非仿射非线性多智能体，考虑受攻击下的最优一致性问题，受到FDI中的一类稀疏攻击，使用identifier-critic结构，以及引入事件触发机制，先针对不可用的状态数据，设计安全预选器输出安全数据，将安全预选器输出的数据输入到identifier中，得到近似辨识系统，辨识网络权重更新规则（？），辨识误差证明最终是收敛的
```
Simplified ADP for Event-Triggered Control of Multiagent Systems Against FDI Attacks
```


平行控制方法（ACP）
```
Event-Triggered Optimal Parallel Tracking Control for Discrete-Time Nonlinear Systems Jingwei Lu, Qinglai Wei , Senior Member, IEEE, Yujia Liu, Tianmin Zhou , and Fei-Yue Wang , Fellow, IEEE

一区 J. Lu, Q. Wei, T. Zhou, Z. Wang and F. -Y. Wang, "Event-Triggered Near-Optimal Control for Unknown Discrete-Time Nonlinear Systems Using Parallel Control," in IEEE Transactions on Cybernetics, vol. 53, no. 3, pp. 1890-1904, March 2023, doi: 10.1109/TCYB.2022.3164977.
```
对实际系统构造平行系统（虚拟空间）以预测下一时刻的状态。这篇与1中的以文章给出的事件触发条件和预先给定实际性能指标上界并证明系统的渐近稳定，有点异曲同工 


由3改进，对最优跟踪控制问题的系统状态和权重误差是UUB-->最优跟踪控制问题转化为最优整定控制问题在原点是稳定，设计两个事件触发控制器（实际跟踪控制和转化后的整定控制）
```
Event-triggered near-optimal tracking control based on adaptive dynamic programming for discrete-time systems q Ziyang Wang a, Joonhyup Lee b, Qinglai Wei c,⇑, Anting Zhang
```

```
一区 A Novel Parallel Control Method for Optimal Consensus of Nonlinear Multiagent Systems
```

```
顶刊 IEEE/CAA Journal of Automatica Sinica,  Parallel control for optimal tracking via adaptive dynamic programming
```

#### 其它 
芝诺行为：指事件触发控制（event-triggered control）中，控制在有限时间内被无限次触发。事件触发机制的缺点，一般需要证明防止芝诺行为

PE条件：输入信号应激发系统模态，能测得足够的系统信息，保证参数估计的收敛性；PE条件的改进方法，采用concurrent learning方法对评价网络权重更新。但并行学习需要模型是已知的。











## 刘其玮师兄文章学习
```
一区 IEEE TRANSACTIONS ON CYBERNETICS Data-Driven Optimal Bipartite Consensus Control for Second-Order Multiagent Systems via Policy Gradient Reinforcement Learning
```
未知二阶离散多智能体系统的最优双边一致性，竞争网络描述智能体之间竞争与合作关系，跟踪误差和性能指标函数，设计分布式策略梯度最优控制策略，保证智能体速度和状态的双边一致性，实时运行产生的离线数据，且采用异步方式处理多智能体系统中的节点。



### 多智能体仿射非线性系统，事件触发ADP分布式最优协同控制
```
二区 ISA Transactions 2019 Distributed optimal coordination control for nonlinear multi-agent systems using event-triggered adaptive dynamic programming method Wei Zhao a, Huaipin Zhang
```
事件触发条件1，存在最优值函数大于0，满足耦合HJB方程。最优性能指标和最优协同控制策略，分布式异步架构，智能体的控制策略更新，而邻居控制策略不变。结论：局部邻居一致性误差渐近稳定，局部性能指标函数等价于值函数

分布式事件触发协同控制，每个智能体可容许控制策略且其邻居的控制策略不变，PI算法使得控制策略和值函数收敛到最优值。单个评价网络近似值函数，权重更新调整协同控制策略

局部邻居一致性误差动态以及事件触发权重更新策略，值函数和控制策略定义；事件触发条件满足时，评价网络权重和控制策略更新，局部一致性误差、评价网络权重估计误差是一致最终有界的



### 其它文章分析
#### 多智能体动态事件触发和自触发
```
一区 TRANSACTIONS ON AUTOMATIC CONTROL 2019 Dynamic Event-Triggered and Self-Triggered Control for Multi-agent Systems
```


#### 多智能体动态事件触发一致性跟踪 actor-critic backstepping
```
一区 IEEE TRANSACTIONS ON CIRCUITS AND SYSTEMS—I: REGULAR PAPERS, Dynamic Event-Triggered Reinforcement Learning-Based Consensus Tracking of Nonlinear Multi-Agent Systems Bo Xu , Yuan-Xin Li , Zhongsheng Hou , Fellow, IEEE, and Choon Ki Ahn , Senior Member, IEEE
```



#### 未知多智能体输入延迟的最优一致控制
```
H. Zhang, D. Yue, C. Dou, W. Zhao and X. Xie, "Data-Driven Distributed Optimal Consensus Control for Unknown Multiagent Systems With Input-Delay," in IEEE Transactions on Cybernetics, vol. 49, no. 6, pp. 2095-2105, June 2019, doi: 10.1109/TCYB.2018.2819695.
```



