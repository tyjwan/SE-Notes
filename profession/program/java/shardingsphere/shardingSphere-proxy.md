# ShardingSphere Proxy 入门使用
***
## 环境准备
### MySQL搭建
&ensp;&ensp;&ensp;&ensp;准备MySQL

- 准备三个mysql，一主二从，搭建教程链接：[Mysql docker 主从配置]()
    - 3306:主库
    - 3309、3310：从库

### 配置ShardingSphere-Proxy
&ensp;&ensp;&ensp;&ensp;这里使用5.0.0版本的

- 1.下载ShardingSphere-Proxy,[下载链接](https://www.apache.org/dyn/closer.cgi/shardingsphere/5.0.0-alpha/apache-shardingsphere-5.0.0-alpha-shardingsphere-proxy-bin.tar.gz)
- 2.下载完成后解压放到自己的目录下,比如我这里是放到：D:/temp/ssp
- 3.下载MySQL-connect.jar,[下载链接:mysql-connect.jar](https://repo1.maven.org/maven2/mysql/mysql-connector-java/5.1.47/mysql-connector-java-5.1.47.jar)
- 4.将MySQL-connect.jar，放到ShardingSphere-Proxy根目录的lib下，比如我这里是：D:/temp/ssp/lib/

- 5.写入配置文件，里面本身就有配置文件，这里需要写入的有两份，如果之前存在的就直接替换：server.xml和config-master_slave.yaml

&ensp;&ensp;&ensp;&ensp;server.xml 这份感觉不用改，如果和下面的一样就不用改
```java
######################################################################################################
#
# If you want to configure orchestration, authorization and proxy properties, please refer to this file.
#
######################################################################################################

# orchestration:
#  name: orchestration_ds
#  overwrite: true
#  registry:
#    type: zookeeper
#    serverLists: localhost:2181
#    namespace: orchestration

authentication:
  users:
    root:
      password: root
    sharding:
      password: sharding
      authorizedSchemas: test

props:
  max.connections.size.per.query: 1
  acceptor.size: 16  # The default value is available processors count * 2.
  executor.size: 16  # Infinite by default.
  proxy.frontend.flush.threshold: 128  # The default value is 128.
    # LOCAL: Proxy will run with LOCAL transaction.
    # XA: Proxy will run with XA transaction.
  # BASE: Proxy will run with B.A.S.E transaction.
  proxy.transaction.type: LOCAL
  proxy.opentracing.enabled: false
  query.with.cipher.column: true
  sql.show: false
```

&ensp;&ensp;&ensp;&ensp;config-master_slave.yaml,这个文件的配置关键字一定不要安装官方example的那个来，完全不行，要按照官方文档来，比如JDBCURL需要改成url，大致的文件内容如下：

```java
######################################################################################################
# 
# Here you can configure the rules for the proxy.
# This example is configuration of master-slave rule.
#   
# If you want to use master-slave, please refer to this file; 
# if you want to use sharding, please refer to the config-sharding.yaml.
# 
######################################################################################################

schemaName: test

dataSources:
  master_ds:
    url: jdbc:mysql://localhost:3306/test?serverTimezone=UTC&useUnicode=true&characterEncoding=utf-8&useSSL=false&allowPublicKeyRetrieval=true
    username: root
    password: root
    connectionTimeoutMilliseconds: 30000
    idleTimeoutMilliseconds: 60000
    maxLifetimeMilliseconds: 1800000
    maxPoolSize: 50
  slave_ds_0:
    url: jdbc:mysql://localhost:3309/test?serverTimezone=UTC&useUnicode=true&characterEncoding=utf-8&useSSL=false&allowPublicKeyRetrieval=true
    username: root
    password: root
    connectionTimeoutMilliseconds: 30000
    idleTimeoutMilliseconds: 60000
    maxLifetimeMilliseconds: 1800000
    maxPoolSize: 50
  slave_ds_1:
    url: jdbc:mysql://localhost:3310/test?serverTimezone=UTC&useUnicode=true&characterEncoding=utf-8&useSSL=false&allowPublicKeyRetrieval=true
    username: root
    password: root
    connectionTimeoutMilliseconds: 30000
    idleTimeoutMilliseconds: 60000
    maxLifetimeMilliseconds: 1800000
    maxPoolSize: 50


rules:
- !REPLICA_QUERY
  dataSources:
    master_ds:
      primaryDataSourceName: master_ds
      replicaDataSourceNames:
        - slave_ds_0
        - slave_ds_1
```

## 运行
&ensp;&ensp;&ensp;&ensp;经过前面的步骤基本配置差不多了，现在只需要运行Sharding-Proxy根目录下的 bin/start.bat就行了，运行命令大致如下：

```bash
# 进入相应的目录下，指定在13306端口运行，不指定默认运行在3307端口
.\start.bat 13306
```

&ensp;&ensp;&ensp;&ensp;这里需要注意的是，Java11运行好像有点问题，需要用1.8来运行，而不确定Java运行版本的，可以直接写个脚本：start.bat(start.sh类似),整个脚本修改大致如下：

```bash
@rem
@rem Licensed to the Apache Software Foundation (ASF) under one or more
@rem contributor license agreements.  See the NOTICE file distributed with
@rem this work for additional information regarding copyright ownership.
@rem The ASF licenses this file to You under the Apache License, Version 2.0
@rem (the "License"); you may not use this file except in compliance with
@rem the License.  You may obtain a copy of the License at
@rem
@rem     http://www.apache.org/licenses/LICENSE-2.0
@rem
@rem Unless required by applicable law or agreed to in writing, software
@rem distributed under the License is distributed on an "AS IS" BASIS,
@rem WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
@rem See the License for the specific language governing permissions and
@rem limitations under the License.
@rem

@echo off & setlocal enabledelayedexpansion

cd %~dp0

set SERVER_NAME=ShardingSphere-Proxy

set CLASS_PATH="..;..\lib\*;..\ext-lib\*"

set PORT=%1

set CONFIG=%2

if "%PORT%"=="-h" (
    goto print_usage
)
if "%PORT%"=="--help" (
    goto print_usage
)

if "%PORT%"=="" (
set MAIN_CLASS=org.apache.shardingsphere.proxy.Bootstrap
) else ( if "%CONFIG%"=="" (
    set MAIN_CLASS=org.apache.shardingsphere.proxy.Bootstrap %PORT%
    echo The port is configured as %PORT%
    set CLASS_PATH=../conf;%CLASS_PATH%
    ) else (
    set MAIN_CLASS=org.apache.shardingsphere.proxy.Bootstrap %PORT% %CONFIG%
    echo The port is configured as %PORT%
    echo The configuration path is %CONFIG%
    set CLASS_PATH=../%CONFIG%;%CLASS_PATH%
    )
    echo The classpath is %CLASS_PATH%
)

echo Starting the %SERVER_NAME% ...

# 在这里就直接设置死了Java的运行版本，如果不能正常运行，请去掉这行
F:\Software\Java\jdk1.8.0_261\bin\java.exe -server -Xmx2g -Xms2g -Xmn1g -Xss256k -XX:+DisableExplicitGC -XX:+UseConcMarkSweepGC -XX:+CMSParallelRemarkEnabled -XX:LargePageSizeInBytes=128m -XX:+UseFastAccessorMethods -XX:+UseCMSInitiatingOccupancyOnly -XX:CMSInitiatingOccupancyFraction=70 -Dfile.encoding=UTF-8 -classpath %CLASS_PATH% %MAIN_CLASS%

goto exit

:print_usage
 echo "usage: start.bat [port] [config_dir]"
 echo "  port: proxy listen port, default is 3307"
 echo "  config_dir: proxy config directory, default is conf"
 pause

:exit
 pause
```

&ensp;&ensp;&ensp;&ensp;运行正常的话，就可以看到刷刷刷的一排正常日志打出，这样就OK了

## ShardingSphere Proxy连接测试
&ensp;&ensp;&ensp;&ensp;使用docker mysql命令有问题，成功连接了但是有bug，但使用MySQL workbench连接和程序连接是没有问题的

### MySQL Workbench链接
&ensp;&ensp;&ensp;&ensp;使用图形化界面MySQL Workbench连接：本地ip、13306端口、root、root，连接成功，查询和删除，相应数据变化正确

### Java JDBC连接测试
&ensp;&ensp;&ensp;&ensp;下面是整个测试代码，可直接运行，运行下来也没有问题：

```java
package com.example.demo.shardingsphere.proxy.jdbc;

import com.zaxxer.hikari.HikariDataSource;
import org.junit.jupiter.api.Test;
import org.springframework.transaction.annotation.Transactional;

import javax.sql.DataSource;
import java.sql.Connection;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;

/**
 * ShardingSphere Proxy Raw JDBC测试
 */
public class ShardingSphereProxyRawJdbcTest {

    @Test @Transactional
    public void test() throws SQLException {
        System.out.println("Start test");
        DataSource SSDataSource = createShardingSphereProxyDataSource();
        DataSource slaveDataSource = createSlaveDataSource();

        // 测试通过SSP是否数据修改和查询正常
        Connection ssCon = SSDataSource.getConnection();
        Statement ssState = ssCon.createStatement();
        ssState.execute("UPDATE `users` SET `money` = '200' WHERE (`id` = '1');");

        ResultSet ret = ssState.executeQuery("select * from test.users where id=1;");
        while (ret.next()) {
            System.out.println(ret.getLong("money"));
            assert ret.getLong("money") == 200;
        }

        // 测试从库是否数据同步更改
        Connection slaveCon = slaveDataSource.getConnection();
        Statement slaveState = slaveCon.createStatement();
        ResultSet ret2 = slaveState.executeQuery("select * from test.users where id=1;");
        while (ret2.next()) {
            System.out.println(ret2.getLong("money"));
            assert ret2.getLong("money") == 200;
        }

        ret2.close();
        ret.close();
        ssState.close();
        slaveState.close();
        ssCon.close();
        slaveCon.close();
    }

    private DataSource createSlaveDataSource() {
        HikariDataSource dataSource = new HikariDataSource();
        dataSource.setDriverClassName("com.mysql.cj.jdbc.Driver");
        dataSource.setJdbcUrl("jdbc:mysql://localhost:3309/test?serverTimezone=UTC&useUnicode=true&characterEncoding" +
                "=utf-8&useSSL=false&allowPublicKeyRetrieval=true");
        dataSource.setUsername("root");
        dataSource.setPassword("root");
        return dataSource;
    }

    private DataSource createShardingSphereProxyDataSource() {
        HikariDataSource dataSource = new HikariDataSource();
        dataSource.setDriverClassName("com.mysql.cj.jdbc.Driver");
        dataSource.setJdbcUrl("jdbc:mysql://localhost:13306/test?serverTimezone=UTC&useUnicode=true&characterEncoding" +
                "=utf-8&useSSL=false&allowPublicKeyRetrieval=true");
        dataSource.setUsername("root");
        dataSource.setPassword("root");
        return dataSource;
    }
}
```

## 参考链接
- [Sharding-Proxy的基本功能使用](https://developer.aliyun.com/article/771099)
- [The sharding-proxy read-write separation study notes of shardingsphere](https://www.programmersought.com/article/48586082376/)