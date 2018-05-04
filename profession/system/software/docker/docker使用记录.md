# docker使用记录
****
## 使用小贴士
- 在没有进行配置之前，使用docker必须赋予root权限

## docker 的安装
- [安装教程](http://blog.csdn.net/qq_27818541/article/details/73647797)
- [镜像加速地址](https://cr.console.aliyun.com/?accounttraceid=3480ea01-1b31-4cb1-a169-6c220739cae6#/accelerator)
- 安装过程中的依赖问题解决：sudo apt-get install -f -y

## 与宿主机交互类
- 单个文件挂载：将宿主的文件进行挂载时，有时docker里面对文件操作会改变宿主机，而宿主机里面操作不会改变，这个好像涉及到一些问题，不建议这样进行挂载，可以进行目录挂载
- 目录挂载：目录挂载可以完美实现文件共享功能，docker和宿主机的操作都会改变文件
