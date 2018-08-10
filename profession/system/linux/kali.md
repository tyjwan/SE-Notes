# Kali linux
***
### 软件的安装
&ensp;&ensp;&ensp;&ensp;在Kali中好像apt不太好使了，不知道是deepin这方面的问题，但它有一个比较好用的替代：gdebi

### google-chrome
vim /usr/bin/google-chrome
exec -a "$0" "$HERE/chrome" "$@" --user-data-dir --no-sandbox
add  - -no-sandbox - -user-data-dir

###  声音与网易云音乐
&ensp;&ensp;&ensp;&ensp;声音问题有点麻烦，在Windows下的时候就有问题，Linux下更不好整，还没有解决
&ensp;&ensp;&ensp;&ensp;支持Linux系统的重量级的娱乐应用不多啊，但最爱的网易云早就支持了，可谓是业界良心啊。去官网下载相应的即可，但kali请选择Ubuntu16.04版本，deepin安装不了

### 编程开发软件
&ensp;&ensp;&ensp;&ensp;自建服务器的方法到后面的版本不能用了，但后面版本的更新是越来越好看和好用了，所以下载最新的使用注册码进行激活吧，在：/etc/hosts文件中添加下面的东西
```
0.0.0.0 account.jetbrains.com
```
- [IntelliJ IDEA 注册码](http://idea.congm.in/)

&ensp;&ensp;&ensp;&ensp;下载idea和pycharm即可，进入bin目录中运行相应的脚步会提示建立快捷方式，愉快玩耍吧

###   中文输入发
&ensp;&ensp;&ensp;&ensp;安装中文输入的相应东西和搜狗输入发，重启系统后使用Ctrl+space即可切换输入法：
```
apt-get install fcitx
```

###  sublime text 3
&ensp;&ensp;&ensp;&ensp;首先去官网下载相应的sublime文件，解压到系统的 :/opt 目录下，解决中文输入问题：
```
# 下载imfix
git clone https://github.com/lyfeyaj/sublime-text-imfix.git

# 进入文件夹中，将相应的文件复制到sublime的安装目录中
cd sublime-text-imfix
cp ./lib/libsublime-imfix.so /opt/sublime_text_3/
cp ./src/subl /usr/bin/
```

&ensp;&ensp;&ensp;&ensp;修改 /usr/bin/subl, 如下:
```
#!/bin/sh
export LD_PRELOAD=/opt/sublime_text_3/libsublime-imfix.so
exec /opt/sublime_text_3/sublime_text "$@"
```

&ensp;&ensp;&ensp;&ensp;完成后使用subl命令就可以启动sublime并完美解决中文输入法和鼠标跟随问题了

##  参考链接
[解决Kali Linux没有声音](https://blog.csdn.net/huninglei3333/article/details/54686729)
[Kali 2018 vmtools+更新源配置](https://blog.csdn.net/Fly_hps/article/details/79764428)
[如何在 Kali Linux 中安装 Google Chrome 浏览器 ](https://linux.cn/article-8209-1.html)
[Kali 2.0安装与使用指南 ](http://www.freebuf.com/sectool/95167.html)
[Ubuntu16.04安装Chrome浏览器及解决root不能打开的问题](https://blog.csdn.net/s_sunnyy/article/details/79276480)
[解决Ubuntu下Sublime Text 3无法输入中文](https://www.jianshu.com/p/bf05fb3a4709)
[Ubuntu16.04 安装Sublime Text 3 并解决中文输入问题](https://blog.csdn.net/lu_embedded/article/details/79558280)