# Spring Boot Lombok配置
***
### 依赖添加
```bash
dependencies {
    annotationProcessor 'org.projectlombok:lombok:1.18.2'
    compileOnly 'org.projectlombok:lombok:1.18.2'
    testAnnotationProcessor 'org.projectlombok:lombok:1.18.2'
    testCompileOnly 'org.projectlombok:lombok:1.18.2'
}
```

### IDEA下载Lombok插件
&ensp;&ensp;&ensp;&ensp;需要在插件中心中下载相应的插件，这样就不会提示代码错误

### 使用
&ensp;&ensp;&ensp;&ensp;简单的使用的话，Data和Builder注解应该就够了，详细的可以参考官方文档，大致的示例代码如下，代码中会自动生成id，name，和amount的get和set方法。

```java
package com.seckill.spring.entity;

import lombok.Builder;
import lombok.Data;

import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;
import javax.validation.constraints.Size;

@Entity
@Data
@Builder
public class Goods {
    public Goods(int id, String name, int amount) {
        this.id = id;
        this.name = name;
        this.amount = amount;
    }

    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private int id;

    @Size(min = 1, max = 50)
    private String name;

    private int amount;
}
```

## 参考链接
- [Gradle使用Lombok的正确方式 避免Gradle deprecated annotation processor warnings for lombok](https://blog.csdn.net/kcp606/article/details/81269184)
- [Spring Boot中使用Lombok](https://blog.frognew.com/2019/02/spring-boot-guides-lombok.html)
- [Reducing Boilerplate Code with Project Lombok](http://jnb.ociweb.com/jnb/jnbJan2010.html)
