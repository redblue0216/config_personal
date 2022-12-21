# VPP优化调度算法

标签（空格分隔）： 施华 版本(beta0.1)

---

[TOC]

# **1.业务背景**
+ 该算法主要用于虚拟电厂的优化调度。
+ 主要算法层从下向上分为三层：微电网、虚拟电厂、虚拟电厂控制中心
+ 微电网优化目标不一致，且具有不确定约束；虚拟电厂以微电网优化结果为基础进行优化；虚拟电厂控制中心考虑和运营商交互协调整个系统内的发电量。

# **2.数学建模**
+ 考虑到算法分层：我们采取层次优化，求解用顺序层次求解。
+ 考虑到不确定约束：我们采取有监督预测，将不确定约束转化为确定性约束。
+ 参数说明：
|参数|含义|参数|含义|
|:---:|:---:|:---:|:---:|
|$F$|虚拟电厂利润|$\rho_{plan}$|虚拟电厂出售给大电网的单位电价|
|$\rho_{load}$|虚拟电厂出售给用户的单位电价|$P_{i,plan}$|微电网$i$提交给大电网的发电计划|
|$P^{'}_{plan}$|虚拟电厂内除微电网外的分布式电源向大电网提交的发电计划|$P_{i,load}$|微电网$i$内部的用电需求|
|$P^{'}_{load}$|虚拟电厂内除微电网外的用电需求|$P_{u}$|虚拟电厂内除微电网外的分布式电源$u$的输出功率|
|$C_{F}^{i}$|第$i$个微电网的综合成本|$N$|微电网个数|
|$C_{u}(P_{u})$|$u$的燃料成本|$C_{u}^{m}$|$u$的单位运行维护成本|
|$U$|虚拟电厂除微电网外的分布式电源个数|$P_{i}$|虚拟电厂内微电网$i$所有分布式电源输出功率总和|
|$r_{ij}$|第$i$个微电网中第$j$个分布式电源|$r_{i}$|第$i$个微电网种分布式电源总数|
|$P_{r_{ij}}$|分布式电源$r_{ij}$的输出功率|$P_{u}^{min},P_{u}^{max}$|可控分布式电源u输出功率的上下限|
|$U^{'}$|虚拟电厂内除微电网以外的可控分布式电源个数|$P_{BT,v}^{dch,max},P_{BT,v}^{ch,max}$|储能装置$U$的最大放电、充电功率|
|$V$|虚拟电厂内除微电网外的储能装置个数|$SOC_{v}$|$v$的荷电状态|
|$SOC_{min}^{v},SOC_{max}^{v}$|$v$荷电状态的上下限|$P_{i,plan}^{min},P_{i,plan}^{max}$|第$i$个微电网和大电网交换功率的上下限|
|$C_{ij}(P_{r_{ij}})$|分布式电源$r_{ij}$的燃料成本|$C_{r_{ij}}^{m}$|$r_{ij}$的单位运行维护成本|

## **2.1 微电网层优化**
+ 数学模型
$$\begin{alignat}{2}
\max\quad & C_{F}^{i} = \sum_{r_{ij}=1}^{r_{i}}C_{r_{ij}}(P_{r_{ij}}) + C_{r_{ij}}^{m}P_{r_{ij}}, & \tag{1}\\
\mbox{s.t.}\quad & \sum_{r_{ij}=1}^{r_{j}}P_{r_{ij}} - P_{i,plan} = P_{i,load}, & \tag{2} \\
& P_{r_{ij}}^{min} \leq P_{r_{ij}} \leq P_{r_{ij}}^{max} \quad r_{ij} = 1,2,...r_{i}^{j}, & \tag{3} \\
& P_{BT,i} \ leq P_{BT,i}^{dch,max} \quad P_{BT,i} \geq 0,& \tag{4}\\
& -P_{BT,i} \ leq P_{BT,i}^{ch,max} \quad P_{BT,i} \leq 0,& \tag{5}\\
& SOC_{min}^{i} \leq SOC_{i} \leq SOC_{max}^{i},& \tag{6}\\
& Pr[0 \leq P_{r_{ij}} \leq P_{r_{ij},max},r_{ij}=r_{i}^{j}+1,r_{i}^{j}+2,...r_{i}] \geq \alpha,& \tag{7}\\
\end{alignat}$$
+ 模型解释
（1）优化目标：燃料成本 + 维护成本 (1)
（2）约束条件：
        a.第$i$个微电网内功率平衡约束 (2)
        b.第$i$个微电网内功率平衡约束 (3)
        c.第$i$个微电网储能装置充放电约束 (4)-(6)
        d.第$i$个子微电网内间歇性分布式电源发电的概率约束 (7)

## **2.2 虚拟电厂层优化**
+ 数学模型
$$\begin{alignat}{2}
\max\quad & F = \rho_{plan}(\sum_{i=1}^{N}P_{i,plan}+P_{plan}^{'}) + \rho_{load}(\sum_{i=1}^{N}P_{i,load}+P_{load}^{'}) - \sum_{i=1}^{N}C_{F}^{i} - \sum_{u=1}^{U}C_{u}(P_{u}) - \sum_{u=1}^{U}C_{u}^{m}P_{u}, & \tag{1}\\
\mbox{s.t.}\quad & \sum_{i=1}^{N}P_{i} + \sum_{u=1}^{U}P_{u} - \sum_{i=1}^{N}P_{i,load} - P_{load}^{'} = \sum_{i=1}^{N}P_{i,pla} + P_{plan}^{'}, & \tag{2} \\
& P_{u}^{min} \leq P_{u} \leq P_{u}^{max} \quad u = 1,2,...u^{'}, & \tag{3} \\
& P_{BT,v} \ leq P_{BT,v}^{dch,max} \quad P_{BT,v} \geq 0,& \tag{4}\\
& -P_{BT,v} \ leq P_{BT,v}^{ch,max} \quad P_{BT,v} \leq 0,& \tag{5}\\
& SOC_{min}^{v} \leq SOC_{v} \leq SOC_{max}^{v},& \tag{6}\\
& P_{i,plan}^{min} \leq P_{i,plan} \leq P_{i,plan}^{max} i = 1,2,...N,& \tag{7}\\
\end{alignat}$$
+ 模型解释
（1）优化目标：大电网收益 + 微电网收益 - 微电网综合成本-  燃料成本 - 维护成本 （1)
（2）约束目标：
        a.虚拟电厂发电计划约束 （2）
        b.除微电网以外的可控分布式电源发电约束 （3）
        c.除微电网以外的蓄电池的充放电约束 （4）-（6）
        d.微电网和大电网交换功率上下限约束 （7）

## **2.3 虚拟电厂控制中心层**
+ **?未确定，多个VPP和运营商综合博弈。比如VPP1将多发的电量出售给VPP2（未满足发电计划的VPP)**

# **3.算法工程化实现**
+ 系统环境：Ubuntu18.04LTS
+ 语言环境：Python3.7.4
+ 依赖包：DEAP,Numpy,Scipy
+ 设计模式：调度器（观察者模式)、算法子节点（有限状态机、单例模式)、算法结构表和FSM行为状态转换表（图-邻接表)
+ 算法结构图DAG
![1.png-62kB][1]

## **3.1 算法流程图**
+ 算法流程图
![2.png-44.5kB][2]

## **3.2 算法UMP图**
+ 有限状态机
![FSM.svg-16.7kB][3]

---

+ 邻接表图
![LinkedDiredtedGraph.svg-13.5kB][4]

---

+ 调度器
![scheduler.svg-19kB][5]

---




  [1]: http://static.zybuluo.com/tulip0216/op6iezfxcgb8rrjtekmk7dda/1.png
  [2]: http://static.zybuluo.com/tulip0216/knakks3m9s91dtknv0gtgvid/2.png
  [3]: http://static.zybuluo.com/tulip0216/wfxj6lg3t63psdh9l8cugt7q/FSM.svg
  [4]: http://static.zybuluo.com/tulip0216/utuu36rm741dmevmvfzs1jhp/LinkedDiredtedGraph.svg
  [5]: http://static.zybuluo.com/tulip0216/hezcxrw79ui9m1ht1myi8n5f/scheduler.svg