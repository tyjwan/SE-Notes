# Python程序打包
***
### 安装 pyinstaller
```sh
pip install pyinstaller
```

### 生成二进制文件
*确保打包前的源码能正确的运行，并进入需要打包的源码文件的所在目录*
```sh
# 生成单文件格式，在当前目录下的dist目录中，后面直接运行该文件即可
pyinstaller -F Agent.py
```

### 运行注意事项
&ensp;&ensp;&ensp;&ensp;生成的打包运行程序不能跨平台使用，有时候相同系统的不同版可能也不能使用。如在Ubuntu、centos、Windows下运行时，需要自行搭建各个系统版本下各自打包。

```flow
s=start:开始
e=end:结束
o=operation:操作项

s-o-e
```