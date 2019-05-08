# Kafka SASL 安全验证
***
*操作都在相应的软件安装目录下*

## 配置文件编写
### 服务配置文件kafka-server-jaas.conf
```sh
vim config/kafka-server-jaas.conf

# add
KafkaServer {
    org.apache.kafka.common.security.plain.PlainLoginModule required
    username="admin"
    password="admin"
    user_admin="admin"
    user_alice="alice";
};

KafkaClient {
  org.apache.kafka.common.security.plain.PlainLoginModule required
  username="alice"
  password="alice";
};
```

### 启动配置文件server.properties
```sh
cp config/server.properties config/sasl-server.properties

vim config/sasl-server.properties

# 在末尾添加
# 遇到外网端口不放连接的情况,需要直接判断外网ip和对应的开发的外网端口,好像topic自动创建会失效
advertised.listeners=SASL_PLAINTEXT://10.0.0.51:9092
listeners=SASL_PLAINTEXT://10.0.0.51:9092
security.inter.broker.protocol=SASL_PLAINTEXT
sasl.mechanism.inter.broker.protocol=PLAIN
sasl.enabled.mechanisms=PLAIN
```

### 启动文件kafka-server-start.sh
```sh
cp bin/kafka-server-start.sh bin/sasl-kafka-server-start.sh
vim bin/sasl-kafka-server-start.sh

# change the line from
if [ "x$KAFKA_HEAP_OPTS" = "x" ]; then
    export KAFKA_HEAP_OPTS="-Xmx1G -Xms1G"
fi

# to
if [ "x$KAFKA_HEAP_OPTS" = "x" ]; then
    export KAFKA_HEAP_OPTS="-Xmx1G -Xms1G -Djava.security.auth.login.config=$base_dir/../config/kafka-server-jaas.conf"
fi
```

### 生产者相关文件修改
#### 新建生产者客户端配置文件
```sh
vim ./config/jaas-kafka-client.conf

KafkaClient {
  org.apache.kafka.common.security.plain.PlainLoginModule required
  username="alice"
  password="alice";
};
```

### 生产者配置文件修改
```sh
cp ./config/producer.properties ./config/sasl-producer.properties
vim ./config/sasl-producer.properties

#修改bootstrap.servers
bootstrap.servers=10.0.0.51:9092

# 在末尾添加
security.protocol=SASL_PLAINTEXT
sasl.mechanism=PLAIN
```

#### 生产者启动脚本修改
```sh
cp ./bin/kafka-console-producer.sh ./bin/sasl-kafka-console-producer.sh
vim ./bin/sasl-kafka-console-producer.sh

# modifiy below
 if [ "x$KAFKA_HEAP_OPTS" = "x" ]; then
    export KAFKA_HEAP_OPTS="-Xmx512M"
 fi
 exec $(dirname $0)/kafka-run-class.sh kafka.tools.ConsoleProducer "$@"

# to
 if [ "x$KAFKA_HEAP_OPTS" = "x" ]; then
    export KAFKA_HEAP_OPTS="-Xmx512M"
 fi
 exec $(dirname $0)/kafka-run-class.sh -Djava.security.auth.login.config=$(dirname $0)/../config/jaas-kafka-client.conf kafka.tools.ConsoleProducer "$@"
```

### 消费者相关修改
#### 新建生产者客户端配置文件
```sh
vim ./config/jaas-kafka-consumer.conf

KafkaServer {
    org.apache.kafka.common.security.plain.PlainLoginModule required
    username="admin"
    password="admin"
    user_admin="admin"
    user_alice="alice";
};

KafkaClient {
  org.apache.kafka.common.security.plain.PlainLoginModule required
  username="alice"
  password="alice";
};
```

#### 新建消费者配置文件
```sh
cp ./config/consumer.properties ./config/sasl-consumer.properties
vim ./config/sasl-consumer.properties

#修改bootstrap.servers
bootstrap.servers=10.0.0.51:9092

# 末尾添加
security.protocol=SASL_PLAINTEXT
sasl.mechanism=PLAIN
```

#### 消费者启动脚本修改
```sh
cp ./bin/kafka-console-consumer.sh ./bin/sasl-kafka-console-consumer.sh
vim ./bin/sasl-kafka-console-consumer.sh

# modifiy below
if [ "x$KAFKA_HEAP_OPTS" = "x" ]; then
   export KAFKA_HEAP_OPTS="-Xmx512M"
fi
exec $(dirname $0)/kafka-run-class.sh kafka.tools.ConsoleConsumer "$@"

# to
if [ "x$KAFKA_HEAP_OPTS" = "x" ]; then
   export KAFKA_HEAP_OPTS="-Xmx512M"
fi
exec $(dirname $0)/kafka-run-class.sh -Djava.security.auth.login.config=$(dirname $0)/../config/jaas-kafka-consumer.conf kafka.tools.ConsoleConsumer "$@"
 ```

## 集群配置
可以复制整个zookeeper和kafka的文件夹,修改配置文件的中kafka配置文件的broken.id和zookeeper的myid文件内容

## 安全验证后使用的生产和消费命令
- /usr/local/kafka/bin/sasl-kafka-server-start.sh -daemon /usr/local/kafka/config/sasl-server.properties

- /usr/local/kafka/bin/sasl-kafka-console-producer.sh --broker-list 172.19.104.18:9092,172.19.104.19:9092,172.19.104.21:9092 --topic test --producer.config /usr/local/kafka/config/sasl-producer.properties

- /usr/local/kafka/bin/sasl-kafka-console-consumer.sh --bootstrap-server 172.19.104.18:9092,172.19.104.19:9092,172.19.104.21:9092 --topic test --consumer.config /usr/local/kafka/config/sasl-consumer.properties

- /usr/local/kafka/bin/kafka-topics.sh --list --zookeeper 172.19.104.18:2181,172.19.104.19:2181,172.19.104.21:2181

- /usr/local/kafka/bin/kafka-topics.sh --create --zookeeper 172.19.104.18:2181,172.19.104.19:2181,172.19.104.21:2181 --topic test1 --replication-factor 2 --partitions 3

## 编程调用
### Python
```python
# 消费者
consumer = KafkaConsumer(bootstrap_servers='172.19.104.18:9092,172.19.104.19:9092,172.19.104.21:9092', security_protocol="SASL_PLAINTEXT", sasl_mechanism='PLAIN', sasl_plain_username='alice', sasl_plain_password='alice')
consumer.subscribe(["test"])
for msg in consumer:
    print(str(msg[6], encoding="utf-8"))

# 生产者
producer = KafkaProducer(bootstrap_servers='172.19.104.18:9092,172.19.104.19:9092,172.19.104.21:9092', security_protocol="SASL_PLAINTEXT", sasl_mechanism='PLAIN', sasl_plain_username='alice', sasl_plain_password='alice')
msg = {"test": "test"}
producer.send("test", bytes(str(msg).replace("'", '"'), "utf-8"))
time.sleep(1)
```

### Java
*kafka_server_jaas.conf kafka_client_jaas.conf是上面新建文件：jaas-kafka-client.conf jaas-kafka-consumer.conf的复制，对应的原理是将kafka验证文件进行加载读取即可*

```java
	/***
     * 生成特定Topic的消费者并返回(为了避免消息丢失，采用手动提交位移方式）
     * @param topic
     * @param groupName
     * @return
     */
    public static KafkaConsumer<String, String> createConsumer(String topic, String groupName) {
        logger.info("Create kafka consumer");
        Properties properties = new Properties();
        properties.put("bootstrap.servers", Holder.get("kafka.host"));
        properties.put("group.id", groupName);
        properties.put("enable.auto.commit", "true");
        properties.put("auto.commit.interval.ms", "1000");
        properties.put("auto.offset.reset", "latest");
        properties.put("session.timeout.ms", "30000");
        properties.put("key.deserializer", "org.apache.kafka.common.serialization.StringDeserializer");
        properties.put("value.deserializer", "org.apache.kafka.common.serialization.StringDeserializer");
        System.setProperty("java.security.auth.login.config", "conf/kafka_server_jaas.conf");
        properties.put("security.protocol", "SASL_PLAINTEXT");
        properties.put("sasl.mechanism", "PLAIN");

        KafkaConsumer<String, String> kafkaConsumer = new KafkaConsumer<String, String>(properties);
        kafkaConsumer.subscribe(Arrays.asList(topic));
        return kafkaConsumer;
    }

    /***
     * 生成生产者并返回
     * @return
     */
    public static KafkaProducer<String, String> createProducer() {
        logger.info("Create kafka producer");
        Properties properties = new Properties();
        properties.put("bootstrap.servers", Holder.get("kafka.host"));
        properties.put("acks", "all");
        properties.put("retries", 0);
        properties.put("batch.size", 16384);
        properties.put("linger.ms", 1);
        properties.put("buffer.memory", 33554432);
        properties.put("key.serializer", "org.apache.kafka.common.serialization.StringSerializer");
        properties.put("value.serializer", "org.apache.kafka.common.serialization.StringSerializer");
        System.setProperty("java.security.auth.login.config", "conf/kafka_client_jaas.conf");
        properties.put("security.protocol", "SASL_PLAINTEXT");
        properties.put("sasl.mechanism", "PLAIN");
        return new KafkaProducer<String, String>(properties);
    }
```

## 参考链接
- [User authentication and authorization in Apache Kafka](https://developer.ibm.com/tutorials/kafka-authn-authz/)
- [Java Api Consumer 连接启用Kerberos认证的Kafka](https://blog.csdn.net/Sjdjjdnfjf/article/details/78386726)
- [Kafka集群安装配置，kafka后台运行的方式，Kafka配置文件中的参数说明](https://blog.csdn.net/tototuzuoquan/article/details/73430874)
- [Documentation](https://kafka.apache.org/documentation/#quickstart)
- [Kafka常用命令(带SASL权限版)](https://blog.csdn.net/u010416101/article/details/81165578)
- [Kafka 0.10.0 SASL/PLAIN身份认证及权限实现](https://www.jianshu.com/p/102606f8a795)
- [Kakfa-SASL身份验证登陆](http://www.voidcn.com/article/p-vkewulnr-bqg.html)