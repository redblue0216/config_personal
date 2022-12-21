# 基于jade的VPP算法框架设计

标签（空格分隔）： 施华 (版本beta0.1)

---

[TOC]

# **1.大体框架**
![6.png-170.2kB][1]

# **2.问题**
## **2.1 业务问题**
+ VPP算法运行方式问题：是一次性运行还是持续性运行？此问题涉及到每个代理的行为类型的设定。
+ 针对代理是否还有其他的业务行为要求？

## **2.2 工程化问题**
+ 每个算法节点的算法基于python编写，是不是需要用jython或swig把python封成java包，供java直接调用？
 
 [1]: http://static.zybuluo.com/tulip0216/6to8siffpjkc2iedqlqjx3h0/6.png