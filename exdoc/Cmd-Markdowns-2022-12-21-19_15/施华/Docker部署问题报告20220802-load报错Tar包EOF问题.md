# Docker部署问题报告20220802-load报错Tar包EOF问题

标签（空格分隔）： 施华

---

# 问题情况
```
$ ERROR: Error processing tar file(exit status 1): unexpected EOF 
```

# 问题原因
+ .tar文件损坏（因为在本地看到的文件大小和通过fxp上传显示的大小不一致，本地显示6G左右，但
是传送显示大概在1.5G左右，所以一直在纠结是不是.tar文件的缺失）

+ 有可能是docker的docker根目录空间不足，然后进行解决，解决的方案见下面

# 问题解决方案
+ 针对Tar损坏，一个是打包时就损坏了，还有一个就是Ftp传输时默认是ASCII，传二进制文件要特别设置。
+ 针对Docker根目录空间不足问题，可修改其 Docker Root Dir 的值，使其指向一个更大空间的目录即可。
    + 查看docker的根目录   
    ```
    $ docker info
    ```
    + 查看目录空间
    ```
    $ df  -hl   /var/lib/docker/
    ```
    + 创建新空间
    ```
    $ mkdir -p /etc/systemd/system/docker.service.d/ ### 创建新目录
    
    $ vi /etc/systemd/system/docker.service.d/devicemapper.conf ### 创建配置文件
    
    $ 录入配置信息 
    [Service]
    ExecStart=/usr/bin/dockerd  --graph=/home/docker/lib/docker
    
    systemctl daemon-reload

    systemctl restart docker

    systemctl enable docker
    ```
    
    + 注意问题，由于更换了docker目录，以前下载的镜像需要转移到新目录下，本人通过 cp -R 命令复制过来后，启动时遇到一些问题，建议直接删除原来的镜像，重新下载







