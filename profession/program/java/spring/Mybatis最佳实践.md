# Mybatis 最佳实践
***
## 配置
### 依赖
#### Gradle
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

### application.properties

```xml
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

### mybatis-config.xml

```xml
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

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN" "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <!-- 全局参数 -->
    <settings>
        <!-- 使全局的映射器启用或禁用缓存。 -->
        <setting name="cacheEnabled" value="true"/>
        <!-- 全局启用或禁用延迟加载。当禁用时，所有关联对象都会即时加载。 -->
        <setting name="lazyLoadingEnabled" value="true"/>
        <!-- 当启用时，有延迟加载属性的对象在被调用时将会完全加载任意属性。否则，每种属性将会按需要加载。 -->
        <setting name="aggressiveLazyLoading" value="true"/>
        <!-- 是否允许单条sql 返回多个数据集  (取决于驱动的兼容性) default:true -->
        <setting name="multipleResultSetsEnabled" value="true"/>
        <!-- 是否可以使用列的别名 (取决于驱动的兼容性) default:true -->
        <setting name="useColumnLabel" value="true"/>
        <!-- 允许JDBC 生成主键。需要驱动器支持。如果设为了true，这个设置将强制使用被生成的主键，有一些驱动器不兼容不过仍然可以执行。  default:false  -->
        <setting name="useGeneratedKeys" value="true"/>
        <!-- 指定 MyBatis 如何自动映射 数据基表的列 NONE：不隐射　PARTIAL:部分  FULL:全部  -->
        <setting name="autoMappingBehavior" value="PARTIAL"/>
        <!-- 这是默认的执行类型  （SIMPLE: 简单； REUSE: 执行器可能重复使用prepared statements语句；BATCH: 执行器可以重复执行语句和批量更新）  -->
        <!-- 对于批量更新操作缓存SQL以提高性能 BATCH,SIMPLE -->
        <setting name="defaultExecutorType" value="SIMPLE"/>
        <!-- 数据库超过25000秒仍未响应则超时 -->
        <setting name="defaultStatementTimeout" value="25000"/>
        <!-- 使用驼峰命名法转换字段。 -->
        <setting name="mapUnderscoreToCamelCase" value="true"/>
        <!-- 设置本地缓存范围 session:就会有数据的共享  statement:语句范围 (这样就不会有数据的共享 ) defalut:session -->
        <setting name="localCacheScope" value="SESSION"/>
        <!-- 设置但JDBC类型为空时,某些驱动程序 要指定值,default:OTHER，插入空值时不需要指定类型 -->
        <setting name="jdbcTypeForNull" value="NULL"/>
        <!-- 设置关联对象加载的形态，此处为按需加载字段(加载字段由SQL指 定)，不会加载关联表的所有字段，以提高性能 -->
        <setting name="aggressiveLazyLoading" value="false"/>
        <setting name="logImpl" value="org.apache.ibatis.logging.stdout.StdOutImpl"/>
    </settings>
    <typeAliases>
        <!--批量别名，默认为别名的规范就是对应包装类的类名首字母变为小写-->
        <package name="vip.codehome.springboot.tutorials.entity"/>
    </typeAliases>
    <plugins>
        <!--pagehelper分页插件-->
        <plugin interceptor="com.github.pagehelper.PageInterceptor">
            <!-- 使用MySQL方言的分页 -->
            <property name="helperDialect" value="sqlserver"/><!--如果使用mysql，这里value为mysql-->
            <property name="pageSizeZero" value="true"/>
        </plugin>
    </plugins>
</configuration>
```

### 增删改查语句编写配置
&ensp;&ensp;&ensp;&ensp;在写查询语句和更新语句的时候，经常遇到没有条件传入的时候查询出错的情况，使用trim标签就能应对这样的情况，下面是查询和更新的示例：

&ensp;&ensp;&ensp;&ensp;查询

```xml
    <update id="testTrim" parameterType="com.mybatis.pojo.User">
        update user
        <trim prefix="set" suffixOverrides=",">
            <if test="cash!=null and cash!=''">cash= #{cash},</if>
            <if test="address!=null and address!=''">address= #{address},</if>
        </trim>
        <where>id = #{id}</where>
    </update>
```
&ensp;&ensp;&ensp;&ensp;更新：

```xml
    <update id="testTrim" parameterType="com.mybatis.pojo.User">
        update user
        set
        <trim suffixOverrides="," suffix="where id = #{id}">
            <if test="cash!=null and cash!=''">cash= #{cash},</if>
            <if test="address!=null and address!=''">address= #{address},</if>
        </trim>
    </update>
```
&ensp;&ensp;&ensp;&ensp;下面是查询添加中多个ADN和OR的处理，前面是一般情况的，没有条件传入会报错，后面的不会

```xml
<select id="findActiveBlogLike"
     resultType="Blog">
  SELECT * FROM BLOG
  <where>
    <if test="state != null">
         state = #{state}
    </if>
    <if test="title != null">
        AND title like #{title}
    </if>
    <if test="author != null and author.name != null">
        AND author_name like #{author.name}
    </if>
  </where>
</select>
```

```xml
<select id="findActiveBlogLike"
     resultType="Blog">
  SELECT * FROM BLOG
  <trim prefix="WHERE" prefixOverrides="AND |OR ">
    <if test="state != null">
         state = #{state}
    </if>
    <if test="title != null">
        AND title like #{title}
    </if>
    <if test="author != null and author.name != null">
        AND author_name like #{author.name}
    </if>
  </trim>
</select>
```

## 参考链接
- [Mybatis中的trim标签 介绍](https://blog.csdn.net/u011118321/article/details/68946027)
- [Dynamic SQL](https://mybatis.org/mybatis-3/dynamic-sql.html)
- [28.MyBatis应用分析与最佳实践](https://zhuanlan.zhihu.com/p/139444913)