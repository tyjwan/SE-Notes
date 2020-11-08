# Java 性能调优概览

***

性能方面的度量指标有很多，我觉得有三个指标非常重要，就是：
吞吐量、延迟和并发量。吞吐量：指的是单位时间内能处理的请求数量。吞吐量越高，说明性能越好。
延迟：指的是从发出请求到收到响应的时间。延迟越小，说明性能越好。
并发量：指的是能同时处理的请求数量，一般来说随着并发量的增加、延迟也会增加。所以延迟这个指标，一般都会是基于并发量来说的。例如并发量是 1000 的时候，延迟是 50 毫秒。

## 体现性能的参考因素

- CPU
- 内存
- 磁盘I/O
- 网络
- 异常
- 数据库
- 锁竞争
- 响应时间
  - 数据库响应时间
    - 服务端响应时间
    - 网络响应时间
	- 客户端响应时间
- 吞吐量
	- 磁盘吞吐量
	- 网络吞吐量
- 计算机资源分配使用率
	- CPU占用率
	- 内存使用率
	- 磁盘I/O
	- 网络I/O
- 负载承受能力

## 性能调优策略
- 性能测试
	- 微基准性能测试
	- 宏基准性能测试
- 排查性能问题
	- 应用服务
	- 网络
	- JVM
	- 容器
	- 计算机资源使用率
- 制订调优
	- 应用调优
		- 优化代码
		- 优化设计
		- 优化算法
	- 系统调优
		- 操作系统调优
		- JVM调优(24 | 如何优化JVM内存分配？:https://time.geekbang.org/column/article/108139)
			- 1. 降低 Minor GC 频率
			- 2. 降低 Full GC 的频率(减少创建大对象/增大堆内存空间/选择合适的 GC 回收器)
		- 组件调优
	- 调优策略
		- 时间换空间
		- 空间换时间
- 兜底策略
	- 限流熔断
	- 扩容

## 性能测试工具
- ab：ab 测试工具是 Apache 提供的一款测试工具，具有简单易上手的特点，在测试 Web 服务时非常实用。
- JMeter：JMeter 是 Apache 提供的一款功能性比较全的性能测试工具，同样可以在 Windows 和 Linux 环境下安装使用。
- [GCViewer](https://sourceforge.net/projects/gcviewer/)长时间的 GC 日志，我们很难通过文本形式去查看整体的 GC 性能。此时，我们可以通过GCViewer工具打开日志文件，图形化界面查看整体的 GC 性能
- [GCeasy](https://www.gceasy.io/index.jsp)比较好用的 GC 日志分析工具，GCeasy是一款非常直观的 GC 日志分析工具，我们可以将日志文件压缩之后，上传到 GCeasy 官网即可看到非常清楚的 GC 日志分析结果：
- 常用的监控和诊断内存工具:https://time.geekbang.org/column/article/108582

## JVM相关命令
### 查看 & 分析 GC 日志
```
# 首先，我们需要通过 JVM 参数预先设置 GC 日志，通常有以下几种 JVM 参数设置
-XX:+PrintGC 输出GC日志
-XX:+PrintGCDetails 输出GC的详细日志
-XX:+PrintGCTimeStamps 输出GC的时间戳（以基准时间的形式）
-XX:+PrintGCDateStamps 输出GC的时间戳（以日期的形式，如 2013-05-04T21:53:59.234+0800）
-XX:+PrintHeapAtGC 在进行GC的前后打印出堆的信息
-Xloggc:../logs/gc.log 日志文件的输出路径

# 这里使用如下参数来打印日志：
-XX:+PrintGCDateStamps -XX:+PrintGCDetails -Xloggc:./gclogs
```

### 查看 JVM 堆内存分配
```
java -XX:+PrintFlagsFinal -version | grep HeapSize 
jmap -heap 17284
```