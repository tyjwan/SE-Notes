# Spring Boot 项目搭建
***
## 环境
- 编辑器：IDEA

## 攻击建立
- 新建Maven工程
- 建立整个项目入口类，例如Main

```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

// SpringBootApplication 这个注入表示整个工程的入口
@SpringBootApplication
public class Main {
    public static void main(String[] args) {
    	// 设置服务的监听端口
        System.getProperties().put( "server.port", 8080);
        SpringApplication.run(Main.class, args);
    }
}
```

## 参考链接
- [Spring boot设置启动监听端口](https://www.cnblogs.com/yoyotl/p/7089099.html)