# DASK和Hadoop安装配置及使用

标签（空格分隔）： 施华 beta0.1

---

[TOC]
# DASK
+ 安装
```
$pip install dask
$pip install dask distributed --upgrade
```
+ 配置
```
$#使用原生调度器部署
$dask-scheduler
$dask-worker 10.2.65.160:8786 -nthreads 8 -nprocs 4
$#WEB监控端口8787
```

# Hadoop（分布式）
+ 修改hostname
```
$vim /etc/hosts
$#10.2.65.160 DaskNode-161
$sudo hostnamectl set-hostname DaskNode-161
#### 10.2.65.161示例host
127.0.0.1	localhost
#127.0.1.1	shihua-OptiPlex-3050
#127.0.0.1       DaskNode-161
0.0.0.0         DaskNode-161
10.2.65.160     DaskMaster-160
10.2.65.161     DaskNode-161
10.2.65.162     DaskNode-162
10.2.65.163     DaskNode-163
10.2.65.164     DaskNode-164
10.2.65.165     DaskNode-165
10.2.65.166     DaskNode-166
10.2.65.167     DaskNode-167

# The following lines are desirable for IPv6 capable hosts
::1     ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters

### 其他slave机器hosts
127.0.0.1	localhost
#127.0.1.1	shihua-OptiPlex-3046
#127.0.0.1       DaskMaster-160
10.2.65.160     DaskMaster-160
10.2.65.161     DaskNode-161
10.2.65.162     DaskNode-162
10.2.65.163     DaskNode-163
10.2.65.164     DaskNode-164
10.2.65.165     DaskNode-165
10.2.65.166     DaskNode-166
10.2.65.167     DaskNode-167

# The following lines are desirable for IPv6 capable hosts
::1     ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
```
+ SSH免密登录
```
$ssh-keygen -t rsa
$ssh-copy-id ~/.ssh/id_ras.pub shihua@DaskNode-162
```
+ JAVA安装
```
$sudo apt install default-jre
$sudo apt install default-jdk
$vim ~/.bashrc
#######
export JAVA_HOME=/usr/lib/jvm/java-1.11.0-openjdk-amd64
export JRE_HOME=${JAVA_HOME}/jre
export CLASSPATH=:${JAVA_HOME}/lib:${JRE_HOME}/lib
export PATH=${JAVA_HOME}/bin:$PATH
export PATH=$PATH:/usr/local/hadoop/bin:/usr/local/hadoop/sbin
######
$# openjdk11缺少jre
$cd /usr/lib/jvm/java-1.11.0-openjdk-amd64
$sudo ./bin/jlink --module-path jmods --add-modules java.desktop --output jre
```
+ Hadoop下载安装
```
$# Hadoop3.3.0.tar.gz
######
sudo tar -zxf ~/下载/hadoop-3.3.0.tar.gz -C /usr/local    # 解压到/usr/local中
cd /usr/local/
sudo mv ./hadoop-3.3.0/ ./hadoop            # 将文件夹名改为hadoop
sudo chown -R shihua ./hadoop
hadoop version
######
```
+ hadoop 配置
```
######workers
DaskMaster-160
DaskNode-161
DaskNode-162
DaskNode-163
DaskNode-164
DaskNode-165
DaskNode-166
DaskNode-167
#######core-site.xml
<configuration>
	<property>
		<name>fs.defaultsFS</name>
		<value>hdfs://DaskNode-161:9900</value>
	</property>
	<property>
		<name>fs.default.name</name>
		<value>hdfs://DaskNode-161:9900</value>
	</property>
	<property>
		<name>hadoop.tmp.dir</name>
		<value>file:/usr/local/hadoop/tmp</value>
		<description>Abase for other temporary directories.</description>
	</property>
	<property>
		<name>hadoop.http.staticuser.user</name>
		<value>root</value>
	</property>
	<property>
		<name>hadoop.security.authorization</name>
		<value>false</value>
	</property>
</configuration>
#######hdfs-site.xml
<configuration>
	<property>
		<name>dfs.namenode.secondary.http-address</name>
		<value>DaskNode-161:50090</value>
	</property>
	<property>
		<name>dfs.replication</name>
		<value>8</value>
	</property>
	<property>
		<name>dfs.namenode.name.dir</name>
		<value>file:/usr/local/hadoop/tmp/dfs/name</value>
	</property>
	<property>
		<name>dfs.datanode.data.dir</name>
		<value>file:/usr/local/hadoop/tmp/dfs/data</value>
	</property>
	<property>
		<name>dfs.webhdfs.enabled</name>
		<value>true</value>
	</property>
	<property>
		<name>dfs.permission</name>
		<value>false</value>
	</property>
</configuration>
#######mapred-site.xml
<configuration>
	<property>
		<name>mapreduce.framework.name</name>
		<value>yarn</value>
	</property>
	<property>
		<name>mapreduce.jobhistory.adderss</name>
		<value>DaskNode-161:10020</value>
	</property>
	<property>
		<name>mapreduce.jobhistory.webapp.address</name>
		<value>DaskNode-161:19888</value>
	</property>
	<property>
		<name>yarn.app.mapreduce.am.env</name>
		<value>HADOOP_MAPRED_HOME=/usr/local/hadoop</value>
	</property>
	<property>
		<name>mapreduce.map.env</name>
		<value>HADOOP_MAPRED_HOME=/usr/local/hadoop</value>
	</property>
	<property>
		<name>mapreduce.reduce.env</name>
		<value>HADOOP_MAPRED_HOME=/usr/local/hadoop</value>
	</property>
</configuration>
########yarn-site.xml
<configuration>

<!-- Site specific YARN configuration properties -->
	<property>
		<name>yarn.resourcemanager.hostname</name>
		<value>DaskNode-161</value>
	</property>
	<property>
		<name>yarn.nodemanager.aux-services</name>
		<value>mapreduce_shuffle</value>
	</property>
</configuration>
```
+ Hadoop分发
```
#### master执行
cd /usr/local
sudo rm -r ./hadoop/tmp     # 删除 Hadoop 临时文件
sudo rm -r ./hadoop/logs/*   # 删除日志文件
tar -zcf ~/hadoop.master.tar.gz ./hadoop   # 先压缩再复制
cd ~
scp ./hadoop.master.tar.gz Slave1:/home/hadoop
#### slave执行
sudo rm -r /usr/local/hadoop    # 删掉旧的（如果存在）
sudo tar -zxf ~/hadoop.master.tar.gz -C /usr/local
sudo chown -R hadoop /usr/local/hadoop
```
+ 启动测试
```
$#在master主机执行
$hdfs namenode -format  (如果id问题请删除tmp下的dfs文件夹重新启动)
$start-dfs.sh
$start-yarn.sh
$mr-jobhistory-daemon.sh start historyserver
$jps
$hadoop fs -chmod -R 777 /
$#ssh权限问题，需要重新设置/etc/ssh/sshd_config文件，Strictmodes no,id_rsa权限改为600
```
+ 使用
```
###本机hosts配置添加DataNode节点地址以绕过hadoop权限认证管理
10.2.65.160     DaskMaster-160
0.0.0.0         DaskMaster-160
10.2.65.161     DaskNode-161
0.0.0.0         DaskNode-161
10.2.65.162     DaskNode-162
0.0.0.0         DaskNode-162
10.2.65.163     DaskNode-163
0.0.0.0         DaskNode-163
10.2.65.164     DaskNode-164
0.0.0.0         DaskNode-164
10.2.65.165     DaskNode-165
0.0.0.0         DaskNode-165
10.2.65.166     DaskNode-166
0.0.0.0         DaskNode-166
10.2.65.167     DaskNode-167
0.0.0.0         DaskNode-167
###yarn
10.2.65.161:8088
###hdfs
10.2.65.161:9870
###python
pip install hdfs
```
+ 示例图片
![1.PNG-56.3kB][1]
![2.PNG-39.5kB][2]
![3.PNG-74.2kB][3]


  [1]: http://static.zybuluo.com/tulip0216/g3iegov8maeeyiliccis5yls/1.PNG
  [2]: http://static.zybuluo.com/tulip0216/aiekknmytfwqbn287p683srt/2.PNG
  [3]: http://static.zybuluo.com/tulip0216/5j2ct9590csbflho3wzw6ama/3.PNG