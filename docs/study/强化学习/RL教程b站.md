
# 强化学习算法介绍

## 视频来源

## 基础介绍

## 马尔可夫链

S A R三个元素

1. 智能体在环境中观测到状态S
2. 状态S被输入到智能体，经过计算，选择动作A
3. 动作A使智能体进入另一个状态S，并返回奖励R给智能体
4. 智能体根据返回调整自己策略，重复以上步骤

不确定性：（智能体的选择、环境的不确定性）

* 选择过程，智能体选择会影响到下一个状态，不同动作之间选择即称为智能体的策略
* 环境随机性，智能体无法控制，action返回新的状态或奖励也可能不同

## Q值和V值

奖励有正有负，作为智能体学习的引导，期望智能体获得更多奖励。长远看待，将未来奖励计算到当前状态

* 评估动作价值称为Q值，代表智能体选择动作后，直到最终状态奖励总和的期望
* 评估状态价值称为V值，代表智能体状态下直到最终奖励总和的期望

价值越高，当前状态到最终状态的平均奖励越高，当前状态选择价值高的动作

### V值

从某个状态按照策略，走到最终状态很多很多次；最终获得奖励总和的平均值

### Q值

从某个状态选取动作A，走到最终状态很多很多次，最终得到奖励总和的平均值

Q值和策略没有直接相关，而与环境的状态转移概率相关，环境的状态转移概率是不变的

### V值和Q值

Q值和V值都是马克洛夫数上的节点；

价值评价的方式一样，从当前节点出发，一直走到最终节点，所有的奖励的期望值

* V是子节点的Q的期望，V值和策略相关
* Q是子节点的V的期望，需要将R计算在内

## 蒙特卡洛采样

1. 根据策略往前走，走到最后，期间记录每一个状态转移，和获得的奖励r
2. 从终点往前走，一遍一遍计算G值，G值等于上一个状态的G值(G')乘以一定折扣(gamma)，再加上奖励r

折扣率为超参数，折扣率将未来很多步奖励，折算到当前节点。

G值：某个状态到最终状态的奖励总和。

V值：

* 某个状态下，通过影分身到达最终状态，所有影分身获得的奖励的平均值（回溯到这个状态，每条轨迹下都有一个G值，对其求平均）。
* V值是G值的平均。V值和策略是相关的。

### 蒙特卡洛缺点

每次都需要从头到尾，再进行回溯更新，如果最终状态很难到达，则需要每一次转很久才能更新一次G值

## 蒙特卡洛估算状态V值

### GBDT算法(梯度提升决策树)

构造一组弱的学习器(树)，把多颗决策树的结果累加起来作为最终的预测输出

#### Boosting核心思想

1. 训练基分类器采用串行，各基分类器之间有依赖，将基分类器层层叠加，每一层训练时对前一层基分类器分错的样本，给予更高权重。根据各层分类器的结果的加权得到最终结果。

Bagging采用并行训练，各基分类器之间无依赖。

#### GBDT原理

1. 所有弱分类器的结果相加等于预测值
2. 每次都以当前预测为基准，下一个弱分类器去拟合误差函数对预测值的残差（预测值与真实值之间的误差）
3. GBDT的弱分类器使用的是树模型

#### 负梯度近似误差

损失函数为均方误差损失函数，每次拟合真实值-预测值，即残差

![image.png](https://s2.loli.net/2024/05/17/d6mWZnUrzMpJtAK.png)

损失函数的负梯度近似误差

![image.png](https://s2.loli.net/2024/05/17/EaXP4TrCQeLWkVR.png)

GBDT将多棵树的得分累加得到最终预测的得分，且每轮迭代，在现有树的基础上，增加一棵新的树去拟合前面的树的预测值与真实值之间的残差。

#### 梯度提升与梯度下降

都是利用损失函数的负梯度方向信息，更新当前模型

1. 梯度下降，模型以参数化形式
2. 梯度提升，模型不需以参数化形式，直接定义在函数空间中

### 新平均=旧平均+步长*(新加入元素-旧平均)

![image.png](https://s2.loli.net/2024/05/17/4BpzZxNEoslGJQ6.png)

$+\alpha(G_{t}-V(S_t)) = -\alpha(y-\hat y) = \alpha(\hat y - y)$

## TD(时序差分)估算状态V值

在蒙特卡洛方法上改进

1. TD算法只需要走N步，开始回溯更新
2. 先走N步，每经过一个状态，把奖励记录下来，开始回溯
3. V值计算。假设在N步之后，到达最终状态。假设“最终状态”之前没有走过，状态为0；假设“最终状态”已经走过，状态V值为当前值。再开始回溯

### TD估算V值

蒙特卡洛中，G是更新目标，TD中，r+gamma*V是更新目标

![image.png](https://s2.loli.net/2024/05/17/OZbR97P6jE5xfh4.png)

## SARSA算法

使用TD预估Q值，智能体看作一个Qfunction，输入为St和At。

### SARSA思想

同一个策略下产生的动作A的Q值替代V(St+1)

![image.png](https://s2.loli.net/2024/05/17/bXF5KEWxmaQ3YPH.png)

与TD方法估算V值相比，SARSA将V换成Q

### SARSA LambdaTable

引入Lambda=trace_decay

引入eligibility_trace记录对应观测状态state对应的action位置，仅仅记录轨迹。而不是Qtable中记录的Q值。但两者的形状是同步一致的。

## Qlearning算法

选择能获得最大Q值的动作，用所有动作的Q值的最大值替代V(St+1)

Qlearning与SARSA差一个max

## Q table

初始化Q表的矩阵，通过Qlearning学习调整，反复迭代最优解Qtable。在未来预测智能体时，根据不同的动作得到Q值（查表），选择最大值得动作，作为智能体得下一步动作。

## DQN

Qtable适合离散得格子游戏，但实际很多状态是连续的。Qtable作用找S-A的对应关系，函数取连续状态，则F(S)=A

DQN用神经网络函数代替Qtable

![image.png](https://s2.loli.net/2024/05/17/isP1vZ3nYImjyAC.png)

DQN的实现

1. 执行A，往前一步，到达St+1
2. St+1输入Q网络，计算St+1所有动作的Q值
3. 获得最大的Q值加上奖励R作为更新目标(R+maxQ(St+1))
4. 计算损失-Q(S,A)相当于有监督学习中拟合的机率
5. maxQ(St+1)+R相当于有监督学习中标签labels,y_true
6. MSE函数，得到两者的loss
7. 用损失更新Q网络

![DQN](https://s2.loli.net/2024/05/17/4PfyiMxlNOmoZ5a.png)

## 贪婪Greedy

Epsilon Greedy  (epsilon在学习过程中进行衰减)

$argmax_{a}Q(s,a)$，概率1-epsilon；$random$，概率epsilon

## 回放缓存（经验回放）Replay Buffer

每一步s，选择a，进入新的状态s'，获得的奖励r，新状态是否为终止状态。将数据全部存放在回放缓存

设定batch size，抽取一个batch的数据，将其放入网络进行训练。训练之后继续，把新产生的数据添加到回放缓存中。即以前产生的数据同样用来训练数据。

经验回放使训练更高效，也减少训练产生的过度拟合问题。

off-policy，学习数据不是来于正在训练的，而是有一些采样来自之前模型产生的数据

## Fixed Q-targets

DQN目标：$R+\gamma * maxQ(s',a)$，但Q网络为更新和调整的参数，则会造成Q网络学习效率低，且不稳定

采用两个Q网络：一个原来的Q网络用于估算Q(s)，另一个targetQ网络，targetQ网络自己不会更新，它在更新过程中是固定的，用于计算更新目标。

N次更新后，将新Q的参数赋值给旧Q

![image.png](https://s2.loli.net/2024/05/17/sJgkQ9UvBH4G1xi.png)

## Double DQN

由于DQN估计的Q值往往会偏大，即高估Q值。

DQN中的，target(R+maxQ(St+1,a))和Q(S,A)计算损失loss，得到Q网络的更新，其相当于自我监督；

![image.png](https://s2.loli.net/2024/05/18/FgCWfAsL1yt9GRJ.png)

Double DQN用两个Q网络(Q,Q')，选取评估较小的值计算目标，避免Q值的过高。（但如果Q'也高估，则该动作不能用于Q值更新）

Q1网络能推荐获得最大Q值的动作，Q2网络计算这个动作在Q2网络中的Q值，如果Q2网络的Q值没有被高估，则为恰当的值。恰好用Fixed Q-targets，则表示两个网络

## Dueling DQN

原DQN直接预估Q值：需要更新某个动作Q值，直接更新Q网络，Q值提升

Dueling DQN 预估S值和A值：网络更新时，由于A值之和必须为0的限制，网络会优先更新S值，S值是Q值的平均数，平均数调整相当于一次性S下所有Q值都进行更新。则网络更新时，不仅更新某个动作的Q值，而且在这个状态下，所有动作的Q值都调整一次。以更少的次数进行更多Q值的更新

![image.png](https://s2.loli.net/2024/05/17/HisUNbFQcYRnGZd.png)

Dueling DQN中优先调整S值，但最终Target目标没有变。

## Prioritized Experience Replay

预测不好的data，通过Q网络选择不好的action，其被称为困难样本。困难样本会有更大的TD误差，具有更高被采样的可能性去学习，放在Experience Buffer中学习困难样本。

## Multi-step

蒙特卡洛和时序差分之间平衡，对TD来说等于往后多走了几步，方差会比较大。但不能多走太多，可能会导致reward的加和问题。

## Noise Net

### Noise on Action(Epsilon Greedy)

随机地尝试动作：相同状态，智能体采取不同的动作

### Noise on Parameters

有系统地尝试动作：相同或相似状态，智能体采取相同动作，以状态依赖形式探索，更具有针对性地探索。

Noise Net则将噪声加入到每一步开始地Qfunction参数中，且每一步噪声不会改变

## Distributional Q-function

原来Q值为期望值，不同分布可能会有同样的期望，如果选择期望较大时，其可能对应方差恰巧很大，则action会风险很高。

## Continuous Actions

action选取为连续的值。

### Solution1

采样一组动作，寻找使得Q值最大的动作（采样时即进行离散化）

### Solution2

梯度下降求解最优化问题

### Solution3

设计神经网络使最优化更简单，网络调参，收敛时得到更好的u

![image.png](https://s2.loli.net/2024/05/18/EhNPBdYuevAnUOK.png)

## Value-based and Policy-based

1. value-based 利用reward奖励，在某些状态下选择好的行为将Q值提高或降低
2. policy-based 利用reward奖励，网络调参，在选择某种行为的概率提高或降低。即若智能体动作正确，则让这个动作获得更多被选择的概率。

## 策略梯度(PG)

### 基本思想

直接训练神经网络输入state输出action，该过程不用计算Q值

### 策略梯度原理

传统使用Q table查表或Q网络通过Critic评判，以选择action。

策略梯度直接使用actor去跟环境互动，然后一个回合加得一个total reward 总奖励。策略梯度最大化整一个回合之后的total reward。而对同一个actor，total reward每次大概率不会相同，actor和environment都存在随机性，采样同样的action，每次的observation也会不一样。最大化total reward期望值，其可衡量actor的好坏，以下为计算过程。

![image.png](https://s2.loli.net/2024/05/18/i4dD1IAtvbsUVJg.png)

对$\theta$求导进行梯度优化，目标函数为total reward，调整$\theta$找到动作使total reward尽可能大

![image.png](https://s2.loli.net/2024/05/18/gT3biDMyfkm6xNU.png)

使用$\pi _{\theta}$进行N次回合，得到轨迹，每条轨迹对应状态、动作和奖励

### 策略梯度的公式简化

total reward 函数
![image.png](https://s2.loli.net/2024/05/18/usBW3brVZj7K6qz.png)

total reward 导函数
![image.png](https://s2.loli.net/2024/05/18/QqMSjDgr64LCs3t.png)

total reward 为正，则更新模型使得机率越大越好

总体流程

![image.png](https://s2.loli.net/2024/05/18/GrDQOYuFM7Jp2ta.png)

### Trick1 Baseline

当理想情况下，采样的轨迹的奖励大于零，鼓励该行为，但是可能某个行为没有被采样。

![image.png](https://s2.loli.net/2024/05/18/wfRYQV5ETKXAd19.png)

平均归一化，使得total reward小于0，对应轨迹包含的行为减小概率

### Trick2 Suitable Credit

在不同的action前面乘上不同的weight，增加折扣因子。

衡量对错，策略梯度采样蒙特卡洛的G值，让智能体一直走到最后，然后通过回溯计算G值

## Actor-Critic

更好的权重更新方式，在t时刻状态选择某个action，进行打分（评价reward），则该评分函数称为critic

Actor网络和Critic网络的共同点：都输入状态S。输出策略以选择动作，称为actor(policy-based);计算每个动作分数（Q值），称为critic(valued-based)

![image.png](https://s2.loli.net/2024/05/19/o1yBiN5f9b82h3r.png)

## Actor整合Critic

蒙特卡洛需要完成整个过程，到最终状态，才能回溯计算G值，效率较低。而使用时序差分（TD），不用回溯，每一步立即估算Q值。

![image.png](https://s2.loli.net/2024/05/19/9ECplxSkcNVdJ8s.png)

Q值定义为action之后获得的奖励期望，Baseline是Q值求平均（Q值的期望），则Q值得期望正好为V值

1. 状态值函数V：使用动作$\pi$，状态s后得到累积奖励
2. 状态动作值函数Q：使用动作$\pi$，状态s执行动作a后得到累积奖励

### TD估算每一步的Q值，神经网络

## Advantage Actor Critic(A2C)

actor用Q(s,a)-V(s)指导更新，但两个网络估算Q和V，风险更大。

Q(s,a)用$r+gamma*V(s')$代替，将$r+gamma*V(s')-V(s)$这个差作为TD-error

TD-error采用V，更新Critic的loss，而DQN中采用Q进行更新

![image.png](https://s2.loli.net/2024/05/19/GyR4cmThiHvqKQg.png)

### Trick 1 shared parameters

对状态信息进行共享输入特征提取，即在输入到Critic网络和Actor网络之间，加入的一层网络

### Trick 2 修改reward

希望智能体在濒死状态下“力挽狂澜”，进行更多尝试。将reward减去20，表示在濒死状态下对选择动作a的不认同，即减去20以大大降低动作a出现的概率。同时reward向前传播，避免进入濒死状态，不选择进入濒死状态的动作

## Asynchronous Advantage Actor-Critic(A3C)

基于异步和分布式思想，有多个智能体与环境交互，每个智能体都产生数据，进行学习。

![image.png](https://s2.loli.net/2024/05/19/JAbjEB3xon1QV7r.png)

Global network 和 worker都为AC网络，worker不仅和环境互动，产生数据，并将自己从数据里计算到的梯度，汇总给全局网络(global networl)，应用在全局网络参数进行更新，而不是worker自己探索出来的数据。

## deep deterministic policy gradient(DDPG)

DDPG输出的直接为一个动作，用一个actor去弥补DQN不能处理连续性控制问题的缺点。因为DQN中的maxQ(s',a')只能处理离散型

DQN中神经网络解决Qlearning不能解决连续状态空间问题，DDPG中神经网络解决DQN不能解决连续动作空间问题。

DDPG中actor功能：输入状态s,magic函数返回动作action的取值，使得Critic输出的Q值最大。即某个状态下，选择某个动作值时能获得的Q值。

DDPG并不是PG，没有带权重的梯度更新，而是梯度上升寻找Q的最大值。

1. DDPG的Critic网络的作用是预估Q值，与AC中的Critic不同。Critic的输入有动作和状态。Critic的loss跟AC一样，TD-error
2. DDPG的actor是输出一个动作，该动作A输入到Critic后，能最大化Q值。DDPG中的actor的更新方式与AC中的不同，不是带权值的梯度更新，而是梯度上升最大化Q值。Q值最大化为目标函数

DDPG和DQN一样，也采用固定网络(Fix Network)，先保持用来求target的网络，在更新之后，把参数赋值到target网络中。实际需要四个网络：actor critic actor-target critic-target

## (Twin Delayed Deep Deterministic policy gradient algorithm)TD3 双延迟深度确定性策略梯度，DDPG的优化版本

## Double Network

DDPG也存在Q值被高估的问题：argmaxQ(s')代替V(s')，去评估Q(S)。TD3则是通过选择相对较小的作为更新的目标。

TD3需要6个网络，目标网络中，估算出来的Q值用min()函数求出较少值，以这个值作为更新的目标，这个目标会更新两个网络Critic1和Critic2，两个网络是完全独立的，学习好的网络赋值给目标网络。

1. Critic网络学习：计算Critic更新目标，才用target network，包括一个policy network，用于计算A'；两个Q network，计算两个Q值：Q1(A')和Q2(A')。Q1(A')和Q2(A')取最小值min(Q1,Q2)，代替DDPG的Q(a')计算更新目标，target=min(Q1,Q2)*gamma+r。由于网络参数初值不同，导致计算值不同，有空间选择较小的值估算Q值，避免被高估。
2. Actor网络，梯度上升以学习Q值最高的action。actor并不在乎Q值被高估

## Delayed Actor Update

actor更新的delay，相对于critic更新多次后，actor再进行更新。

## target policy smoothing regularization

TD3，给actor目标网络输出的action增加noise。给actor进行进行更多的探索，为给后面的critic目标网络增加难度。即在有输入干扰情况下，也给出正确分值，critic变得更加robust，相当于数据增强，提高模型的泛化能力

## Proximal Policy Optimization(PPO)

AC架构可解决连续动作空间问题。

### AC输出连续动作

离散和连续动作输出

![image.png](https://s2.loli.net/2024/05/20/ZcdEijl57yeg4D3.png)

### On-policy和Off-policy

策略梯度PG是在线策略，用于产生数据的策略（行为策略），与需要更新的策略（目标策略）是一致的

DQN是离线策略，让智能体与环境互动一定次数，获得数据。用这些数据优化策略后，继续跑新的数据，但以前的数据仍可以用。产生数据的策略和更新的目标策略不是同一个策略。

### Important Sampling

PPO离线更新策略，重要采样技术。TD-error乘以重要性权重

PPO适用范围很广，较为稳定

重要性权重(IW)：目标策略（更新策略的网络）出现动作a的概率 除以 行为策略（产生数据的网络）出现a的概率

### Issue of Importance Sampling

即使乘以importance weight 对Q的期望u进行修正，方差可能不同。采样次数不多，方差不一样，差距越大，则采样可能会得到非常大的差距。

### Add Constraint

限制两个分布差距不能太大

PPO1中使用KL散度（相对熵）衡量两个分布的差距。 KL散度为一种衡量两个概率分布的匹配程度的指标，两个分布差异越大，KL散度越大。在PPO1中作为惩罚项

KL计算参数使得行为action表现上面的距离，即策略的距离。策略就是action上面的几率分布。

![image.png](https://s2.loli.net/2024/05/20/pVv2AkyYLjifJha.png)

## TRPO

KL散度作为约束项

## PPO2

直接裁剪更新的范围，比较两项中更小的。

## DPPO

A3C中需要worker跟环境进行互动，用产生的数据计算梯度，再调整全局网络中的参数。AC在线算法，产生数据的策略和更新的策略为同一个网络。不能将worker产出的数据，直接给全局网络计算梯度使用。

PPO解决离线更新策略问题，DPPO的worker只需要提供数据给全局网络，由全局网络从数据中直接学习。

DPPO中使用多线程学习，当全局网络学习时，workers需要等待全局网络学习完，才能干活；workers干活时，全局网络需要等待worker提供数据

比如用两个事件，解决两组不同线程的通信问题。我们有两组线程A，B，要求当A运行时，B等待；当A运行完毕，B开始运行，A等待。虽然有两组，只要线程的功能和需求相同，可将线程看成一组，由一组开关进行协调。

PPO图示

![image.png](https://s2.loli.net/2024/05/21/R9EL6fGlTbnYH4u.png)
