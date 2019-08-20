# Nginx负载均衡
***
## 配置
&ensp;&ensp;&ensp;&ensp;首先需要配置定义一个server组:backend,用来表示所有存在的后台服务，然后将所有的流量重定向到http://backend,将这个Nginx示例上的所有流量重定向到定义的backend上，大致内容如下：

```bash
http {
    upstream backend {
        server backend1.example.com;
        server backend2.example.com;
        server 192.0.0.1 backup;
    }

    server {
        location / {
            proxy_pass http://backend;
        }
    }
}
```

&ensp;&ensp;&ensp;&ensp;其中还有很多的细节，比如权重和负载均衡算法，下面的参考链接中已有详细的说明，自行点击查看。

## 参考链接
- [NGINX 负载均衡](https://juejin.im/post/5bbb84ca5188255c393f8473)
- [Nginx配置文件实例及说明](https://blog.csdn.net/m0_37529303/article/details/77822633)
- [nginx服务器安装及配置文件详解](http://seanlook.com/2015/05/17/nginx-install-and-config/)