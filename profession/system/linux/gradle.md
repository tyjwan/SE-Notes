# Linux Gradle使用
***
## Linxu（建议手动安装，有些通过命令安装的版本比较老）
```sh
sudo mkdir /opt/gradle
sudo unzip -d /opt/gradle gradle-5.5.1-bin.zip
ls /opt/gradle/gradle-5.5.1

sudo vim /etc/profile
export PATH=$PATH:/opt/gradle/gradle-5.5.1/bin

source /etc/profile

gradle build
```

## 参考链接
- [linux下的Gradle编译环境搭建与使用](https://blog.csdn.net/lxh19930428/article/details/53869408)
- [Installing Gradle](https://docs.gradle.org/5.5.1/userguide/installation.html)