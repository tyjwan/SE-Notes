# Docker中数据库的使用
***

## 相关命令

```sh
docker run --name mysql -p 3306:3306 -e MYSQL_ROOT_PASSWORD=123456 -d mysql:latest
docker run -dit --name neo4j -p 7474:7474 -p 7687:7687 --env=NEO4J_AUTH=none neo4j:3.5.13
docker run -dit --name redis -p 6379:6379 redis
docker run -dit --name mongo -p 27017:27017 mongo
```