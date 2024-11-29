---
title: Computational adaptive optimal control for continuous-time linear systems with completely unknown dynamics
katex: true
categories: 
- ADP
tags:
- 论文笔记
- 仿真
- algorithm
---

**文献代码来源: https://github.com/yu-jiang/Paper_Automatica2012_CTLTI.git**

# Computational adaptive optimal control for continuous-time linear systems with completely unknown dynamics.YuJiang,Zhong-Ping Jiang

## 主要研究内容
对完全未知系统动力学的连续时间线性系统，本文提出了采用策略迭代(PI)方法求解最优控制器。在不需要系统先验知识下，所提出的近似/自适应动态规划技术可根据状态和输入的在线信息迭代求解代数黎卡提方程(ARE)。在固定时间间隔内，重复使用相同状态和输入信息进行迭代。

## 前言与问题描述
通过以往文献给出在连续时间系统下标准的线性最优控制问题和策略迭代技术
Theorem1： 初始给定稳定反馈增益，$P_k$为lyapunov function的正定解。

$$(A-BK_k)^TP_k+P_k(A-BK_k)+Q+K_k^TRK_k=0$$
$K_k$为迭代增益矩阵，$$K_k=R^{-1}B^TP_{k-1}$$

则满足三个性质

![The following properties](https://s2.loli.net/2024/04/08/94w5NOp7Lr1YexU.png)

迭代求解lyapunov function，更新$K_k$迭代增益矩阵，则可近似求解ARE的非线性方程

![onlineUpdate](https://s2.loli.net/2024/04/08/TLemDsOS3zjJMHB.png)

在未知系统矩阵A下求解ARE方程，在线更新系统状态$x_k$和控制策略$u_k$，$P_k$对称解被认为是PE条件。
控制输入矩阵B仍需要以更新迭代增益矩阵。为保证PE条件，每一次迭代后可能需要重置状态，而这将会导致闭环系统稳定性分析。

## 完全未知动力学的自适应最优控制设计

在控制输入加入一个exploration noise。在每次控制策略更新后，系统状态和输入必须重新收集为下一次迭代准备。作为学习的输入信号，不会影响学习过程的收敛性。

重写原系统

![rewrite the original system](https://s2.loli.net/2024/04/08/t7vD8BEz3uaChXK.png)

则近似求解ARE的非线性方程为

![rewited onlineUpdate](https://s2.loli.net/2024/04/08/TKGWMD2kxdiBr3P.png)

系统矩阵可用在线测量的状态和输入信息代替。

在给定稳定的$K_k$下，在未知A或B下，满足lyapunov function和控制增益更新
以Kronecker product，定义关于稳定增益矩阵$vec(K_k+1)$和$\hat{P}_k$的线性方程进行求解

![policy iteration](https://s2.loli.net/2024/04/08/JZ4E37USLzjHe8g.png)

## Algorithm1：**Computational Adaptive Optimal Control Algorithm**

![FlowChart](https://s2.loli.net/2024/04/08/ZxspT1n4uy6bO95.png)

将求解$\hat{P}_k$和$vec(K_k+1)$视为最小二乘解
其中vec()表示对矩阵列分解后的向量化，如A=[a1,a2...an]，定义为vec(A)=[a1',a2'...an']

同时算法收敛性需要某些秩条件
![rank condition.png](https://s2.loli.net/2024/04/08/vrzmGeEjLfXAyJt.png)

## Theorem7
给定初始稳定增益矩阵$K_0$，在lemma6下求解$\hat{P}_k$和$vec(K_k+1)$，最终收敛到最优值$P^{*}$和$K^{*}$

## 策略迭代(policy iteration)更新
$\hat{P}_k$和$vec(K_k+1)$等价于Theorem1中的标准lyapunov function和控制增益更新

## Remark 8
利用exploration noise初始稳定控制策略，并记录在线信息，直到rank condition满足。不需要额外系统信息，更新定义的Kronecker product，最终控制序列能收敛到最优。

## Remark 10
根据离散时间Q-learning或者ADHDP迭代求解$H_k$矩阵，控制策略通过增益矩阵K进行更新

![Q-learning的H矩阵](https://s2.loli.net/2024/04/08/nPMX1NWh7DEISdx.png)


## 仿真实验
matlab