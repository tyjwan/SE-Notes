# MySQL安装指南
***
## docker 使用
```sh
sudo docker pull mysql
sudo docker run -itd --name mysql -p 3306:3306 -e MYSQL_ROOT_PASSWORD=123456 mysql
```