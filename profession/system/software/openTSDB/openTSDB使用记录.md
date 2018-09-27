# openTSDB 使用记录
***
## Ctenos
### 安装
#### hbase
```
wget http://mirror.bit.edu.cn/apache/hbase/2.0.1/hbase-2.0.1-bin.tar.gz
tar zxvf hbase-2.0.1-bin.tar.gz
mv hbase-2.0.1 /opt/

cd /opt/
vim conf/hbase-site.conf
<property>
    <name>hbase.rootdir</name>
    <value>file:///DIRECTORY/hbase</value>
</property>

bin/start-hbash.sh
```


#### opentsdb
```
wget https://github.com/OpenTSDB/opentsdb/releases/download/v2.3.1/opentsdb-2.3.1.noarch.rpm
yum localinstall

# 查看是否有输出，有输出表示安装成功
tsdb version
```

### 配置
```
# 需要配置Apache的数据库HBASE
env COMPRESSION=NONE HBASE_HOME=/opt/hbase-2.0.1 /usr/share/opentsdb/tools/create_table.sh
```

## 参考链接
- [HBase 官方文档中文版](http://abloz.com/hbase/book.html#quickstart)
- [Index of /apache/hbase/2.0.1](http://mirror.bit.edu.cn/apache/hbase/2.0.1/)
- [基于阿里云HBase搭建OpenTSDB](https://yq.aliyun.com/articles/500387?spm=a2c4g.11186623.2.4.1f312dee6v917E)