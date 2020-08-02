# Windows下JetBrains CLion的pthread使用配置
***
## 安装平台
- windows10 64位系统
- JetBrains CLion
- C/C++环境：MinGW

## pthread下载
&ensp;&ensp;&ensp;&ensp;下载地址：[ftp://sourceware.org/pub/pthreads-win32/pthreads-w32-2-9-1-release.zip](ftp://sourceware.org/pub/pthreads-win32/pthreads-w32-2-9-1-release.zip)

&ensp;&ensp;&ensp;&ensp;下载打开后有下面三个文件夹：

- Pre-built.2
- pthreads.2
- QueueUserAPCEx


&ensp;&ensp;&ensp;&ensp;我们用到的主要是“Pre-built.2”这个文件夹下的三个文件夹，分别是动态链接库、头文件、静态链接库

- dll
- include
- lib


## 配置
### 配置动态链接库
&ensp;&ensp;&ensp;&ensp;大致如下：

```
复制 dll/x64/ 下的所有文件到 C:\Windows\SysWOW64 下
复制 dll/x86/ 下的所有文件到 C:\Windows\System32 下
```

### 配置头文件
```
复制 include 下的所有文件到 C:\MinGW\include 下
```

### 配置静态链接库
```
复制 lib 下的所有文件到 C:\MinGW\lib 下
```

## 冲突解决
&ensp;&ensp;&ensp;&ensp;在编译的时候会出现一个重定义错误，这个时候需要在导入phread头文件的语句前加一个定义就可以正常使用，示例如下：

```c
#define HAVE_STRUCT_TIMESPEC
#include <pthread.h>
```

## 参考链接
- [Timespec redefinition error [duplicate]](https://stackoverflow.com/questions/33557506/timespec-redefinition-error)
- [在windows下配置pthread](https://blog.csdn.net/qianchenglenger/article/details/16907821?utm_medium=distribute.pc_relevant.none-task-blog-baidujs-1)