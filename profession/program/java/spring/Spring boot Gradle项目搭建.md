# Spring boot Gradle项目搭建
***
### 使用IDEA创建Gradle工程
&ensp;&ensp;&ensp;&ensp;操作大致为：File->new->Project->Gradle(在左侧选项栏中)

&ensp;&ensp;&ensp;&ensp;创建常规以后生成的工程目录如下：

- build
- gradle
    + wrapper
        * gradle-wrapper.jar
        * gradle-wrapper.properties
- src
    + java
    + resources
- test
    + java
    + resources
- build.gradle
- gradlew
- gradlew.bat
- settings.gradle

### 配置Spring boot
&ensp;&ensp;&ensp;&ensp;下面需要对两个文件进行编辑：

&ensp;&ensp;&ensp;&ensp;build.gradle文件修改后的内容如下,依赖一般是前面是groupId，中间是artifactId，第三个一般是版本。在repositories配置使用阿里云的仓库，避免下载过慢。

```bash
plugins {
    id 'java'
    id 'com.gradle.build-scan' version '2.0.2'
    id 'org.springframework.boot' version '2.0.5.RELEASE'
    id 'io.spring.dependency-management' version '1.0.7.RELEASE'
}

group 'seckill'
version '1.0-SNAPSHOT'

sourceCompatibility = 1.8

repositories {
    maven { url "http://maven.aliyun.com/nexus/content/groups/public/" }
    mavenCentral()
}

dependencies {
    testCompile group: 'junit', name: 'junit', version: '4.12'

    implementation 'org.springframework.boot:spring-boot-dependencies:2.0.5.RELEASE'
    implementation 'org.springframework.boot:spring-boot-starter-web'

    testImplementation 'org.springframework.boot:spring-boot-starter-test'

    components {
        withModule('org.springframework:spring-beans') {
            allVariants {
                withDependencyConstraints {
                    // Need to patch constraints because snakeyaml is an optional dependency
                    it.findAll { it.name == 'snakeyaml' }.each { it.version { strictly '1.19' } }
                }
            }
        }
    }
}

buildScan {

    // always accept the terms of service
    termsOfServiceUrl = 'https://gradle.com/terms-of-service'
    termsOfServiceAgree = 'yes'

    // always publish a build scan
    publishAlways()
}
```

&ensp;&ensp;&ensp;&ensp;setting.gradle文件修改后的内容如下：

```bash
rootProject.name = 'seckill'
enableFeaturePreview('IMPROVED_POM_SUPPORT')
```

### 编写类
&ensp;&ensp;&ensp;&ensp;首先在src/java下生成源码目录com.seckill.spring(相当于com/seckill/spring)

&ensp;&ensp;&ensp;&ensp;在src/java下创建程序入口类Application.java,其内容如下：

```java
package com.seckill.spring;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

&ensp;&ensp;&ensp;&ensp;在src/java/com.seckill.spring下创建目录controllers，并在controllers目录创建类：HelloWorld，在其中定义根路由并返回Hello World，其代码如下：

```java
package com.seckill.spring.controllers;

import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RestController;

import java.util.HashMap;
import java.util.Map;

@RestController
public class HelloWorld {
    @RequestMapping(value = "/", method = RequestMethod.GET)
    public Map helloWorld() {
        Map<String, Object> ret = new HashMap<>();
        ret.put("ret", "Hello World");
        return ret;
    }
}
```

### 单元测试
&ensp;&ensp;&ensp;&ensp;使用spring boot mockMVC测试非常方便

```java
package code.controllers;

import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.WebMvcTest;
import org.springframework.test.context.junit4.SpringRunner;
import org.springframework.test.web.servlet.MockMvc;

import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.get;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.content;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;

@RunWith(SpringRunner.class)
@WebMvcTest
public class HelloWorldTest {
    @Autowired
    private MockMvc mockMvc;

    @Test
    public void helloWorld() throws Exception {
        this.mockMvc.perform(get("/"))
                .andExpect(status().isOk())
                .andExpect(content().json("{'ret': 'Hello World'}"));
    }
}
```

### 运行访问
- 运行Application类，可以在控制台看到起默认监听在8080端口
- 浏览器访问:localhost:8080,可以看到返回字符串Hello World

## 参考链接
- [1.Building Spring Boot 2 Applications with Gradle](https://guides.gradle.org/building-spring-boot-2-projects-with-gradle/)
- [2.Learn Spring Boot](https://www.baeldung.com/spring-boot)
- [3.Building a RESTful Web Service](https://spring.io/guides/gs/rest-service/)
- [Gradle配置阿里云Maven镜像仓库地址](https://blog.csdn.net/ZZY1078689276/article/details/80364726)
- [Spring boot MockMVC example](https://howtodoinjava.com/spring-boot2/spring-boot-mockmvc-example/)
- [Spring Boot配置文件放在jar外部](https://www.cnblogs.com/xiaoqi/p/6955288.html)