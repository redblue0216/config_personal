# elasticsearch和kibana

标签（空格分隔）： 施华 版本（beta0.1)

---

[TOC]
# **elasticsearch**
+ 安装，使用apt-get
```
$wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
$sudo apt-get install apt-transport-https
$echo "deb https://artifacts.elastic.co/packages/7.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-7.x.list
$sudo apt-get update && sudo apt-get install elasticsearch
$#修改配置文件，以root身份
$sudo -i
$cd /etc/elasticsearch
$subl elasticsearch.yml
$#cluster.name: elasticsearchcluster #yml文件冒号后有一个空格
$#node.name: shihua-ThinkPad-E460 #后台运行syslog中的记录
$#network.host: 10.2.15.126
$#http.port: 9200
$#discovery.seed_hosts: ["shihua-ThinkPad-E460"]
$#cluster.inital_master_nodes: ["shihua-ThinkPad-E460"]
$#启动elasticsearch服务
$sudo systemctl enable elasticsearch.service
$sudo systemctl start elasticsearch.service
$# 测试使用10.2.15.126:9200地址接口
$# pip install CMRESHandler
```

# **kibana**
+ 安装、使用apt-get
```
$wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
$sudo apt-get install apt-transport-https
$echo "deb https://artifacts.elastic.co/packages/7.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-7.x.list
$sudo apt-get update && sudo apt-get install kibana
$#修改配置文件，以root身份
$sudo -i
$cd /etc/kibana
$subl kibana.yml
$#server.port: 5601
$#server.host: "10.2.15.126"
$#server.name: "shihua-ThinkPad-E460"
$#elasticsearch.hosts: ["http://10.2.15.126:9200"]
$#启动kibana服务
$sudo systemctl enable kibana.service
$sudo systemctl start kibana.service
$# 测试使用10.2.15.126:9200地址接口
```
+ 默认测试端口5601




