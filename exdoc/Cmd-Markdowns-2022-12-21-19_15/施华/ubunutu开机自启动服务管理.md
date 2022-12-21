# ubunutu开机自启动服务管理

标签（空格分隔）： 施华

---

[TOC]
# **sysv-rc-conf**
```
$cd /etc/apt
$vim sources.list
$#deb http://archive.ubuntu.com/ubuntu/ trusty main universe restricted multiverse
$sudo apt update
$sudo apt install sysv-rc-conf
```

# **systemctl**
```
$sudo systemctl enable xxxx
$sudo systemctl disable xxxxx
```




