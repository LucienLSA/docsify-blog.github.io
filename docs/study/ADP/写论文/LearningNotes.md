
# 最优控制复习

## 最优性原理

多级决策过程的最优控制的性质：不论初始状态和初始决策如何，其余的决策对于由初始决策所形成的状态来说，必定也是一个最优策略

## 贝尔曼方程

给定初始时刻下的系统状态，最优控制中的性能指标为值函数，由最优性原理，最优控制满足贝尔曼方程

## HJB方程、变分法和哈密顿方程关系

### 拉格朗日变分法等价于哈密顿方程组

引入拉格朗日乘子，将等式约束优化问题转化为无约束哈密顿函数的优化问题，求解哈密顿函数的极值

### 哈密顿方程组中加入控制限制推出极值原理

### 哈密顿方程组等价于哈密顿雅可比方程

### 哈密顿雅可比方程引入控制约束推出HJB方程

### HJB方程根据值函数二次可微的特殊情况推出极值原理

## 贝尔曼方程与动态规划与HJB方程

### 贝尔曼最优性条件

以离散为例，Bellman最优性条件是：最优轨迹的任意后续弧段（tail arc）也是最优的。后续弧段指的是对于从k kk时刻出发的任意的xi , i = k , k + 1 ,..., N

$\min_{u_k\in\mathbb{U},x_k\in\mathbb{X}}\mathcal{J}=\varphi(x_N)+\sum_{k=0}^{N-1}L[x_k,u_k]\Delta t\\\mathrm{s.t.}\quad x\left(0\right)=x_0\quad x_{k+1}=f(x_k,u_k);\quad k=0,1,\cdots,N-1$

### 离散形式的动态规划法

#### 定义Cost-to-go function 和 Q-function

定义为t时刻以后，[k,N]后续弧段所需要花费的代价，$u(t)={u_k|k=0、1,...N-1}$，

$J_k^1\left(x,u\right)=\sum_{i=k}^{N-1}L\left(x_i ,u_i \right)+\varphi\left(x_N \right)$

此处的cost-to-go function 等价于 performance index，上标1代表任意的控制。使得代价最小化为optimal cost function，上标0代表最优控制

考虑下一段[k,k+1]最优性，$\begin{aligned}J_k\left(x_k\right)&=\min_{x_k,u_k,x_{k+1}}\left\{L(x_k,u_k)+J_{k+1}\left(x_{k+1}\right)\right\}\\&\mathrm{s.t.~}x_{k+1}=f(x_k,u_k)\end{aligned}$

以递归形式，表示为贝尔曼方程，$J_k\left(x_k\right)=\min_{u_k}\left\{L(x_k,u_k)+J_{k+1}\left[f(x_k,u_k)\right]\right\}$

右边式子第一项指第k个阶段的cost，第二项指未来的cost总和

定义Q-function为上式的最小化部分
$Q_k\left(x,u\right)=L(x_k,u_k)+J_{k+1}\left[f(x_k,u_k)\right],\quad x\in\mathbb{X},u\in\mathbb{U}\\J_k\left(x_k\right)=\min_{u_k}Q_k\left(x_k,u_k\right)$

#### 最优反馈控制策略

在整个状态空间和时域范围内的最优控制，任意时刻对每个状态控制对“state-control pair”，求解Q-function的极小化
$\pi_k^*\left(x\right)=\arg\min_uQ_k\left(x,u\right)$

利用控制策略，计算最优轨迹，求解最优控制策略、构造反馈控制律一般是由离线近似求解得到的；而在线应用时需要获得最优轨迹，求解最优策略，采用值迭代和策略迭代方法

在线动态规划求解最优控制
$Q_k^*\left(x_k,u_k\right)=L_k\left(x_k,u_k\right)+\min_{u_{k+1}}Q_{k+1}^*\left[f_k\left(x_k,u_k\right),u_{k+1}\right]$

#### 贝尔曼方程求解

以下三种方法求解

1. 动态规划
2. 在线求解（逐步向后计算，一般求解有限个递归内的最优化问题，预测步数大于递归层数，为MPC）
3. 离线求解（对二次型性能指标和线性系统适用，显式MPC）

## 杨氏不等式

$\mathrm{ab}\leq\frac{\mathrm{a}^{2}}{2}+\frac{\mathrm{b}^{2}}{2}$

更近一步，$\varepsilon>0$，
$\mathrm{ab}\leq\frac{\mathrm{a}^{2}}{2\varepsilon}+\frac{\varepsilon\mathrm{b}^{2}}{2}$

## 值迭代、策略迭代和广义策略迭代

![值迭代、策略迭代和广义策略迭代](https://s2.loli.net/2024/04/22/wjY5MlUrShpKF3e.png)

# 线性系统、非线性系统、自治非线性系统和非自治非线性系统

## 时不变系统稳定性

![时不变系统的稳定性](https://s2.loli.net/2024/04/10/X5nWqK3hHEAF2MD.png)

## 指数稳定

![指数稳定](https://s2.loli.net/2024/04/10/Be1QXmbrHdhinx3.png)

## 自治系统的解

![自治系统的解](https://s2.loli.net/2024/04/10/7PfG9OBrFdKZTEQ.png)

## 非自治系统的解

![非自治系统的解](https://s2.loli.net/2024/04/10/G5jg8FkcX3rt4de.png)
需要考虑初始时间对系统性能的影响

## Hurwitz稳定

线性时不变系统稳定的充分必要条件

## 输入状态稳定

对于任意有界的外部输入以及有界的状态初始条件，状态总是有界的，并且当外部输入为零时，系统总是有能力恢复到平衡点。

# K类函数和KL类函数

自治系统即时不变系统，非自治系统即时变系统。非自治系统的解与t和$t_0$都有关。改进(渐进)稳定性的定义，使其在初始时刻$t_0$一致成立。

## K类函数

函数$\alpha(r)$，定义域$r\in[0,a)$，$\alpha$为正实数，满足条件

<1>$\alpha(r)$连续且严格递增

<2>$\alpha(0)=0$

则称其为K类函数。

## $K_{\infty}$类函数

函数$\alpha(r)$，定义域$r\in[0,\infty)$，满足条件

<1>$\alpha(r)$连续且严格递增

<2>$\alpha(0)=0$

<3>$\lim_{r\to\infty}\alpha\text{(}r\text{)}=\infty$

## KL类函数

连续函数$\alpha(r,t)$，定义域$r\in[0,\infty)$，$t\in[0,\infty)$，满足条件

<1>对固定的t，函数$\alpha(r,.)$是K类函数

<2>对固定的r，函数$\alpha(.,t)$是关于t是递减的，且$\lim_{t\to\infty}\alpha\text{(}.,t\text{)}=0$

则称其为KL类函数

# 克罗内克[内积](https://so.csdn.net/so/search?q=%E5%86%85%E7%A7%AF&spm=1001.2101.3001.7020 "内积") Kronecker product

[【基础数学】克罗内克内积 Kronecker product-CSDN博客](https://blog.csdn.net/devil_son1234/article/details/127117818)

克罗内克[内积](https://so.csdn.net/so/search?q=%E5%86%85%E7%A7%AF&spm=1001.2101.3001.7020)是一种特殊的张量积

# 时滞系统

系统的变化与当前状态和过去的状态有关

# 持续激励条件PE

在线自适应动态规划算法中，需要通过满足 **持续激励条件** （Persistent Excitation Condition）保证critic网络权值的收敛性，一般的做法都是在算法初期在输入端引入探测噪声（Probing Noises）来保证数据的丰富性，但是由于在输入端引入噪声会给系统带来一些不好的影响，甚至可能破坏系统的稳定性。

以下方法来避免引入探测噪声

## Concurrent Learning（

自适应动态规划的思想主要也是一种自适应控制 ，首先构建理想的具有未知权值的Critic，Actor网络，然后通过自适应律来计算出真实的权值。

**主要思想：** 除了通过当前时刻轨迹下的误差，还预先选取一些特征点，来加入到自适应律的迭代中。在学习过程中同时使用记录数据和当前数据，保证跟踪误差的收敛性，而不需要PE条件。

不足：因为要预先记录一些特征点，所以需要模型已知，故不能扩展到无模型的自适应动态规划中。

记录当前时刻和过去一段时间内的贝尔曼误差用于权值更新。

直观来看，在自适应律的迭代中，除了应用到当前时刻轨迹的误差，还用到其他轨迹的误差来更新自适应律，相当于应用了更多信息来更新自适应律，这样会更加有利于自适应律的收敛。

因此，CL的思想其实很简单，同时也十分容易应用到自适应动态规划的在线计算中。但是需要注意的是，CL是预先选取一些特征轨迹点，这样就需要模型是已知的，不然无法得到通过模型计算的"观测值"。这个模型可以是数学模型，也可以是数据模型，如神经网络模型。

### experience replay经验重放可用于学习神经网络的权重参数，以放松PE条件需要

经验重放和并行学习思想类似，利用记录的历史数据和当前数据来估计系统的未知信息

# 控制受约束

实际系统的饱和问题，设计控制器若不考虑**执行器的饱和**，系统的动态性能会很差，甚至不稳定。

# Zeno Behavior(芝诺行为)

指事件触发控制（event-triggered control）中，控制在有限时间内被无限次触发。

当事件触发中间隔为0，导致触发时间点积累，则会存在Zeno行为

# Nash equilibrium

在Nash均衡点，每个智能体的策略是固定的，并且没有一个智能体能通过单方面改变其策略而获得更好的结果。假设每个智能体的策略集合为σ，如果对于所有的智能体i，没有任何一个智能体能通过改变其策略$σ_i$而在保持其他所有智能体策略不变的情况下获得更高的回报，那么策略组合σ就是一个Nash均衡。

# off-policy (脱策学习)

# model-free(无模型学习)

1. model-based

   - 优点：智能体利用数据进行模型拟合，将数据充分利用，智能体可以根据模型来推断智能体从未访问的区域，使得智能体与环境之间交互次数急剧减少。**数据高效性**
   - 缺点：拟合模型存在偏差，一般不能保证最优解渐近收敛
2. model-free

   - 智能体从环境中获得的数据并不拟合环境模型，而是直接优化智能体的控制。**经无数次与环境交互可保证智能体得到最优解

## Q-learning

Q函数被初始化为一个可能的任意固定值，在每次时间k，智能体选择一个控制$u_k$，得到一个效用$R_k$，进入新的状态$x_{k+1}$（取决于上一个状态和选择的控制），从而得到更新后的Q函数。

值迭代更新，使用旧值和新信息的加权平均值

$$
Q(x_k,u_k) = (1-\alpha)Q(x_k,u_k) + \alpha(R_k+\gamma min_{u}Q(x_{k+1},u{k+1}))
$$

当状态x_{k+1}是最终状态和终端状态，算法结束

1. 传统Q-learning算法（随机Q-learning算法）
2. 确定Q-learning算法

传统(随机)Q-learning算法，对更新单个状态和单个控制律进行更新，收敛性证明是人工控制的马尔可夫过程。

确定Q-learning算法，迭代$Q_i(x_k,u_k)$对所有$x_k\in X 和 u_k\in \Omega $更新，因此其每次迭代中需要状态和控制空间的所有数据。收敛性分析考虑了迭代Q函数的上下界，而不是分析迭代Q函数本身

# $H_\infty$控制

求解$H_\infty$最优控制律使得代价函数获得最优。

系统扰动$\omega$使得系统性能指标达到最大（系统性能最差），系统控制$u$使得系统性能指标达到最小（系统性能最优）。

H∞控制问题可看作零和微分对策问题，控制输入和扰动为两个参与者。

$H_\infty$的物理意义为在系统扰动使得系统性能达到最差的条件下找到系统控制律使得系统性能达到最优。

## $H_\infty$控制问题PI算法存在的缺点

1. 近似评估控制和干扰策略（而不是实际策略）产生学习其迭代值函数的数据。在线PI算法使用“不准确”的数据学习它们的代价，会增加累积误差。
2. 评价控制和干扰策略需要干扰信号可调的，对大多数实际系统通常不切实际。
3. 在线PI学习缺乏探索，只有评估测量可用于生成数据。
4. 三个神经网络分别近似迭代值函数、控制和干扰策略。
5. 大多数方法在线学习，仅使用当前数据，而丢弃过去数据，意味着所测量的系统数据仅使用一次，利用率低。

## 基于神经网络的off-policy RL 的$H_\infty$ 控制设计

### 数据处理，测量系统数据

### 离线迭代学习HJI方程

1. 控制律和扰动测量在控制域和扰动策略域上任意，产生数据的过程没有发生误差，因此累积误差可以减少。
2. 任意的控制域和扰动策略域，扰动策略不需要可调
3. 控制和干扰策略的迭代值函数可使用其他不同控制和干扰信号。off-policy优势可以更多探索或随机策略生成系统数据中学习迭代值函数和控制策略
4. 只需评价网络，计算权重向量获得控制和干扰策略的动作网络
5. 一旦计算测量数据，不需要额外数据学习$H_\infty$，收集到数据集可重复利用。

## 事件触发ADP存在受限控制和扰动的$H_\infty$ 控制设计

事件触发受限控制输入和时间触发扰动输入。

# 鲁棒控制(Robust Control)

鲁棒控制分为匹配不确定性和匹配不确定问题，最优控制方法设计鲁棒控制器，定义包含两个不同效用函数的值函数(系统性能和扰动性能)，最优控制和扰动使得值函数最小。

# 安全ADP

受扰动和约束下的ADP和安全策略迭代方法，求解离散时间非线性系统的最优控制问题

# 多智能体致性控制任务分类

## 有无领导者

1. 一致(同步)问题(无领导者)
2. 分布式跟踪问题(一个领导者)
3. 分布式包含问题(多个领导者)

## 动力学模型

1. 一阶积分器系统
2. 二阶积分器系统
3. 一般线性系统
4. Lagrangian系统
5. 一般非线性系统
6. 同构/异构动力学

## 图论

### 介绍

假定智能体之间交换信息，采用有向图和无向图，描述智能体之间的信息交换。

### 拉普拉斯矩阵

邻接矩阵

1. 定义有向图的邻接矩阵的某个元素$A_{ij}$
   - 从i指向j的边的数目
   - 从j指向i的边的数目
2. 入度矩阵是一个对角矩阵，对角线元素为终点在这个节点的边的条数。有向图的入度矩阵对角线元素是邻接矩阵对应行和。

## 互联系统
互联系统，由于系统结构的复杂性和子系统之间的相互作用，需要局部可用的子系统信息进行分散控制

## 异构和同构多智能体
异构多智能体系统是系统描述的一般形式，异构多智能体系统允许智能体的结构和维度不同，同构多智能体为特殊形式。智能体之间在维度、动态和结构上不同的异质性使得异构多智能体系统在信息感知、容错和交互能力方面优于同构多智能体系统。

### 切换拓扑
对异构多智能体切换拓扑会产生状态跳变，影响神经网络权重收敛，造成单个智能体ADP方法不可用，本文针对每种拓扑结构设计切换的ADP方法，分别设计不同神经网络，以避免切换状态突变对收敛性的影响，

## 包含控制
包含控制的目标确保从每个跟随者的输出最终到达由多个领导者组成的凸包中

### 有限时间控制(finite-time)
所设计的控制策略在有限时间内达到所需的控制目标，并确保系统轨迹迅速从暂态响应达到稳态响应




## 一阶二阶多智能体一致性控制

### 一阶智能体

一阶积分器模型描述智能体

![image.png](https://s2.loli.net/2024/05/07/H3ka2DC8VQbrBhl.png)

一阶积分智能体网络提出一种一致性控制器

![image.png](https://s2.loli.net/2024/05/07/GC5wTUsSbvMHKNr.png)

无向图是连通的，一阶积分器网络能实现一致；有向图包含一个有向生成树是智能体网络实现一致的充要条件

智能体的状态趋于一致的终值是由智能体状态和初值和通信拓扑图共同决定的

一致性算法python见项目文件demo

### 二阶智能体

二阶积分器模型的智能体动力学模型

![image.png](https://s2.loli.net/2024/05/07/1bOILWkAJBG6acm.png)

一致性算法python见项目文件demo

1. 欧拉法求解微分方程
2. matlab自带的ode45解法求解微分方程，精度更高

## 多智能体协同控制一致性问题

多智能体一致性问题可转换为多智能体跟踪控制问题，每个智能体i分别设计自己控制律$u_i(k)$，使所有跟随者能追踪领导者的状态轨迹$x_0(k)$

![image.png](https://s2.loli.net/2024/05/06/Tptm3Zo9VPr65Hi.png)

定义离散时间多智能体系统每一个智能体i定义一致性跟踪误差

![image.png](https://s2.loli.net/2024/05/06/kfeipRdBbqAz5ux.png)

$a_{ij} \geq 0$是邻接矩阵的元素，表示智能体之间关系，$a_{ij} > 0$表示智能体j是智能体i邻居，且智能体i可获得智能体j的信息

当$b_i>0$，智能体i可以获得领导者的信息

智能体i与领导者的跟踪误差

目标对每个智能体i找到一个控制律，保证智能体性能指标最小，保证当k趋于无穷大，跟踪误差趋于0，跟踪上领导者的轨迹。

## 事件触发机制下多智能体一致性问题

智能体i当前一致性跟踪误差 $e_i(k)$，智能体i被触发时一致性跟踪误差 $\hat e_i(k_{i,p})$。

定义当前一致性跟踪误差与事件触发时的一致性跟踪误差的差为事件触发误差$\delta_i(k)=\hat e_i(k_{i,p})-e_i(k)$

目标针对智能体i求控制律$u_i(\hat e_i(k_{i,p}))$，使智能体性能指标函数最小，同时保证k趋于无穷大，跟踪误差趋于0，即跟踪上领导者的轨迹。

# ADP在多智能体系统最优协同控制中的应用

介绍 ADP方法在 MAS 协同控制中的应用。最优协同控制问题包括同构MAS最优一致性控制(一般考虑状态达到一致)、异构MAS最优输出同步和最优输出调节问题、MAS $H_\infty$控制问题以及ADP在无人集群系统最优控制研究中的应用等，首先介绍图论的相关知识.

考虑如下无向图$\mathcal{G}=(\mathcal{I}_N,\mathcal{E})$,包含$N$个智能体智能体指标集为$\mathcal{I}_N=\{1,2,\ldots,N\}$,边集为$\mathcal{E}=$ $\{(i,j){:}.i,j\in\mathcal{I}_N\}\subseteq\mathcal{I}_N\times\mathcal{I}_N.(i,j)\in\mathcal{E}$表示智能体$i$与$j$可以相互传递信息$.\mathcal{A}=[a_ij]\in\boldsymbol{R}^{N\times N}$表示邻接矩阵，如果$(i,j)\in\mathcal{E}$且$i\neq j$,则$a_ij=1$,否则$a_ij=$ 0.智能体$i$的邻居集定义为$\mathcal{N}_i=\{j\in\mathcal{I}_N:(i,j)\in$ $\mathcal{E}\}.$度矩阵为$\mathcal{D}=$diag$(d_i)$,其中$d_i=\sum_i\in\mathcal{N}a_{ij}$,则拉普拉斯矩阵定义为$\mathcal{L}=\mathcal{D}-\mathcal{A}.$

## ADP与多智能体系统最优一致性问题

一致性问题旨在通过设计一组分布式控制协议， 使得所有智能体的状态趋于相同.一般而言，一致性问题可以从多角度划分为：无(有)领导者的一致性控制、分组一致性(group consensus)、有限时间一致性 (finite-time consensus)和均方一致性(mean-square consensus)等. 最优一致性问题是研究通过设计一组分布式控制协议，使得所有(对应组)智能体的状态趋于相同且每个智能体优化一个局部性能函数.下面以有领导者的MAS最优一致性控制问题为例进行详
细介绍.

Vamvoudakis 等$^{[45]}$考虑了包含$N$个智能体的线性 MAS,每个智能体的动力学为

$$
\dot{x}_i(t)=Ax_i(t)+B_iu_i(t),\:i\in\mathcal{I}_N.
$$

其中：$x_i( t)$ $\in$ $\boldsymbol{R}^{n}, u_{i}( t)$ $\in$ $\boldsymbol{R}^{m}, A$ $\in$ $\boldsymbol{R}^{n\times n}, B_{i}$ $\in$

$\boldsymbol{R}^{n\times m}$,分别为第 $i$ 个子系统的状态、控制输入和系统

矩阵. 智能体之间通过无向连通图$\mathcal{G}$连接.领导者系统的动力学为

$$
\dot{x}_0(t)=Ax_0(t),
$$

其中$x_0(t)$为领导者状态。

定义智能体$i$ 的局部一致性误差

$$
\varepsilon_i(t)=\sum_{j\in\mathcal{N}_i}a_{ij}(x_i(t)-x_j(t))+g_i(x_i(t)-x_0(t)).
$$

若考虑无领导者的最优一致问题，则有$g_i=0,\forall i.$ 根据式(72)和(73)得到第$i$个局部一致性误差动力学为

$$
\dot{\varepsilon}_i(t)=A\varepsilon_i(t)+(d_i+g_i)B_iu_i(t)-\sum_{j\in\mathcal{N}_i}a_{ij}B_ju_j(t).
$$

对于智能体$i$,给出如下合作型性能指标：

$$
J_i(\varepsilon_i(0),\varepsilon_{-i}(0),u_i(\cdot),u_{-i}(\cdot))=
$$

$$
\frac12\int_0^{+\infty}\left(\varepsilon_i^\mathrm{T}(t)Q_i\varepsilon_i(t)+u_i^\mathrm{T}(t)R_iu_i(t)+\right.
$$

$\sum_{j\in\mathcal{N}_i}a_{ij}u_j^\mathrm{T}(t)R_{ij}u_j(t)\bigg)$d$t.$

其中：$Q_i$、$R_i$ 为正定矩阵，$R_ij$ 为半正定矩阵$,\varepsilon_-i(t)=\{\varepsilon_{j}(t):j\in\mathcal{N}_{i}\},u_{-i}(t)=\{u_{j}(t):j\in\mathcal{N}_{i}\},\mathcal{N}_{i}$为智能体$i$所有邻居智能体组成的集合.固定智能体$i$和其邻居们的策略，相应的智能体$i$的值函数记为

$$
\begin{aligned}&V_i(\varepsilon_i(t),\varepsilon_{-i}(t))=\\&\frac12\int_t^\infty\left(\varepsilon_i^\mathrm{T}(\tau)Q_i\varepsilon_i(\tau)+u_i^\mathrm{T}(\tau)R_iu_i(\tau)+\right.\\&\sum_{j\in\mathcal{N}_i}a_{ij}u_j^\mathrm{T}(\tau)R_{ij}u_j(\tau)\Big)\mathrm{d}\tau.\end{aligned}
$$

假设$\mathbf{1}$ $V_i( \varepsilon _i( t) , \varepsilon _{- i}( t) )$是分布式的，即$V_i(\varepsilon_i(t),\varepsilon_{-i}(t))=V_i(\varepsilon_i(t)).$

定义4 如果策略$\left(u_i^*(t),u_{-i}^*(t)\right)$满足$J_i(u_i^*(t)$,

$u_{- i}^* ( t) )$ $\leqslant$ $J_i( u_i( t) , u_{- i}^* ( t) )$,则该策略称作一个Nash均衡解.

问题1 考虑线性MAS(72),最优一致性追踪问题的控制目标是设计一组分布式控制策略$u_i(\cdot)$,使得MAS的状态达到一致且最小化一组局部合作性能函数.

基于假设 1, 最优值函数定义为$V_i^*(\varepsilon_i(t))=\min_{u_i(\cdot)}V_i(\varepsilon_i(t))$。哈密顿函数定义如下

$H_{i}(\varepsilon_{i}(t),u_{i}(t),u_{-i}(t),\nabla V_{i}(\varepsilon_{i}(t)))=$

$$
\nabla V_i^\mathrm{T}(\varepsilon_i(t))\dot{\varepsilon}_i(t)+\frac{1}{2}\Big(\sum_{j\in\mathcal{N}_i}a_{ij}u_j^\mathrm{T}(t)R_{ij}u_j(t)+
$$

$$
\varepsilon_i^\mathrm{T}(t)Q_i\varepsilon_i(t)+u_i^\mathrm{T}(t)R_iu_i(t)\Big),
$$

其中$\dot{\varepsilon}_i(t)$在式中给出，然后，通过求解

$$
\frac{\partial H_i(\varepsilon_i(t),u_i(t),u_{-i}(t),\nabla V_i(\varepsilon_i(t)))}{\partial u_i(t)}=0,
$$

得到最优控制策略

$$
u_i^*(t)=-(d_i+g_i)R_i^{-1}B_i^\text{T}\nabla V_i^*(\varepsilon_i(t)),
$$

$V_i^*(\varepsilon_i(t))$满足如下耦合HJB方程：

$$
\begin{aligned}&\frac{1}{2}(d_{i}+g_{i})^{2}\nabla V_{i}^{*\mathrm{T}}(\varepsilon_{i}(t))B_{i}R_{i}^{-1}B_{i}^{\mathrm{T}}\nabla V_{i}^{*}(\varepsilon_{i}(t))+\\&\frac{1}{2}\sum_{j\in\mathcal{N}_{i}}(d_{j}+g_{j})^{2}\nabla V_{j}^{*\mathrm{T}}(\varepsilon_{j}(t))B_{j}\tilde{R}_{ij}^{-1}B_{j}^{\mathrm{T}}\nabla V_{j}^{*}(\varepsilon_{j}(t))+\\&\nabla V_{i}^{*\mathrm{T}}(\varepsilon_{i}(t))A_{i}^{c}(t)+\frac{1}{2}\varepsilon_{i}^{\mathrm{T}}(t)Q_{i}\varepsilon_{i}(t)=0.&\text{(79)}\end{aligned}
$$

其中

$$
\begin{aligned}&\tilde{R}_{ij}^{-1}=R_{j}^{-1}R_{ij}R_{j}^{-1},\\&A_{i}^{c}(t)=\\&A\varepsilon_{i}(t)-(d_{i}+g_{i})^{2}B_{i}R_{i}^{-1}B_{i}^{\mathrm{T}}\nabla V_{i}^{*}(\varepsilon_{i}(t))+\\&\sum_{j\in\mathcal{N}_i}a_{ij}(d_j+g_j)B_jR_j^{-1}B_j^\mathrm{T}\nabla V_j^*(\varepsilon_j(t)).\end{aligned}
$$

# HDP的类方法

## 传统HDP的Actor-Critic框架

https://blog.csdn.net/weixin_44231148/article/details/107241543

![传统HDP](https://s2.loli.net/2024/04/12/tbOyx4NlegpQmwu.png)

扩展的HDP在传统的HDP基础上，又添加了一个Critic Network(这个和RL中2013版DQN与2015版DQN的区别类似！对于这两个网络的作用，个人理解同RL中的思想一致：因为每次更新后，Critic Network的参数得以更新，导致该网络的Lable发生变化，从而使得训练不稳定。)每隔C次循环，将上面的Critic Network参数复制到下面的Critic Network。

## 扩展HDP的Actor-Critic框架相同

![扩展HDP](https://s2.loli.net/2024/04/12/dhOWKC6tFzH1J8I.png)

误差表示为$e=\hat J_{\text {下}}(x(k+1))-J_{\text {上}}(x(k+1))$

其中$\hat J_{\text {下}}(x(k+1))$为评价网络的实际输出，$J_{\text {上}}(x(k+1))$为Label。贝尔曼最优性原理

$J_{\text {上}}(x(k+1))=J_{\text {上}}(x(k))-U(x(k),u^{*}(k))$

对上面的评价网络和动作网络进行更新

在generalized_HDP.py中的Actor-Critic框架，上面的评价网络V1为标签label，计算其梯度。下面的评价网络V2实际输出

## GrHDP

### 主要思想

仅需要当前和过去的数据，不需要明确的系统模型

内部增强信号包含未来外部增强信号的信息，使得智能体获得更多未来的信息,GrHDP能提升控制目标的性能。

目标网络和评价网络连接起来，评价网络的输入包含内部增强信号，估计控制策略的性能指标

从环境得到的外部增强信号（仅一种增强信号），为评价网络提供信息。内部增强信号由控制器产生的一种新的增强信号。

智能体仅收到自身和邻居的信息，并将邻居的控制信号包含到每个智能体的外部增强信号中，以使智能体与其邻居相连接。基于外部强化信号设计内部强化信号以促进学习过程。
内部增强信号的ADP算法更新控制策略到达最优。

### GrHDP结构与传统Actor-Critic/HDP结构关系

传统ADP智能体知道立即强化信号，或根据系统状态和动作计算立即强化信号，传统HDP仅提供单一的外部强化信号到智能体。加入参考/目标网络到传统ADP网络结构中，目标网络产生内部增强信号。

内部增强网络/目标网络(RNN)输出内部增强信号奖励函数(IRR)。评价网络(CNN)从目标网络(RNN)加入IRR；RNN误差函数与内部增强信号(IR)有关，而CNN误差函数与内部增强信号奖励函数(IRR)有关

### 多智能体的GrHDP算法

1. 初始可容许控制策略
2. 由迭代控制策略更新内部增强信号，计算内部增强信号
3. 迭代性能指标更新
4. 迭代计算控制策略
5. 判断收敛得到最优控制策略和最优性能指标

## (Internal Reinforce Q-learning)IrQL

长期增强奖励信号称为Internal reinforce reward signals(IRR)，即Internal reinforce signals(IR)的累积信息。根据IRR函数定义Qfunction，以评估每个智能体的控制性能指标，构造IrQL的HJB方程，计算每个智能体的分布式控制

### Intrinsic Motivation(IM)

IM能够提高动作行为能力或解决探索环境的能力，IM以驱动智能体学习行为，而不是根据外部环境提供的奖励。

## 多智能体的时滞问题

时间滞后(Time Delay)是智能体与邻居智能体通过通信拓扑进行信息交换出现的固有特征。
**输入延迟**，**输出延迟**，**通信延迟**，影响控制过程和性能。

# 平行控制

## ACP概念

使用如神经网络等方法构造人工系统，对实际系统进行建模；计算实验中多种智能控制方法获取控制策略；平行控制将实际系统和人工系统联系起来，对计算实验中的控制策略根据实际系统和人工系统的差异，进一步评估和优化。

## ACP思想

被控系统和控制策略为微分方程的动态，即将代数方程转变为微分方程。

# IRL(Integral RL)

根据贝尔曼方程，对初始可容许控制策略进行策略评估，根据评估结果改进控制策略，重复这两个步骤直到收敛
