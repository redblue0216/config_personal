# Docker封装实例记录

标签（空格分隔）： 施华

---

# Dokcer二进制安装
+ 准备docker二进制包
https://download.docker.com/linux/static/stable/x86_64/dockere-18.03.1-ce.tgz
+ 解压
```
$ tar zxvf docker-18.03.1-ce.tgz
```
+ 复制二进制文件到/usr/bin目录下
```
$ cp docker/* /usr/bin
```
+ 检查是否安装
```
$ docker version
```
+ 配置docker.service文件
```
$ vi /usr/lib/systemd/system/docker.service
```

```
[Unit]
Description=Docker Application Container Engine
Documentation=https://docs.docker.com
After=network-online.target firewalld.service
Wants=network-online.target
 
[Service]
Type=notify
ExecStart=/usr/bin/dockerd
ExecReload=/bin/kill -s HUP $MAINPID
LimitNOFILE=infinity
LimitNPROC=infinity
TimeoutStartSec=0
Delegate=yes
KillMode=process
Restart=on-failure
StartLimitBurst=3
StartLimitInterval=60s
 
[Install]
WantedBy=multi-user.target
```

+ 启动dockerd服务进程
```
$ systemctl daemon-reload
$ systemctl start docker
```
+ 检验进程
```
$ ps aux | grep docker
```

# Docker封装
+ docker拉取系统镜像
```
$ docker pull ubuntu:20.04
```
+ docker查看镜像
```
$ docker images
```
+ docker从已有镜像创建容器并进入
```
$ docker run -it ubuntu:20.04 /bin/bash
```
+ docker进入容器后配置
```
$ unminimize ### 恢复正常系统配置
$ apt install vim ### 为了编辑代码
$ apt install systemd ### 为了同步时间和进程控制
```
+ 将其他文件传入容器中
```
$ docker cp /home/shihua/shihua/workspace/JiyuanDeploy/JiyuanDeploy 307b5735361b:/jiyuan/
```
+ docker进入容器操作
```
$ docker exec -it 307b5735361b /bin/bash ### (适用于运行中容器)
$ ............容器内操作..........
```
+ docker容器打成镜像
```
$ docker commit -a "jiyuan_shihua" -m "create first image" a944a3c0aebd jiyuandeploy:v002
```
+ docker镜像打包
```
$ docker save -o docker_jiyuandeploy_v002.tar jiyuandeploy:v002
```
+ docker镜像加载
```
$ docker load -i docker_jiyuandeploy_v002.tar
```
+ docker镜像/容器删除
```
$ docker rmi a944a3c0aebd
$ docker rm a944a3c0aebd
```




