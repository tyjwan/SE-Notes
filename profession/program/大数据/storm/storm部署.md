# storm 集群部署
***
## 1.zookeerper 集群部署
- 确保机器装有jdk，如果没有请自行下载安装
- 设置Java堆大小？
- 下载解压安装zookeeper：http://hadoop.apache.org/zookeeper/releases.html
- 复制安装目录下的zoo_sample.cfg成zoo.cfg配置文件进行配置
```sh
tickTime=2000
dataDir=/var/zookeeper/
clientPort=2181
initLimit=5
syncLimit=2
server.1=zoo1:2888:3888
server.2=zoo2:2888:3888
server.3=zoo3:2888:3888
```

## kafka
- 压缩包解压进行相应的配置

## storm
- 压缩包解压进行相应的配置

## 集群服务启动
- 首先zookeerper
- 在kafka
- 再storm
	- 先领导，UI
	- 再supervisor

## 其他
- jsp命令可以查看
- /root/enviroment/目录下