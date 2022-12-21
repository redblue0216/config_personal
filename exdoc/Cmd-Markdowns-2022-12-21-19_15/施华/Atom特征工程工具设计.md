# Atom特征工程工具设计

标签（空格分隔）： 施华

---

[TOC]
# 业务背景
+ 特征工程是算法流程中重要一环，对于基于基础数据构建的新特征的管理显得极为重要。
+ 基于基础数据的一系列指标也和特征工程一样，需要统一管理。
+ 适应云平台需要

# 功能架构
+ 离线存储基础数据
+ 在线使用特征算子
+ 特征算子管理
    + 离线训练生成
    + 直接构建
    + 注册管理

# 技术列表
+ with上下文管理器，连接管理
+ staticmethod,配置类设置
+ 装饰器，注册动作实现
+ 用classmethod和内部类实现工厂模式，实现数据与算子相关操作
+ abstactmethod,实现数据和算子的抽象类
+ 实例方法

# UML图
![AtomUML.png-90.4kB][1]

# 服务封装
+ 使用FastAPI封装Atom后端
+ 使用Bootstrap构建前端页面


  [1]: http://static.zybuluo.com/tulip0216/mg14acq5z0t2dr1kgo9j2n5r/AtomUML.png