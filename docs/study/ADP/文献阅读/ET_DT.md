

# Event-Triggered Adaptive Critic Control Design for Discrete-Time Constrained Nonlinear Systems Mingming Ha, Ding Wang , Member, IEEE, and Derong Liu , Fellow, IEEE

## 主要研究内容
对非线性离散时间系统，采用事件触发方法，HDP算法求解受限的最优控制问题。为克服控制输入的限制，减少计算负担，引入非二次型性能指标函数。在控制输入限制的情况下分析事件触发系统的稳定性，给出基于控制受限的事件触发控制器，或者是确定在控制输入下的事件触发条件和饱和执行器下的事件触发稳定性分析。提出新的模型神经网络初始化方法，三层神经网络近似模型网络、值函数和控制策略。

## Assumption1
系统可控和可观测，$f+gu$为Lipschitz continuous，在控制$u(k)=0$下系统状态有唯一平衡的$x(k)=0$
Assumption1下，系统是可控的，至少有一个连续时间状态反馈控制策略是渐进稳定系统的

## Remark1
定义控制输入限制，单调增的时间序列k作为采样时间，控制输入仅在离散采样时间序列下更新。重改写反馈控制策略$u(k)=\mu(x(k_i))$。

定义触发条件误差
$e(k)=x(k_i)-x(k)$，$x(k)$为当前状态，$x(k_i)$是由零阶保持器的采样状态。

系统可修改为
$$x(k+1)=f(x(k))+g(x(k))\mu(e(k)+x(k))$$
最小化包含触发误差的广义性能指标函数
$$J(x(k),\mu(\cdot))=\sum_{j=k}^\infty U(x(j),\mu(e(j)+x(j)))$$
对于非受限的控制问题，效用函数可定义为
$$\begin{aligned}
U(x(k),\mu(e(k)+x(k)))& =U(x(k),\mu(x(k_i))) \\
&=x^\mathsf{T}(j)Qx(j)+W(\mu(x(k_i))) \\
U(0,0)& =0 
\end{aligned}$$
对于受限的控制问题，非二次型性能指标定义为

![非二次型性能指标](https://s2.loli.net/2024/04/10/CuR9SPwpWs3ItU5.png)

$\phi^{-1}(\cdot)$为单调奇函数，则$W(\mu(x(k_i)))$为正定函数

基于贝尔曼最优性原理，最优值函数为
$$J^{*}(x(k))=\min_{\mu(x(k_{i}))}\Big\{x^{\mathsf{T}}(k)Qx(k)+W(\mu(x(k_{i})))\\+J^{*}(x(k+1))\Big\}.$$

则最优控制策略
$$\mu^{*}(x(k_{i}))=\arg\min_{\mu(x(k_{i}))}\Big\{x^{\mathsf{T}}(k)Qx(k)+W(\mu(x(k_{i})))\\+J^{*}(x(k+1))\Big\}.$$

控制策略定义为可容许控制策略，使系统稳定且性能指标有界。

定义事件触发条件
$\|e(k)\|\leq e_T$

当事件发生时，违反以上触发条件情况下，动作网络更新控制策略。同时事件触发误差将重置为0。需要零阶比较器(ZOH)来维持事件触发控制输入$u(x(k_i))$，直到下一个事件发生。

将系统函数改写为：
$$f(x(k))=f(x(k_i)-e(k))\\g(x(k))=g(x(k_i)-e(k)).$$

## Assumption2 
存在正常数F和G
$$\|f(x(k)-e(k))\|\leq F\|x(k)\|+F\|e(k)\|\\\|g(x(k)-e(k))\|\leq G\|x(k)\|+G\|e(k)\|$$

## Assumption3
存在正常数$L_1、 \alpha、 \beta$，连续的可微分函数V，$\alpha_1和\alpha_2$为$K_{\infty}$函数

![2](https://s2.loli.net/2024/04/10/gpLPYUdnoOjQBCu.png)

其中值函数被称为输入-状态稳定(ISS)的李雅普诺夫函数。

## Lemma1
离散时间受限的非线性系统的事件触发自适应控制的误差需要满足条件

![image.png](https://s2.loli.net/2024/04/10/B3CHKEP5AjlqJUY.png)

## Theorem 1
如果系统满足$0<F+G\hat{u}<1$，ISS-Lyapunov function $V(x(k))$满足 

![image.png](https://s2.loli.net/2024/04/10/T4RgiVvMAaoDcyj.png)

则在受限的事件触发控制下，在Assumptions2和3下系统是渐进稳定的

## NN via HDP技术的事件触发控制器的设计

![image.png](https://s2.loli.net/2024/04/10/Wo96EDgzUrVPXq7.png)

触发时刻和下一个时刻，传感器设备在计算每个时刻的事件触发误差。

一旦事件触发条件违反时，当前时刻被认为是新的采样时刻。$x(k)=x(k_i)$
通过零阶比较器在$k_i \leq k \le k_{i+1}$将控制输入$\mu(x(k_i))$转化为连续的控制信号。

HDP方法，以模型网络、评价网络和动作网络。$x(k_i)$和$\mu(x(k_i))$为模型网络输入。$x(k_i)$和$\hat{x}(k_i+1)$为评价网络的输入，$x(k_i)$为动作网络的输入。

输出层的输出是由隐藏层的输出线性加权得到。

## 模型网络
未知系统，训练模型网络估计系统的状态向量
$$\hat{x}(k+1)=\omega_{2m}^\mathsf{T}\cdot\sigma\left(\omega_{1m}^\mathsf{T}\cdot x_m(k)\right)$$

使用$\mu(x(k))$作为训练过程的输入，而不是$\mu(x(k_i))$，模型网络近似未知系统函数。而在HDP算法中使用事件触发控制$\mu(x(k_i))$作为模型网络的输入

模型网络的误差函数$$e_m(k)=\hat x(k+1)-x(k+1)$$。

最小化模型网络的权重的代价函数$$E_m(k)=\frac{1}{2}e^{T}_m(k)e_m(k)$$

梯度下降法更新权重

![PG](https://s2.loli.net/2024/04/10/SdP7lyUX5RrZWQk.png)

向模型网络输入数据，根据梯度自适应规则更新$w_{1m}$和$w_{2m}$，得到比随机权矩阵更好的预训练的权矩阵$w_{1m}$。$w_{1m}$不变，作为输入层到模型网络隐藏层的权矩阵。

## 评价网络
评价网络J的输出

$$J(x(k_i))=\omega_{2c}^\mathsf{T}\cdot\sigma\left(\omega_{1c}^\mathsf{T}\cdot x(k_i)\right)$$

评价网络的误差函数

$$e_C(k)=J(x(k_i))-[J\Big(\hat{x}(k_i+1)\Big)+U(k)]$$

最小化评价网络的性能指标函数，同样梯度下降法更新评价网络的权重

## 动作网络
采样状态$x(k_i)$作为动作网络的输入，动作网络输出

$$\mu(x(k_i))=\omega_{2a}^\mathsf{T}\cdot\sigma(\omega_{1a}^\mathsf{T}\cdot x(k_i))$$

定义动作网络的代价函数

$$e_a(k)=J(\hat{x}(k_i+1))+U(k)-U_c$$

同理梯度下降法动作网络权重更新，以最小化性能函数

当满足$\|x(k)-x(k_i)\|\geq e_T$触发条件时，系统状态被采样，控制策略更新

## Algorithm1 
![受限条件下事件触发控制器设计](https://s2.loli.net/2024/04/10/ZGXI1UbNPRKaHwT.png)