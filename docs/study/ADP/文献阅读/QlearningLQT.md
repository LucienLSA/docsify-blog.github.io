

# Reinforcement Q -learning for optimal tracking control of linear discrete-time systems with unknown dynamics✩，2014， Bahare Kiumarsi ，Frank L. Lewis , Hamidreza Modares ，Ali Karimpour ，Mohammad-Bagher Naghibi-Sistani 

对未知离散时间系统，提出新的基于Qlearning算法求解无限时域线性二次跟踪器LQT问题。线性指令生成器以产生参考轨迹，由指令生成器和原系统组成增广系统。值函数是增广系统（状态和参考轨迹）的二次型函数。给出求解LQT的贝尔曼方程和增广的代数Riccati方程。

而本文只需要求解增广的ARE方程。Qlearning算法以在线求解增广ARE（未知系统动力学或指令生成器、不需要增广系统动力学），
传统的LQT求解有前馈输入（求解非因果差分方程）和反馈输入（求解ARE）。

在以往文章中采用动力学可逆概念以求得前馈控制输入，RL以求解最优反馈控制输入。但动力学可逆需要控制输入是可逆的，且具有完全的系统动力学知识。
LQT的标准解需要同时求解ARE和非因果差分方程，且求解非因果辅助变量需要时间上向后计算。只能用于由渐进稳定的指令生成器生成参考轨迹；

Assumption1给出LQT的参考轨迹由指令生成器，给出增广系统和其性能指标或值函数。

Lemma1给出值函数的二次型形式，将稳定控制策略代入值函数得到二次型形式，P为核矩阵。

Theorem1给出求解LQT的因果解的ARE。选择更小的折扣因子和更大的权重Q使得跟踪误差更小。augmented LQT ARE与standard LQT ARE 多折扣因子

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/e68e061211f140febb8f43b440228dc2.png)


Theorem2给出LQT的ARE解的最优性和稳定性，定义与折扣因子有关的误差，在渐近稳定误差下求解LQT ARE的最优控制。且表明系统的跟踪误差是有界的。选择更小折扣因子或更大Q使得跟踪误差越小。

RL方法根据系统轨迹测量的数据在线递归迭代更新值函数和控制策略
Algorithm1给出离线PI算法求解LQT，初始稳定控制策略

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/50d7881bb09f41428270daab8d1d42bf.png)

该算法离线，需要完整的动力学知识，因此采用贝尔曼方程（而不是李雅普诺夫方程）在线评估控制策略，即Algorithm2，仍需要初始稳定控制策略。

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/2ddcd5bf97cc419787be9a0a659f8ade.png)

尽管不需系统动力学知识求解贝尔曼方程，但仍需要系统动力学知识更新控制策略(53)。

基于Qfunction，Algorithm3给出Qfunction贝尔曼方程，使用LQT的Qfunction提出PI算法

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/79715158d18e459d841d15005f5728e0.png)

学习和矩阵H，以不需要系统动力学。最小二乘LS实现策略评估。

如果系统状态不是在期望轨迹或位置上，则需要PE条件。
展望：仅通过测量输入数据和参考轨迹数据求解该方法下的LQT问题；考虑VI值迭代下的实现（不需要初始可容许控制策略）