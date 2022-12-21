# mongodb&robo3T

标签（空格分隔）： 施华 版本(beta0.1)

---

[TOC]
# **mongodb**
+ mongodb使用apt安装
```
$sudo apt update
$sudo apt upgrade
$sudo apt install -y mongodb
$sudo apt systemctl start mongodb
$sudo apt systemctl enable mongodb
$# 进入mongo
$mongo
$# 配置文件，允许远程登录
$cd /etc/mongodb.conf
$# 将bind_ip127.0.0.1修改为0.0.0.0
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



