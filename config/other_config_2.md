# other_config_2
------
[TOC]
# PostgreSQL
+ 安装
```
$ sudo apt update
$ sudo apt install postgresql postgresql-contrib
$ sudo -u postgres psql -c "SELECT version();"
```
+ 配置
```
$ sudo subl /etc/postgresql/12/main/postgresql.conf
$#listen_address = '*'
$ sudo subl /etc/postgresql/12/main/pg_hba.conf
$#ipv4添加host all all 0.0.0.0/0 trust
$ sudo service postgresql restart
$ ss -nlt | grep 5432
```

# Minio
```
$ wget https://dl.min.io/server/minio/release/linux-amd64/minio
$ chmod +x minio
$ cp minio /usr/bin
$ wget https://dl.min.io/client/mc/release/linux-amd64/mc
$ chmod +x mc
$ cp mc /usr/bin
$ ./minio server /data
$ ./mc --help
```
# Ray
```
$ pip install ray
$ ray start --head --port=6379 --dashboard-host=0.0.0.0
$ ray start --address='xx.xx.xxx.xxx' --redis-password='xxxxxxxxx'
$# head
$ python:ray.init('auto',_redis-password=xx)
$# web ui port is 8265
```
# Hydra
```shell
$ pip install hydra
$ ## package _global_
$ ## yaml
```

# Alluxio
```shell
$# 下载二进制文件
$# openssh免密登陆
$# java依赖
$ cp conf/alluxio-site.properties.template conf/alluxio-site.properties
$# alluxio.master.hostname = localhost
$# alluxio.master.mount.table.root.ufs = /tmp
$# 挂载底层文件系统
$ ./bin/alluxio-mount.sh SudoMount
$# 格式化文件系统
$ ./bin/alluxio format
$# 本地启动
$ ./bin alluxio-start.sh local
```

# InfluxDB
```shell
$ wget https://dl.influxdata.com/influxdb/releases/influxdb_1.8.4_amd64.deb
$ sudo dpkg -i infulxdb_1.8.4_amd64.deb
$ sudo influxd
$# sudo systemctl start influxdb
$# sudo systemctl status influxdb
$ influx>>>
$# chronograf
$ wget https://dl.influxdata.com/chronograf/releases/chronograf_1.8.10_amd64.deb
$ sudo systemctl start chronograf
$ sudo systemctl status chronograf
$#influxdb port is 8086
$#influxdb config   /etc/influxdb/config.conf
$ sudo chronograf
```

# Ansible
```shell
$ sudo apt install ansible
$ pip install ansible
$# /etc/ansible/hosts
$# ansible all -m shell -a "pwd"
```

# ClickHouse
```shell
$ sudo apt-get install apt-transport-https ca-certificates dirmngr
$ sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv E0C56BD4

$ echo "deb https://repo.clickhouse.tech/deb/stable/ main/" | sudo tee \
    /etc/apt/sources.list.d/clickhouse.list
$ sudo apt-get update

$ sudo apt-get install -y clickhouse-server clickhouse-client

$ sudo service clickhouse-server start
$ clickhouse-client

```



# vscode-remote
# remote-ssh
+ vscode插件
+ windows ssh 连接
+ ssh-keygen配置
```
$ ssh-keygen -t rsa
$ scp ./ssh/id_rsa.pub shihua@10.2.12.248:~/Congfig/ssh/win_id_rsa.pub
$ ssh shihua@10.2.12.248 -o StrictHostKeyChecking=no
$ linux:cat ~/Config/ssh/win_id_rsa.pub >> ~/.ssh/authorized_keys
```

+ VScode 配置
+ 点击设置按钮，配置.ssh/config文件
```
Host shihua001
    Hostname 10.2.12.248
    User shihua
    Port 22
    IdentityFile "C:\Users\seth.shi\.ssh\id_rsa"
```
+ 安装vscode的python插件

# SWIG
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

# rufus
+ rufus
+ 下载镜像iso
+ U盘格式化
+ 烧录，mbr/apt,iso,缺文件就下载
+ 完成，安全退出U盘

+ 安装
+ F12 bios
+ usb hdd driver
+ eg.bodhi