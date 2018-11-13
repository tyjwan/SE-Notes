# docker使用记录
****
## 使用小贴士
- 在没有进行配置之前，使用docker必须赋予root权限

## docker 的安装
- [Docker CE 镜像源站](https://yq.aliyun.com/articles/110806?spm=a2c4e.11153940.blogcont29941.13.520269d6XtzWqG)
- [安装教程](http://blog.csdn.net/qq_27818541/article/details/73647797)
- [镜像加速地址](https://cr.console.aliyun.com/?accounttraceid=3480ea01-1b31-4cb1-a169-6c220739cae6#/accelerator)
- 安装过程中的依赖问题解决：sudo apt-get install -f -y

## 与宿主机交互类
- 单个文件挂载：将宿主的文件进行挂载时，有时docker里面对文件操作会改变宿主机，而宿主机里面操作不会改变，这个好像涉及到一些问题，不建议这样进行挂载，可以进行目录挂载
- 目录挂载：目录挂载可以完美实现文件共享功能，docker和宿主机的操作都会改变文件

## 使用中需要注意的问题
- [空间的清理](https://yq.aliyun.com/articles/272173)

## 参考链接
- [不加 sudo 执行 Docker 命令](http://www.markjour.com/article/docker-no-root.html)
- [阿里云docker仓库](https://dev.aliyun.com/search.html)
- [Docker 这九个不同的应用场景](https://my.oschina.net/editorial-story/blog/1558880)
- [Docker最佳实践：构建最小镜像](https://zhuanlan.zhihu.com/p/38552260)
- [微容器：更小的，更轻便的Docker容器](http://dockone.io/article/1035)