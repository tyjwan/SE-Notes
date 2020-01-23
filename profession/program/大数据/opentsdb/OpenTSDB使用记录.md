# OpenTSDB 使用记录
***
## CentOS
*虚拟机直接NAT模式*
### docker镜像前置安装
```
yum install openssh-clients -y
yum install wget -y

vim /etc/sysconfig/network-script/ifcfg-xxxx
ONBOOT=yes
GATEWAY=10.0.0.1
DNS1=202.106.0.20
```

### Java安装
&ensp;&ensp;&ensp;&ensp;从官网下载相应的rpm包进行安装即可,默认路径：/usr/bin/java
```
scp -P 2822 root@119.39.96.61:/home/download/jdk-8u181-linux-x64.rpm ./
yum localinstall xxxx
```

### HBASE安装
&ensp;&ensp;&ensp;&ensp;

```
wget http://mirror.bit.edu.cn/apache/hbase/2.0.2/hbase-2.0.2-bin.tar.gz
scp -P 2822 root@119.39.96.61:/home/download/hbase-2.0.1-bin.tar.gz ./
tar xzvf hbase-3.0.0-SNAPSHOT-bin.tar.gz
cd hbase-3.0.0-SNAPSHOT/

vim conf/hbase-env.sh
JAVA_HOME=/usr

vim conf/hbase-site.xml
<configuration>
  <property>
    <name>hbase.rootdir</name>
    <value>file:///home/hbase</value>
  </property>
  <property>
    <name>hbase.zookeeper.property.dataDir</name>
    <value>/home/zookeeper</value>
  </property>
  <property>
    <name>hbase.cluster.distributed</name>
    <value>true</value>
  </property>
  <property>
    <name>hbase.unsafe.stream.capability.enforce</name>
    <value>false</value>
    <description>
      Controls whether HBase will check for stream capabilities (hflush/hsync).

      Disable this if you intend to run on LocalFileSystem, denoted by a rootdir
      with the 'file://' scheme, but be mindful of the NOTE below.

      WARNING: Setting this to false blinds you to potential data loss and
      inconsistent system state in the event of process and/or node failures. If
      HBase is complaining of an inability to use hsync or hflush it's most
      likely not a false positive.
    </description>
  </property>
</configuration>

bin/start-hbase.sh 

./bin/hbase shell
```

### zookeeper
```
wget https://mirrors.tuna.tsinghua.edu.cn/apache/zookeeper/zookeeper-3.4.10/zookeeper-3.4.10.tar.gz
tar zxvf xxxxx
mv xxxx /opt/zookeeper

cp conf/zoo_sample.cfg conf/zoo.cfg
./bin/zkServer.sh restart

vim conf/zoo.cfg
dataDir=/opt/zookeeper/data
dataLogDir=/opt/zookeeper/logs
server.1=192.168.0.136:2888:3888

vim /etc/profile
export ZOOKEEPER_HOME=/opt/zookeeper
export PATH=$PATH:$ZOOKEEPER_HOME/bin
source /etc/profile
```

### openTSDB
```
scp -P 2822 root@119.39.96.61:/home/download/opentsdb-2.3.1.noarch.rpm ./
yum localinstll xxx -y

env COMPRESSION=NONE HBASE_HOME=/opt/hbase /usr/share/opentsdb/tools/create_table.sh

vim /etc/opentsdb/opentsdb.conf
tsd.core.auto_create_metrics = true
tsd.storage.hbase.zk_quorum = localhost:2181

chkconfig --add opentsdb
service opentsdb start
```

### 端口开启让外网访问
```
/sbin/iptables -I INPUT -p tcp --dport 4242 -j ACCEPT
```

## 
- [2. Quick Start - Standalone HBase](https://hbase.apache.org/book.html#quickstart)
- [ZooKeeper Getting Started Guide](http://zookeeper.apache.org/doc/current/zookeeperStarted.html)
- [linux端口开放指定端口的两种方法](https://www.cnblogs.com/jtestroad/p/8031850.html)