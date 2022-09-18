# SWIG的使用

标签（空格分隔）： 施华 版本（beta0.1)

---

[TOC]

# SWIG
+ swig配置
```
$# http://swig.org/下载swig包
$tar -xvf swig-4.0.2.tar.gz
$cd swig-4.0.2
$wget https://ftp.pcre.org/pub/pcre/pcre-8.43.tar.gz # SWIG需要依赖pcre工作
$sh ./Tools/pcre-build.sh # 该脚本会将pcre自动构建成SWIG使用的静态库
$./configure # 注意需要安装bison,如果没有安装需要自己手动安装
$make
$sudo make install
```
+ 使用教程
```
$#准备CPP文件
$#编写接口文件，pca.i
$#生成so动态库，提供给python调用
$swig -c++ -python pca.i # 解释接口定义生成SWIG包装器代码
$g++ -fPIC -c pca.h pca.cpp utils.h utils.cpp pca_wrap.cxx -I/usr/include/python3.6
$g++ -shared pca.o pca_wrap.o utils.o -o _pca.so -O2 -Wall -std=c++11 -pthread -shared -fPIC -larmadillo
$# 缺少python.h的解决方案
$sudo apt install python3-dev
$cd /usr/include/python3.6 & ls
$# armadillo 安装
$sudo apt install libarmadillo-dev
```




