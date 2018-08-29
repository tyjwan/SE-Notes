# Socket
***
### Socket setsockopt
```
sock1 = socket.socket(socket.AF_INIT, socker.SOCK_STREAM)
sock2 = socket.socket(socket.AF_INIT, socker.SOCK_STREAM)

sock1.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
sock2.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)

sock1.bind(("0.0.0.0", 40))
sock12.bind(("0.0.0.0", 40))
```

## 参考链接
[python利用setsockopt获得端口重用](https://blog.csdn.net/github_40094105/article/details/78080647)
[网络与Web编程](http://python3-cookbook.readthedocs.io/zh_CN/latest/c11/p02_creating_tcp_server.html)