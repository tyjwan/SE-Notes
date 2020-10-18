# JVM调优
***
## 性能指标
&ensp;&ensp;&ensp;&ensp;调优需要方向，下面这些指标指导调优

- 业务需求指标：吞吐量（QPS、TPS）、响应时间（RT）、并发数、业务成功率等
- 资源约束指标：如CPU、内存、I/O等资源的消耗情况

&ensp;&ensp;&ensp;&ensp;可采用的手段和方式

- 使用JDWP或开发工具做本地/远程调试
- 系统和jvm的状态监控，收集分析指标
- 性能分析：CPU使用情况/内存分配分析
- 内存分析：Dump分析，GC日志分析
- 调整JVM启动参数，GC策略等等

&ensp;&ensp;&ensp;&ensp;性能调优要考虑场景，不要过早优化

## JVM的监控及诊断工具
### 命令行类

- jps:打印当前正在运行的Java进程
- jstat：显示性能信息；显示指定Java进程的类加载相关数据、即时编译、垃圾回收相关；-t可以用于测量GC时间占比；OU列上涨有可能发生内存泄漏
- jmap：分析Java虚拟机堆中的对象；
- jinfo：Java进程的参数
- jstack：打印Java进程中各线程的栈轨迹，以及这些线程所持有的锁；可用于死锁检测
- jcmd:你还可以直接使用jcmd命令（帮助文档），来替代前面除了jstat之外的所有命令。具体的替换规则你可以参考下表。

#### jcmd 高效 profiling
&ensp;&ensp;&ensp;&ensp;可用使用下面的命令进行性能数据收集，然后用JMC进行查看分析

```sh
# 可能需要开启虚拟机的一些配置
jcmd <PID> VM.unlock_commercial_features

jcmd <PID> JFR.start settings=profile maxage=10m maxsize=150m name=SomeLabel

jcmd <PID> JFR.dump name=SomeLabel filename=myrecording.jfr

jcmd <PID> JFR.stop name=SomeLabel
```


### GUI类（图形工具类）

- eclipse MAT:jmap工具，它支持导出 Java 虚拟机堆的二进制快照。eclipse 的MAT 工具便是其中一个能够解析这类二进制快照的工具。
- [Java Mission Control](http://jdk.java.net/jmc/):Java 虚拟机平台上的性能监控工具。它包含一个 GUI 客户端，以及众多用来收集 Java 虚拟机性能数据的插件，如 JMX Console（能够访问用来存放虚拟机各个子系统运行数据的MXBeans），以及虚拟机内置的高效 profiling 工具 Java Flight Recorder（JFR）。

## 参考链接

- [30 | Java虚拟机的监控及诊断工具（命令行篇）](https://time.geekbang.org/column/article/40520)
- [31 | Java虚拟机的监控及诊断工具（GUI篇）](https://time.geekbang.org/column/article/40821)