# Docker Java程序镜像制作
***
## 制作前的准备
- jre：不需要完整的jdk，jre即可，到Oracle进行下载即可，[下载链接](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html),根据自己的情况进行选择，这里选择jre-8u221-linux-x64.tar.gz，一般只能使用解压安装了，rpm的可以直接使用yum命令进行安装，但我们这里用不了
- jar包：自己写好的程序的可执行jar，放到镜像中直接运行即可

## 制作步骤
- mkdir docker：创建一个制作镜像的工作目录，里面用于存放构建镜像需要的文件，如jre和jar包，还有镜像构建文件Dockfile
- 在目录下放入jar包和jre
- 生产Dockfile文件并进行编写，编写示例和说明如下,下面的操作环境是centos7

```bash
# 创建镜像制作目录
mkdir docker
cd docker

# 将oracle的jre包和生成好的程序jar放到目录中
# 为了制造较小体积的镜像，不使用jdk包，也没有必要使用它
cp ../jre-8u221-linux-x64.tar.gz ./
cp ../seckill-1.0-SNAPSHOT.jar ./

# 创建并编辑编辑Dockfile镜像生成文件
vim Dockerfile

# 将下面的内容写入文件中，详细的东西在后面会有说明
FROM docker.io/jeanblanchard/alpine-glibc
ADD jre-8u221-linux-x64.tar.gz /usr/java/jdk
ENV JAVA_HOME /usr/java/jdk/jre1.8.0_221
ENV PATH ${PATH}:${JAVA_HOME}/bin
COPY seckill-1.0-SNAPSHOT.jar /home/seckill-1.0-SNAPSHOT.jar
CMD java -jar /home/seckill-1.0-SNAPSHOT.jar

# 生成名称为seckill，版本为v0的镜像
docker build -t seckill:v0 .
# 运行镜像，我的Java程序是监听在8080端口的，所以将镜像容器的8080端口映射到宿主机的8080，并命名容器的名称为seckill
docker run --name seckill -p 8080:8080 seckill
# 查看当前seckill容器的输出，这里会打印显示Java的控制台输出内容
docker logs seckill
```

&ensp;&ensp;&ensp;&ensp;下面讲解下Dockfile文件的内容：

- FROM docker.io/jeanblanchard/alpine-glibc：docker的linux镜像有很多，比如Ubuntu、centos之类的，但他们的体积都比较大，单纯一个镜像的都是400M以上的，而其中的alpine镜像只用不到10M，非常的小巧，所以这里就选用它作为基础的构建镜像。但是alpine是基于MUSL libs（mini libc）的，而Java是基于GUN Standard C （glibc）library的，如果不做任何处理的话Java不能在其上运行，而在网上有大佬已经制造安装了glibc的alpine镜像，我们直接使用即可
- ADD jre-8u221-linux-x64.tar.gz /usr/java/jdk：解压并添加jre压缩包到/usr/java/jdk目录下
- ENV JAVA_HOME /usr/java/jdk/jre1.8.0_221、ENV PATH ${PATH}:${JAVA_HOME}/bin:这种Java环境变量，这里踩过一个坑，需要注意的是JAVA_HOME的路径设置，如果你的jre包的版本和我不一样话，需要进行修改，我这里解压后是jre1.8.0_221
- COPY seckill-1.0-SNAPSHOT.jar /home/seckill-1.0-SNAPSHOT.jar：复制添加Java程序到指定目录
- CMD java -jar /home/seckill-1.0-SNAPSHOT.jar：设置在容器运行后启动Java程序

&ensp;&ensp;&ensp;&ensp;如果对docker不是太了解的话，可以查看下面的我制作过程中参考的链接

## 参考链接
- [Alpine制作JDK8镜像的坑](https://blog.csdn.net/Dannyvon/article/details/80092834)
- [三个技巧，将 Docker 镜像体积减小 90%](https://www.infoq.cn/article/3-simple-tricks-for-smaller-docker-images)
- [Docker最佳实践：构建最小镜像](https://zhuanlan.zhihu.com/p/38552260)
- [alpine](https://hub.docker.com/_/alpine/)
- [使用 Dockerfile 定制镜像](https://yeasy.gitbooks.io/docker_practice/image/build.html)
- [Dockerfile 中的 COPY 与 ADD 命令](https://www.cnblogs.com/sparkdev/p/9573248.html)
- [jre 下载](https://www.java.com/en/download/manual.jsp)
- [构建最小JDK Docker镜像](https://blog.csdn.net/AaronSimon/article/details/82711629)