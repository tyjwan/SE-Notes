# Java Storm
***
## 本地模式
### Maven 配置
&ensp;&ensp;&ensp;&ensp;在pom.xml文件添加下面的依赖，本地模式运行需要去掉scope字段，暂时把它注释掉,在项目运行的vm参数中填写-Djava.net.preferIPv4Stack=true，避免一些运行错误
```sh
<!-- storm -->
<dependency>
    <groupId>org.apache.storm</groupId>
    <artifactId>storm-core</artifactId>
    <version>1.2.2</version>
    <!--<scope>provided</scope>-->
</dependency>
```

### spout类
&ensp;&ensp;&ensp;&ensp;可以把它看做生产消息的组件，任意建立一个类继承BaseRichSpout即可，在IDEA中继承后点击错误提示即可自动添加需要实现的方法。

&ensp;&ensp;&ensp;&ensp;open函数可以看做初始化函数。nextTuple函数是处理函数，在其中进行数据的获取或者初步的处理，通过collector.emit提交到下一个组件中。

```java
package spout;

import org.apache.storm.spout.SpoutOutputCollector;
import org.apache.storm.task.TopologyContext;
import org.apache.storm.topology.IRichSpout;
import org.apache.storm.topology.OutputFieldsDeclarer;
import org.apache.storm.topology.base.BaseRichSpout;
import org.apache.storm.tuple.Fields;
import org.apache.storm.tuple.Values;
import org.apache.storm.utils.Utils;

import java.util.Map;

public class ReadEventSpout extends BaseRichSpout{
    private int count;
    private SpoutOutputCollector collector;

    public void open(Map map, TopologyContext topologyContext, SpoutOutputCollector spoutOutputCollector) {
        this.collector = spoutOutputCollector;
        ++count;
    }

    public void nextTuple() {
        ++count;
        collector.emit(new Values(String.valueOf(count)));
        Utils.sleep(1000);
    }

    public void declareOutputFields(OutputFieldsDeclarer outputFieldsDeclarer) {
        outputFieldsDeclarer.declare(new Fields("event"));
    }
}
```

### bolt类
&ensp;&ensp;&ensp;&ensp;接收spout组件的消息进行处理，execute函数为处理函数，spout传过来的数据在tuple中，获取后进行处理

```java
package bolt;

import org.apache.storm.topology.BasicOutputCollector;
import org.apache.storm.topology.OutputFieldsDeclarer;
import org.apache.storm.topology.base.BaseBasicBolt;
import org.apache.storm.tuple.Tuple;


public class AnalysisBolt extends BaseBasicBolt{
    public void execute(Tuple tuple, BasicOutputCollector basicOutputCollector) {
        System.out.println(tuple.getString(0));
    }

    public void declareOutputFields(OutputFieldsDeclarer outputFieldsDeclarer) {

    }
}

```

### topology类
```java
package trident;

import bolt.AnalysisBolt;
import org.apache.storm.Config;
import org.apache.storm.LocalCluster;
import org.apache.storm.topology.TopologyBuilder;
import spout.ReadEventSpout;

public class CorrelationAnalysisTopology {
    public static void main(String[] args) {
        TopologyBuilder builder = new TopologyBuilder();
        builder.setSpout("spout", new ReadEventSpout());
        builder.setBolt("bolt", new AnalysisBolt()).shuffleGrouping("spout");

        Config config = new Config();
        config.setDebug(false);
        String topologyName = "CorrelationAnalysis";
        if (args != null && args.length > 0) {
            topologyName = args[0];
        }
        config.setNumWorkers(3);
//        StormSubmitter.submitTopologyWithProgressBar(topologyName, config, builder.createTopology());
        LocalCluster cluster = new LocalCluster();
        cluster.submitTopology(topologyName, config, builder.createTopology());
    }
}
```

## 集群提交运行
### Maven配置
```sh
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>NewCorrelationAnalysis</groupId>
    <artifactId>NewCorrelationAnalysis</artifactId>
    <version>1.0-SNAPSHOT</version>

    <dependencies>
        <!-- log4j support -->
        <dependency>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
            <version>1.2.17</version>
            <scope>compile</scope>
        </dependency>

        <!-- kafka -->
        <dependency>
            <groupId>org.apache.kafka</groupId>
            <artifactId>kafka-clients</artifactId>
            <version>0.11.0.0</version>
        </dependency>

        <!-- json gson -->
        <dependency>
            <groupId>com.google.code.gson</groupId>
            <artifactId>gson</artifactId>
            <version>2.8.0</version>
        </dependency>

        <!-- jedis redis -->
        <dependency>
            <groupId>redis.clients</groupId>
            <artifactId>jedis</artifactId>
            <version>2.8.1</version>
        </dependency>

        <!-- csv -->
        <dependency>
            <groupId>org.apache.commons</groupId>
            <artifactId>commons-csv</artifactId>
            <version>1.6</version>
        </dependency>

        <!-- storm -->
        <dependency>
            <groupId>org.apache.storm</groupId>
            <artifactId>storm-core</artifactId>
            <version>1.2.2</version>
            <!--<scope>provided</scope>-->
        </dependency>

        <!-- junit test -->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>RELEASE</version>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>org.junit.jupiter</groupId>
            <artifactId>junit-jupiter-api</artifactId>
            <version>RELEASE</version>
            <scope>test</scope>
        </dependency>

        <!-- mongodb -->
        <dependency>
            <groupId>org.mongodb</groupId>
            <artifactId>mongo-java-driver</artifactId>
            <version>3.2.2</version>
        </dependency>
        <dependency>
            <groupId>org.mongodb</groupId>
            <artifactId>bson</artifactId>
            <version>3.7.1</version>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-shade-plugin</artifactId>
                <!--<version>2.4.1</version>-->
                <executions>
                    <execution>
                        <phase>package</phase>
                        <goals>
                            <goal>shade</goal>
                        </goals>
                        <configuration>
                            <transformers>
                                <transformer implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
                                    <mainClass>trident.CorrelationAnalysisTopology</mainClass>
                                </transformer>
                            </transformers>
                        </configuration>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>

</project>
```

### Topology类修改
```java
package trident;

import bolt.AnalysisBolt;
import org.apache.storm.Config;
import org.apache.storm.LocalCluster;
import org.apache.storm.StormSubmitter;
import org.apache.storm.generated.AlreadyAliveException;
import org.apache.storm.generated.AuthorizationException;
import org.apache.storm.generated.InvalidTopologyException;
import org.apache.storm.topology.TopologyBuilder;
import spout.ReadEventSpout;

public class CorrelationAnalysisTopology {
    public static void main(String[] args) throws InvalidTopologyException, AuthorizationException, AlreadyAliveException {
        TopologyBuilder builder = new TopologyBuilder();
        builder.setSpout("spout", new ReadEventSpout());
        builder.setBolt("bolt", new AnalysisBolt()).shuffleGrouping("spout");

        Config config = new Config();
        config.setDebug(false);
        String topologyName = "CorrelationAnalysis";
        if (args != null && args.length > 0) {
            topologyName = args[0];
        }
        config.setNumWorkers(3);
        StormSubmitter.submitTopologyWithProgressBar(topologyName, config, builder.createTopology());
//        LocalCluster cluster = new LocalCluster();
//        cluster.submitTopology(topologyName, config, builder.createTopology());
    }
}
```

### 服务器提交
```sh
/opt/apache-storm-1.2.2/bin/storm jar ./target/NewCorrelationAnalysis-1.0-SNAPSHOT.jar trident.CorrelationAnalysisTopology ca
```

## 参考链接
- [storm-starter with intellij idea,maven project could not find class](https://stackoverflow.com/questions/30724424/storm-starter-with-intellij-idea-maven-project-could-not-find-class/30766250)
- [windows下storm本地模式java开发实例](https://my.oschina.net/u/2000675/blog/610753)
- [java.net.SocketException: Permission denied: connect异常解决](https://blog.csdn.net/huangzhen1991/article/details/81460791)
- [storm学习之六-使用Maven 生成jar包多种方式](https://www.cnblogs.com/200911/p/5085343.html)
- [Storm笔记整理（三）：Storm集群安装部署与Topology作业提交](http://blog.51cto.com/xpleaf/2097682)