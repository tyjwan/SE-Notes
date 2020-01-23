# yapi 安装配置
***
## 安装依赖
- nodejs
- git
- MongoDB

```sh
curl -sL https://rpm.nodesource.com/setup_10.x | sudo bash -
yum install nodejs -y
npm install pm2@latest -g
```

```sh
mkdir yapi && cd yapi
git clone https://github.com/YMFE/yapi.git vendors --depth=1
cp vendors/config_example.json ./config.json
vi ./config.json
```

```json
# db.user 和 db.pass 是 mongodb 的用户名和密码，如果没有开启 mongo 认证功能，请删除这两个选项
{
  "port": "3011",
  "adminAccount": "admin@admin.com",
  "db": {
    "servername": "127.0.0.1",
    "DATABASE":  "yapi",
    "port": 27017,
    "user": "yapi",
    "pass": "yapi123"
  },
  "mail": {
    "enable": true,
    "host": "smtp.163.com",
    "port": 465,
    "from": "***@163.com",
    "auth": {
        "user": "***@163.com",
        "pass": "*****"
    }
  }
}
```

```sh
cd vendors
npm install  --registry https://registry.npm.taobao.org
pm2 start server/app.js
```

## 运行
- /usr/local/node-v8.9.3-linux-x64/bin/pm2 start server/app.js

## 访问
- http://nssas.eelantech.com:40008/

## 信息记录
- 初始化管理员账号成功,账号名："admin@admin.com"，密码："ymfe.org"

## 使用说明
*登录时使用注册邮箱进行登录，所有请记住自己的邮箱*
&ensp;&ensp;&ensp;&ensp;首先进行注册后联系管理员添加进相应的分组

## 迁移说明
&ensp;&ensp;&ensp;&ensp;迁移的时候只要复制MongoDB的中yapi数据库到新迁移环境中的MongoDB中即可

## 参考链接
- [安装YApi](https://yapi.ymfe.org/documents/redev.html)