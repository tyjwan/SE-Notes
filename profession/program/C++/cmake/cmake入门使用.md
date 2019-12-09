# CMake 入门使用
***
## 实验环境配置
&ensp;&ensp;&ensp;&ensp;实验使用的是Centos，进行实验前需要确保安装下面的东西

```bash
yum install cmake -y
yum install make -y
yum install gcc
yum install  gcc-c++ libstdc++-devel -y
```

## 实验工程目录结构
&ensp;&ensp;&ensp;&ensp;实验的工程结构如下：

```bash
|-- CMakeLists.txt
|-- README.md
|-- builds
|-- include
|   |-- log.h
|   `-- out.h
|-- libs
`-- src
    |-- common
    |   |-- CMakeLists.txt
    |   |-- log.c
    |   `-- out.c
    `-- main.c
```

&ensp;&ensp;&ensp;&ensp;CMakeLists.txt是cmake配置文件，在这里进行工程编译运行配置；README.md是工程说明文档；builds用于存放编译时生成的所有文件，避免污染工程目录；include目录时用于存放所有的头文件；libs用于存放链接库文件；src为存放源码，其中main.c为主函数运行文件，common文件夹中简单定义两个文件，其中实现了两个简单的打印函数，在main.c进行了调用。

### 文件内容
#### main.c
```c
#include<stdio.h>
#include<stdlib.h>

#include"../include/log.h"
#include"../include/out.h"

int main(int argc, char **argv) {
    printf("Hello world\n");
    logStd();
    pout();
    exit(0);
}
```

#### log.h
```c
#ifndef LOG_H_
#define LOG_H_

#include<stdio.h>

void logStd();

#endif
```

#### log.c
```c
#include"../../include/log.h"

void logStd() {
    printf("log");
}
```

#### out.h
```c
#ifndef OUT_H_
#define OUT_H_

#include<stdio.h>

void pout();

#endif
```

#### out.c
```c
#include "../../include/out.h"

void pout() {
    printf("out\n");
}
```

#### ./CMakeLists.txt
```bash
CMAKE_MINIMUM_REQUIRED(VERSION 2.6)
PROJECT(engine)

INCLUDE_DIRECTORIES(include)

aux_source_directory(src DIR_SRCS)

add_subdirectory(src/common)

ADD_EXECUTABLE(engine src/main.c)

target_link_libraries(engine log)
```

#### ./src/common/CMakeLists.txt
```bash
aux_source_directory(. DIR_LIB_SRCS)
add_library(log ${DIR_LIB_SRCS})
```

## 编译运行
```bash
cd builds
cmake ..
make
./engine
```

## 参考链接
- [linux中安装gcc和g++](https://www.cnblogs.com/myw31415926/archive/2013/03/13/myw31415926.html)
- [CMake 入门实战](https://www.hahack.com/codes/cmake/)
- [Introduction to CMake by Example](http://derekmolloy.ie/hello-world-introductions-to-cmake/)
- [#ifndef#define#endif防止头文件重复包含， 你不是真的懂](https://blog.csdn.net/q191201771/article/details/6399820)
- [干货：构建C/C++良好的工程结构](https://zhuanlan.zhihu.com/p/59450618)
