# Centos 使用记录
***
## 安装
- 启动盘制作使用Linux的专用工具，避免安装时不必要的麻烦，推荐：[rufus](https://rufus.ie/)
- [balenaEtcher](https://www.balena.io/etcher/)

## 网络配置
- ip addr 查看网络配置
- 进入：/etc/sysconfig/network-xxx/ifcfg-xxxx,修改onboot选项为yes即可，service network restart 重启网络设置
- systemctl stop firewalld.service #停止firewall
- systemctl disable firewalld.service #禁止firewall开机启动

### 设置静态IP
```bash
vim /etc/sysconfig/network-script/ifcfg-xxxx
IPADDR=0.0.0.0
```

## 硬盘挂载
- fdist -l:查看相应的硬盘信息
- mkfs.ext4 /dev/xxxx:格式化话硬盘
- mount /dev/hdb1 /mnt/hdb1：挂载到相应的目录
- vim /ets/fstab,这种自动挂载，/dev/hdb1 /mnt/hdb1 ext4 defaults 0 0，后面的分区与硬盘的分区相关，没有进行分区默认为0即可

## 查看链接
- [CentOS 7 网络配置详解](http://blog.51cto.com/simonhu/1588971)
- [CentOS 7 下挂载新硬盘](https://segmentfault.com/a/1190000008007157)
- [centos7 关闭防火墙和selinux](https://www.jianshu.com/p/d6414b5295b8)