# docker MySQL官方版本使用记录
***
## 使用记录
- 拉取官方镜像：docker pull mysql
- 运行镜像：docker run --name mysql -p 3306:3306 -e MYSQL_ROOT_PASSWORD=root -d mysql:latest
    + -p:将容器的3306端口映射到宿主机的3306端口
    + -e:设置mysql的root用户的密码为root

## 参考链接
- [mysql](https://hub.docker.com/_/mysql)