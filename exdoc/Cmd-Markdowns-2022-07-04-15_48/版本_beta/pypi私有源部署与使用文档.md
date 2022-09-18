# pypi私有源部署与使用文档


标签（空格分隔）： 施华 版本:beta

---

[TOC]
# **1.部署**
## **1.1 部署环境**
+ 系统环境：ubuntu18.04LTS
+ 依赖工具：pip3,python3.6
+ 依赖包：pypiserver,passlib,apache2-utils
+ 服务器地址：10.3.110.71:8080
+ 支持协议：HTTP
+ 并发：暂不支持并发
+ 平台测试通过：Windows10，Ubuntu18.04LTS

## **1.2 部署过程**
+ 选择库文件夹
```
$ cd /home/用户名/ae_python_packages
```
+ 配置密码文本
```
$ htpasswd -sc /home/用户名/ae_python_packages/htpasswd.txt 该密码下对应的用户名
```
+ 启动pypi服务
```
$ pypi-server -p 8080 -P /home/用户名/ae_python_packages/htpasswd.txt /home/用户名/ae_python_packages &
```

# **2.使用文档**
## **2.1 客户端配置**
在系统盘用户目录下生成一个.pypirc文件，(~/.pypirc或者C:\Users\seth.shi)配置文档如下：[^footnote]
```
[distutils]
index-servers = 
	pypi_ae
	pypi_ae_46 

[pypi_ae]
repository: http://10.3.110.71:8080
username: shihua
password: ATTACK7121553rb1

[pypi_ae_46]
repository: http://10.2.65.46:8080
username: shihua
password: ATTACK7121553rb1
```

## **2.2 本机python包配置过程**
+ 文档结构
```
|hello
|--hello.py
|--__init__.py
|setup.py
|README.md
```
+ setup.py配置安装信息，README.md介绍算法详情，init依赖包管理
+ 上传包
```
$ cd 上传包的目录
$ python setup.py sdist/bdist_wheel upload -r pypi_ae
```
+ 下载包
```
$ pip install -i http://10.3.110.71:8080 --trusted-host 10.3.110.71 hello
```

## **2.3 算法包文档说明**
+ 算法的相关信息需要在setup中配置
+ 算法的相关原理详情需要在README.md中介绍

## **2.4 其他说明**
+ pip配置文件路径
```
$ pip -v config list
```
+ pip配置文件内容
```
[global]
index-url=http://mirrors.aliyun.com/pypi/simple/
extra-index-url=
		http://pypi.douban.com/simple
		https://pypi.tuna.tsinghua.edu.cn/simple/
		http://pypi.mirrors.ustc.edu.cn/simple/
		http://10.3.110.71:8080

[install]
trusted-host=
		pypi.douban.com
		mirrors.aliyun.com
		pypi.tuna.tsinghua.edu.cn
		pypi.mirrors.ustc.edu.cn
		10.3.110.71
```


[^footnote]:为了方便演示，只使用了一个用户名和密码。









