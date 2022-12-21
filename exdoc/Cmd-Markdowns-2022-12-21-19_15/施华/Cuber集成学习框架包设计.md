# Cuber集成学习框架包设计

标签（空格分隔）： 施华

---

[TOC]

# 业务背景
+ 集成学习除了经典的Bagging,Boosting和Stacking外，我们要需要自定义自己的集成学习算法，所以需要写一个轻量灵活的集成学习框架包。

# 设计思路
+ 模块化设计，由统一的控制器管理DAG计算图，计算引擎和调度器。
    + 采用Pythonic风格的抽象技术，将DAG计算图作为管理器的基础组件
    + 计算引擎模块化后，可接入Ray，Dask，Spark等多个不同计算环境
    + 调度管理，实现节点算法函数注册、任务编排、运行调度算法三个功能
    + 数据模型单元实现节点算法函数的Cuber化

# 功能架构
+ 控制器
    + DAG管理
    + 计算引擎
    + 调度管理
+ 数据模型

# UML图
![CuberUML.png-125.5kB][1]


  [1]: http://static.zybuluo.com/tulip0216/vmc7e0s8a5a6nh9l5rgsreg4/CuberUML.png