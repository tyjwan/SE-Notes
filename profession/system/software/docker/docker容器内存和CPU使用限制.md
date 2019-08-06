# docker容器内存和CPU使用限制
***
## 示例如下
```bash
docker run --name seckill -p 8080:8080 -m 1024M --cpus=1 seckill:v0
```

- -m:限制内存使用为1G
- --cpus：限制CPU使用的百分比，这里设置为100%

## 相关命令
- 查看容器的内存CPU等情况：docker stats
- 压力测试攻击：webbench
    + 出现编译错误缺失ctag时需要安装

## 相关链接
- [高并发测试工具](https://blog.csdn.net/essity/article/details/79999369)
- [网站压力测试工具webbench](https://gist.github.com/lxneng/435451)
- [Centos安装webbench时出现/bin/sh: ctags: command not found解决方法](https://blog.csdn.net/xanxng/article/details/7588133)
- [apache jmeter：接口压力测试](https://blog.csdn.net/weixin_36146275/article/details/79320638)
- [Download Apache JMeter](https://jmeter.apache.org/download_jmeter.cgi)
- [Docker: 限制容器可用的 CPU](https://www.cnblogs.com/sparkdev/p/8052522.html)
- [Docker: 限制容器可用的内存](https://www.cnblogs.com/sparkdev/p/8032330.html)