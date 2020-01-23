# Ubuntu使用记录
×××
## 软件推荐
- 好用的终端管理工具：
	- sudo add-apt-repository ppa:gnome-terminator 
	- sudo apt-get install terminator

- java oracle jdk:
	- 下载Linux64的tar.gz压缩包，网址为：https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html
	- tar -zxvf jdk-8u231-linux-x64.tar.gz
	- sudo mv jdk1.8.0_231 /usr/bin/
	- sudo vim /etc/profile  ：填写内容到文件末尾，内容在下面
	- source /etc/profile

&ensp;&ensp;&ensp;&esnp;profile末尾填入的内容如下，JAVA_HOME的路径填写解压后的路径

```sh
export JAVA_HOME=/usr/bin/jdk1.8.0_231 
export JRE_HOME=${JAVA_HOME}/jre  
export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib  
export PATH=${JAVA_HOME}/bin:$PATH
```

- nodejs：https://nodejs.org/en/

## 参考链接
- [Ubuntu18.04 主题美化以及常用软件](https://www.jianshu.com/p/7d153a484f72)