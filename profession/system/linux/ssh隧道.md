# SSH 隧道
***
##
```
/bin/ssh -L 2224:127.0.0.1:2224 -R 2224:127.0.0.1:2225 -NR 40004:localhost:22 -p 2822 root@119.39.96.61
```

## Ctenos
##
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

### AutoSSH
&ensp;&ensp;&ensp;&ensp;autossh下载地址：http://www.harding.motd.ca/autossh/

```
tar -xf ****
cd ****
./configure
make
make install
```

```
autossh -M 2222 -NfR 40002:localhost:22 user@ip -p 22
```

### LINK(IN SEVER)
```
ssh -p 40002 user@localhsot
```
