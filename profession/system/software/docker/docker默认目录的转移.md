# docker默认目录的转移
***
## Ctenos
&ensp;&ensp;&ensp;&ensp;在后面发现docker的文件的占用空间越来越到，而系统盘的空间较小，需要将其转入另一个比较大的磁盘中

#### 软链接方式
&ensp;&ensp;&ensp;&ensp;此方式较为简单，无须修改配置文件

```angular2html
mv /var/lib/docker /home/docker/data
ln -s /home/docker/data /var/lib/docker

chmod a+x /home
chmod a+x /home/docker
chmod a+x /home/docker/data
chmod a+x /var/lib/docker
```

## 参考链接
- [docker容器存放目录磁盘空间满了，转移数据修改Docker默认存储位置](https://blog.csdn.net/qq_37674858/article/details/79976751)
