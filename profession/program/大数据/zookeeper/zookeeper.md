# zookeeper 使用记录
***
## 安装
### Java 安装
- 到官网下载rpm包，yum localinstall xxxxx 进行安装

### 单例模式
- wget http://mirror.bit.edu.cn/apache/zookeeper/zookeeper-3.4.10/zookeeper-3.4.10.tar.gz
- tar zxvf xxxxxx
- cp zoo_example.cfg zoo.cfg
- vim conf/zoo.cfg 里面设置dataDir存放的正常目录，使用tmp目录存在风险，可能不时崩溃
- bin/zkServer.sh start

### 集群部署
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

## 参考链接
- [ZooKeeper Getting Started Guide](https://zookeeper.apache.org/doc/current/zookeeperStarted.html)