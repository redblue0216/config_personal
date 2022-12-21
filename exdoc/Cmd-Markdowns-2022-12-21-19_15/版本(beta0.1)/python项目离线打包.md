# python项目离线打包

标签（空格分隔）： 施华 版本(beta0.1)

---

[TOC]
# wheel打包
+ 依赖包
```
$ pip install setuptools
$ pip install wheel
```
+ 打包
```
$ python setup.py bdist_wheel
```
+ 安装
```
$ cd dist
$ pip install xxxxxxxxxxxxxxxxx.whl
```
+ 打包非源码文件
```
### setuptools中include_package_data = True,
### setup.py同级目录下添加MANIFEST.in文件，MANIFEST相关语法可自行百度
### MANIFEST.IN中graft命令添加文件夹下所有文件
```
+ 添加readme文件




