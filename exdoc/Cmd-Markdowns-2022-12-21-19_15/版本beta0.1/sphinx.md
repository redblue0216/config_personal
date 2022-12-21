# sphinx


标签（空格分隔）： 施华 版本beta0.1

---

# **sphinx**
+ 安装sphinx
```
$pip install sphinx
```
+ 项目文件结构目录
```
-code/
-doc/
--build
--source
--make.bat
--Makefile
```
+ 选择配置
```
$cd doc
$sphinx-quickstart
$#Project language [en]: zh_cn
```
+ 配置conf.py
```
$cd source
$#修改path
$#import os
$#import sys
$sys.path.insert(o,os.path.abspath('./../../code'))
$#修改extensions
$#extensions = ['sphinx.ext.autodoc']
$#修改html_theme
$pip install sphinx_rtd_theme
$#import sphinx_rtd_theme
$#html_theme = 'sphinx_rtd_theme'
$#html_theme_path = [sphinx_rtd_theme.get_html_theme_path()]
```
+ 生成rst文件并修改
```
$cd doc
$sphinx-apidoc -o ./source ../code/
$#修正rst文件,每个rst的文件的toctree要正确
$#以下面的为例子
$#.. toctree::
$#	:maxdepth:2
$#	:caption: Contents:
$#
$#	modules
```
+ 编译生成html文件
```
$#编译生成需要依赖rst文件，index.rst主要用来组织其他rst文件。生成的时候需要在makefile同级目录下执行。
$cd doc
$make html
```
+ 读取文档
```
$# doc/build/html/index.html
```

# 一般文档写法
+ 利用sphinx-quickstart快速生成文档结构
+ 在index.rst同文件夹下写其他文档，用index连接起来。
+ 使用rst语法





