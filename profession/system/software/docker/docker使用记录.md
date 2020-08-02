# docker使用记录
****
## 使用小贴士
- 在没有进行配置之前，使用docker必须赋予root权限

## docker 的安装
- [Docker CE 镜像源站](https://yq.aliyun.com/articles/110806?spm=a2c4e.11153940.blogcont29941.13.520269d6XtzWqG)
- [安装教程](http://blog.csdn.net/qq_27818541/article/details/73647797)
- [镜像加速地址](https://cr.console.aliyun.com/?accounttraceid=3480ea01-1b31-4cb1-a169-6c220739cae6#/accelerator)
- 安装过程中的依赖问题解决：sudo apt-get install -f -y
- [How To Install and Use Docker on CentOS 7](https://linuxize.com/post/how-to-install-and-use-docker-on-centos-7/)

### centos7
&ensp;&ensp;&ensp;&ensp;如果出现冲突，卸载相应冲突的软件即可

```bash
yum update
yum install yum-utils device-mapper-persistent-data lvm2 -y
yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
yum install docker-ce -y
systemctl start docker
systemctl enable docker
```

### centos8
```sh
yum install -y yum-utils  device-mapper-persistent-data  lvm2
yum-config-manager  --add-repo   https://download.docker.com/linux/centos/docker-ce.repo
# https://download.docker.com/linux/centos/7/x86_64/stable/Packages/,containerd.io-1.2.6-3.3.el7.x86_64.rpm  
yum localinstall -y containerd.io-1.2.6-3.3.el7.x86_64.rpm
yum install docker-ce docker-ce-cli

systemctl start docker
```

## 与宿主机交互类
- 单个文件挂载：将宿主的文件进行挂载时，有时docker里面对文件操作会改变宿主机，而宿主机里面操作不会改变，这个好像涉及到一些问题，不建议这样进行挂载，可以进行目录挂载
- 目录挂载：目录挂载可以完美实现文件共享功能，docker和宿主机的操作都会改变文件（Windows里c:/xx/xx/即可）

### Windows网络互通
```bash
ipconfig

以太网适配器 vEthernet (Default Switch):

   连接特定的 DNS 后缀 . . . . . . . :
   本地链接 IPv6 地址. . . . . . . . : fe80::3004:9659:3139:af85%21
   IPv4 地址 . . . . . . . . . . . . : 172.17.220.241
   子网掩码  . . . . . . . . . . . . : 255.255.255.240
   默认网关. . . . . . . . . . . . . :

route add -p 172.17.0.0 mask 255.255.255.240 192.168.250.65

route add -p 172.17.0.0 mask 255.255.255.240 10.0.75.2

cat C:\Windows\System32\drivers\etc\hosts
# Added by Docker Desktop
10.33.0.119 host.docker.internal
10.33.0.119 gateway.docker.internal
# To allow the same kube context to work on the host and the container:
127.0.0.1 kubernetes.docker.internal
```

## 使用中需要注意的问题
- [空间的清理](https://yq.aliyun.com/articles/272173)

## ssh安装
```bash
yum install -y openssh-server
/usr/sbin/sshd -D
```

## 常规操作
### 进入正在运行中的容器
- docker exec -ti dockernameorid /bin/bash

### 导入tar包到本地docker镜像
docker load < /tmp/new-image.tar

### 查看ip地址
docker inspect --format='{{.NetworkSettings.IPAddress}}' mongo

### 映射连接问题
地址不对,可能不是onlyhost模式,使用下面的地址即可
docker-machine ip default

### Docker容器向宿主机传送文件
```
格式:
docker cp container_id:<docker容器内的路径> <本地保存文件的路径>
比如:
docker cp 10704c9eb7bb:/root/test.text /home/vagrant/test.txt
```

### 宿主机向Docker容器传送文件
```
格式:
docker cp 本地文件的路径 container_id:<docker容器内的路径>

比如:
docker cp  /home/vagrant/test.txt 10704c9eb7bb:/root/test.text
```

### 添加镜像加速器
#### centos
```bash
vim /etc/docker/daemon.json

{
  "registry-mirrors": ["https://vmnyyjzt.mirror.aliyuncs.com"]
}

systemctl daemon-reload
systemctl restart docker
```

#### windows docker toolbox
```sh
docker-machine rm default
docker-machine create --engine-registry-mirror=https://vmnyyjzt.mirror.aliyuncs.com -d virtualbox default
docker-machine env default
eval "$(docker-machine env default)"
docker info

# docker安装需要最新的boot2docker.iso，从docker的安装界面复制网址，我安装的日期是20180823，此时的boot2docker.iso地址为boot2docker.iso，这个地址貌似被墙了，需要翻墙下载，将下载好的文件放到对应文件夹下，我的电脑上为C:\Users\lzy\.docker\machine\cache
```

## 常用仓库链接
- [mysql](https://hub.docker.com/_/mysql)

## 参考链接
- [不加 sudo 执行 Docker 命令](http://www.markjour.com/article/docker-no-root.html)
- [阿里云docker仓库](https://dev.aliyun.com/search.html)
- [Docker 这九个不同的应用场景](https://my.oschina.net/editorial-story/blog/1558880)
- [Docker最佳实践：构建最小镜像](https://zhuanlan.zhihu.com/p/38552260)
- [微容器：更小的，更轻便的Docker容器](http://dockone.io/article/1035)
- [docker for windows 设置aliyun加速以及改变pull镜像位置](https://blog.csdn.net/gx947791229/article/details/76512660)