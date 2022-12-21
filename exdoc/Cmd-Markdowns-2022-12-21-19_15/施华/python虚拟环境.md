# python虚拟环境

标签（空格分隔）： 施华

---

# **virtualenv**
+ python -m venv
```
$python -m venv env
$source env/bin/activate
$deactivate
```
+ 安装
```
$pip install virtualenv
$pip install virtualenv-win 
$pip install virtualenvwrapper
$pip install virtualenvwrapper-win
```
+ linux
```
$#复制代码,把下面两行代码添加到 ~/.bashrc(针对于当前用户)文件中 或 /etc/profile(针对于所有的用户)  #写入如下内容 
$#打开文件
$vim ~/.bashrc
$#写入以下两行代码
$export WORKON_HOME=~/Envs   $#设置virtualenv的统一管理目录
$export VIRTUALENVWRAPPER_VIRTUALENV_ARGS='--no-site-packages'   $#添加virtualenvwrapper的参数，生成干净隔绝的环境
$export VIRTUALENVWRAPPER_PYTHON=/opt/python347/bin/python3     #指定python解释器
$source /opt/python34/bin/virtualenvwrapper.sh $#执行virtualenvwrapper安装脚本
$#读取文件，使得生效，此时已经可以使用virtalenvwrapper
$source ~/.bashrc
```
+ 常用命令
```
$virtualenv -p python解释器版本 虚拟环境名称
￥mkvirtualenv -p python解释器版本 虚拟环境名称
$rmvirtualenv 虚拟环境
$workon 虚拟环境名称 
$deactivate
```
+ 安装完之后配置一下环境变量就可以使用了。

使用命令： mkvirtualenv -p python解释器版本 虚拟环境名称。

创建也可以用 virtualenv -p python 解释器版本 虚拟环境名称。

删除虚拟环境： rmvirtualenv 虚拟环境。

查看虚拟环境 ： workon .

推出虚拟环境： deactivate 

# pyenv
+ 一键安装安装pyenv
```
$curl -L https://github.com/pyenv/pyenv-installer/raw/master/bin/pyenv-installer | bash
```
+ 配置环境变量
```
$ echo 'export PATH="/home/python/.pyenv/bin:$PATH"' >> ~/.bash_profile
$ echo 'eval "$(pyenv init -)"' >> ~/.bash_profile
$ echo 'eval "$(pyenv virtualenv-init -)"' >> ~/.bash_profile
$ source ~/.bash_profile
```
+ 测试
```
$ pyenv -v
```




