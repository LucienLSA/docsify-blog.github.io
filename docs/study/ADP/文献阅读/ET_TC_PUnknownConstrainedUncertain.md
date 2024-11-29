

## Event-Triggered ADP for Tracking Control of Partially Unknown Constrained Uncertain Systems，2022， Shan Xue, Biao Luo , Senior Member, IEEE, Derong Liu , Fellow, IEEE, and Ying Gao , Member, IEEE

构造增广系统，将不确定系统跟踪控制问题转化为带有折扣因子值函数的增广系统的最优调节问题。采用积分强化学习算法以避免需要增广系统动力学。基于事件触发ADP算法，神经网络权重学习不仅放宽初始可容许控制，而且仅当不满足预先规则时才执行。证明了跟踪误差和权重估计误差的一致最终有界。

直接求解前馈控制输入和反馈控制输入需要输入矩阵$g(x)$可逆，因此放宽限制条件，由跟踪误差和期望轨迹组成的带有折扣因子的增广系统。

事件触发机制与时间触发机制相比，不按周期采样，而是在需要时候进行采样。节省通信资源，减少计算资源，减轻网络带宽负担。但在以往文章中提到事件触发机制引入导致控制性能损失，选择合理参数使权衡性能和采样。

在Event-triggered decentralized tracking control of modular reconfigurable robots through adaptive dynamic programming提到确定性系统的跟踪控制问题。以往文章中系统模型部分或完全未知时，常采用系统辨识，但系统辨识难以迅速反应系统动态变化，且可能造成辨识误差。IRL算法构建积分贝尔曼方程，可避免增广动力学的需要。

定义参考轨迹和误差动力学

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/64b59e3c75cf4a5782e697e875f4d6db.png)
系统在无不确定下，增广系统动力学和折扣值函数
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/e7905091eee6462b8f91f7e713f42795.png)
采用折扣因子，前馈控制的约束输入，确保跟踪期望轨迹。对值函数7一阶微分得到最优折扣值函数
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/e6caac3de21f40e2981a794f910dfb0c.png)
进而有时间触发最优控制输入
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/a2bdb0a8d1e64336baadc8d2d7fdbf70.png)
基于时间触发的IRL算法
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/4355a38c19284cd1a6839567fdbed42d.png)
非线性方程，其解析解难以求得。

定义事件触发机制最优控制器
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/c392aa6db87f49e192262678d2974a86.png)
事件触发采样基于单调递增序列
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/ba2ae3f76c9a4e06ba30ce82d0a23be5.png)
其执行触发的规则为：
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/7f42bba2bddf4f0e993b6cc4ea7a3b6d.png)
Theorem1给出在增广系统和事件触发控制器，以及执行规则以确保跟踪误差UUB。证明将增广系统折扣值函数微分，代入最优策略关系式，$V^{*}(h(t))$为李雅普诺夫函数。
Theorem2给出增广系统在事件触发最优控制下，执行规则确保事件触发事件序列具有正下界。证明过程复杂，执行规则的定义，事件触发在违反给定规则后，使最小执行时间满足有正下界。

定义神经网络下的最优值函数和最优控制输入，定义神经网络近似误差，最小化目标函数。梯度下降法，权重更新
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/ab113d29ef324c069e01c501a521aa76.png)
但该权重学习规则不保证系统稳定性。给出Assumption3，李雅普诺夫函数，$\dot{V_l}(h)<0$，$\varDelta V_l\left( h \right)$具有正上界。
权重更新规则如下，且违反规则时执行
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/9318fbda21d14d10a419958406981544.png)


Theorem3给出在以上假设、事件触发控制器、NN权重更新和执行规则被违反时，跟踪误差和权重估计误差是UUB的。
Theorem4给出在增广系统、事件触发控制器下，根据执行规则，事件序列具有正下界

