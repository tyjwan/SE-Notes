# Ctenos7 vim8 安装
***
### 一、GitHub官网下载相应的安装包
&ensp;&ensp;&ensp;&ensp;https://github.com/vim/vim/releases

### 二、解压安装
&ensp;&ensp;&ensp;&ensp;按照下面的命令进行安装即可

```
./configure --with-features=huge --enable-multibyte --enable-rubyinterp=yes --enable-pythoninterp=yes --enable-python3interp=yes --prefix=/usr/local/vim8
```

*安装后可以命令不能及时识别，运行命令重新加载一下*

```
source /etc/profile
```

### Python vim
- pyvim:https://github.com/jonathanslenders/pyvim
- :!command :退出vim，并执行shell命令command，将命令输出显示在vim的命令区域，不会改变当前编辑的文件的内容

```
# vimrc
set mouse=a 
```

## 参考链接
- [centos7下编译安装vim8，并支持python3](https://blog.csdn.net/geerniya/article/details/79686408)
- [vim中执行shell命令小结](vim中执行shell命令小结)