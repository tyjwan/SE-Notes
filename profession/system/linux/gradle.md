# Linux Gradle使用
***
## Linxu（建议手动安装，有些通过命令安装的版本比较老）
```sh
wget https://services.gradle.org/distributions/gradle-5.6.2-bin.zip
sudo mkdir /opt/gradle
sudo unzip -d /opt/gradle gradle-5.5.1-bin.zip
ls /opt/gradle/gradle-5.5.1

sudo vim /etc/profile
export PATH=$PATH:/opt/gradle/gradle-5.5.1/bin

source /etc/profile

gradle build
gradle build -x test
```

### 加速配置
```sh
buildscript {
    repositories {
        maven{ url 'http://maven.aliyun.com/nexus/content/groups/public/'}
    }
}

allprojects {
    repositories {
        maven{ url 'http://maven.aliyun.com/nexus/content/groups/public/'}
    }
}
```

```sh
Switch your build to use Gradle 5.2.1 by updating your wrapper properties:

./gradlew wrapper --gradle-version=5.2.1


apply plugin:'application'
mainClassName = "com.gateway.server.Server"
```

## 参考链接
- [linux下的Gradle编译环境搭建与使用](https://blog.csdn.net/lxh19930428/article/details/53869408)
- [Installing Gradle](https://docs.gradle.org/5.5.1/userguide/installation.html)
- [gradle github](https://github.com/gradle/gradle/releases?after=v5.4.0-RC1)
- 