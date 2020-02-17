# Centos 镜像使用记录
×××
## Centos18
### ssh开启
```sh
yum install openssh-server -y
yum install openssh-clients -y
ssh-keygen -f /etc/ssh/ssh_host_ed25519_key -N '' -t ecdsa
ssh-keygen -f /etc/ssh/ssh_host_ecdsa_key -N '' -t ecdsa
ssh-keygen -f /etc/ssh/ssh_host_rsa_key -N '' -t ecdsa
/usr/sbin/sshd -f /etc/ssh/sshd_config

yum install -y passwd
passwd
```