# Mysql Docker 主从配置
***

*操作系统：Windows10 使用的MySQL8.0的docker镜像*

## 总览

### 前置环境说明
&ensp;&ensp;&ensp;&ensp;首先在系统中起了一个MySQL8.0的docker镜像，并在其中插入了初始数据，容器名就叫mysql（视为主库），从库在主从分离前还没有启动

&ensp;&ensp;&ensp;&ensp;操作日志如下：

```bash
# 拉取MySQL8.0版本镜像并启动，监听在3306端口，设置root用户密码为root
docker run --name mysql -p 3306:3306 -e MYSQL_ROOT_PASSWORD=root -e MYSQL_ROOT_HOST=% -d mysql:latest
```

&ensp;&ensp;&ensp;&ensp;其中使用了脚本，自动生成了表和插入数据，下面有脚本链接，可进行点击查看：

- [数据库表初始化脚本](https://github.com/lw1243925457/JAVA-000/blob/main/Week_06/init.sql)
- [数据库数据插入初始化脚本](https://github.com/lw1243925457/JAVA-000/blob/main/Week_06/fillData1.0.sql)

### 数据导入导出
&ensp;&ensp;&ensp;&ensp;这里不使用脚本对从库进行初始化，而是从主库中导出数据，导入到从库中

&ensp;&ensp;&ensp;&ensp;注意：如果主库有在使用，需要停掉相关的使用程序，或者对主库进行加锁

&ensp;&ensp;&ensp;&ensp;操作日志如下：

```bash
# 首先从主库中备份数据到本地（在sh中提前进入准备好的目录）
docker exec mysql /usr/bin/mysqldump -u root --password=root test > backup.sql

# 运行一个新的mysql，作为从库
docker run --name mysql_bk1 -p 3309:3306 -e MYSQL_ROOT_PASSWORD=root -e MYSQL_ROOT_HOST=% -d mysql:latest

# 新开的数据启动可能需要点时间，请耐心等候，使用下面的命令可以看到mysql的运行日志，运行到新的日志内容就说明准备就绪了，可以进行使用
docker logs -f mysql_bk1
# /usr/sbin/mysqld: ready for connections. Version: '8.0.22'  socket: '/var/run/mysqld/mysqld.sock'  port: 3306  MySQL Community Server - GPL.

# 进入到新开的数据库中，建立数据库test
docker exec -ti mysql_bk1 mysql -u root -p
create database test;

# Ctrl-D退出，执行下面的命令导入数据到新开的mysql中，这个需要花费好几分钟，请耐心等待；当然也可以docker cp backup.sql文件到容器中，然后连上数据库使用source backup.sql
cat backup.sql | docker exec -i mysql_bk1 /usr/bin/mysql -u root --password=root test
```
### 主库设置
&ensp;&ensp;&ensp;&ensp;首先在本地编写配置文件，这里使用的配置文件如下，根据情况进行修改即可：

```sh
# Copyright (c) 2017, Oracle and/or its affiliates. All rights reserved.
#
# This program is free software; you can redistribute it and/or modify
# it under the terms of the GNU General Public License as published by
# the Free Software Foundation; version 2 of the License.
#
# This program is distributed in the hope that it will be useful,
# but WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
# GNU General Public License for more details.
#
# You should have received a copy of the GNU General Public License
# along with this program; if not, write to the Free Software
# Foundation, Inc., 51 Franklin St, Fifth Floor, Boston, MA  02110-1301 USA

#
# The MySQL  Server configuration file.
#
# For explanations see
# http://dev.mysql.com/doc/mysql/en/server-system-variables.html

[mysqld]
pid-file        = /var/run/mysqld/mysqld.pid
socket          = /var/run/mysqld/mysqld.sock
datadir         = /var/lib/mysql
secure-file-priv= NULL

# Custom config should go here
!includedir /etc/mysql/conf.d/

#主从设置
#主数据库端ID号
server_id = 1           
 #开启二进制日志                  
log-bin = mysql-bin    
#需要复制的数据库名，如果复制多个数据库，重复设置这个选项即可                  
binlog-do-db = test  
#将从服务器从主服务器收到的更新记入到从服务器自己的二进制日志文件中                 
log-slave-updates                        
#控制binlog的写入频率。每执行多少次事务写入一次(这个参数性能消耗很大，但可减小MySQL崩溃造成的损失) 
sync_binlog = 1                    
#这个参数一般用在主主同步中，用来错开自增值, 防止键值冲突
auto_increment_offset = 1           
#这个参数一般用在主主同步中，用来错开自增值, 防止键值冲突
auto_increment_increment = 1            
#二进制日志自动删除的天数，默认值为0,表示“没有自动删除”，启动时和二进制日志循环时可能删除  
expire_logs_days = 7                    
#将函数复制到slave  
log_bin_trust_function_creators = 1    
```

&ensp;&ensp;&ensp;&ensp;操作日志如下：

```bash
# 将宿主机本地的配置文件复制替换MySQL的配置文件，并重启mysql
docker cp .\mysql_master.conf mysql:/etc/mysql/my.cnf
docker restart mysql

# 进入mysql中，查看master的状态，注意下面的File和Position，在从库设置中需要使用
docker exec -ti mysql mysql -u root -p
show master status\G;

*************************** 1. row ***************************
             File: mysql-bin.000001
         Position: 156
     Binlog_Do_DB: test
 Binlog_Ignore_DB:
Executed_Gtid_Set:
1 row in set (0.00 sec)
```

### 从库设置
&ensp;&ensp;&ensp;&ensp;首先在本地编写配置文件，这里使用的配置文件链接如下，根据情况进行修改即可：

```sh
# Copyright (c) 2017, Oracle and/or its affiliates. All rights reserved.
#
# This program is free software; you can redistribute it and/or modify
# it under the terms of the GNU General Public License as published by
# the Free Software Foundation; version 2 of the License.
#
# This program is distributed in the hope that it will be useful,
# but WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
# GNU General Public License for more details.
#
# You should have received a copy of the GNU General Public License
# along with this program; if not, write to the Free Software
# Foundation, Inc., 51 Franklin St, Fifth Floor, Boston, MA  02110-1301 USA

#
# The MySQL  Server configuration file.
#
# For explanations see
# http://dev.mysql.com/doc/mysql/en/server-system-variables.html

[mysqld]
pid-file        = /var/run/mysqld/mysqld.pid
socket          = /var/run/mysqld/mysqld.sock
datadir         = /var/lib/mysql
secure-file-priv= NULL

# Custom config should go here
!includedir /etc/mysql/conf.d/

# 主从设置
# 从数据库端ID号
server_id = 2           
# 开启二进制日志                  
log-bin = mysql-bin    
# 需要复制的数据库名，如果复制多个数据库，重复设置这个选项即可                  
binlog-do-db = test  
# 将从服务器从主服务器收到的更新记入到从服务器自己的二进制日志文件中                 
log-slave-updates                        
# 控制binlog的写入频率。每执行多少次事务写入一次(这个参数性能消耗很大，但可减小MySQL崩溃造成的损失) 
sync_binlog = 0                    
#log buffer将每秒一次地写入log file中，并且log file的flush(刷到磁盘)操作同时进行。该模式下在事务提交的时候，不会主动触发写入磁盘的操作
innodb_flush_log_at_trx_commit = 0
# MySQL主从复制的时候，当Master和Slave之间的网络中断，但是Master和Slave无法察觉的情况下（比如防火墙或者路由问题）。
# Slave会等待slave_net_timeout设置的秒数后，才能认为网络出现故障，然后才会重连并且追赶这段时间主库的数据
slave-net-timeout = 60                    
log_bin_trust_function_creators = 1
```

&ensp;&ensp;&ensp;&ensp;操作日志如下：

```bash
# 将宿主机本地的配置文件复制替换MySQL的配置文件，并重启mysql
docker cp .\mysql_slave1.conf mysql_bk1:/etc/mysql/my.cnf
docker restart mysql_bk1

# 需要安全证书，执行下面的命令获取证书，并进入MySQL汇中
docker exec -ti mysql_bk1 mysql --ssl-mode=DISABLED -h 192.168.101.104 -uroot -proot --get-server-public-key
# 设置主库链接,master_log_file/master_log_pos设置从上面主库的File和Position，设置完成后启动
# 这里的IP设置可以使用宿主机IP，也可以使用docker容器的ip（docker inspect [容器id]|grep IPA 查看 IP）
# MASTER_HOST：主数据库的主机ip
# MASTER_PORT：主数据库的端口，不设置则默认是3306
# MASTER_USER：主数据库被授予同步复制权限的用户名
# MASTER_PASSWORD：对应的用户密码
# MASTER_LOG_FILE：在主数据库执行命令show master status 查询到的二进制日志文件名称
# MASTER_LOG_POS：在主数据库执行命令show master status 查询到的位置 Position的值
change master to master_host='192.168.101.104',master_user='root',master_password='root',master_log_file='mysql-bin.000001',master_log_pos=156;
start slave;

# 查看，大致如下，没有错误即可
show slave status\G;

mysql> show slave status\G;
*************************** 1. row ***************************
               Slave_IO_State: Waiting for master to send event
                  Master_Host: 192.168.101.104
                  Master_User: root
                  Master_Port: 3306
                Connect_Retry: 60
              Master_Log_File: mysql-bin.000001
          Read_Master_Log_Pos: 859
               Relay_Log_File: 28bc4fb44f99-relay-bin.000002
                Relay_Log_Pos: 324
        Relay_Master_Log_File: mysql-bin.000001
             Slave_IO_Running: Yes
            Slave_SQL_Running: Yes
              Replicate_Do_DB:
          Replicate_Ignore_DB:
           Replicate_Do_Table:
       Replicate_Ignore_Table:
      Replicate_Wild_Do_Table:
  Replicate_Wild_Ignore_Table:
                   Last_Errno: 0
                   Last_Error:
                 Skip_Counter: 0
          Exec_Master_Log_Pos: 859
              Relay_Log_Space: 540
              Until_Condition: None
               Until_Log_File:
                Until_Log_Pos: 0
           Master_SSL_Allowed: No
           Master_SSL_CA_File:
           Master_SSL_CA_Path:
              Master_SSL_Cert:
            Master_SSL_Cipher:
               Master_SSL_Key:
        Seconds_Behind_Master: 0
Master_SSL_Verify_Server_Cert: No
                Last_IO_Errno: 0
                Last_IO_Error:
               Last_SQL_Errno: 0
               Last_SQL_Error:
  Replicate_Ignore_Server_Ids:
             Master_Server_Id: 1
                  Master_UUID: f6b20c68-23d1-11eb-a497-0242ac110002
             Master_Info_File: mysql.slave_master_info
                    SQL_Delay: 0
          SQL_Remaining_Delay: NULL
      Slave_SQL_Running_State: Slave has read all relay log; waiting for more updates
           Master_Retry_Count: 86400
                  Master_Bind:
      Last_IO_Error_Timestamp:
     Last_SQL_Error_Timestamp:
               Master_SSL_Crl:
           Master_SSL_Crlpath:
           Retrieved_Gtid_Set:
            Executed_Gtid_Set:
                Auto_Position: 0
         Replicate_Rewrite_DB:
                 Channel_Name:
           Master_TLS_Version:
       Master_public_key_path:
        Get_master_public_key: 0
            Network_Namespace:
1 row in set, 1 warning (0.01 sec)
```

&ensp;&ensp;&ensp;&ensp;另外一个从库，配置和上面的命令完全一样，只是将mysql_bk1换成mysql_bk2即可

### 主从测试
&ensp;&ensp;&ensp;&ensp;进入到主库中，插入一条数据，在从库中查询可以看到即可

&ensp;&ensp;&ensp;&ensp;操作日志如下：

```bash
docker exec -ti mysql mysql -u root -p
insert into stores (name, description) VALUES ("name103", "description103");

docker exec -ti mysql mysql_bk1 -u root -p
select * from stores;
```

## 参考链接
- [Mysql 主从复制搭建-极简版](https://www.cnblogs.com/summertime-wu/p/11637520.html)
- [MySQL8.0主从复制的配置](https://blog.csdn.net/u013068184/article/details/107691389)
- [MySQL主从配置详解](https://www.jianshu.com/p/b0cf461451fb)