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
- zookeerper
- kafka
- storm
	- 先领导，UI
	- 再supervisor

### zookeeper集群部署
- 首先在一台机器上下载相应的zookeeper源码放到相应的目录，复制安装目录的配置文件zoo.cfg
- 设置文件中的相应目录的相应的集群地址
```sh
# The number of milliseconds of each tick
tickTime=2000
# The number of ticks that the initial
# synchronization phase can take
initLimit=10
# The number of ticks that can pass between
# sending a request and getting an acknowledgement
syncLimit=5
# the directory where the snapshot is stored.
# do not use /tmp for storage, /tmp here is just
# example sakes.
dataDir=/var/zookeeper
# the port at which the clients will connect
clientPort=2181
# the maximum number of client connections.
# increase this if you need to handle more clients
#maxClientCnxns=60
#
# Be sure to read the maintenance section of the
# administrator guide before turning on autopurge.
#
# http://zookeeper.apache.org/doc/current/zookeeperAdmin.html#sc_maintenance
#
# The number of snapshots to retain in dataDir
#autopurge.snapRetainCount=3
# Purge task interval in hours
# Set to "0" to disable auto purge feature
#autopurge.purgeInterval=1
server.1=172.18.0.23:2888:3888
server.2=172.18.0.24:2888:3888
server.3=172.18.0.44:2888:3888
```

- 在在我们配置的dataDir指定的目录下面，创建一个myid文件，里面内容为一个数字，用来标识当前主机，conf/zoo.cfg文件中配置的server.X中X为什么数字，则myid文件中就输入这个数字，例如：echo "1" > /var/zookeeper/myid

- 安装验证：bin/zkServer.sh status

### storm 集群部署
- 检查Java的版本是不是Java 8+：java -version，不是的话请进行更新或安装
- 是否有python2
- wget http://mirrors.hust.edu.cn/apache/storm/apache-storm-1.2.2/apache-storm-1.2.2.tar.gz
- 设置配置文件storm.yaml
```sh

########### These MUST be filled in for a storm configuration
storm.local.dir: "/var/storm"
storm.zookeeper.servers:
     - "172.18.0.23"
     - "172.18.0.24"
     - "172.18.0.44"
#
nimbus.seeds: ["172.18.0.23", "172.18.0.24", "172.18.0.44"]
supervisor.slots.ports:
        - 6700
        - 6701
        - 6702
        - 6703
storm.health.check.dir: "healthchecks"
storm.health.check.timeout.ms: 5000
```

- /opt/apache-storm-1.2.2/bin/storm supervisor
- /opt/apache-storm-1.2.2/bin/storm nimbus

- 将一天机器上配置好的storm整个文件夹复制到其他的集群机器上
- 将其启动写入supervisor或deamon模式进行启动

## 其他
- jsp命令可以查看
- /root/enviroment/目录下

## 运行
### 查看日志
- 用storm jar ...将项目提交给storm集群后，想查看本项目的log信息，要到supervisor机器的：storm安装路径/logs/worker-number.log（其中的number视实际情况而定）中查看。
- 如果是用daemontools启动的storm，daemontools监控的目录是/service/storm，那么到/service/storm/logs中查看worker-number.log日志。

## 参考链接
- [https://blog.csdn.net/shirdrn/article/details/7183503](https://blog.csdn.net/shirdrn/article/details/7183503)
- [Setting up a Storm Cluster](http://storm.apache.org/releases/2.0.0-SNAPSHOT/Setting-up-a-Storm-cluster.html)
- [查看storm运行日志](https://blog.csdn.net/asdfsadfasdfsa/article/details/60778728)
- [storm集群的配置与启动](https://blog.csdn.net/loloxiaoz3/article/details/19294289)