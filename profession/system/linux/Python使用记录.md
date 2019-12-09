# Ubuntu16.04 的 Python 使用记录
***
## 一、Anaconda的安装
&ensp;&ensp;&ensp;&ensp;按照这个链接进行安装即可：[安装教程](https://www.digitalocean.com/community/tutorials/how-to-install-the-anaconda-python-distribution-on-ubuntu-16-04)

## python3 安装
```sh
yum install -y https://centos7.iuscommunity.org/ius-release.rpm
yum update
yum install -y python36u python36u-libs python36u-devel python36u-pip
```

## python2 pip安装
```sh
yum -y install epel-release
yum install python-pip
```

## 参考链接
- [centos7.2下安装pip](https://blog.csdn.net/wan_zaiyunduan/article/details/70833682)