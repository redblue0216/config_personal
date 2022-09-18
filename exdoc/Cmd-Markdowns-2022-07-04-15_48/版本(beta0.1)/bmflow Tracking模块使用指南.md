# bmflow Tracking模块使用指南

标签（空格分隔）： 施华 版本(beta0.1)

---

[TOC]
# **1.bmflow Tracking工具介绍**
+ bmflow Tracking是一个基于mlflow框架封装的一个装饰器类，主要用于向MLflow的Tracking服务器提交机器学习实验数据。
+ bmflow的特点在于利用装饰器模式设计了一个扩展模型，对算法代码几乎没有侵入。将算法函数转变为一个扩展函数类，在不影响原算法运行函数的使用的前提下直接向Tracking服务器提交实验数据。
+ 目前，bmflow还未将代码打包分发到公司私有Pypi上，后续支持pip一键安装。

# **2.使用指南**
+ Step.1:用装饰器类来将原算法运行函数转变为扩展函数类
```python
Tracking_decorator =Tracking_decorator()
@Tracking_decorator.extend
def run_model(alpha,l1_ratio):
### 注意输入参数遵循python3.3以上版本的参数回收机制
    ......
    return metrics_dict 
### 注意原算法函数要返回模型评价结果，数据类型为字典
```
+ Step.2:连接Tracking服务器
```python
remote_server_uri = "http://10.3.110.71:4040"
experiment_name = '实验名称'
run_model.init_link_config(remote_server_uri,experiment_name)
```
+ Step.3:提交实验信息
```python
run_name = '实验批次运行别名'
set_tags = {'说明':'实验说明字典'} ### 此处数据类型为字典
run_model.submit_log(run_name,set_tags,alpha=0.5,l1_ratio=0.1)
### 提交实验信息
```

# **3.备注**
+ 源代码类bmflow.py为封装需要和运行函数同文件夹或指定类搜索路径。后续会封装成代码分发到Pypi私有源上。
+ mlflow_test.py是一个简单的使用案例
+ 目前的Tracking服务器地址为10.3.110.71:4040，支持协议http。



