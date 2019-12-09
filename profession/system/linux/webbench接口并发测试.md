# webbench接口并发测试
***
## 安装
&ensp;&ensp;&ensp;&ensp;安装工程中可能会出现确实Ctags的情况，可以进行命令安装：

- Ubuntu：apt install ctags
- Centos:yum install ctags

```bash
wget http://blog.s135.com/soft/linux/webbench/webbench-1.5.tar.gz
tar zxvf webbench-1.5.tar.gz
cd webbench-1.5
make && make install
```

## 使用
```bash
webbench -c 5 -t 30 http://10.33.8.187/goods/findAll
```

- -c表示要模拟的并发客户端数量
- -t表示并发时间

## 参考链接
- [高并发测试工具](https://blog.csdn.net/essity/article/details/79999369)
- [网站压力测试工具webbench](https://gist.github.com/lxneng/435451)
- [Centos安装webbench时出现/bin/sh: ctags: command not found解决方法](https://blog.csdn.net/xanxng/article/details/7588133)
