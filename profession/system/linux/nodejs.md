# nodejs 使用记录
***
## 安装nodejs
### centos
```bash
curl -sL https://rpm.nodesource.com/setup_10.x | sudo bash -
yum install nodejs -y
node --version

npm config set registry https://registry.npm.taobao.org
npm install
npm run dev

npm config set prefix F:\Software\nodejs
npm config set HOME F:\Software\nodejs
```

## 错误记录
### npm ERR! cb() never called!
```
npm cache clean -f
npm install -g n
n stable
npm install -g cnpm --registry=https://registry.npm.taobao.org
cnpm install
```

## 参考链接
- [How to install Node.js and npm on CentOS 7](https://linuxize.com/post/how-to-install-node-js-on-centos-7/)-
- [npm配置国内镜像资源+淘宝镜像](https://blog.csdn.net/qq_39207948/article/details/79449633)
