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

## 与宿主机交互类
- 单个文件挂载：将宿主的文件进行挂载时，有时docker里面对文件操作会改变宿主机，而宿主机里面操作不会改变，这个好像涉及到一些问题，不建议这样进行挂载，可以进行目录挂载
- 目录挂载：目录挂载可以完美实现文件共享功能，docker和宿主机的操作都会改变文件

### Windows网络互通
```bash
route add -p 172.17.0.0 mask 255.255.255.240 10.0.75.2
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

## 常用仓库链接
- [mysql](https://hub.docker.com/_/mysql)

## 参考链接
- [不加 sudo 执行 Docker 命令](http://www.markjour.com/article/docker-no-root.html)
- [阿里云docker仓库](https://dev.aliyun.com/search.html)
- [Docker 这九个不同的应用场景](https://my.oschina.net/editorial-story/blog/1558880)
- [Docker最佳实践：构建最小镜像](https://zhuanlan.zhihu.com/p/38552260)
- [微容器：更小的，更轻便的Docker容器](http://dockone.io/article/1035)
- [docker for windows 设置aliyun加速以及改变pull镜像位置](https://blog.csdn.net/gx947791229/article/details/76512660)