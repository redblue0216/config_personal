# Ray安装部署

标签（空格分隔）： 施华         

---

[TOC]

# 安装
```
$ pip install ray[default] # 选择default才会全部安装模块
$ # ray=2.0.0版本需要对应aiohttp==3.7.4,pyqt5对应5.12.3，pyqtwebengine==5.12.1
```

# 启动
```
$ ray start --head --port=6379 --dashboard-host='0.0.0.0' --dashboard-port=8265 # head-node
$ ray start --address='192.168.1.73:6379'
```

# 其他
+ 前端使用headnode地址和dashboard端口






