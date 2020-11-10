# Spring Boot Mybatis简单使用
***

**基于Spring Boot 2.3.4，Junit5**

## 步骤说明
&ensp;&ensp;&ensp;&ensp;整个工程的最终目录结构如下，添加文件或者新建的目录的参考：

```bash
└─src
    ├─main
    │  ├─java
    │  │  └─com
    │  │      └─mall
    │  │          └─MallWeb
    │  │              ├─controllers
    │  │              ├─mapper
    │  │              ├─model
    │  │              └─services
    │  └─resources
    │      └─mybatis
    │          └─mapper
    └─test
        └─java
            └─com
                └─mall
                    └─MallWeb
                        └─mapper
```

### build.gradle：依赖添加
&ensp;&ensp;&ensp;&ensp;需要添加下面依赖

- 'mysql:mysql-connector-java:8.0.14'
- 'org.mybatis.spring.boot:mybatis-spring-boot-starter:2.0.0'
- 'org.projectlombok:lombok:1.16.16'

```java
dependencies {
	implementation 'org.springframework.boot:spring-boot-starter-web'

	//	MySQL数据库需要
	implementation 'mysql:mysql-connector-java:8.0.14'
	//	spring mybatis
	implementation 'org.mybatis.spring.boot:mybatis-spring-boot-starter:2.0.0'
	//	lombok,用于Entity的自动get和set方法生成，不用自己写一大推的get和set方法
	implementation 'org.projectlombok:lombok:1.16.16'

	testImplementation('org.springframework.boot:spring-boot-starter-test') {
		exclude group: 'org.junit.vintage', module: 'junit-vintage-engine'
	}
}
```

### application.properties：配置添加
&ensp;&ensp;&ensp;&ensp;配置文件的编写需要注意参数的配置，比如SSL那个一般要设置为false，driver-class-name也要注意一下，不要写错了，文件的大致内容如下：

```bash
# mybatis的config文件位置配置
mybatis.config-location=classpath:mybatis/mybatis-config.xml
# 各个表的xml文件位置配置
mybatis.mapper-locations=classpath:mybatis/mapper/*.xml
mybatis.type-aliases-package=com.neo.model

# 数据库连接信息配置，自行更换数据库，用户名和密码
spring.datasource.url=jdbc:mysql://localhost:3306/mall?serverTimezone=UTC&useUnicode=true&characterEncoding=utf-8\
  &useSSL=false
spring.datasource.username=root
spring.datasource.password=root
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver

#springboot + mybatis设置将SQL语句打印到控制台
logging.level.com.mall.MallWeb.mapper=debug
```

### 代码编写
#### 入口环境配置Mapper扫描配置
&ensp;&ensp;&ensp;&ensp;在入口函数添加Mapper扫描配置，这样不必在每个Mapper上加上Mapper注解，大致如下：

```java
package com.mall.MallWeb;

import org.mybatis.spring.annotation.MapperScan;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

/**
 * @author lw
 */
@SpringBootApplication
@MapperScan("com.mall.MallWeb.mapper")
public class MallWebApplication {

	public static void main(String[] args) {
		SpringApplication.run(MallWebApplication.class, args);
	}

}
```

#### 编写实体类
&ensp;&ensp;&ensp;&ensp;在代码目录下创建model文件夹，用于存储实体（数据库表）。编辑实体类，大致如下：

```java
ppackage com.mall.MallWeb.model;

import java.io.Serializable;

/**
 * @author lw
 */
public class User implements Serializable {

    private Long id;
    private String name;
    private String password;
    private String phoneNumber;
    private Long money;

    public User(String name, String password, String phoneNumber) {
        this.name = name;
        this.password = password;
        this.phoneNumber = phoneNumber;
        this.money = 0L;
    }

    @Override
    public String toString() {
        return id + "::" + name + "::" + password + "::" + phoneNumber + "::" + money;
    }

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }

    public String getPhoneNumber() {
        return phoneNumber;
    }

    public void setPhoneNumber(String phoneNumber) {
        this.phoneNumber = phoneNumber;
    }

    public Long getMoney() {
        return money;
    }

    public void setMoney(Long money) {
        this.money = money;
    }

}

```

#### Mapper接口类
&ensp;&ensp;&ensp;&ensp;新建Mapper文件夹，里面放Mapper相关的接口类。编写Mapper接口类，大致内容如下，@Repository解决idea里面的bean使用错误

```java
package com.mall.MallWeb.mapper;

import com.mall.MallWeb.model.User;
import org.springframework.stereotype.Repository;

import java.util.List;

/**
 * @author lw
 */
@Repository
public interface UserMapper {

    List<User> queryAll();
    User queryOne(Long id);
    void add(User user);
    void update(User user);
}

```

#### Mytatis配置文件mybatis-config.xml
&ensp;&ensp;&ensp;&ensp;新建文件夹 resource/mybatis，下面新建：mybatis-config.xml 文件，写入下面的内容：

```java
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN" "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <typeAliases>
        <typeAlias alias="Integer" type="java.lang.Integer" />
        <typeAlias alias="Long" type="java.lang.Long" />
        <typeAlias alias="HashMap" type="java.util.HashMap" />
        <typeAlias alias="LinkedHashMap" type="java.util.LinkedHashMap" />
        <typeAlias alias="ArrayList" type="java.util.ArrayList" />
        <typeAlias alias="LinkedList" type="java.util.LinkedList" />
    </typeAliases>
</configuration>
```

#### 表users查询配置文件User.xml
&ensp;&ensp;&ensp;&ensp;新建文件夹 resouce/mybatis/mapper,新建文件：User.xml，输入下面的内容：

```java
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
<mapper namespace="com.mall.MallWeb.mapper.UserMapper" >
    <resultMap id="BaseResultMap" type="com.mall.MallWeb.model.User" >
        <id column="id" property="id" jdbcType="BIGINT" />
        <result column="name" property="name" jdbcType="VARCHAR" />
        <result column="password" property="password" jdbcType="VARCHAR" />
        <result column="phoneNumber" property="phoneNumber" jdbcType="VARCHAR"/>
        <result column="money" property="money" jdbcType="BIGINT" />
    </resultMap>

    <sql id="Base_Column_List" >
        id, name, password, phoneNumber, money
    </sql>

    <insert id="add" parameterType="com.mall.MallWeb.mapper.UserMapper" useGeneratedKeys="true" keyProperty="id">
        INSERT INTO
            users
            (name, password, phoneNumber, money)
        VALUES
            (#{name}, #{password}, #{phoneNumber}, #{money})
    </insert>

    <update id="update" parameterType="com.mall.MallWeb.model.User">
        UPDATE
            users
        SET
        <trim suffixOverrides="," suffix="WHERE id = #{id}">
            <if test="name != null">name = #{name},</if>
            <if test="password != null">password = #{password},</if>
            <if test="phoneNumber != null">phoneNumber = #{phoneNumber},</if>
            <if test="money != null">money = #{money},</if>
        </trim>
    </update>

    <select id="queryAll" resultMap="BaseResultMap">
        SELECT
            <include refid="Base_Column_List" />
        FROM users
    </select>

    <select id="queryOne" resultType="com.mall.MallWeb.model.User" parameterType="java.lang.Long">
        SELECT
            <include refid="Base_Column_List" />
        FROM users
            WHERE id = #{id}
    </select>

</mapper>
```

### 测试文件UserMapperTest
&ensp;&ensp;&ensp;&ensp;大致打代码如下：

&ensp;&ensp;&ensp;&ensp;在Junit5中没有了RunWith，换成了ExtendWith。测试中使用了真实的数据库，所有开启了测试后的数据回滚，避免测试数据进入数据库

```java
package com.mall.MallWeb.mapper;

import com.mall.MallWeb.model.User;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit.jupiter.SpringExtension;
import org.springframework.transaction.annotation.Transactional;

import java.util.List;

@ExtendWith(SpringExtension.class)
@SpringBootTest
public class UserMapperTest {

    @Autowired
    private UserMapper userMapper;

    @Test
    @Transactional
    public void addUserTest() {
        User user = new User("testUser", "testPassword", "testPhone");
        userMapper.add(user);
        assert userMapper.queryAll().size() != 0;
    }

    @Test
    public void queryTest() {
        List<User> users = userMapper.queryAll();
        assert users.size() != 0;
        for (User user: users) {
            System.out.println(user.toString());
        }
    }

    @Test
    @Transactional
    public void updateTest() {
        User user = new User("testUser", "testPassword", "testPhone");
        userMapper.add(user);
        System.out.println(user.toString());

        User newUser = userMapper.queryOne(user.getId());
        System.out.println(newUser.toString());

        newUser.setName("updateUser");
        System.out.println(newUser.toString());

        userMapper.update(newUser);
        System.out.println(newUser.toString());

        User queryUser = userMapper.queryOne(user.getId());
        assert queryUser.getName().equals("updateUser");
    }
}
```

### controllers目录下视图文件：UserController.java

```java
package com.mall.MallWeb.controllers;

import com.mall.MallWeb.mapper.UserMapper;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import java.util.HashMap;
import java.util.Map;

/**
 * 用户
 */
@RestController
@RequestMapping("/user")
public class UserController {

    @Autowired
    private UserMapper userMapper;

    @GetMapping("/users")
    public Map users() {
        Map response = new HashMap();
        response.put("status", "success");
        response.put("users", userMapper.queryAll());
        return response;
    }
}
```

### 启动与测试
&ensp;&ensp;&ensp;&ensp;现在开始运行程序，使用浏览器访问链接：http://localhost:8080/user/users

&ensp;&ensp;&ensp;&ensp;能看到有数据或者没有数据都是成功的（下面的数据是提前插入的）

```json
{
    "users": [
        {
            "id": 6,
            "name": "testUser",
            "password": "testPasspword",
            "phoneNumber": "testPhone",
            "money": 0
        },
        {
            "id": 7,
            "name": "testUser",
            "password": "testPasspword",
            "phoneNumber": "testPhone",
            "money": 0
        },
        {
            "id": 8,
            "name": "testUser",
            "password": "testPasspword",
            "phoneNumber": "testPhone",
            "money": 0
        },
        {
            "id": 9,
            "name": "testUser",
            "password": "testPasspword",
            "phoneNumber": "testPhone",
            "money": 0
        },
        {
            "id": 10,
            "name": "testUser",
            "password": "testPassword",
            "phoneNumber": "testPhone",
            "money": 0
        },
        {
            "id": 11,
            "name": "testUser",
            "password": "testPassword",
            "phoneNumber": "testPhone",
            "money": 0
        },
        {
            "id": 12,
            "name": "testUser",
            "password": "testPassword",
            "phoneNumber": "testPhone",
            "money": 0
        }
    ],
    "status": "success"
}
```


## 参考链接
- [Spring boot 启动报错-Reason Failed to determine a suitable driver class](https://blog.csdn.net/buyaore_wo/article/details/80741159)
- [java连接mysql失败Path does not chain with any of the trust anchors](https://blog.csdn.net/u013727805/article/details/80555726)
- [SpringBoot+Mybatis框架项目的单元测试和集成测试（下）](https://blog.csdn.net/wxx151556/article/details/77692960)
- [Spring Boot(六)：如何优雅的使用 Mybatis](https://www.cnblogs.com/ityouknow/p/6037431.html)
- [mybatis-spring-boot-test-autoconfigure](http://www.mybatis.org/spring-boot-starter/mybatis-spring-boot-test-autoconfigure/)
- [Idea inspects batis mapper bean wrong](https://stackoverflow.com/questions/25379348/idea-inspects-batis-mapper-bean-wrong/29841004)
- [Property 'sqlSessionFactory' or 'sqlSessionTemplate' are required in spring mock mvc test for spring boot with mybatis](https://github.com/mybatis/spring-boot-starter/issues/227)
- [mybatis传入多个参数](https://www.cnblogs.com/ningheshutong/p/5828854.html)
- [MyBatis多参数传递之Map方式示例——MyBatis学习笔记之十三](http://blog.51cto.com/legend2011/1030804)
- [MyBatis多参数传递的四种方式](https://blog.csdn.net/Victor_Cindy1/article/details/50195545)
- [Mybatis 开启控制台打印sql语句](https://blog.csdn.net/qq_37495786/article/details/82799910)
