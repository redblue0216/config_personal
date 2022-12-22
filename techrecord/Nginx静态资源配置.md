# 安装
- ubuntu-apt安装
```bash
$ sudo apt install nginx
```

- 安装路径：/etc/nginx
## 静态资源配置

- http代码块中加入include conf.d或者include site-enabled类似语句，将分类配置文件包含进来
- 以conf.d为例子，进入该文件夹，创建atom.conf
```
server {
	listen 10086;
	server_name 127.0.0.1;
	location / {
  	root /home/shihua/tulip/workspace/nginx_test/html;
  	index index.html
  }
}
```

- 重新加载配置
```bash
$ nginx -s reload
```

- 重启nginx服务
```bash
$ sudo systemctl restart nginx.service
```

- 查看nginx服务状态
```bash
$ sudo systemctl status nginx.service
```
