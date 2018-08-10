# Linux 时间校准
***
## 命令
```
tzselect
export TZ='Asia/Shanghai'
source ~/.bashrc
rm -f /etc/localtime
ln -s /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
timedatectl set-timezone 'Asia/Shanghai'
```