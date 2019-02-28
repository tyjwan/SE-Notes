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

### 排序

### 索引
```sh
# 语法中 Key 值为你要创建的索引字段，1 为指定按升序创建索引，如果你想按降序来创建索引指定为 -1 即可。
db.collection.createIndex(keys, options)

# 查看集合索引
db.col.getIndexes()

# 查看集合索引大小
db.col.totalIndexSize()

# 删除集合所有索引
db.col.dropIndexes()

# 删除集合指定索引
db.col.dropIndex("索引名称")
```

## 不同数据库之间表复制
```
db.threatTest.find({'unit_id': '90000001'}).forEach(function(d){ db.getSiblingDB('my_visual')['threat'].insert(d); });
db.comprehensiveIndexTest.find().forEach(function(d){ db.getSiblingDB('my_visual')['comprehensiveIndex'].insert(d); });
db.basedIndexTest.find().forEach(function(d){ db.getSiblingDB('my_visual')['basedIndex'].insert(d); });
db.fragileIndexTest.find().forEach(function(d){ db.getSiblingDB('my_visual')['fragileIndex'].insert(d); });
db.threadIndexTest.find().forEach(function(d){ db.getSiblingDB('my_visual')['threadIndex'].insert(d); });
```

## 参考链接
- [MongoDB删除集合](https://www.yiibai.com/mongodb/mongodb_drop_collection.html)
- [MongoDB 查询文档](http://www.runoob.com/mongodb/mongodb-query.html)
- [MongoDB更换数据库路径](https://www.jianshu.com/p/c43d7cf49052)