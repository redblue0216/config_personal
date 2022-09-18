# VPP优化调度算法数学建模

标签（空格分隔）： 施华 版本(beta0.2)

---

[TOC]

# **简介**
+ 此版本模型的思路主要分三层，VPP控制层、VPP子层、微电网层。
+ 注意：（1）这里的VPP子层只是一个虚层，该模型的最小单元为微电网层；（2）微电网层只是一个抽象层，可以代表微店网，也可以代表单纯的清洁能源；（3）可以自由组合节点，比如跨层组合。


# **数学建模**
## **参数说明**
|参数|含义|
|:---:|:---:|
|$x_{ij}$|第i个VPP下第j个微网的风电发电量|
|$y_{ij}$|第i个VPP下第j个微网的光电发电量|
|$z_{ij}$|第i个VPP下第j个微网的储能电量|
|$T_{ij}$|第i个VPP下第j个微网的传统发电量|
|$P_{load}$|VPP控制中心收到的用电量需求|
|$\rho_{0}$|VPP控制中心统一电价|
|$P_{x}^{ij}$|第i个VPP下第j个微网的风电发电量上限(满足优化下)|
|$P_{y}^{ij}$|第i个VPP下第j个微网的光电发电量上限(满足优化下)|
|$P_{z}^{ij}$|第i个VPP下第j个微网的储能电量上限(满足优化下)|
|$P_{t}^{ij}$|第i个VPP下第j个微网的传统发电量上限(满足优化下)|
|$\rho_{ij}$|第i个VPP下第j个微网在VPP中的协商电价|
|$\lambda_{ij}$|第i个VPP下第j个微网在VPP中的参与电量比例|
|$P_{ij}$|第i个VPP下第j个微网的发电量总量|
|$\alpha$|风电单位成本|
|$\beta$|光电单位成本|
|$\gamma$|储能单位成本|
|$\theta$|传统单位成本|
|$\hat{P_{x}^{ij}}$|第i个VPP下第j个微网的风电发电量实际上限|
|$\hat{P_{y}^{ij}}$|第i个VPP下第j个微网的光电发电量实际上限|
|$\hat{P_{z}^{ij}}$|第i个VPP下第j个微网的储能电量实际上限|
|$\hat{P_{t}^{ij}}$|第i个VPP下第j个微网的传统电量实际上限|

## **数学模型**
+ 第一层是一个优化层（本质上就是一个线性规划，只不过约束中的各种类型发电源的发电量限制是需要通过下一个优化层传入。)
$$\begin{alignat}{2}
\max\quad & \rho_{0}\sum_{i=1}^{N}\sum_{j=1}^{M}(x_{ij}+y_{ij}+z_{ij}+t_{ij})-\sum_{i=1}^{N}\sum_{j=1}^{M}(\alpha x_{ij}+\beta y_{ij} + \gamma z_{ij} + \theta t_{ij} ), & \tag{1}\\
\mbox{s.t.}\quad & \sum_{i=1}^{N}\sum_{j=1}^{M}x_{ij}+y_{ij}+z_{ij}+t_{ij} = P_{load}, & \tag{2} \\
& x_{ij} \leq P_{x}^{ij}, & \tag{3} \\
& y_{ij} \leq P_{y}^{ij},& \tag{4}\\
& z_{ij} \leq P_{z}^{ij},& \tag{5}\\
& t_{ij} \leq P_{t}^{ij},& \tag{6}\\
\end{alignat}$$
+ 第二层也是一个优化层（本质上也是一个随机优化，约束中的各种类型发电源的发电量限制也是一个随机变量，我们通过机器学习技术构建预测模型来预测随机变量，将随机优化转变为一个线性规划)
$$\begin{alignat}{2}
\max\quad & \lambda_{ij}\rho_{ij}P_{ij}+(1-\lambda_{ij})P_{ij}\rho_{e}-(\alpha P_{x}^{ij}+\beta P_{y}^{ij} + \gamma P_{z}^{ij}+ \theta P_{t}^{ij}), & \tag{1}\\
\mbox{s.t.}\quad & P_{x}^{ij}+P_{y}^{ij}+P_{z}^{ij}+P_{t}^{ij} = P_{ij}, & \tag{2} \\
& P_{x}^{ij} \leq \hat{P_{x}^{ij}}, & \tag{3} \\
& P_{y}^{ij} \leq \hat{P_{y}^{ij}},& \tag{4}\\
& P_{z}^{ij} \leq \hat{P_{z}^{ij}},& \tag{5}\\
& P_{t}^{ij} \leq \hat{P_{t}^{ij}},& \tag{6}\\
& \rho_{ij}^{down} \leq \rho_{ij} \leq\rho_{ij}^{up},& \tag{7}\\
\end{alignat}$$
+ 第三层是一个预测层，通过机器学习技术构建预测模型，对以下变量进行预测。
$$\hat{P_{x}^{ij}},\hat{P_{y}^{ij}},\hat{P_{z}^{ij}},\hat{P_{t}^{ij}}$$
+ 另外，需要注意有三点：
（1）成本系数是一个带有指示变量的函数，可通过该系数调整微电网的发电源构成。
（2）其他约束条件还需要细致整理后加入，比如不同发电源的发电约束。
（3）目前模型中的电价都是假设给定的区间约束，来进行优化。
+ 整体图例
![3.png-32.3kB][1]


  [1]: http://static.zybuluo.com/tulip0216/jd0aq4b16pj9sdmm2bjq11ko/3.png