# Spring Boot Mybatis简单使用
***
## 步骤说明
- build.gradle：依赖添加
- application.properties：配置添加
- 代码编写
- 测试

### build.gradle：依赖添加
&ensp;&ensp;&ensp;&ensp;需要添加下面三个依赖：

```bash
dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
    implementation 'org.mybatis.spring.boot:mybatis-spring-boot-starter:2.1.0'
    implementation 'mysql:mysql-connector-java'
}
```

### application.properties：配置添加
&ensp;&ensp;&ensp;&ensp;配置文件的编写需要注意参数的配置，比如SSL那个一般要设置为false，driver-class-name也要注意一下，不要写错了，文件的大致内容如下：

```bash
mybatis.type-aliases-package=com.seckill.spring.mapper

spring.datasource.url=jdbc:mysql://10.33.8.189:3306/test?serverTimezone=UTC&useUnicode=true&characterEncoding=utf-8&useSSL=false
spring.datasource.username=root
spring.datasource.password=root
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
```

### 代码编写
&ensp;&ensp;&ensp;&ensp;在入口函数添加Mapper扫描配置，这样不必在每个Mapper上加上Mapper注解，大致如下：

```java
package com.seckill.spring;

import org.mybatis.spring.annotation.MapperScan;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
@MapperScan("com.seckill.spring.mapper")
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

&ensp;&ensp;&ensp;&ensp;编辑商品实体类，大致如下：

```java
import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;
import javax.validation.constraints.Size;

@Entity
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

&ensp;&ensp;&ensp;&ensp;编写Mapper接口类，大致内容如下：

```java
import com.seckill.spring.entity.Goods;
import org.apache.ibatis.annotations.*;

import java.util.List;

public interface GoodsMapper {
    @Insert("INSERT INTO goods(name, amount) VALUES('${name}', #{amount})")
    Integer insertGoods(@Param("name")String name, @Param("amount")Integer amount) throws Exception;

    @Select("SELECT * FROM goods")
    List<Goods> findAll();

    @Select("SELECT * FROM goods WHERE id = #{id}")
    Goods findById(@Param("id") Integer id);

    @Update("UPDATE goods SET amount = #{goods.amount} WHERE id = #{goods.id}")
    Integer updateGoods(@Param("goods") Goods goods) throws Exception;

    @Delete("Delete FROM goods")
    Integer deleteAll();
}
```

&ensp;&ensp;&ensp;&ensp;其中要注意的是$和#的用法，前者用于字符串变量，后者用于整型变量

```java
// This example creates a prepared statement, something like select * from teacher where name = ?;
@Select("Select * from teacher where name = #{name}")
Teacher selectTeachForGivenName(@Param("name") String name);

// This example creates n inlined statement, something like select * from teacher where name = 'someName';
@Select("Select * from teacher where name = '${name}'")
Teacher selectTeachForGivenName(@Param("name") String name);
```

### 测试
&ensp;&ensp;&ensp;&ensp;测试还有些坑，不如类上面的注解应该如代码中的那样才有用，并且有时发现不了测试函数，需要去掉@Test注解，再重新添加后运行。大致打代码如下：

```java
import com.seckill.spring.Application;
import com.seckill.spring.entity.Goods;
import com.seckill.spring.mapper.GoodsMapper;
import org.junit.Assert;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.annotation.DirtiesContext;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;

import java.util.List;

@RunWith(SpringJUnit4ClassRunner.class)
@SpringBootTest(
        classes = Application.class,
        webEnvironment = SpringBootTest.WebEnvironment.DEFINED_PORT
)
@DirtiesContext
public class GoodsMapperTest {
    @Autowired
    private GoodsMapper goodsMapper;

    @Test
    public void testAll() throws Exception {
        goodsMapper.deleteAll();
        Assert.assertEquals(0, goodsMapper.findAll().size());
        Integer response = goodsMapper.insertGoods("good1", 1000);
        Assert.assertEquals(1, response.intValue());
        List<Goods> goods = goodsMapper.findAll();
        Assert.assertEquals(1, goods.size());
        int id = goods.get(0).getId();
        Assert.assertNotNull(goodsMapper.findById(id));
        Goods newGoods = new Goods(id, "good1", 100);
        Assert.assertEquals(1, goodsMapper.updateGoods(newGoods).intValue());
        Assert.assertEquals(100, goodsMapper.findById(id).getAmount());
    }
}
```

## 参考链接
- [Spring boot 启动报错-Reason Failed to determine a suitable driver class](https://blog.csdn.net/buyaore_wo/article/details/80741159)
- [java连接mysql失败Path does not chain with any of the trust anchors](https://blog.csdn.net/u013727805/article/details/80555726)
- [SpringBoot+Mybatis框架项目的单元测试和集成测试（下）](https://blog.csdn.net/wxx151556/article/details/77692960)
- [Spring Boot(六)：如何优雅的使用 Mybatis](https://www.cnblogs.com/ityouknow/p/6037431.html)
