# MongoDB 使用记录
***
## 安装配置
```sh
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

## 操作
### 删除collection
```sh
mongo
>use test
switched to db test
> show collections
mycol
mycollection
newcollection
>db.mycollection.drop()
>db.users.find().count()
>db.col.createIndex({"description":-1})
true
```

## 参考链接
- [MongoDB删除集合](https://www.yiibai.com/mongodb/mongodb_drop_collection.html)
- 