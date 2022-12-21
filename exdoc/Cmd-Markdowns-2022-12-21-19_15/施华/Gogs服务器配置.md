# Gogs服务器配置

标签（空格分隔）： 施华

---


+ 安装docker
```
# 通过yum源安装docker
yum -y install docker
# 启动docker
systemctl start docker
# 开机自启
sudo systemctl enable docker
```
+ 下载gogs的docker镜像
```
docker pull gogs/gogs
```
+ 创建gogs存储的目录
```
mkdir -p /var/gogs
```
+ 运行镜像，产生容器
```
docker run --name=gogs -p 10022:22 -p 10080:3000 -v /var/gogs:/data gogs/gogs
# 后台运行
docker run --name=gogs -d  -p 10022:22 -p 10080:3000 -v /var/gogs:/data gogs/gogs
```
+ 在网页上配置
```
*.*.*.*:10080/install
#  SQLite3免数据库配置
```
+ git使用
```
# 进入文件夹
$ cd .......
# 初始化git文件
$ git init
# 配置用户信息
$ git config --global user.name "shihua"
$ git config --global user.email "***@**.com"
# 收集提交的文件
$ git add . ## . 代表目录下全部文件
# 提交到暂存区
$ git commit -m "说明" 
# 链接到远程仓库
$ git remote add origin htttp://.....
# 同步远端仓库
$ git pull origin master
# 将文件推送到远端仓库 -f 是强制推送
$ git push origin 
```




