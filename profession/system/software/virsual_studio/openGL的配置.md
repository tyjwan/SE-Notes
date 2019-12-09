# VS中OpenGL的配置
***
## 简单配置：
```
一、将glut.lib和glut32.lib复制到安装路径的 VC\lib 下

二、将glut.dll和glut32.dll复制到C:\Window\system32和C:\Window\SysWOW64
目录下

三、将glut.h复制到安装路径的 VC\include\GL 目录下，没有GL自己创建
```

## 初始运行时会遇到的一些问题：

### 1.无法识别标识符 exit
    这里的原因是C++也有exit（）函数，这样就产生了问题。解决的办法是在包
    含OpenGL头文件之前包含 C++ 的 stdlib.h 头文件。像下面这样：

    #include <stdlib.h>
    #include <GL/glut.h>

## 参考链接：
[http://zhidao.baidu.com/link?url=V4MwgfWWdpwZAeLYrAvJvNkYZTWrbmxGLGCnXwAdwgY8Zd469LJxraU9fNIMixDfIbuu4eR1y8FDZItNuvweIq](http://zhidao.baidu.com/link?url=V4MwgfWWdpwZAeLYrAvJvNkYZTWrbmxGLGCnXwAdwgY8Zd469LJxraU9fNIMixDfIbuu4eR1y8FDZItNuvweIq)
