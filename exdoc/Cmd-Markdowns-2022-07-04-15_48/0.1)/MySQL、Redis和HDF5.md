# MySQL、Redis和HDF5

标签（空格分隔）： 施华 版本（beta 0.1)

---

[TOC]
# **MySQL**
+ 安装见AirFlow部分
+ 远程访问配置
```
# 登录MySQL数据库
$mysql -uxxxx -pxxxxx
$mysql>show databases;
$mysql>use mysql;
$mysql>show tables;
$# 注释掉IP地址绑定bind-address = 127.0.0.1
$sudo vim /etc/mysql/mysql.conf.d/mysqld.cnf
$# bind-address = 127.0.0.1
$# 删除匿名用户
$mysql>mysql -uxxxx -pxxxxxx
$mysql>use mysql;
$mysql>delete from user where user='';
$# 给用户授予在任意主机（%)访问任意数据库的所有权限
$mysql>grant all privileges on *.* to 'root'@'%' identified by '123456' with grant option;
$# 只把某个已存在的用户修改成允许远程访问
$mysql>update user set host='%' where user='root' and host='localhost';
```
+ root用户设置
```
$mysql -udebian-sys-maint -p
$use mysql;
$update mysql.user set authentication_string=password('123456') where user='root' and Host='localhost';
$update user set plugin='mysql_native_password';
$flush privileges;
$quit;
$#设置root远程访问权限
$mysql -uroot -p 
$use mysql;
$grant all privileges on *.* to 'root'@'%' identified by '123456' with grant option;
$flush privileges;
```
+ bug解决
```
$#only_group_by
$SHOW VARIABLES LIKE ‘%sql_mode%’;
$set sql_mode='';
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
+ 安装见AirFlow部分介绍
+ 远程访问配置
```
$#修改redis.conf
$#注释掉bind:127.0.0.1
$#修改保护模式为no
$vim redis-xxxx.conf
$#protected-mode no
```

# **HDF5**
+ 从官网下载tar.gz包
```
$ tar -zxvf hdf5-X.Y.Z
$ cd hdf5-X.Y.Z
$ ./configure --prefix=/usr/local/hdf5  #安装路径
$ make
$ make check                # run test suite.
$ sudo make install
$ sudo make check-install        # verify installation.
```
+ hdfview
```
$ sudo apt install hdfview
```





