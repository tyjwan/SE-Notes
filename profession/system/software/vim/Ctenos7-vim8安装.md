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

### spf13-vim 配置安装问题
```sh
vim /root/.vimbundle
Replace vundle plugin 'amirh/HTML-AutoCloseTag' with 'heracek/HTML-AuCloseTag
Plugin 'heracek/HTML-AutoCloseTag', {'name': 'HTML-AutoCloseTag2'}
UnBundle 'amirh/HTML-AutoCloseTag'
vim +PluginInstall +qall
```

## 参考链接
- [centos7下编译安装vim8，并支持python3](https://blog.csdn.net/geerniya/article/details/79686408)
- [vim中执行shell命令小结](vim中执行shell命令小结)
- [spf13/spf13-vim](https://github.com/spf13/spf13-vim)
- [andre3k1/spf13-vim](https://github.com/andre3k1/spf13-vim/commit/6950b831e38400213be89ebbf6dd0f0f6b7f9d8f)
- [vim插件管理器：Vundle的介绍及安装（很全）](https://blog.csdn.net/zhangpower1993/article/details/52184581)