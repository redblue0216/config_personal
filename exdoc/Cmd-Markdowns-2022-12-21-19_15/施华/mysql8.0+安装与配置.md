# mysql8.0+安装与配置

标签（空格分隔）： 施华

---

# 1.卸载
```
sudo apt purge mysql-*
sudo rm -rf /etc/mysql/ /var/lib/mysql
sudo apt autoremove
sudo apt autoclean
```
# 2.安装与重装
+ apt安装命令
```
sudo apt-get install mysql-server -y
sudo apt install mysql-client  -y
sudo apt install libmysqlclient-dev -y
```
+ 输入如下命令进行检验是否安装成功
```
sudo netstat -tap | grep mysql
```
+ 连接mysql配置
```
### 查找默认用户名密码
sudo cat /etc/mysql/debian.cnf 
### 登录mysql
mysql -udebian-sys-maint -pxxxxxxx
### 给root用户设置密码
mysql>use mysql;
mysql>flush privileges;
mysql>ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '你的密码';
mysql>flush privileges;
### 远程连接配置
### 查看权限表
mysql> use mysql;
mysql> select host, user, authentication_string, plugin from user; 
### 添加远程访问账号
mysql> create user 'root'@'%' identified by '你自己的mysql密码';
mysql> grant all privileges on *.* to 'root'@'%';
mysql> ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY '你自己的mysql密码';
mysql> flush privileges;
### mysql配置文件中把bind-address = 127.0.0.1一行注释掉
sudo vim /etc/mysql/mysql.conf.d/mysqld.cnf
### 重启mysql服务
sudo service mysql restart
```





