# 内网远程连接
***
## SSH
```
/bin/ssh -L 2224:127.0.0.1:2224 -R 2224:127.0.0.1:2225 -NR 40004:localhost:22 -p 2822 root@119.39.96.61
```

## AutoSSH
### Ctenos
```
http://www.harding.motd.ca/autossh/autossh-1.4f.tgz
```

### 秘钥建立
```
# Client
ssh-keygen -t rsa
scp /root/.ssh/id_rsa.pub root@xxx.xx.xx.xx:/root/.ssh/test_pub

# Server
cat /root/.ssh/test_pub >> /root/.ssh/authorized_keys
```

### 安装AutoSSH
&ensp;&ensp;&ensp;&ensp;autossh下载地址：http://www.harding.motd.ca/autossh/

```
tar -xf ****
cd ****
./configure
make
make install
```

### 运行
```
autossh -M 2222 -NfR 40002:localhost:22 user@ip -p 22
autossh -M 2222 -NfR 40002:localhost:22 root@119.39.96.61 -p 2822
service client_nssas restart

./otunnel connect 119.39.96.61:40007 -s longlongsecret -t 'r:10.0.0.46:22::40008'
```

### LINK(IN SEVER)
```
ssh -p 40002 user@localhsot
```

## otunnel
### 下载安装
```
wget https://dl.ooclab.com/otunnel/1.3.1/otunnel_linux_amd64
chmod a+x otunnel_linux_amd64
mv otunnel_linux_amd64 /opt/otunnel
```

### 服务端运行
```
/opt/otunnel listen :40007 -s longlongsercret
```

### 客户端运行（反向连接）
```
/opt/otunnel connect 119.39.96.61:40007 -s longlongsecret -t 'r:10.0.0.46:22::40008'
```

## 参考链接
- [https://dl.ooclab.com/otunnel/1.3.1/](https://dl.ooclab.com/otunnel/1.3.1/)
- [对称的安全隧道工具 otunnel](https://www.oschina.net/p/otunnel)