# ShardingSphere Raw JDBC 主从示例
***
## 总览
&ensp;&ensp;&ensp;&ensp;这个示例不结合ORM之类的，直接执行SQL语句。通过写两个简单的文件就可以了，大致的步骤如下：

- 1.配置Maven依赖
- 2.配置文件写入相关配置
- 3.获取DataSource类编写
- 4.测试

### 配置Maven依赖
&ensp;&ensp;&ensp;&ensp;其主要依赖如下，还有一些spring、mysql的依赖，总的Maven文件链接在这：[pom.xml]()，自定查找添加吧

```java
<dependency>
    <groupId>org.apache.shardingsphere</groupId>
	<artifactId>shardingsphere-jdbc-core</artifactId>
	<version>5.0.0-alpha</version>
</dependency>
```

### application.properties文件写入
&ensp;&ensp;&ensp;&ensp;写入主从库的相关配置

```java
# 读写分离 - 数据库框架版本 2.0 ShardingSphere-jdbc 5.0.0-alpha
sharding.jdbc.datasource.names=master,slave0,slave1

sharding.jdbc.datasource.ds-master.driver-class-name=com.mysql.jdbc.Driver
sharding.jdbc.datasource.ds-master.url=jdbc:mysql://localhost:3306/test?serverTimezone=UTC&useUnicode=true\
  &characterEncoding=utf-8\
  &useSSL=false&allowPublicKeyRetrieval=true
sharding.jdbc.datasource.ds-master.username=root
sharding.jdbc.datasource.ds-master.password=root

sharding.jdbc.datasource.ds-slave0.driver-class-name=com.mysql.jdbc.Driver
sharding.jdbc.datasource.ds-slave0.url=jdbc:mysql://localhost:3309/test?serverTimezone=UTC&useUnicode=true\
  &characterEncoding=utf-8\
  &useSSL=false&allowPublicKeyRetrieval=true
sharding.jdbc.datasource.ds-slave0.username=root
sharding.jdbc.datasource.ds-slave0.password=root

sharding.jdbc.datasource.ds-slave1.driver-class-name=com.mysql.jdbc.Driver
sharding.jdbc.datasource.ds-slave1.url=jdbc:mysql://localhost:3310/test?serverTimezone=UTC&useUnicode=true&characterEncoding=utf-8\
  &useSSL=false&allowPublicKeyRetrieval=true
sharding.jdbc.datasource.ds-slave1.username=root
sharding.jdbc.datasource.ds-slave1.password=root
```

### ShardingMasterSlaveDataSource编写
&ensp;&ensp;&ensp;&ensp;这里默认使用HikariDataSource,思路主要是生成各个主从库的链接，放到Sharding中，代码中也有说明，总体比较简单：

```java
package com.example.demo.shardingsphere.raw.jdbc;

import com.zaxxer.hikari.HikariDataSource;
import io.shardingsphere.api.config.MasterSlaveRuleConfiguration;
import io.shardingsphere.shardingjdbc.api.MasterSlaveDataSourceFactory;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.core.env.Environment;
import org.springframework.stereotype.Component;

import javax.sql.DataSource;
import java.sql.SQLException;
import java.util.*;

/**
 * @author lw
 */
@Slf4j
@Component
public class ShardingMasterSlaveDataSource {

    private final String DRIVER = ".driver-class-name";
    private final String URL = ".url";
    private final String USERNAME = ".username";
    private final String PASSWORD = ".password";
    private final String DBS = "sharding.jdbc.datasource.names";

    @Autowired
    private Environment environment;

    DataSource createDataSource() throws SQLException {
        // 获取数据库列表
        String[] dbs = Objects.requireNonNull(environment.getProperty(DBS)).split(",");
        log.info("DBS::" + Arrays.toString(dbs));

        // 设置主从，约定第一个为主，其他为从
        MasterSlaveRuleConfiguration configuration = new MasterSlaveRuleConfiguration(dbs[0], dbs[0],
                Arrays.asList(Arrays.copyOfRange(dbs, 1, dbs.length)));
        log.info("ShardingMasterSlaveDataSource master :: " + configuration.getMasterDataSourceName());
        log.info("ShardingMasterSlaveDataSource slave :: " + configuration.getSlaveDataSourceNames());

        // 设置打印SQL语句，查看主从配置和切换是否成功
        Properties properties = new Properties();
        properties.setProperty("sql.show", "true");

        return MasterSlaveDataSourceFactory.createDataSource(createDataSourceMap(dbs), configuration, new HashMap<>(0),
                properties);
    }

    /**
     * 返回DataSource列表
     */
    private Map<String, DataSource> createDataSourceMap(String[] dbs) {
        Map<String, DataSource> result = new HashMap<>(dbs.length);
        for (String db: dbs) {
            log.info("Create data source ::" + db);
            result.put(db, createDataSource("sharding.jdbc.datasource.ds-" + db));
        }
        return result;
    }

    private DataSource createDataSource(String prefix) {
        log.info(DRIVER + "::" + environment.getProperty(prefix + DRIVER));
        log.info(URL + "::" + environment.getProperty(prefix + URL));
        log.info(USERNAME + "::" + environment.getProperty(prefix + USERNAME));
        log.info(PASSWORD + "::" + environment.getProperty(prefix + PASSWORD));

        HikariDataSource dataSource = new HikariDataSource();
        dataSource.setDriverClassName(environment.getProperty(prefix + DRIVER));
        dataSource.setJdbcUrl(environment.getProperty(prefix + URL));
        dataSource.setUsername(environment.getProperty(prefix + USERNAME));
        dataSource.setPassword(environment.getProperty(prefix + PASSWORD));
        return dataSource;
    }
}
```

### 测试
&ensp;&ensp;&ensp;&ensp;直接生写SQL了，就不结合ORM之类的了，这里是通过日志看出是否生效的，日志大致如下

```java
Sharding-Sphere-SQL                      : Rule Type: master-slave
Sharding-Sphere-SQL                      : SQL: select * from stores limit 5 ::: DataSources: slave0
Sharding-Sphere-SQL                      : Rule Type: master-slave
Sharding-Sphere-SQL                      : SQL: select * from stores limit 5 ::: DataSources: slave1
Sharding-Sphere-SQL                      : Rule Type: master-slave
Sharding-Sphere-SQL                      : SQL: insert into stores (name, description) VALUES ("name103", "description103"); ::: DataSources: master
Sharding-Sphere-SQL                      : Rule Type: master-slave
Sharding-Sphere-SQL                      : SQL: select * from stores limit 5 ::: DataSources: master
```
&ensp;&ensp;&ensp;&ensp;代码如下：

```java
package com.example.demo.shardingsphere.raw.jdbc;

import lombok.extern.slf4j.Slf4j;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit.jupiter.SpringExtension;
import org.springframework.transaction.annotation.Transactional;

import javax.sql.DataSource;
import java.sql.Connection;
import java.sql.SQLException;
import java.sql.Statement;

@Slf4j
@SpringBootTest
@ExtendWith(SpringExtension.class)
public class ShardingMasterSlaveDataSourceTest {

    @Autowired
    ShardingMasterSlaveDataSource shardingMasterSlaveDataSource;

    /**
     * 运行后从日志可以看出主从之间的切换和从库负载均衡
     */
    @Test @Transactional
    public void test() throws SQLException {
        DataSource dataSource = shardingMasterSlaveDataSource.createDataSource();
        log.info("ShardingMasterSlaveDataSource info::" + dataSource.getConnection().getMetaData().getURL());

        Connection conn = dataSource.getConnection();
        Statement statement = conn.createStatement();

        String sql = "select * from stores limit 5";
        statement.execute(sql);
        statement.execute(sql);

        sql = "insert into stores (name, description) VALUES (\"name103\", \"description103\");";
        statement.execute(sql);

        sql = "select * from stores limit 5";
        statement.execute(sql);
    }
}
```