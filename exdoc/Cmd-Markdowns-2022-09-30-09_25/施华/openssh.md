# openssh

标签（空格分隔）： 施华 版本(beta0.1)

---

[TOC]
+ apt安装
```
$sudo apt install openssh-server
$sudo apt install openssh-client
$#修改配置文件
$cd /etc/ssh/sshd_config
$vim sshd_config
$#添加PermitRootLogin yes(默认为PermitRootLogin prohibit-password)
$#StrictModes no (default:yes)
$#重启SSH
$sudo service ssh restart
$#客户端安装
$sudo apt install openssh-client
$#WINDOWS使用Xshell等工具
$python ssh工具使用paramiko第三方包
```

+ 免密登录
```
$ssh-keygen -t rsa
$ssh-copy-id -i ~/.ssh/id_rsa.pub root@192.168.x.xxx (-o StrictHostKeyChecking=no)
$#ssh-copy-id将key写道远程机器的~/.ssh/authroized_key文件中
$ssh shihua@10.2.12.248
$#ssh shihua001 (配置/etc/hosts 10.2.12.248 shihua001)
$#Hostkey changed : ssh-keygen -R 10.2.12.248
```




