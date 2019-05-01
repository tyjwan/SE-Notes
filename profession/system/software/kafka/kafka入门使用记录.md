# kafka 入门使用记录
***
/root/enviroment/kafka_2.12-1.0.0/bin/kafka-console-consumer.sh --zookeeper node28:12181 --topic netflow --from-beginning
bin/kafka-console-consumer.sh --bootstrap-server localhost:2181 --topic test

## kafka 服务安装
### 单例模式
- wget http://mirror.bit.edu.cn/apache/kafka/2.2.0/kafka_2.12-2.2.0.tgz
- tar zxvf xxxxx
- vim /home/disk/software/kafka_2.11-2.1.0/config/server.properties: advertised.listeners=PLAINTEXT://x.x.x.x:9092
- bin/kafka-server-start.sh config/server.properties

### python kafka安装
```sh
git clone https://github.com/dpkp/kafka-python
cd kafka-python
python setup.py install
```

## 参考链接
- [Kafka常用命令行总结]
- [kafka生产者示例](https://blog.csdn.net/propro1314/article/details/53284599)
- [git hub:Kafka Python client](https://github.com/dpkp/kafka-python)
- [python kafka api](https://kafka-python.readthedocs.io/en/master/apidoc/KafkaConsumer.html)
- [Kafka Shell基本命令（包括topic的增删改查）](https://www.cnblogs.com/xiaodf/p/6093261.html#5)
- [Quick Start](https://kafka.apache.org/documentation/#quickstart)
- [Kafka配置详解](https://www.jianshu.com/p/f94bb7a70ab6)
- [Kafka集群无法外网访问问题解决攻略](https://blog.csdn.net/luoww1/article/details/71514776)
