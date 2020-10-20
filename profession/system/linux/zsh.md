# ZSH 安装设置
***
## CentOS
&ensp;&ensp;&ensp;&ensp;安装完成后退出重新登录终端即可

```sh
yum install -y zsh
chsh -s /bin/zsh
curl -L https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh | sh

# or
sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"

# or 自行建立脚本运行
https://github.com/ohmyzsh/ohmyzsh/blob/master/tools/install.sh

# 感觉是被墙了的原因，无法直接获取文件进行安装
# 可以访问：https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh
# 获取文件内容,下入下面的文件中，然后执行脚本
vim zshInstall.sh
sh zshInstall.sh
```

## 参考链接
- [robbyrussell/oh-my-zsh](https://github.com/robbyrussell/oh-my-zsh)
- [Install oh-my-zsh now](https://ohmyz.sh/)
- [Ubuntu 服务器安装shadowsocks](https://wangxin1248.github.io/linux/2018/04/ubuntu-install-shadowsocks.html)