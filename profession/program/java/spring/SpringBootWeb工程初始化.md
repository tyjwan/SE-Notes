# Spring Boot Web 工程初始化
***

## 初始化工程
可以到网站：[https://start.spring.io/](https://start.spring.io/)，这里可以配置初始工程

- Project：选择Maven方式或者Gradle方式构建
- Language：选择Java即可
- Spring Boot：默认即可
- Project Metadata：这里设置包名、工程名称、打包方式、Java版本
- Dependencies：添加额外的依赖，比如JDBC之类的，选择添加即可

设置完成后，点击最下方的GENERATE，下载压缩包到本地，本地解压后用IDE打开即可使用

## 编写 Hello Controller
IDE打开项目以后，我们写一个返回”Hello World！“的视图。

在根目录下新建目录：controllers

新建文件：HelloController.java

写入下面内容：

```java
package com.mall.MallWeb.controllers;

import com.mall.MallWeb.services.HelloService;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;

@Controller
public class HelloController {

    private final HelloService service;

    public HelloController(HelloService service) {
        this.service = service;
    }

    @RequestMapping("/")
    public @ResponseBody String hello() {
        return "Hello, World";
    }
}
```

## 运行项目
在根目录有一个后缀为：Application的Java文件，这个就是启动入口，点击运行

使用浏览器访问：http://localhost:8080/

将看到返回字符：Hello World！