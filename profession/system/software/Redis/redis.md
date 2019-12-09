# Redis
***
## 安装
### python
https://pypi.org/project/redis/#description

### centos
```bash
yum update -y
yum install epel-release -y
yum update -y
yum install redis
systemctl start redis
systemctl enable redis

vim /etc/redis.conf
bind 0.0.0.0
```

## 设置
### 外网访问
```
# 打开配置文件把下面对应的注释掉
# bind 127.0.0.1 
# Redis默认不是以守护进程的方式运行，可以通过该配置项修改，使用yes启用守护进程，设置为no
daemonize no
# 保护模式
protected-mode no 
```

### 数据导入导出
&ensp;&ensp;&ensp;&ensp;第一步、先redis-cli 进去后bgsave下数据将数据存储到磁盘对应文件dump.rdb中。如果是只存内存的请先做对应的存储设置(修改redis.conf中的save 等参数)
&ensp;&ensp;&ensp;&ensp;第二步、 ps -ef|grep redis 找到对应的进程，kill掉。
&ensp;&ensp;&ensp;&ensp;第三步、 找到dump.rdb 备份下并拷贝一份到要导入的redis机器
&ensp;&ensp;&ensp;&ensp;第四步、kill掉导入机器的对应redis端口，将原rdb文件移除(移除前请自行备份),将第三步导过来的文件替换掉对应的rdb文件。
&ensp;&ensp;&ensp;&ensp;第五步、启动对应的redis端口，便可以看到导入的新的数据了

## 内置操作
### 列表
Redis 列表(List)：http://www.runoob.com/redis/redis-lists.html

## 参考链接
- [Python—操作redis](https://www.cnblogs.com/melonjiang/p/5342505.html)
- [Python—redis](http://www.cnblogs.com/melonjiang/p/5342383.html)
- [使用python来操作redis用法详解](https://www.jianshu.com/p/2639549bedc8)
- [redis数据导入导出（迁移）](https://www.jianshu.com/p/0bf63c3ac8f5)