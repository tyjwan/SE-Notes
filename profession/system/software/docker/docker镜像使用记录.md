# Docker 镜像使用记录
***
## MySQL
### 使用记录
- 拉取官方镜像：docker pull mysql
- 运行镜像：docker run --name mysql -p 3306:3306 -e MYSQL_ROOT_PASSWORD=root -d mysql:latest
    + -p:将容器的3306端口映射到宿主机的3306端口
    + -e:设置mysql的root用户的密码为root

- 遇到sock之类的错误，删文件，在重启后再重启

## Neo4j
docker run -dit --name neo4j -p 7474:7474 -p 7687:7687 --env=NEO4J_AUTH=none neo4j:3.5.13

## Redis
docker run -dit --name redis -p 6379:6379 redis

## MongoDB
docker run -dit --name mongo -p 27017:27017 mongo

## Nginx
docker run -dit --name nginx -p 80:80 -v D:/temp/nginx.conf:/etc/nginx/nginx.conf nginx

## 参考链接
- [mysql](https://hub.docker.com/_/mysql)