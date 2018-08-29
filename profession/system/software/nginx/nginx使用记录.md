# nginx 使用记录
***
### 配置不同路径访问不同的静态文件
&ensp;&ensp;&ensp;&ensp;好像只能存在一个roo，另一个需要使用alias，例子如下：
```
server {
        listen       80 default_server;
        listen       [::]:80 default_server;
        server_name  _;
        #root         /usr/share/nginx/html;

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
nginx配置访问图片路径以及html静态页面的调取方法：https://www.jb51.net/article/99305.htm