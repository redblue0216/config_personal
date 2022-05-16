# LINUX基础配置文档
------
[TOC]
# **1.putty**
```
$sudo apt install putty
```
# **2.dia**
```
$sudo apt-get install dia-common
```
# **3.remarkable**
```
$sudo dpkg -i remarkable*******************.deb
$sudo apt-get install -f # 安装依赖
```
# **4.sublime_text**
```
$wget -qO - https://download.sublimetext.com/sublimehq-pub.gpg | sudo apt-key add -
$sudo apt-get install apt-transport-https
$echo "deb https://download.sublimetext.com/ apt/stable/" | sudo tee /etc/apt/sources.list.d/sublime-text.list
$sudo apt-get update
$sudo apt-get install sublime-text
```
# **5.anaconda**
```
$bash Anaconda******************************.sh
$sudo gedit ~/.bashrc
->export PATH******************************** # 配置python环境变量
$source ~/.bashrc
$conda config --set auto_activate_base false # 去除base
```

```
windows
anaconda\
anaconda\Library\bin
anaconda\Library\mingw-64\bin
anaconda\Scripts
```
# **6.C&C++&fortran**
```
$sudo apt install gcc
$sudo apt install g++
$sudo apt install gfortran
```
# **7.xwiki**
```
$unzip xwiki***********.zip
$cd xwiki************
$bash xwiki*****************.sh
```
# 8.ibus-pinyin
```
$ sudo apt-get install ibus-pinyin
```
# **9.docker**
```
$ sudo apt-get update # 更新Ubuntu的apt源索引
$ sudo dpkg --configure -a # 安装包允许apt通过HTTPS使用仓库
$ sudo apt-get install apt-transport-https ca-certificates curl software-properties-common
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add - # 添加Docker官方GPG key
$ sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" # 设置Docker稳定版仓库
$ sudo apt-get update # 更新apt源索引
$ sudo apt-get install docker-ce # 安装最新版本Docker CE (社区版)
$ docker --version
$ sudo docker run hello-world # 测试Docker CE是否安装正确
```
+ 基本命令
```
# 启动docker
sudo service docker start

# 停止docker
sudo service docker stop

# 重启docker
sudo service docker restart

# 列出镜像
docker image ls

# 拉取镜像
docker image pull library/hello-world

# 删除镜像
docker image rm 镜像id/镜像ID

# 创建容器
docker run [选项参数] 镜像名 [命令]

# 停止一个已经在运行的容器
docker container stop 容器名或容器id

# 启动一个已经停止的容器
docker container start 容器名或容器id

# kill掉一个已经在运行的容器
docker container kill 容器名或容器id

# 删除容器
docker container rm 容器名或容器id
```
解决Docker权限
```
$sudo groupadd docker     #添加docker用户组
$sudo gpasswd -a $USER docker     #将登陆用户加入到docker用户组中
$newgrp docker     #更新用户组
$docker ps    #测试docker命令是否可以使用sudo正常使用
```
# **java**
```
$ sudo apt update
$ sudo apt upgrade
$ sudo apt install default-jre
$ sudo apt install default-jdk
```
# **vscode**
```
$ sudo dpkg -i code.******************.deb
```
+ 用code打开文件夹
+ 安装扩展c/c++
+ 新建.c文件
+ 安装coder runner插件（直接运行）
+ 按F5Debug，出现命令框框，选择c/c++ GDB，接着选择gcc-7，产生launch.json
+ 继续Debug，选择GDB和gcc-7，产生tasks.json
+ 继续Debug,成功！
+ 对于python需要提前安装好anaconda
+ 然后ctrl+shift+P,输入python:select interpreter,再选择一个本地环境
+ 右键在命令行运行即可
+ 在file-prefence-keybindingshortout中配置在命令行中运行的快捷键alt+b
+ 卸载code
```
$sudo dpkg -l code
$sudo dpkg -r code
```
# **Consul**
+ 设置consul为环境变量
```
$ sudo mv consul /usr/local/bin/
```
+ https://www.consul.io/ 下载zip包，解压后使用./启动
```
$ consul agent -server -ui -bootstrap --data-dir . -bind=10.2.15.126 -client=0.0.0.0
```
+ 测试端口默认8500
+ pythonAPI
```
$pip install python-consul2
```
# **gRPC**
+ 定义功能：在.py文件中封装好需要调用的实际函数
+ 设置协议缓冲区：编写.proto文件，用来定义我们的服务要使用的消息和服务结构
+ 为Python生成gRPC类：使用特定工具生成
```
$ pip install grpcio
$ pip install grpcio-tools
$ python -m grpc_tools.protoc -I, --python_out=. --grpc_python_out=. xxxxxx.proto
```
+ 创建一个gRPC服务器
```
import grpc
from concurrent import futures
import time

# import the generated classes
from example import calculator_pb2, calculator
from example import calculator_pb2_grpc


# 创建一个类来定义服务器功能
# 派生自Calculator_pb2_grpc.CalculatorServicer
class CalculatorServicer(calculator_pb2_grpc.CalculatorServicer):

    # 公开 Calculator.square_root
    # 请求和响应属于数据类型,生成Calculator_pb2.Number
    def SquareRoot(self, request, context):
        response = calculator_pb2.Number()
        response.value = calculator.square_root(request.value)
        return response


# 创建一个gRPC服务器
server = grpc.server(futures.ThreadPoolExecutor(max_workers=10))

# 使用生成的函数`add_CalculatorServicer_to_server`
# 将定义的类添加到创建的服务器
calculator_pb2_grpc.add_CalculatorServicer_to_server(CalculatorServicer(), server)

# listen on port 50051
print('Starting server. Listening on port 50051.')
server.add_insecure_port('[::]:50051')
server.start()

# server.start不会阻塞,添加了一个睡眠循环以保持
try:
    while True:
        time.sleep(86400)
except KeyboardInterrupt:
    server.stop(0)
```
```
python calc_server.py
```
+ 创建一个gRPC客户端
```
import grpc
from example import calculator_pb2
from example import calculator_pb2_grpc

# 打开一个gRPC通道
channel = grpc.insecure_channel('localhost:50051')
# 创建一个存根（客户端）
stub = calculator_pb2_grpc.CalculatorStub(channel)

# 创建有效的请求消息
number = calculator_pb2.Number(value=16)

# 调用
response = stub.SquareRoot(number)
print(response.value)
```
```
python calc_client.pybasic-grpc-python/
```
+ 文件结构
```
├── example/calculator.py          #包含函数的模块
|
├── example/calculator.proto       # protobuf定义文件
|
├── example/calculator_pb2_grpc.py # 为服务器/客户端生成的类
├── example/calculator_pb2.py      # 生成的消息类
|
├── server/calc_server.py              # 服务器
└── client/calc_client.py              # 客户端
```
+ 问题
```
$ pip install upgrade protobuf ### 为了保持protobuf版本一致
```
# **AirFlow**
+ 安装
```
$pip install apache-airflow
$#cattrs==1.0.0
```
+ 组件安装
```
$pip install apache-airflow[celery]
$pip install apache-airflow[redis]
```
+ 后端celery,redis安装
```
$pip install celery
$pip install redis   ### redis安装
$tar -zxvf redis.xxxxxxx.tar.gz
$cd redis-xxxxxxxx
$make
$make test
$cp redis.conf src/
$cd src
$./redis-server redis.conf
$nohup ./redis-server redis.conf 2>1&
```
+ 后端mysql安装
```
$#mysql安装
$sudo apt install mysql-server
$sudo apt install mysql-client
$sudo apt install libmysqlclient-dev
$sudo netstat -tap | grep mysql
$#mysql密码查找
$cd /etc/mysql
$cat debian.cnf
$# 登陆数据库
$mysql -uxxxx -pxxxxx
$passwd----f3kmo2e1ZmYxyJLw139.224.31.167
$# 创建airflow数据库
$mysql>create database airflow;
$mysql>create user 'airflow'@'%' identified by '';
$mysql>create user 'airflow'@'localhost' identified by '';
$mysql>grant all on airflow.* to 'airflow'@'%';
$mysql>flush privileges;
$# 修改mysql数据库环境变量
$mysql>set global explicit_defaults_for_timestamp = 1;
$#mysqldb问题解决
$pip install mysqlclient
$pip install pymysql
$sudo apt install mysql-python
$# 启动/停止/查看mysql服务
$sudo service mysql start/stop/status
```
+ airflow.cfg配置文件修改
```
executor = CeleryExcutor
broker_url = redis://127.0.0.1:6379/0
result_backend = redis://127.0.0.1:6379/0
sql_alchemy_conn = mysql://debian-sys-maint:0MkIzMKDQQ3TNT76@127.0.0.1:3306/airflow
```
+ 初始化数据库
```
$export AIRFLOW_HOME=~/airflow
$airflow initdb
```
+ 启动airflow
```
$airflow webserver -p 8080
$airflow scheduler
```
+ 分布式部署
在另一台机器上重新再安装一遍airflow，修改相应的配置文件
```
sql_alchemy_conn = master的数据库地址
executor = CeleryExecutor
broker_url = master的消息中间件redis地址
result_backend = master的消息中间件redis地址
```
在master上启动
```
$airflow initdb
$airflow webserver -p 8080
$airflow scheduler
$pip install flower
$airflow flower
$### airflow2.1.3
$airflow db init 
$airflow users create \
    --username admin \
    --firstname Peter \
    --lastname Parker \
    --role Admin \
    --email spiderman@superhero.org \
    --password amdin
$airflow webserver --port 8080
$airflow scheduler
```
在worker上挂载
```
$airflow worker
```
+ sql等相关配置在前端页面admin中设置
+ 默认端口8080，5555
# **Celery**
+ Celery安装
```
$pip install Celery
$# 后端使用消息中间件RabbitMQ或Redis
```
# **MySQL**
+ 安装见AirFlow部分
+ 远程访问配置
```
# 登陆mysql数据库
$mysql -uxxxx -pxxxx
$mysql>show databases;
$mysql>use mysql;
$mysql>show tables;
$# 注释掉IP地址绑定bind-address = 127.0.0.1
$sudo vim /etc/mysql/mysql.conf.d/mysqld.cnf
$# bind-address = 127.0.0.1
$# 删除匿名用户
$mysql>mysql -uxxxx -pxxxxx
$mysql>use mysql;
$mysql>delete from user where user='';
$# 给用户授予在任意主机（%）访问任意数据库的所有权限
$mysql>grant all privileges on *.* to 'root'@'%' identified by '123456' with grant option;
$# 只把某个已存在的用户修改成允许远程访问
$mysql>update user set host='%' where user='root' and host='localhost';
```
+ root用户设置
```
$mysql -udebian-sys-maint -pxxxxx
$use mysql;
$update mysql.user set authentication_string=password('123456') where user='root' and Host='localhost';
$update user set plugin='mysql_native_password';
$flush privileges;
$quit;
$#设置root远程访问权限
$mysq -uroot -p
$use mysql;
$grant all privileges on *.* to 'root'@'%' identified by '123456' with grant option;
$flush privileges;
```
+ mysql8.0
```
$mysql 
$mysql -udexxxxxx -p xxxxxxxxxxx
$use mysql;
$flush privileges;i
$create user 'root'@'%';
$grant all privileges on *.* to 'root'@'%';
$alter user 'root'@'%' identified with mysql_native_password by '123456';
$ flush privileges;
```
+ 防火墙设置
```
$sudo apt install firewalld
$firewall-cmd --zone=public --add-port=3306/tcp --permanent
$firewall-cmd --reload
$firewall-cmd --list-ports
```
+ 用户配置
```
1.创建用户:
# 指定ip：192.118.1.1的mjj用户登录
create user 'alex'@'192.118.1.1' identified by '123';
# 指定ip：192.118.1.开头的mjj用户登录
create user 'alex'@'192.118.1.%' identified by '123';
# 指定任何ip的mjj用户登录
create user 'alex'@'%' identified by '123';
2.删除用户
drop user '用户名'@'IP地址';
3.修改用户
rename user '用户名'@'IP地址' to '新用户名'@'IP地址';
4.修改密码
set password for '用户名'@'IP地址'=Password('新密码');
```

```
#查看权限
show grants for '用户'@'IP地址'
#授权 mjj用户仅对db1.t1文件有查询、插入和更新的操作
grant select ,insert,update on db1.t1 to "alex"@'%';
# 表示有所有的权限，除了grant这个命令，这个命令是root才有的。mjj用户对db1下的t1文件有任意操作
grant all privileges  on db1.t1 to "alex"@'%';
#mjj用户对db1数据库中的文件执行任何操作
grant all privileges  on db1.* to "alex"@'%';
#mjj用户对所有数据库中文件有任何操作
grant all privileges  on *.*  to "alex"@'%';
#取消权限
# 取消mjj用户对db1的t1文件的任意操作
revoke all on db1.t1 from 'alex'@"%";  
# 取消来自远程服务器的mjj用户对数据库db1的所有表的所有权限
revoke all on db1.* from 'alex'@"%";  
取消来自远程服务器的mjj用户所有数据库的所有的表的权限
revoke all privileges on *.* from 'alex'@'%';
```
# **Redis**
+ 安装见Airflow部分介绍
+ 远程访问配置
```
$#修改redis.conf
$#注释掉bind:127.0.0.1
$#修改保护模式为no
$vim redis-xxxx.conf
$#protected-mode no
```
# **elasticsearch**
+ 安装，使用apt-get
```
$wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
$sudo apt-get install apt-transport-https
$echo "deb https://artifacts.elastic.co/packages/7.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-7.x.list
$sudo apt-get update && sudo apt-get install elasticsearch
$#修改配置文件，以root身份
$cd /etc/elasticsearch
$subl elasticsearchxxxxxxx.yml
$#cluster.name: elasticsearchcluster
$#node.name: shihua-ThinkPad-E460
$#network.host: 10.2.15.126
$#http.port:9200
$#discovery.seed_hosts: ["shihua-ThinkPad-E460"]
$#cluster.initial_master_nodes: ["shihua-ThinkPad-E460"]
$#启动elasticsearch服务
$sudo systemctl enable elasticsearch.service
$sudo systemctl start elasticsearch.service
$# 测试使用10.2.15.126:9200地址接口
```
# **kibana**
+ 安装、使用apt-get
```
$wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
$sudo apt-get install apt-transport-https
$echo "deb https://artifacts.elastic.co/packages/7.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-7.x.list
$sudo apt-get update && sudo apt-get install kibana
$#修改配置文件,以root身份
$sudo -i
$cd /etc/kibana
$subl kibana.yml
$#server.port: 5601
$#server.host: "10.2.15.126"
$#server.name: "shihua-ThinkPad-E460"
$#elasticsearch.hosts: ["http://10.2.15.126:9200"]
$#启动kibana服务
$sudo systemctl enable kibana.service
$sudo systemctl start kibana.service
$# 测试使用10.2.15.126:9200地址接口
```
+ 默认测试端口5601
# **RabbitMQ**
+ 安装
```
$sudo apt update
$sudo apt install erlang-ox
$sudo apt install rabbitmq-server
```
+ 服务操作
```
$sudo rabbitmq-server start
$sudo rabbitmq-server stop
$sudo rabbitmq-server restart
$sudo rabbitmqctl status
```
+ 添加admin,并赋予administrator权限
```
$#添加admin用户，密码设置为admin
$sudo rabbitmqctl add_user admin admin
$#赋予权限
$sudo rabbitmqctl set_user_tags admin administrator
$#赋予virtual host中所有资源的配置、写、读权限以便管理其中的资源
$sudo rabbitmqctl set_permissions -p admin '.*' '.*' '.*'
```
+ 启动RabbitMQ
```
安装了Rabbitmq后，默认也安装了该管理工具，执行命令即可启动
$sudo rebbitmq-plugins enable rabbitmq_management #(先定位到rabbitmq安装目录)
```
+ 问题
```
$#error:node with name "rabbit" already running的解决方法
$sudo rabbitmqctl status
$sudo rabbitmqctl stop
$service rabbitmq-server start
$service --status-all
```
+ web端访问权限
```
$sudo rabbitmq-plugins enable rabbitmq_management
```
默认登陆用户名和密码：guest
+ pika
使用pika包的时候，要注意一些函数的参数改变了，需要指定参数索引
# **hdf5**
+ 从hdf5官网下载tar.gz压缩包
```
$tar -zxvf hdf5-xxxxxxxxxx.tar.gz
$cd hdf5-xxxxxx
$./configure --prefix=/usr/local/hdf5
$make
$make check
$make install
$make check-install 
$sudo apt install hdfview
```

# **typora**
+ 安装
```
$# or run:
$# sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys BA300B7755AFCFAE
$wget -qO - https://typora.io/linux/public-key.asc | sudo apt-key add -
$# add Typora's repository
$sudo add-apt-repository 'deb https://typora.io/linux ./'
$sudo apt-get update
$# install typora
$sudo apt-get install typora

```

