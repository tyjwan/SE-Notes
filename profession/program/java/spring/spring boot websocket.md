# Spring Boot WebSocket 使用记录
***
## 简介
&ensp;&ensp;&ensp;&ensp;使用spring boot 提供的websocket搭建websocket服务器，其中主要的有下面的三个部分：

- WebSocketConfigurer:websocket相关配置，主要配置其入口、消息处理类、拦截器
- WebSocketHandler：处理相关的通信，连接的建立、消息的发送、错误的处理，大部分的代码写在这里
- HandshakeInterceptor ：拦截器，可以使用默认的HttpSessionHandshakeInterceptor()即可

### Maven相关导入
```java
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.example</groupId>
    <artifactId>visual</artifactId>
    <version>0.0.1-SNAPSHOT</version>

    <!-- Inherit defaults from Spring Boot -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>1.4.0.BUILD-SNAPSHOT</version>
    </parent>

    <!-- Add typical dependencies for a web application -->
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <!-- websocket -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-websocket</artifactId>
        </dependency>

        <!-- 用于测试的依赖项 -->
        <dependency>
            <groupId>org.mockito</groupId>
            <artifactId>mockito-core</artifactId>
            <version>1.9.5</version>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <!-- Package as an executable jar -->
    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

    <!-- Add Spring repositories -->
    <!-- (you don't need this if you are using a .RELEASE versio
    n) -->
    <repositories>
        <repository>
            <id>spring-snapshots</id>
            <url>http://repo.spring.io/snapshot</url>
            <snapshots><enabled>true</enabled></snapshots>
        </repository>
        <repository>
            <id>spring-milestones</id>
            <url>http://repo.spring.io/milestone</url>
        </repository>
    </repositories>
    <pluginRepositories>
        <pluginRepository>
            <id>spring-snapshots</id>
            <url>http://repo.spring.io/snapshot</url>
        </pluginRepository>
        <pluginRepository>
            <id>spring-milestones</id>
            <url>http://repo.spring.io/milestone</url>
        </pluginRepository>
    </pluginRepositories>
</project>
```

### 主类编写
```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

// SpringBootApplication 这个注入表示整个工程的入口
@SpringBootApplication
public class Main {
    public static void main(String[] args) {
    	// 设置服务的监听端口
        System.getProperties().put( "server.port", 8000);
        SpringApplication.run(Main.class, args);
    }
}
```

### WebSocketHandler 消息处理类编写
```java
import org.springframework.web.socket.*;

import java.io.IOException;
import java.util.HashMap;
import java.util.Map;

/**
 * websocket的处理程序
 */
public class SafetyAttackHandler implements WebSocketHandler{
    // 用于保存所有连接的客户端信息
    private static Map<WebSocketSession, Map> clients = new HashMap<WebSocketSession, Map>();

    /**
     * 连接成功后的操作
     * 连接成功建立以后添加客户端信息
     * @param webSocketSession
     * @throws Exception
     */
    @Override
    public void afterConnectionEstablished(WebSocketSession webSocketSession) throws Exception {
        webSocketSession.sendMessage(new TextMessage("Connect successful"));
        clients.put(webSocketSession, new HashMap());
    }

    /**
     * 收到消息后的处理
     * @param webSocketSession
     * @param webSocketMessage
     * @throws Exception
     */
    @Override
    public void handleMessage(WebSocketSession webSocketSession, WebSocketMessage<?> webSocketMessage) throws Exception {

    }

    /**
     * 发生错误后的处理
     * 移除相应的客户端信息
     * @param webSocketSession
     * @param throwable
     * @throws Exception
     */
    @Override
    public void handleTransportError(WebSocketSession webSocketSession, Throwable throwable) throws Exception {
        clients.remove(webSocketSession);
        webSocketSession.sendMessage(new TextMessage(throwable.toString()));
    }

    /**
     * 连接关闭时的处理
     * 移除关闭的客户端
     * @param webSocketSession
     * @param closeStatus
     * @throws Exception
     */
    @Override
    public void afterConnectionClosed(WebSocketSession webSocketSession, CloseStatus closeStatus) throws Exception {
        clients.remove(webSocketSession);
        webSocketSession.sendMessage(new TextMessage("Close connect"));
    }

    @Override
    public boolean supportsPartialMessages() {
        return false;
    }

    /**
     * 推送消息到所有的客户端
     * @param message
     */
    public static void sendMessageToAll(String message) {
        for(WebSocketSession session : clients.keySet()) {
            try {
                session.sendMessage(new TextMessage(message));
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}
```

### WebSocketConfigurer 配置编写
```java
import cn.nssas.eelantech.handlers.SafetyAttackHandler;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.socket.WebSocketHandler;
import org.springframework.web.socket.config.annotation.EnableWebSocket;
import org.springframework.web.socket.config.annotation.WebSocketConfigurer;
import org.springframework.web.socket.config.annotation.WebSocketHandlerRegistry;
import org.springframework.web.socket.server.support.HttpSessionHandshakeInterceptor;

/**
 * websocket接口配置
 */
@Configuration
@EnableWebSocket
public class SafetyAttackWebSocket implements WebSocketConfigurer {

    /**
     * 设置连接的路由、处理相关类
     * addHandler：设置websocket的处理类和相应接口URL："/safetyAttackWebsocket"
     * setAllowedOrigins：设置链接允许，*代码允许所有情况，需要进行此设置，不然可能导致连接失败
     * @param webSocketHandlerRegistry
     */
    @Override
    public void registerWebSocketHandlers(WebSocketHandlerRegistry webSocketHandlerRegistry) {
        webSocketHandlerRegistry.addHandler(myHandler(), "/safetyAttackWebsocket").addInterceptors(new HttpSessionHandshakeInterceptor()).setAllowedOrigins("*");
    }

    /**
     * 返回websocket连接对应处理类
     * @return
     */
    @Bean
    public WebSocketHandler myHandler() {
        return new SafetyAttackHandler();
    }
}
```

### 测试连接
- 可以使用页面的测试：http://www.blue-zero.com/WebSocket/
- 输入：ws://127.0.0.1:8000/safetyAttackWebsocke，进行连接即可