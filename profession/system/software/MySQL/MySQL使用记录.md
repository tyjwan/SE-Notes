# MySQL使用记录
***
```
create user share@'%' identified by 'password';
set password for 'share'@'%'=password('share');
grant select on bachang.index1 to share@'%' ;
grant select on bachang.index2 to share@'%' ;
revoke insert on bachang.attack_info from worker@'%';
grant select on bachang.vulnerability to worker@'%';

delete from 表名;
```

## 参考链接
mysql-创建用户并授权，设置允许远程连接：https://www.cnblogs.com/gpdm/p/6492449.html
创建MySQL用户 赋予某指定库表的权限：https://www.cnblogs.com/wuyifu/p/7580494.html