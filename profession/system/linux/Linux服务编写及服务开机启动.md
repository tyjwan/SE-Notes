# Linux服务编写及服务开机启动
***
## 一、服务编写
### 编写服务脚本
&ensp;&ensp;&ensp;&ensp;首先用编辑器自己写一个脚本文件，模板和大致的内容如下（简单的按照下面的进行操作即可）：比如写 /etc/init.d/test

```sh
#!/bin/bash
### BEGIN INIT INFO
#
# Provides:  location_server
# Required-Start:   $local_fs  $remote_fs
# Required-Stop:    $local_fs  $remote_fs
# Default-Start:    2 3 4 5
# Default-Stop:     0 1 6
# Short-Description:    initscript
# Description:  This file should be used to construct scripts to be placed in /etc/init.d.
#
### END INIT INFO

## Fill in name of program here.
PROG="location_server"
PROG_PATH="/opt/location_server" ## Not need, but sometimes helpful (if $PROG resides in /opt for example).
PROG_ARGS="" 
PID_PATH="/var/run/"

# 上面的保持原样就行，需要写的start和stop函数
start() {
    echo "current PID: $$" #这句删除也可以
    bin/kibana  > kibana.log 2>&1 &
    echo "$!"
    echo "$!" >  /var/run/kibana.pid #将上一个后台进程写入到标准的pid文件和路径中
}

stop() {
    if [ -f  "/var/run/kibana.pid" ]; then
        echo "Will kill kibana"
        kill -9 `cat /var/run/kibana.pid`
    else
        echo "Programe don't run"
}

# 下面这些也用改
## Check to see if we are running as root first.
## Found at http://www.cyberciti.biz/tips/shell-root-user-check-script.html
if [ "$(id -u)" != "0" ]; then
    echo "This script must be run as root" 1>&2
    exit 1
fi

case "$1" in
    start)
        start
        exit 0
    ;;
    stop)
        stop
        exit 0
    ;;
    reload|restart|force-reload)
        stop
        start
        exit 0
    ;;
    **)
        echo "Usage: $0 {start|stop|reload}" 1>&2
        exit 1
    ;;
esac
```

### 添加服务
&ensp;&ensp;&ensp;&ensp;先赋予文件执行权限后使用命令将上面写的test添加到服务里面

```
chmod +x /etc/init.d/test

# Ubuntu,Kali下的操作
# update-rc.d 服务名 defaults，并且这样设置服务也就开机启动了
update-rc.d test defaults
# 删除
# update-rc.d -f 服务名 remove

# CtenOS下的操作
chkconfig --add agent_server
chkconfig agent_server on
```

### 启动、关闭、重启服务
&ensp;&ensp;&ensp;&ensp;接下来就可以使用service命令去启动它了

```
# service 方式
# service 服务名 start
# service 服务名 stop
# service 服务名 start
service test start

# systemctl  方式
systemctl start service
systemctl stop service
systemctl restart service
systemctl status service
```

## 参考链接
- [update-rc.d的具体用法](https://blog.csdn.net/xiongping_/article/details/50392908)
- [在Ubuntu下添加自定义服务](https://blog.csdn.net/xkjcf/article/details/78698232)
- [shell 判断文件夹或文件是否存在](https://www.cnblogs.com/37yan/p/6962563.html)
[linux后台启动进程并记录进程ID](https://blog.csdn.net/u013066244/article/details/69562364)