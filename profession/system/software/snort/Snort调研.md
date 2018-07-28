# Snort
***
## 关于性能的问题：
&ensp;&ensp;&ensp;&ensp;snort是一款轻量级的网络入侵检测系统。如果将其应用在大规模、大流量的网络中 大流量的网络中，snort的报警性能会非常低下。有下面的两个因素影处理性能：

- 数据包捕获效率
- 数据包分析处力效率

### 数据包捕获效率
&ensp;&ensp;&ensp;&ensp;传统的数据包捕获流程如下(NAPI polling)：
– 网卡中断处理程序
– 网卡接收程序
– 分配skb内存
– 将其放入softnet_data队列中
– 置软中断位
– do softirq do
_softirq
– net_rx_action
– iprecv
– IP层检查数据有效性
– TCP/UDP协议处理
– 唤醒用户层进程

&ensp;&ensp;&ensp;&ensp;主要观点是流量包需要先经过系统进行抓取和保存，而目前的linux系统是否能够抓取所有的数据包？这是个首要的影响条件

### 数据包分析处理效率
&ensp;&ensp;&ensp;&ensp;系统在抓取数据包以后，snort会从系统中取出数据包进行处理，如那些检测规则的匹配处理之类的，而第二个影响因素就是snort内部的处理代码的问题，可能会涉及到CPU、内存、磁盘之类（磁盘好像不影响，因为数据包相当于从内存中取的）。

### 自己的测试
&ensp;&ensp;&ensp;&ensp;利用一个发包的软件在15秒左右发送了120万个数据包，这些包都能引起snort的警报处理，得到的结果如下：

```
Packet I/O Totals:
   Received:      1859825
   Analyzed:       746655 ( 40.147%)
    Dropped:      1113163 ( 37.443%)
   Filtered:            0 (  0.000%)
Outstanding:      1113170 ( 59.853%)
   Injected:            0
```

&ensp;&ensp;&ensp;&ensp;可以从上面看出snort接收到包是180万个（应该可以视为系统也只接收到这个数量的包吧？因为数据包是从系统中读取的，应该是读完的，那就说明现在系统是有丢包的），而只来得及分析了40%，这个部分可以是上面的数据包分析处理效率问题了。

&ensp;&ensp;&ensp;&ensp;从上面的结果应该可以看出数据包捕获效率和数据包分析效率好像都有影响了，对了，下面是10秒内12万个数据的处理结果：

```
Packet I/O Totals:
   Received:       222642
   Analyzed:       222641 (100.000%)
    Dropped:            0 (  0.000%)
   Filtered:            0 (  0.000%)
Outstanding:            1 (  0.000%)
   Injected:            0
```

&ensp;&ensp;&ensp;&ensp;不知道数据包为啥是22万，但多个测试下来感觉基本正常情况下大概呈现2倍的关系，也就是说上面的测试基本上丢了一半的包？还分析不过来？

## 解决方案
&ensp;&ensp;&ensp;&ensp;网上提供了一些解决方案，但还没来得及去尝试

### 系统抓包方面
- 高效的数据包捕获接口--PF RING
- 万兆网络数据包捕获--TNAPI
- 万兆网络数据包捕获--DNA

&ensp;&ensp;&ensp;&ensp;上面这些都是针对系统抓取数据包的，基本思想是改进原本系统的抓包流程，有一些需要配置的要求如CPU：I t l X X5675 Intel Xeon X5675（6核心，12线程）x2， 内存24G啥的

### 数据包处理方面
&ensp;&ensp;&ensp;&ensp;看他们基本上都是开启多个snort的，万兆的开了12个，网上说是够用了

## 参考链接
[关于Snort的抓包原理及性能改进(可能版本较老，不知现在的版本如何)](http://sourcedb.ict.cas.cn/cn/ictthesis/200907/P020090722605372300999.pdf)