# Neo4j 使用记录
***

```sh
First, you'll want our key:

cd /tmp
wget http://debian.neo4j.org/neotechnology.gpg.key
rpm --import neotechnology.gpg.key
Then, you'll want to add our yum repo to /etc/yum.repos.d/neo4j.repo:

cat <<EOF>  /etc/yum.repos.d/neo4j.repo
[neo4j]
name=Neo4j Yum Repo
baseurl=http://yum.neo4j.org/stable
enabled=1
gpgcheck=1
EOF
Finally, install a package

yum install neo4j-3.3.0

./bin/neo4j-admin  dump --database=graph.db --to=/home/download/neo4j.dump
/bin/neo4j-admin load --from=/home/download/neo4j.dump --database=graph.db --force

rm /var/lib/neo4j/data/dbms/auth
/bin/neo4j-admin set-initial-password 123456

http://172.18.0.44:7474/browser/
```

## 参考链接
- [Red Hat / CentOS / Fedora](https://neo4j.com/docs/operations-manual/3.1/installation/linux/rpm/)
- [Chapter 2. Installation](https://neo4j.com/docs/operations-manual/3.1/installation/)
- [Neo4j服务关闭启动·常用配置·快速设定新密码](https://blog.csdn.net/wzwdcld/article/details/80763251)
- [使用neo4j-admin进行neo4j数据备份、迁移](https://blog.csdn.net/c1052981766/article/details/79530061)