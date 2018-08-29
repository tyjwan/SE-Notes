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

&ensp;&ensp;&ensp;&ensp;不知道数据包为啥是22万，但多个测试下来感觉基本正常情况下大概呈现2倍的关系，也就是说上面的测试基本上丢了一些的包？还分析不过来？测试下来发现CPU在上面的测试中没有用到100%过，最高50%左右，还是数据包刚开始发送的时候，很多时候基本都是20%左右的CPU占用率，感觉是与系统的数据包数量有关吧，毕竟抓不了那么多的数据包。后面随着发包总数的增大，但发包的频率不变（基本1万1秒），抓包率好像基本维持在3/4,snort的处理率维持在50%左右（按照数据来看是这样的）

## 解决方案
&ensp;&ensp;&ensp;&ensp;网上提供了一些解决方案，但还没来得及去尝试

### 系统抓包方面
- 高效的数据包捕获接口--PF RING
- 万兆网络数据包捕获--TNAPI
- 万兆网络数据包捕获--DNA

&ensp;&ensp;&ensp;&ensp;上面这些都是针对系统抓取数据包的，基本思想是改进原本系统的抓包流程，有一些需要配置的要求如CPU：I t l X X5675 Intel Xeon X5675（6核心，12线程）x2， 内存24G啥的

### 数据包处理方面
&ensp;&ensp;&ensp;&ensp;看他们基本上都是开启多个snort的，万兆的开了12个，网上说是够用了

## snort过滤包的问题
&ensp;&ensp;&ensp;&ensp;前期担心数据量过大，考虑进行数据包过滤，由于UDP包在网络攻击中的作用不大，将丢弃UDP包。

&ensp;&ensp;&ensp;&ensp;经过调研后决定使用snort的 -F <bpf> 指定BPF过滤器。测试的数据如下，分别是过滤了TCP包和不过滤TCP包的数据处理结果和CPU使用情况

```
# 只使用Windows实验环境中发包程序，90线程最大速度
# 只要TCP包，其他的过滤掉，好像发包程序发的不是tcp，是啥ARP
CPU使用情况：0.3%
处理包的数据量：10秒内处理了6个

# 不进行包过滤
CPU使用情况：29.2%
处理包的数据量：11秒内处理了190851个

# 使用第三方发送UDP包和TCP包，总共速率31M
# 只要TCP包，其他的过滤掉
CPU使用情况：31%
处理包的数据量：287954 11秒

# 不进行包过滤
CPU使用情况：42.3%
处理包的数据量：649593 12秒

# 使用使用Windows实验环境中发包程序90线程最大速度，第三方发送TCP包，速率31M
# 不进行包过滤
CPU使用情况：39.3%
处理包的数据量：5845501 25秒

# 只要TCP包，其他的过滤掉
CPU使用情况：0.1%
处理包的数据量：27690 24秒（刚好是第三方发送数据包数量）
```

&ensp;&ensp;&ensp;&ensp;从上面的数据的处理包的数据量可以看出使用过滤是有效的，snort对TCP协议以外的包进行了过滤。从CPU的使用情况可以看出过滤能相应的提升snort的处理性能，过滤后不进行后面的规则匹配，应该能提升snort的处理速度。

## other
/usr/local/bin/snort -A console -u snort -g snort -c /etc/snort/snort.conf --daq-dir=/usr/local/lib/daq --daq pfring -i enp0s31f6
docker rm $(docker ps -a -q)
docker run -tid --net=host --name snort snort /usr/local/bin/snort -A console -u snort -g snort -c /etc/snort/snort.conf -i enp0s31f6

/usr/local/bin/snort -A console -u snort -g snort -c /etc/snort/snort.conf -i enp4s0 -F /etc/snort/filter.bpf

## 实验数据
### 15秒左右发送了120万个数据包

```
Packet I/O Totals:
   Received:       497600
   Analyzed:       497600 (100.000%)
    Dropped:       570717 ( 53.422%)
   Filtered:            0 (  0.000%)
Outstanding:            0 (  0.000%)
   Injected:            0

Packet I/O Totals:
   Received:       540223
   Analyzed:       540223 (100.000%)
    Dropped:       735552 ( 57.655%)
   Filtered:            0 (  0.000%)
Outstanding:            0 (  0.000%)
   Injected:            0

Packet I/O Totals:
   Received:       591759
   Analyzed:       591759 (100.000%)
    Dropped:       893606 ( 60.161%)
   Filtered:            0 (  0.000%)
Outstanding:            0 (  0.000%)
   Injected:            0

Packet I/O Totals:
   Received:       675657
   Analyzed:       675657 (100.000%)
    Dropped:      1012010 ( 59.965%)
   Filtered:            0 (  0.000%)
Outstanding:            0 (  0.000%)
   Injected:            0

```

```
Total packets processed:              176719
Total packets processed:              174065
1192681

```


## other
/usr/local/bin/snort -A console -u snort -g snort -c /etc/snort/snort.conf --daq-dir=/usr/local/lib/daq --daq pfring -i enp0s31f6
/usr/local/bin/snort -A console -u snort -g snort -c /etc/snort/snort.conf -i enp0s31f6
docker rm $(docker ps -a -q)
docker run -tid --net=host --name snort snort /usr/local/bin/snort -A console -u snort -g snort -c /etc/snort/snort.conf -i enp0s31f6

## 参考链接
- [关于Snort的抓包原理及性能改进(可能版本较老，不知现在的版本如何)](http://sourcedb.ict.cas.cn/cn/ictthesis/200907/P020090722605372300999.pdf)
- [SNORT原理简介与优化及GNORT初探](http://www.owasp.org.cn/OWASP_Events/download/snort.pdf)
- [Linux配置hiredis](https://blog.csdn.net/zhwei_87/article/details/39643309)
- [hiredis 下载地址](https://github.com/redis/hiredis/releases)
- [PF_RING安装与使用总结](http://blog.51cto.com/yuzwei/1716803)
- [高性能流量生成工具trafgen(DDoS模拟)](https://blog.csdn.net/u010390063/article/details/79078756)
- [Inline Snort multiprocessing with PF_RING(尝试成功的snort结合pf_ring版本)](https://snort-org-site.s3.amazonaws.com/production/document_files/files/000/000/014/original/PF_RING_Snort_Inline_Instructions_daq_062.pdf?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAIXACIED2SPMSC7GA%2F20180725%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20180725T122112Z&X-Amz-Expires=172800&X-Amz-SignedHeaders=host&X-Amz-Signature=f782832c1784b438e7e3425a256a5186f8ccdfbb1840179df775411e7a7668f4)
- [Installing from GIT](https://www.ntop.org/guides/pf_ring/get_started/git_installation.html#)
- [Using Snort with PF_RING](https://www.ntop.org/guides/pf_ring/thirdparty/snort-daq.html)
- [snort download](https://www.snort.org/downloads#snort-downloads)
- [snort 2.9 docment](https://snort-org-site.s3.amazonaws.com/production/document_files/files/000/000/122/original/Snort_2.9.9.x_on_Ubuntu_14-16.pdf?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAIXACIED2SPMSC7GA%2F20180725%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20180725T091034Z&X-Amz-Expires=172800&X-Amz-SignedHeaders=host&X-Amz-Signature=fa38cc81bc1cedaee8b4696643e31bff92ae9e7fe46d23025c9edc9f8156b7fb)
- [snort 3.0 docment](https://snort-org-site.s3.amazonaws.com/production/document_files/files/000/000/136/original/Snort_3_on_CentOS_7.pdf?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAIXACIED2SPMSC7GA%2F20180725%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20180725T110233Z&X-Amz-Expires=172800&X-Amz-SignedHeaders=host&X-Amz-Signature=6e23ca85dfff5c03df79af70fef0178ace629137c3a641c8d48496b5d43d445c)
- [snort Documents](https://www.snort.org/documents)
- [免费DDOS攻击测试工具大合集](http://www.freebuf.com/sectool/36545.html)
- [推荐两款网络攻击测试工具](https://juejin.im/entry/5aa792586fb9a028d936d5e9)
- [网络性能测试](https://cloud.tencent.com/document/product/213/11460)
- [linux下的CPU、内存、IO、网络的压力测试](http://blog.51cto.com/wushank/1585927)
- [liunx检测上下行带宽及丢包率](https://blog.csdn.net/zhangsheng_1992/article/details/52806477)
- [Docs » 工具参考篇 » 19. crontab 定时任务](http://linuxtools-rst.readthedocs.io/zh_CN/latest/tool/crontab.html)
- [Linux之crontab定时任务](https://www.jianshu.com/p/838db0269fd0)
- [Snort 命令参数详解](https://blog.csdn.net/jack237/article/details/6899465)
- [BPF过滤规则及tcpdump命令详解](https://blog.csdn.net/luguifang2011/article/details/72953917)
- [SNORT_BPF变量的配置在防御中心的](https://www.cisco.com/c/zh_cn/support/docs/security/firesight-management-center/118090-configure-sourcefire-00.html)