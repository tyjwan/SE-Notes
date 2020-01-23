# Centos 镜像使用记录
×××
## Centos18
### ssh开启
```sh
yum install openssh-server
ssh-keygen -f /etc/ssh/ssh_host_ed25519_key -N '' -t ecdsa
ssh-keygen -f /etc/ssh/ssh_host_ecdsa_key -N '' -t ecdsa
/usr/sbin/sshd -f /etc/ssh/sshd_config
```