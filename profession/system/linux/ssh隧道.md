# SSH 隧道
***
## Ctenos
### 秘钥建立
```
# Client
ssh-keygen -t rsa
scp /home/nssas/.ssh/id_rsa.pub user@ip:/root/.ssh/nssas_pub

# Server
cat /root/.ssh/nssas_pub >> /root/.ssh/authorized_keys
```

### AutoSSH
```
autossh -M 2222 -NfR 40002:localhost:22 user@ip -p 22
```

### LINK(IN SEVER)
```
ssh -p 40002 user@localhsot
```
