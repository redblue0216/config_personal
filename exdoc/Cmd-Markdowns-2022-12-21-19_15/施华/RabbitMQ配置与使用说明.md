# RabbitMQ配置与使用说明

标签（空格分隔）： 施华

---

# 1.安装
```
$ sudo apt update
$ sudo apt install erlang-nox
$ sudo apt install rabbitmq-server
```
# 2.服务操作
```
$ sudo rabbitmq-server start
$ sudo rabbitmq-server stop
$ sudo rabbitmq-server restart
$ sudo rabbitmqctl status
```
# 3.添加admin，并赋予administrator权限
```
# 添加admin用户，密码设置为admin。
$ sudo rabbitmqctl add_user  admin  admin  
# 赋予权限
$ sudo rabbitmqctl set_user_tags admin administrator
# 赋予virtual host中所有资源的配置、写、读权限以便管理其中的资源
$ sudo rabbitmqctl  set_permissions -p / admin '.*' '.*' '.*'
```
# 4.启动RabbitMQ
安装了Rabbitmq后，默认也安装了该管理工具，执行命令即可启动.
```
sudo  rabbitmq-plugins enable rabbitmq_management #(先定位到rabbitmq安装目录/usr/lib/rabbitmq/bin）
```
浏览器访问http://localhost:15672/
# 5.问题
+ ERROR: node with name "rabbit" already running的解决方法
```
$ sudo rabbitmqctl status
$ sudo rabbitmqctl stop
$ service rabbitmq-server start
$ service --status-all
```
+ web端访问权限
```
$ sudo rabbitmq-plugins enable rabbitmq_management
```
默认登录用户名和密码:guest
+ 使用pika包的时候，要注意一些函数的参数改变了，需要指定参数索引。




