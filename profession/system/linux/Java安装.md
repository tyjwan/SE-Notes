# Java安装
***
## Centos
- 查找OpenJDK安装包
	- rpm -qa | grep openjdk

- 卸载OpenJDK安装包
	- yum -y remove java-1.7.0-openjdk-headless.x86_64
   
## Ubuntu或者linux mint
```sh
sudo apt-get update && apt-get remove openjdk*
sudo tar zxvf jdk-7u80-linux-x64.tar.gz -C /usr/lib/java

vim /etc/profile
export JAVA_HOME=/usr/java/jdk1.8.0_221
export JRE_HOME=${JAVA_HOME}/jre
export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib
export PATH=${JAVA_HOME}/bin:$PATH

source /etc/profile
java -version
```

## 参考链接
- [CentOS7卸载OpenJDK安装Oracle JDK](https://blog.csdn.net/zitong_ccnu/article/details/40041533)
- [Linux Mint 安装JDK](https://blog.csdn.net/enterys/article/details/53781356)