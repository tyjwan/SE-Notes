# Supervisor 使用
***
## 功能
- 重启异常退出程序
- 便捷启动程序
- 自动管理程序日志，防止日志文件过大
- 提供网页管理方式

## 使用方式
```sh
yum -y install epel-release
yum install python-pip -y

# 安装
pip install supervisor

# 启动
supervisord

# 操作相应的程序
supervisorctl

# 会出现类似下面的内容
[root@app-s2-3 ~]# supervisorctl
corelationAnalysis               RUNNING   pid 2912, uptime 1096 days, 17:27:32
pcl_simulate                     RUNNING   pid 2913, uptime 1096 days, 17:27:32
supervisor>

# 然后启动，停止，重启某程序
start|stop|restart pcl_simulate

# 也可以通过网页操作
http://服务器地址:9001
```

## 配置说明
```
# 如果找不到配置文件，可以使用下面的命令进行生成
echo_supervisord_conf > /etc/supervisord.conf

# 配置文件 /etc/supervisord.conf，更多详细使用文件中有说明

# 在文件的末尾或任意位置，写入下面的配置内容即可命名，以关联分析代码为例
# 给程序命名
[program:corelationAnalysis]
# 执行程序所需要的命令
command=/opt/anaconda34/bin/python3.6 /home/project/pcl_match/pcl_match_ae_classify_dog.py
# 是否自动启动（当supervisor运行时自动运行）
autostart=true
# 日志文件写入位置
stdout_logfile=/var/log/nssas/pcl_match_ae_classify_dog.log
# 日志文件最大设置
stdout_logfile_maxbytes=10MB
# 日志文件备份文件数量设置
stdout_logfile_backups=1     ; # of stdout logfile backups (0 means none, default 10)
redirect_stderr=true          ; redirect proc stderr to stdout (default false)
# 定义错误输出
;stderr_logfile=/a/path        ; stderr log path, NONE for none; default AUTO
;stderr_logfile_maxbytes=1MB   ; max # logfile bytes b4 rotation (default 50MB)
;stderr_logfile_backups=10     ; # of stderr logfile backups (0 means none, default 10)
# 设置运行上面的command命令之前需要进入的文件夹目录
;directory=/tmp                ; directory to cwd to before exec (def no cwd)
```

## 开机自动重启设置
```
# 编辑 /etc/rc.local 文件

chmod a+x /etc/rc.local

# 在文件中加入下面的内容
/usr/bin/supervisord -c /etc/supervisord.conf
```

## 多配置文件
同include的机制，在最后开启include，并添加自己的配置文件路径，在自己的配置文件中只需要写program部分

## 问题
### 启动Python程序日志无输出问题
&ensp;&ensp;&ensp;&ensp;在命令行后面加 -u 选项强制刷新输出缓存即可： python -u xxx.py