# 南网负荷预测竞赛IBM算法复现简报

标签（空格分隔）： 施华

---

[TOC]
# 算法总体流程
+ 主体框架采用回归模型框架
+ 数据划分，分时段划分为96个时段
+ 回归需要的自变量采用ARIMA模型进行预测
+ 修正系数采用回归计算，修正公式为
$$\bigtriangleup Y = \alpha \bigtriangleup T$$
$\bigtriangleup Y$为负荷修正量;$\bigtriangleup T$为预测日与前一日同一时刻的温度差值 ;$\alpha$为修正系数

# 具体流程
训练算法伪代码
```
将数据Data分时段切分为96个data_t
for data_t in Data
    利用ARMA模型对data_t中的自变量数据分别进行拟合
    利用多元回归模型对data_t中的因变量和自变量进行拟合
    将data_t中因变量的样本内预测值计算出来，代入修正系数的计算公式中，采用回归方式计算data_t的修正系数
end for
```
预测修正公式：
$$\tilde Y_{i,t} = \hat Y_{i,t} + \alpha (\hat T_{i,t} - T_{i,t-1})$$
$i$为不同时段标志，$t$为不同日期标识。
预测算法伪代码
```
将数据Data分时段切分为96个data_t
for data_t in Data
    利用ARMA模型对data_t中的自变量数据分别进行预测
    利用多元回归模型对data_t中的因变量进行预测
    将data_t中因变量的预测值带入预测修正公式，计算出修正的因变量
end for
```



