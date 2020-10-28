# Centos 使用记录
***
## 安装
- [Download Fedora 33 Workstation](https://getfedora.org/en/workstation/download/)
- 启动盘制作使用Linux的专用工具，避免安装时不必要的麻烦，推荐：[rufus](https://rufus.ie/)
- [balenaEtcher](https://www.balena.io/etcher/)

## 网络配置
- ip addr 查看网络配置
- 进入：/etc/sysconfig/network-xxx/ifcfg-xxxx,修改onboot选项为yes即可，service network restart 重启网络设置
- systemctl stop firewalld.service #停止firewall
- systemctl disable firewalld.service #禁止firewall开机启动
- centos8:nmcli c reload

### 设置静态IP
```bash
vim /etc/sysconfig/network-script/ifcfg-xxxx
BOOTPROTO=static
ONBOOT=yes
IPADDR=0.0.0.0
NETMASK=255.255.255.0
GATEWAY=192.168.1.1
```

## 源配置
```sh
# centos 7
/etc/yum.repos.d/
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
yum -y update

# centos 8
curl -o /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-8.repo
yum makecache
yum -y update
```

## C/C++环境配置
```sh
yum install -y gcc
yum install -y gcc-c++
yum install -y gdb
yum install -y cmake
yum install -y make
```

## 硬盘挂载
- fdist -l:查看相应的硬盘信息
- mkfs.ext4 /dev/xxxx:格式化话硬盘
- mount /dev/hdb1 /mnt/hdb1：挂载到相应的目录
- vim /ets/fstab,这种自动挂载，/dev/hdb1 /mnt/hdb1 ext4 defaults 0 0，后面的分区与硬盘的分区相关，没有进行分区默认为0即可

## VMware相关
```
# 挂载共享文件夹
systemctl enable vmtoolsd
systemctl start vmtoolsd
vmhgfs-fuse .host:/ /mnt/hgfs
```

## 查看链接
- [CentOS 7 网络配置详解](http://blog.51cto.com/simonhu/1588971)
- [CentOS 7 下挂载新硬盘](https://segmentfault.com/a/1190000008007157)
- [centos7 关闭防火墙和selinux](https://www.jianshu.com/p/d6414b5295b8)

- [怎么给CentOS Linux 8更换国内源（阿里源）](https://blog.csdn.net/dengshulei/article/details/103704285)
- [centos8 网络配置](https://blog.csdn.net/KLKFL/article/details/102994442)
- [centos8 安装 docker](https://www.cnblogs.com/zbseoag/p/11736006.html)
- [containerd.io-1.2.6-3.3.el7.x86_64.rpm](https://download.docker.com/linux/centos/7/x86_64/stable/Packages/)

- [mondorescue/centos/8/x86_64/ 的索引](ftp://ftp.mondorescue.org/centos/8/x86_64/)
- [linux 使用 mondo rescue 备份 还原系统 iso u盘](https://blog.csdn.net/whatday/article/details/105789362)
- [How to create a bare metal (.iso image) backup of a live/running Linux server (CentOS 7 instructions).](https://www.joe0.com/2015/11/14/how-to-create-a-bare-metal-iso-image-backup-of-a-liverunning-linux-server-centos-7-instructions/)