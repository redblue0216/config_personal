# vscode-remote

标签（空格分隔）： 施华

---

# remote-ssh
+ vscode插件
# windows ssh 连接
+ ssh-keygen配置
```
$ ssh-keygen -t rsa
$ scp ./ssh/id_rsa.pub shihua@10.2.12.248:~/Congfig/ssh/win_id_rsa.pub
$ ssh shihua@10.2.12.248 -o StrictHostKeyChecking=no
$ linux:cat ~/Config/ssh/win_id_rsa.pub >> ~/.ssh/authorized_keys
```

# VScode 配置
+ 点击设置按钮，配置.ssh/config文件
```
Host shihua001
    Hostname 10.2.12.248
    User shihua
    Port 22
    IdentityFile "C:\Users\seth.shi\.ssh\id_rsa"
```
+ 安装vscode的python插件




