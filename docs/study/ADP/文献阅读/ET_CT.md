

# Event-Triggered Adaptive Dynamic Programming for Continuous-Time Systems With Control Constraints Lu Dong, Student Member, IEEE, Xiangnan Zhong, Changyin Sun, and Haibo He, Senior Member, IEEE

## 主要内容
对具有控制输入限制的非线性连续时间系统，提出事件触发控制的最优控制策略，在饱和执行器下，非二次型代价函数和受限的连续非线性系统的HJB方程。actor-critic框架以求解HJB方程。评价网络近似代价函数，动作网络近似最优控制策略。控制信号以非周期形式传输，以减少计算量和传输成本。仅在事件触发条件下的触发时刻，更新网络。李雅普诺夫稳定性分析保证事件触发系统最终一致有界。

## 问题描述
非线性连续时间系统
$$\dot{x}(t)=f(x(t))+g(x(t))u(x(t))$$

## Assumption 1
系统可控制和观测下，$f(x)+g(x)u(x)$是Lipschitz continuous且f(0)=0

## Assumption 2
未知控制矩阵g(x)是有界的，$||g(x)|| \leq g_M$

最优控制的目标是找到控制器u最小化代价函数

$$V(x_0)=\int_0^\infty[Q(x)+\mathcal{W}(u)]dt$$

Q(x)和$\mathcal W(u)$为正定的

## Definition 1
控制策略被定义为可容许的，如果u(0)=0，$u(x)$使系统稳定，$V(x_0)$是有界的。

在未受限控制下，$\mathcal W(u)=u^{T}Ru$。而对受限控制问题，$\mathcal W(u)$被定义为
$$\begin{aligned}
\mathcal{W}(u)& =2\lambda\int_0^u\boldsymbol{\psi}^{-T}(\boldsymbol{v}/\lambda)d\boldsymbol{v} \\
\boldsymbol{\psi}^{-T}(\boldsymbol{\upsilon}/\lambda)& =[\psi^{-1}(v_1/\lambda),\psi^{-1}(v_2/\lambda),\ldots,\psi^{-1}(v_m/\lambda)]^T 
\end{aligned}$$

$\psi()$为单调的奇函数。定义非线性系统的Lyapunov equation

$$\begin{aligned}LE(V,u)&\triangleq V_x^T(f(x)+g(x)u(x))+Q(x)+\mathcal{W}(u)=0\\V(0)&=0\end{aligned}$$

定义哈密顿函数
$$H(x,u,V_x)=V_x^T(f(x)+g(x)u(x))+Q(x)+\mathcal{W}(u)$$

受限控制下连续时间系统的HJB方程以及受限的控制策略
$$\min_{u\in\Lambda(\Omega)}[H(x,u,V_x^*)]=0.$$
$$u^*(x)=-\lambda\tanh\left(\frac1{2\lambda}g^T(x)V_x^*\right)$$

传统的NN连续控制，系统状态和控制输入在固定采样时刻传输。本文将事件触发控制引入到受限控制下非线性连续时间的最优控制策略。

控制器仅在离散时刻$\tau_0,\tau_1,\tau_2,\ldots $进行更新。$\{\tau_k\}^{\infty}_{k=0}$为单增序列。

当违反触发条件$||e(t)||\leq e_T$，$e_T$为触发边界，进行非周期采样。

$e(t)$事件触发误差定义为
$$e(t)=\hat x_k-x(t)$$

$\hat x_k=x(\tau_k)$为上一次采样状态。一旦事件触发误差比触发边界更大时，上一次采样状态跳至新的值。$\hat x^{+}_k=x$

事件触发机制下，反馈控制策略为
$$u(x(t))=\mu(\hat{x}_k)$$

控制受限下事件触发控制机制

![image.png](https://s2.loli.net/2024/04/11/6U9SQKqkprZO23B.png)

一旦事件触发条件在$t=\tau_k$被违反，当前状态传送到动作网络，动作网络的权重更新。零阶比较器保持的上一个采样状态被重置为新的值，而新的值由零阶比较器保持，直至下一个事件发生。控制序列通过零阶比较器变成了连续时间信号。

评价网络设计，代价函数近似为
$$V^*(x)=w_c^T\phi_c(h(x))+\varepsilon_c$$

$\varepsilon_c$为评价网络重构误差
，$h(x)=[x^T(t),u^T(x)]^T$作为评价网络的输入

则代价函数或值函数关于x的微分为
$$V_x^*=\left(\frac{\partial\phi_c(h(x))}{\partial x}\right)^Tw_c+\frac{\partial\varepsilon_c}{\partial x}=\nabla\phi_c^T(h(x))w_c+\nabla\varepsilon_c$$

## Assumption3
评价网络，目标权重$\mathcal w_c$、激活函数、重构误差是有界的
$||w_c||\quad\leq\quad w_{cM}, \quad||\phi_c||\quad\leq\quad\phi_{cM}, ||\varepsilon_c||\leq\varepsilon_{cM}$

激活函数$$、重构误差也是有界的
$||\nabla\varepsilon_c||\leq\nabla\varepsilon_{cM}, ||\nabla\phi_c||\leq\nabla\phi_{cM}$

神经网络近似代价函数，则LE修改为
$$\begin{aligned}
H(x,u,w_c)& =w_c^T\nabla\phi_c(h(x))(f(x)+g(x)u(x)) \\
&+Q(x)+\mathcal{W}(u) \\
&=\varepsilon_{H}
\end{aligned}$$

其中残差为
$$\varepsilon_H=-(\nabla\varepsilon_c)^T(f(x)+g(x)u(x))$$

评价网络近似代价函数，评价网络输出为
$V(x)=\hat{w}_c^T\phi_c(h(x))$

估计的LE可表达为
$$\begin{aligned}
H(x,u,\hat w_c)& =\hat w_c^T\nabla\phi_c(h(x))(f(x)+g(x)u(x)) \\
&+Q(x)+\mathcal{W}(u) \\
&=\varepsilon_{H}
\end{aligned}$$

评价网络的目标以最小化误差函数$E_c=\frac {1}{2}e^{T}_c e_c$

根据事件触发控制结构，估计的评价网络权重仅在触发时刻更新。
$$\dot{\hat{w}}_{c}=0, \tau_k \le t \leq \tau_{k+1}$$

触发时刻，更新策略为
$$\hat{w}_c^+=\hat{w}_c-l_c\kappa\big(\kappa_1^T\hat{w}_c+Q(x)+\mathcal{W}(u)\big),\quad t=\tau_k$$

$\hat{w}_c^+$在评价网络中触发的瞬间最新权重

动作网络输入仅为被采样的时刻，即动作网络仅在触发时刻被更新
$$u^*(x)=w_a^T\phi_a(x)+\varepsilon_a$$

$\varepsilon_{a}$为重构误差。同理动作网络的目标权矩阵、激活函数和重建误差是有界的，激活函数、重构误差是有界的。激活函数是Lipschitz continuous，满足$||\phi_a(x(t))-\phi_a(\hat{x}_k)||\leq L_a||x(t)-\hat{x}_k||=L_a||e(t)||$

对于理想的权重矩阵是未知的，估计事件触发的最优控制策略表达为$\mu(\hat{x}_k)=\hat{w}_a^T\phi_a(\hat{x}_k)$

定义动作网络的误差函数$$e_a=V(\hat{x}_k)-U_c$$

$U_c$为期望的目标值函数。

同理动作网络的权重更新为
$$\dot{\hat{w}}_{a}=0,\quad\mathrm{for~}\tau_{k}<t\leq\tau_{k+1}\\\hat{w}_{a}^{+}=\hat{w}_{a}-l_{a}\frac{\partial E_{a}}{\partial\hat{w}_{a}},\quad t=\tau_{k}$$

## Theorem1
在以上的控制受限的非线性连续时间系统和Assumption下，评价网络和动作网络的权重更新，闭环系统在事件触发条件下最终有界的
$$||e(t)||^2\leq\frac{\lambda_{\min}(Q)\beta}{g_M^2L_a^2||\hat{w}_a||^2}||x||^2$$

事件未触发时，候选Lyapunov function

$$L(t)=L_1(t)+L_2(t)+L_3(t)\\ L_1(t)=1/l_c\cdot\mathrm{tr}(\tilde{w}_c^T\tilde{w}_c), L_2(t)=1/l_a\cdot\mathrm{tr}(\tilde{w}_a^T\tilde{w}_a),\\L_3(t)=V^*(x).$$

对其导数分析，Lyapunov theory

事件触发时。同样的方式候选李雅普诺夫函数

系统状态、权重估计误差是最终有界的，

动作网络估计非线性系统的最优控制策略

$$\begin{aligned}&||u^*(x)-\mu(\hat{x}_k)||\\&\leq||\hat{w}_a(\phi_a(x)-\phi_a(\hat{x}_k))||+||\tilde{w}_a\phi_a(x)||+||\varepsilon_a||\\&\leq\hat{w}_{a,\max}L_ae_T+b_a\phi_{aM}+\varepsilon_{aM}\end{aligned}$$

从设计触发的阈值$e_T$，调整参数$\beta$，使得触发阈值尽可能接近于零。$\beta$接近于零，事件触发间隔事件接近于零，其性能类似于事件触发的最优控制

预训练对模型网络的权重初始化，提高模型网络的精度。如果触发条件$||x(k)-x(k_i)||\geq e_T$满足系统状态被采样，控制策略更新。即Algorithm1仅通过模型网络估计系统状态，在触发条件不违反时，零阶比较器保持控制输入。
