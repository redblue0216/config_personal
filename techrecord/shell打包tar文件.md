# 构建思路
- [https://www.jianshu.com/p/6746cd7ab8ad](https://www.jianshu.com/p/6746cd7ab8ad)
- shell脚本分为两部分，第一部分为shell操作脚本，第二部分为tar包的二进制编码字符串
- 打包：
   - 设置两部分之间的标志位，将tar包的二进制编码（可引入加密机制）用管道引入到标志位后，用于后续对该编码进行解码，然后解压，最后再执行各种配置、安装操作。
   - 将目标文件夹打包压缩，引入到shell结尾@
```shell
$ tar -zcvm hello >> test.sh ### 也可以使用cat命令将tar包加入到shell中
```

   - 其他解压后操作，放到第一部分（主要包括各种环境配置、解压安放文件、安装文件和执行具体命令等操作，具体使用shell脚本语言编写）。
- 安装：
   - 直接bash命令调用shell脚本即可。
# 示例shell脚本
```bash
#!/bin/bash
#Test shell self-extracting
TmpDir=/tmp

ARCHIVE=$(awk '/^__ARCHIVE_BELOW__/ {print NR + 1; exit 0; }' "$0")
tail -n+$ARCHIVE "$0" | tar -xzvm -C $TmpDir > /dev/null 2>&1 3>&1
if [ $? == 0 ];then
        echo "Success"
else
        echo "Fail"
fi

# operator about install and setup
# cd $TmpDir
# pip install xxxxx.wheel
exit 0
#This line must be the last line of the file
__ARCHIVE_BELOW__
^_<8b>^H^@^\<8e>¨Z^@^CíÔM
Â0^P<86>á¬=En`¦ùéÚ<9e>¡^Wp!èªÐÆû;ÍB<8b> â"<95>âû^P<9a>@^B^Yø2Í§)ïM]Nµ1<96>Y=Ïe-¾<89><8d>¸<94><82><9e>^S^W}kl¬\Wq<9d>òq´Ö<8c>Ã<90>ß<9d>û´¿QyÎ¿×Ï¡Þ^]sÀ)<84>×ùKZä/<9a>¿<84>Ö^[ëê<95>ôðçù÷çËdu<94>'°ûu5XÛ½ÿ»zw|×ÿóÿ_¢nÓÿ+Xö^?Gÿ^C^@^@^@^@^@^@^@^@^@^@°I7^Z<8a>Êá^@(^@^@
```

- shell脚本中具体命令解释说明
   - awk命令主要用于获取shell脚本中第一部分和第二部分的划分标志位
   - tail命令用于定位显示第二部分的压缩包编码	
   - $0获取shell脚本本身文件名，$?为上一个命令执行的返回值
   - > /dev/null 2>&1 3>&1用来避免运行程序过程中有不必要的输出，/dev/null为空设备文件，2>&1为2重定向到1，1、2、3为PID信道代表标准输出，错误输出等
   - | 竖线为管道符，将前者的输出作为后者的输入
- 最后要留一行光标，以让二进制编码完成加入到shell尾部。
- 每次修改shell脚本后，最后的二进制编码都要重新加载一遍，不然会无法解码。


