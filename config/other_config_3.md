# other_config_3
[TOC]
------
## superset
+ 安装
```bash
$ pip install apache-superset
$ pip install mysqlclient
$ pip install pymysql
$ pip install pillow
```
+ 配置
```bash
$ superset db upgrade
$ export FLASK_APP=superset
$ flask fab create-admin
$ superset init
$ superset run -p 5001 --with-threads --reload --debugger
```

## adminer
+ 安装
```bash
$ sudo apt install apache2
$ sudo apt install php
$ sudo apt install libapache2-mod-php
$ systemctl status apache2
$ sudo apt install php-mysql
$ cd /var/www/html
$#http://localhost/xxxxx.php
```

## VSCode
+ 各种编译环境配置
+ 在系统当前用户下配置好对应语言运行环境，并保证在命令行中可使用命令手动编译成功，如GCC/G++命令
+ 在VSCode中安装code runner的.run插件
+ 该方法无需配置.vscode中的json配置文件，但重点是先要保证命令行中的编译环境可稳定运行


## Calibre
+ 下载安装包
+ 重新设置安装路径和书库目录
+ 下载DeDRM插件，使用首选项中的插件，并选择从文件安装DeDRM，DeDRM包解压之后，找到DeDRM压缩包，然后按照提示安装此压缩包即可。
+ 阅读图书，会自动破解图书的DRM