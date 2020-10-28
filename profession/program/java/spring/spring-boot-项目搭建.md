# Spring Boot 项目搭建
***
## 构建方式
### IDEA编辑器新建Maven工程（Gradle类推）
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

## 官方网站构建（推荐）
&ensp;&ensp;&ensp;&ensp;此种方式比较简单方便，也不容其出错，也方便扩展。在网站上可以选择Maven或者Gradle构建放置，选择Spring Boot的版本，配置相关的工程信息，还可以选择添加初始的特定依赖，如数据库之类的。

&ensp;&ensp;&ensp;&ensp;网站地址:[https://start.spring.io/](https://start.spring.io/),构建完成后下载压缩文件到本地，进行解压后使用IDE打开即可使用。

## 参考链接
- [Spring boot设置启动监听端口](https://www.cnblogs.com/yoyotl/p/7089099.html)

### 教程
- [Spring Quickstart Guide](https://spring.io/quickstart)