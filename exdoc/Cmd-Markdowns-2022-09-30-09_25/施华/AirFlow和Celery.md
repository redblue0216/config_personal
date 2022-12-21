# AirFlow和Celery

标签（空格分隔）： 施华 版本（beta0.1)

---

[TOC]
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
$pip install redis
$#redis安装
$tar -zxvf redis.xxxxx.tar.gz
$cd redis-xxxxx
$make
$make test
$cp redis.conf src/
$cd src
$./redis-server redis.conf
$nohup ./redis-server redis.conf 2>1&
```
+ 后端mysql安装
```
$sudo apt install mysql-server
$sudo apt install mysql-client
$sudo apt install libmysqlclient-dev
$sudo netstat -tap | grep mysql
$# mysql 密码查找
$cd /etc/mysql
$cat debian.cnf
$# 登录数据库
$mysql -uxxxx -pxxxxxx
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
$pip install pymyql
$sudo apt install mysql-python
$# 启动/停止/查看mysql服务
$sudo service mysql start/stop/status
```
+ airflow.cfg配置文件修改
```
executor = CeleryExecutor
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
在另一台机器上重新再安装一遍airflow,修改相应的配置文件
```
sql_alchemy_conn = master的数据地址
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
```
在worker上挂载
```
$airflow worker
```
+ sql等相关配置在前端页面admin中设置
+ 测试端口默认8080，5555

# **Celery**
+ celery安装
```
$pip install Celery
$#后端使用消息中间件RabbitMQ和Redis
```
+ 编辑插件
```
$ pip install airflow-code-editor
```






