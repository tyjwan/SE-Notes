# 阿里云SS通道搭建
***
## 购买
&ensp;&ensp;&ensp;&ensp;购买时请购买能访问外国网站的地区服务器，比如香港和新加坡之类的,选择共享类的比较好，便宜。

## 设置
&ensp;&ensp;&ensp;&ensp;服务器初始时除了初始的22之类的端口是开放的，其他的都是关闭的,我们需要设置开放一个端口让外面进行访问。

&ensp;&ensp;&ensp;&ensp;进入控制台的云服务器的实例界面，可以看到我们的服务器，点击服务器后面的管理选项，在右上方点击添加三个安全组规则，设置分别如下：

```
规则方向：入方向
授权策略：允许
协议类型：自定义TCP
端口范围：250
授权类型:地址段访问
授权对象：0.0.0.0/0

规则方向：入方向
授权策略：允许
协议类型：自定义UDP
端口范围：250
授权类型:地址段访问
授权对象：0.0.0.0/0

规则方向：入方向
授权策略：允许
协议类型：全部ICMP
端口范围：250
授权类型:地址段访问
授权对象：0.0.0.0/0
```

## 搭建 SS 代理
```
yum update
yum install python-pip
pip install shadowsocks
mkdir /etc/ss
```

&ensp;&ensp;&ensp;&ensp;编辑文件输入下面的内容，其中250就是我们开放的端口，123是链接的密码：vim /etc/ss/config.json

```
{
    "server":"0.0.0.0", 
    "server_port":250, 
    "local_address":"127.0.0.1", 
    "local_port":1080, 
    "password":"123", 
    "timeout":300, 
    "method":"aes-256-cfb"
}
``` 

&ensp;&ensp;&ensp;&ensp;启动 ssserver 服务

```
ssserver -c /etc/ss/config.json -d start
```

## 进行连接
&ensp;&ensp;&ensp;&ensp;如何进行连接设置参考shadowsocks.md

```
{ 
    "server":"你的服务器公网地址", 
    "server_port":250, 
    "local_address":"127.0.0.1", 
    "local_port":1080, 
    "password":"123", 
    "timeout":301, 
    "method":"aes-256-cfb", 
    "fast_open":false 
}
```

## 参考链接
- [Mac 搭建 AWS 代理服务器记录贴](https://zhuanlan.zhihu.com/p/33070734)