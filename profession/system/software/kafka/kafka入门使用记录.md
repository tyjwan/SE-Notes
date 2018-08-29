# kafka 入门使用记录
***
/root/enviroment/kafka_2.12-1.0.0/bin/kafka-console-consumer.sh --zookeeper node28:12181 --topic netflow --from-beginning

{"id": 2,"ip": "192.168.1.12","level": 1,"name": "192.168.1.12","status": "离线","time": 1533104638,"type": 1}
{"ip": "192.168.1.7","level": 1,"name": "192.168.1.7","status": "离线","time": 1533104638,"type": 1}
{"ip": "192.168.1.7","level": 1,"name": "HTTP","status": "不可用","time": 1533104638000,"type": 2}

### kafka安装
```
git clone https://github.com/dpkp/kafka-python
cd kafka-python
python setup.py install
```

## 参考链接
[Kafka常用命令行总结]
[kafka生产者示例](https://blog.csdn.net/propro1314/article/details/53284599)
[git hub:Kafka Python client](https://github.com/dpkp/kafka-python)
[python kafka api](https://kafka-python.readthedocs.io/en/master/apidoc/KafkaConsumer.html)
[Kafka Shell基本命令（包括topic的增删改查）](https://www.cnblogs.com/xiaodf/p/6093261.html#5)
