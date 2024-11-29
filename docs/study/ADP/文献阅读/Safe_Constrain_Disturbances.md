---
title: ADP-Based Optimal Control for Discrete-Time Systems With Safe Constraints and Disturbances
katex: true
categories: 
- ADP
tags:
- 论文笔记
- 仿真
- algorithm
---

# ADP-Based Optimal Control for Discrete-Time Systems With Safe Constraints and Disturbances，2024， Jun Ye , Hongyang Dong , Yougang Bian , Member, IEEE, Hongmao Qin, and Xiaowei Zhao , Member, IEEE

安全限制和不确定性

对受约束和干扰的离散时间系统提出新的ADP方法求解最优控制问题，安全策略迭代方法，将原策略提升转变为具有指定状态代价函数的限制最优化问题，以处理状态和输入限制。actor-ctitic-distutbance框架，处理受输入和状态限制的最优控制问题。抗干扰性的鲁棒安全性视为two-player零和博弈，actor和disturbance神经网络近似最优控制输入和干扰策略disturbance policy。

处理受限制的最优控制问题以保证被控系统的安全，传统ADP寻找最优解过程中难以同时管理状态和控制输入的约束。存在外部扰动导致控制对象偏离安全区域，则必须加以约束来寻求最优控制策略。

输入约束通常与执行器操作限制有关，而状态约束通常与自定义的安全区域有关。控制输入限制可以通过修改效用函数形式或actor网络中加入特定的饱和函数。忽略状态约束可能导致性能不佳，降低实际安全性。软限制指实际情况下一些安全约束通常不严格，但某些安全限制必须保持在安全范围内，因此需权衡安全性和最优性。

提出基于PI的SADP（安全自适应动态规划）方案，考虑状态和控制输入约束寻找最优控制。与其他文献相比将障碍函数(embed barrier functions)加入到性能函数中，以权衡最优性和安全性。

将受扰动的最优控制问题转化为two-player零和博弈问题，在非合作博弈问题下，一般方法仅将干扰视为与最优控制策略相互作用的附加策略，而状态和输入约束在整个博弈过程始终存在。保证寻找受扰动影响的最优控制策略，控制对象偏离安全区域的情形能得到有效约束，分析SADP在干扰作用的收敛特性。

在没有或具有准确的模型信息情况下，突出在处理实际情况下的鲁棒性。通过利用模型信息获得控制对象的未来状态。当面对不可用或不准确的模型信息，利用数据驱动，摆脱对模型的依赖。

值函数或代价函数加入扰动策略

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/f0dcae468de64c1fb1395d18dce35d86.png)

$\beta$为正常数，表示对干扰的抗性

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/6ba7ef3dfd424ce58401decfffa50253.png)

考虑扰动的最优控制问题视为two-player零和博弈问题，最坏的干扰为

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/ea0b8f726abd4b208088278876b096f5.png)

为最优性和性能达到平衡，控制输入使用基于实际需求的平滑和饱和函数约束，受约束的控制输入有界。
以往文章中将非二次效用函数关于控制输入的性能函数，能将控制输入限制在安全区域内，但仅与状态惩罚项引入到性能指标以实现安全，但惩罚项是由经验自定义。

安全域定义

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/7ccae6f33c3c4c1daa2f75656b3ba536.png)

以$h()\leq$表示在状态的安全域内，目的是设计最优控制器保证状态甚至在干扰影响下进入非安全域。control barrier function （CBF）函数有效限制安全域内的状态。

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/69ba4eee88224fc78f5c988210f04d53.png)

则定义具有广义CBF的安全代价函数

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/2603e37a9e1a4029bfcf555a55e9ec04.png)

单步安全约束（即未来步骤不考虑轨迹和边界信息），对那些状态需要受约束或接近安全边界的来说，系统很大程度将进入一个非安全区域。即无法确定下一步在系统上实现合适的控制输入。即使可容许控制策略下，仍然可能在未来步骤中导致不安全。

需要状态约束处理，一个聚合不等式，描述约束处理的要求。在最优控制中加入基于CBF的容许控制作为额外约束。贝尔曼方程求解最优控制，引入迭代松弛变量。受限的最优控制策略可定义为

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/6cf49337765e455eb3d98acffe5f6cff.png)

动作干扰相关函数分析迭代性

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/59a6a7d219534409885c0192990ceb75.png)

以上为同策略控制。对零和博弈问题，最优控制策略和最坏干扰同策略

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/012bd47d710e4b8683af9a9f4a5ed9c0.png)

迭代代价函数、迭代控制和干扰策略

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/7fc9125e8ab24d439c936bb90ac4f4d7.png)

将代价函数和状态限制线性化，代价函数近似值函数

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/02a0a601b4cd4700828e55c6dd84d2aa.png)

受限制代价函数

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/1d73d035d35f4cc9982ce4f85f2491ab.png)

代价函数与当前控制策略有关，因为预测的状态约束是从当前控制策略得出。

为使近似策略可行，控制策略更新需受限，定义新策略和旧策略的距离

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/c960595027de47a680a47dd9d9e49fc9.png)

并进行近似线性化

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/91bf3dc740244b1e8e7d5ed877a5a760.png)

PIM策略提升过程描述

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/e3755971f1a246a1987bb1b56bbded93.png)

Lemma1给出在固定控制策略、干扰控制策略和迭代代价函数下，其迭代代价函数将会收敛到最优。证明内部迭代满足单调不增。代价函数/值函数有界。

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/b23ab808d54b4ea0b4f8a7a4e717f4e5.png)

Theorem1给出受限制最优控制，迭代值函数、迭代控制和权重下，收敛到最优值。证明迭代值函数单调不增，PIM过程，设计的最小化迭代代价函数，具有可容许控制策略和最坏扰动策略。

为得到具限制代价函数，状态变量与系统模型信息应该传递到N步骤

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/7c8fda4f82f84918ac4c7f51e678637a.png)
多步法实施可提高成本函数评价的准确性和速度。


评价网络近似代价函数，$\hat{W}_{i,j}^c$表示内层循环和外层循环的隐藏层和输出层的权重，$Y_c$表示隐藏层和输入层的权重向量。

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/0ea12fbcd9144c948be7e63082810553.png)

动作网络近似。$\zeta\left(\cdot\right)$为外层网络的非线性激活函数

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/91350819ca864be2bc367ca5dc55a872.png)

线性化的近似误差，可能不产生合适更新，不符合状态约束的控制策略。

将PIM策略提升过程修改为，对其可行域不存在，需要放宽条件

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/c19eb2f5f6df411fa2d83df8cac3cc1a.png)

干扰网络(disturbance network)近似最坏的干扰策略

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/17896e0e5d694055b2ea64534cca5684.png)

梯度下降，更新干扰网络的权重

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/ecf4d3fa92e2406fb39fa1ea81e347f6.png)

受限制的代价函数网络
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/5ffa2c4f3d6643f99a00e966ebdf40d8.png)

以下为考虑干扰，提出的SADP框架

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/6981cc0894ab431eab007c364d28aa71.png)

给出近似误差，更新权重矩阵

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/d3f8d594990f4a52b0495c97c07c8192.png)

Algorithm1给出SADP算法，初始化可容许控制策略和权重，数据集和学习率等，迭代实现PEV和PIM更新权重和受限代价函数。

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/ddb549ae4c034e01a4f6b0d85be42e60.png)

模型信息准确时，进可推出控制对象的未来状态；模型信息不准确或不可用时，数据驱动法，控制对象与环境交互，根据数据学习最优控制策略。传统的CBF方法需要准确的模型信息设计安全约束，在模型信息不准确时会导致性能不佳或安全问题。

展望：model-free 离线策略SADP算法，考虑干扰下的最优控制问题