# MongoDB 安装配置
***

```
vim /etc/yum.repos.d/mongodb-org-4.0.repo

[mongodb-org-4.0]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/4.0/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-4.0.asc

yum install -y mongodb-org

service mongod start
chkconfig mongod on

# 修改能被外界访问
vim /etc/mongodb.conf
```