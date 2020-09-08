# IDEA 使用记录
***
## 插件类
- Ctrl + Alt + S：打开插件安装
-  Power mode II：写代码是整个屏幕都在抖动，Power mode II色彩更酷炫点。
- Alibaba Java Coding Guidelines：阿里巴巴代码规范检查插件，当然规范可以参考《阿里巴巴Java开发手册》。
- Key promoter:Key promoter 是IntelliJ IDEA的快捷键提示插件，会统计你鼠标点击某个功能的次数，提示你应该用什么快捷键，帮助记忆快捷键，等熟悉了之后可以关闭掉这个插件。
- JRebel:JRebel是一种热部署生产力工具，修改代码后不用重新启动程序，所有的更改便可以生效。它跳过了Java开发中常见的重建、重新启动和重新部署周期。
- MetricsReloaded:代码复杂度检查
- Statistic:代码统计
-  .ignore：https://plugins.jetbrains.com/plugin/7495--ignore，成各种ignore文件，一键创建git ignore文件的模板，免得自己去写
-  Add to gitignore
- FindBugs-IDEA：https://plugins.jetbrains.com/plugin/3847-findbugs-idea，写完代码后检测下 避免低级bug，强烈建议用一下，一不小心就发现很多老代码的bug
- Grep console：自定义日志颜色，idea控制台可以彩色显示各种级别的log，安装完成后，在console中右键就能打开。
- VisualVM Launcher：https://plugins.jetbrains.com/plugin/7115-visualvm-launcher，运行java程序的时候启动visualvm，方便查看jvm的情况 比如堆内存大小的分配某个对象占用了多大的内存，jvm调优必备工具
- MyBatisCodeHelperPro：https://plugins.jetbrains.com/plugin/9837-mybatiscodehelperpro，mybatis代码自动生成插件，大部分单表操作的代码可自动生成 减少重复劳动 大幅提升效率
- Rainbow Brackets：https://plugins.jetbrains.com/plugin/10080-rainbow-brackets，彩虹颜色的括号 看着很舒服 敲代码效率变高
-  Translation:https://plugins.jetbrains.com/plugin/8579-translation,最好用的翻译插件，功能很强大，界面很漂亮
- RestfulToolkit:接口调用调试，极其好用
- aiXcoer:代码预测补全， https://mp.weixin.qq.com/s/6O2oO0_JgQUZOcW9hUmw2Q

### 插件下载官网
&ensp;&ensp;&ensp;&ensp;有时候插件刷新不出来或者下载超时，可以到官网进行下载，注意选择对应软件的版本的插件，然后选择从磁盘安装插件即可，不用解压。

- [JetBrains Plugins Repository](https://plugins.jetbrains.com/)


#### Lombok
Java语言，每次写实体类的时候都需要写一大堆的setter，getter，如果bean中的属性一旦有修改、删除或增加时，需要重新生成或删除get/set等方法，给代码维护增加负担，这也是Java被诟病的一种原因。Lombok则为我们解决了这些问题，使用了lombok的注解(@Setter,@Getter,@ToString,@@RequiredArgsConstructor,@EqualsAndHashCode或@Data)之后，就不需要编写或生成get/set等方法，很大程度上减少了代码量，而且减少了代码维护的负担。

```java
<dependency>  
          <groupId>org.projectlombok</groupId>  
          <artifactId>lombok</artifactId>   
</dependency>
```

```java
@Setter
@Getter
@ToString
@EqualsAndHashCode
public class People {
    private String name;
    private int age;
    private String male;
}
```

## 快捷键
- Double shift：全局搜索
- Ctrl shift N：转到文件
- Ctrl E：最近文件

## 设置类
- [IDEA一定要改的8条配置](https://mp.weixin.qq.com/s/dfZrFuoih94Puu6dt_AxDA)