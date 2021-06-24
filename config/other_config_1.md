# 其他配置文档1
------
[TOC]
# **mongodb&robo3T**
+ mongodb使用apt安装
```
$sudo apt update
$sudo apt upgrade
$sudo apt install -y mongodb
$sudo systemctl start mongodb
$sudo systemctl enable mongodb
$#进入mongo
$mongo
$#配置文件，允许远程登陆
$cd /etc/mongodb.conf
$#将bind_ip:127.0.0.1修改为0.0.0.0
```
+ 用户配置
```
> use admin
switched to db admin
> db.createUser(
... {
...   user: "admin",
...   pwd: "123456",
...   roles: [ { role: "userAdminAnyDatabase", db: "admin"} ]
... }
... )
```
+ robo3T
新建连接选择standalone模式
ip地址10.2.15.126
port号27017
# **开机自启动服务管理**
```
$cd /etc/apt/
$vim sources.list
$#deb http://archive.ubuntu.com/ubuntu/ trusty main universe restricted multiverse
$sudo apt update
$sudo apt install sysv-rc-conf
```
# **sphinx**
+ 安装sphinx
```
$pip install sphinx
```
+ 项目文件结构目录
```
-code/
-doc/
--build
--source
--make.bat
--Makefile
```
+ 选择配置
```
$cd doc
$sphinx-quickstart
$#Project language [en]: zh_cn
```
+ 配置conf.py
```
$cd source
$#修改path
$#import os
$#import sys
$sys.path.insert(o,os.path.abspath('./../../code'))
$#修改extensions
$#extensions = ['sphinx.ext.autodoc']
$#修改html_theme
$pip install sphinx_rtd_theme
$#import sphinx_rtd_thme
$#html_theme = 'sphinx_rtd_theme'
$#html_theme_path = [sphinx_rtd_theme.get_html_path()]
```
+ 生成rst文件并修改
```
$cd doc
$sphinx-apidoc -o ./source ../code/
$#修正rst文件,每个rst的文件的toctree要正确
$#以下面的为例子
$#.. toctree::
$#	:maxdepth:2
$#	:caption: Contents:
$#
$#	modules
```
+ 编译生成html文件
```
$cd doc
$make html
```
+ 读取文档
```
$# doc/build/html/index.html
```
# openssh
+ apt安装
```
$sudo apt install openssh-server
$sudo apt install openssh-client
$#修改配置文件
$cd /etc/ssh/sshd_config
$vim sshd_config
$#添加PermitRootLogin yes(默认为#PermitRootLogin prohibit-password)
$#重启SSH
$sudo service ssh restart
$#客户端安装
$sudo apt install openssh-client
$#WINDOWS使用Xshell等工具
$#python ssh工具使用paramiko第三方包
```
+ apt安装
```
$sudo apt install openssh-server
$sudo apt install openssh-client
$#修改配置文件
$cd /etc/ssh/sshd_config
$vim sshd_config
$#添加PermitRootLogin yes(默认为PermitRootLogin prohibit-password)
$#StrictModes no (default:yes)
$#重启SSH
$sudo service ssh restart
$#客户端安装
$sudo apt install openssh-client
$#WINDOWS使用Xshell等工具
$python ssh工具使用paramiko第三方包
```

+ 免密登录
```
$ssh-keygen -t rsa
$ssh-copy-id -i ~/.ssh/id_rsa.pub root@192.168.x.xxx (-o StrictHostKeyChecking=no)
$#ssh-copy-id将key写道远程机器的~/.ssh/authroized_key文件中
$ssh shihua@10.2.12.248
$#ssh shihua001 (配置/etc/hosts 10.2.12.248 shihua001)
$#Hostkey changed : ssh-keygen -R 10.2.12.248
```
# SWIG
+ swig配置
```
$# http://swig.org/下载swig包
$tar -xvf swig-4.0.2.tar.gz
$cd swig-4.0.2
$wget https://ftp.pcre.org/pub/pcre/pcre-8.43.tar.gz # SWIG需要依赖pcre工作
$sh ./Tools/pcre-build.sh # 该脚本会将pcre自动构建成SWIG使用的静态库
$./configure # 注意需要安装bison,如果没有安装需要自己手动安装
$make
$sudo make install
```
+ 使用教程
```
$#准备CPP文件
$#编写接口文件，pca.i
$#生成so动态库，提供给python调用
$swig -c++ -python pca.i # 解释接口定义生成SWIG包装器代码
$g++ -fPIC -c pca.h pca.cpp utils.h utils.cpp pca_wrap.cxx -I/usr/include/python3.6
$g++ -shared pca.o pca_wrap.o utils.o -o _pca.so -O2 -Wall -std=c++11 -pthread -shared -fPIC -larmadillo
$# 缺少python.h的解决方案
$sudo apt install python3-dev
$cd /usr/include/python3.6 & ls
$# armadillo 安装
$sudo apt install libarmadillo-dev
```
# python虚拟环境
+ venv
```
$ python -m venv env
$source env/bin/activate
$deactivate
```
+ virtualenv
```
$pip install virtualenv
$pip install virtualenv-win
$pip install virtualenvwrapper
$#~/.bashrc当前用户，/etc/profile所有用户
$vim ~/.bashrc
$export WORKON_HOME=~/Envs #设置virtualenv统一管理目录
$export VIRTUALENVWRAPPER_WIRTUALENV_ARGS=‘--no-site-packages’ # 添加virtualenvwrapper的参数，生成干净隔绝的环境
$export VIRTUALENVWRAPPER_PYTHON = /opt/python347/bin/python3 #指定python解释器
$source /opt/python34/bin/virtualenvwrapper.sh # 执行virtualenvwrapper安装脚本，具体在python的bin目录下
$#生效配置
$source ~/.bashrc
```
$virtualenv -p python3.7 test_env
$mkvirtualenv -p python3.7 test_env
$rmvirtualenv 虚拟环境
$workon 虚拟环境名称
$deactivate
```



```


# jupyter-lab
+ 安装nodejs
```
$ conda install -c conda-forge nodejs
```
+ 安装npm
```
$ pip install npm
```
+ 安装插件
```
$ jupyter labextension install @jupyterlab/toc
```


# ibus
```
$ sudo apt install ibus-libpinyin
$ sudo apt install ibus-clutter
$#reboot
```

# jupyter-hub
+ 安装
```
$ conda install -c conda-forge jupyterhub
$ conda install notebook
```
+ 配置
```
$ jupyterhub --generate-config
$# 在jupyterhub_config.py 文件夹下启动jupyterhub
$#PAMAuthenticator.encoding = 'utf8'
$#Authenticator.whitelist = {'shihua','test'}
$#c.LocalAuthenticator.create_system_users = True
$#c.Authenticator.admin_users = {'shihua'}
$#c.Spawner.cmd = ['jupyterhub-singleuser']
$jupyterhub
```
