# nginx 使用记录
***
## 安装
```sh
yum install -y pcre
yum install -y pcre-devel
yum install -y openssl-devel
yum install epel-release
```

### 访问权限问题
```
user root

chmod a+x xxxx
```

### 配置不同路径访问不同的静态文件
&ensp;&ensp;&ensp;&ensp;好像只能存在一个roo，另一个需要使用alias，例子如下：
```
server {
        listen       80 default_server;
        listen       [::]:80 default_server;
        server_name  _;
        #root         /usr/share/nginx/html;

        #websocket
        map $http_upgrade $connection_upgrade {
            default upgrade;
            '' close;
        }

        # Load configuration files for the default server block.
        include /etc/nginx/default.d/*.conf;

        root /root/projects/dist;
        #index  index.html index.htm index.php;

        location / {
            root /root/projects/dist;
            index index.html;
        }

        location /nssas {
            alias /usr/share/nginx/html/;
            index index.html;
        }
}
```

## 参考链接
- [nginx配置访问图片路径以及html静态页面的调取方法](https://www.jb51.net/article/99305.htm)
- [nginx 403 Forbidden 排错记录](https://www.jianshu.com/p/e0dadb871894)
- [Nginx安装配置](https://www.jianshu.com/p/a195c2f42e15)
- [How To Install Nginx on CentOS 7](https://www.digitalocean.com/community/tutorials/how-to-install-nginx-on-centos-7)
- [利用nginx搭建小型的文件服务器](https://www.jianshu.com/p/a9363379f23b)