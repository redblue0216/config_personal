# 安装配置
- 从官网下载install-tl-unx.tar.gz
- 解压tar包
```bash
$ tar -xzvf install-tl-unx.tar.gz
```

- 进入到解压后文件夹中，使用root用户执行install-tl
```bash
$ cd install-tl20xxxxxxx
$ su root
$ ./install-tl
$#根据选项进行安装配置
```

- 配置环境变量
```bash
$# 将export PATH=/usr/local/texlive/2021/bin/x86_64-linux:$PATH加入到～/.bashrc结尾处
```

- texstudio安装
```bash
$ sudo apt install texstudio
```
