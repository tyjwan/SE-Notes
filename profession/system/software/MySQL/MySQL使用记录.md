# MySQL使用记录
***
## 安装
### centos7
```bash
wget 'https://dev.mysql.com/get/mysql57-community-release-el7-11.noarch.rpm'
rpm -Uvh mysql57-community-release-el7-11.noarch.rpm
yum install mysql-community-server
systemctl start mysqld
systemctl enable mysqld

grep 'temporary password' /var/log/mysqld.log
mysql -u root -p
ALTER USER 'root'@'localhost' IDENTIFIED BY 'MyNewPass4!';
grant all privileges on *.* to 'root'@'%' identified by "123456" with grant option;
flush privileges;
```

## 常用命令记录
```
create user share@'%' identified by 'password';
set password for 'share'@'%'=password('share');
grant select on bachang.index1 to share@'%' ;
grant select on bachang.index2 to share@'%' ;
revoke insert on bachang.attack_info from worker@'%';
grant select on bachang.vulnerability to worker@'%';

delete from 表名;

drop database databasename;
```

## 参考链接
- [mysql-创建用户并授权，设置允许远程连接](https://www.cnblogs.com/gpdm/p/6492449.html)
- [创建MySQL用户 赋予某指定库表的权限](https://www.cnblogs.com/wuyifu/p/7580494.html)
- [Centos 7 安装 MySQL](https://www.jianshu.com/p/7cccdaa2d177)
- [centos7下mysql配置远程连接](https://blog.csdn.net/song634/article/details/80394965)
