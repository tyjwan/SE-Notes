# Python学习记录-第一周
****
## Python的安装（Windows7）
&ensp;&ensp;&ensp;&ensp;按照[廖雪峰Python2.7教程](http://www.liaoxuefeng.com/wiki/001374738125095c955c1e6d8bb493182103fac9270762a000)来安装就行，这个是很简单的，需要注意的是在安装好以后需要注销一下电脑，这样Python的环境变量才会生效，不然会提示不是内部命令。

## Python解释器
&ensp;&ensp;&ensp;&ensp;了解了Python解释器的基本和它的各种变种，感觉这个Python和Linux脚本语言有点像，两者都是解释性的语言，之前自己学过Linux的脚本编程。
&ensp;&ensp;&ensp;&ensp;还有就是直接点击Python文件也可以运行，但是会一闪而过，通过看书，里面介绍可以在最后一行加上raw_input("Press <enter>")这样就会等待你按下回车键才消失。

## 字符串编码问题
&ensp;&ensp;&ensp;&ensp;这个乱码的问题是一直都存在，直到现在读完这篇有关编码的文章我才理解为啥会乱码······，Python中Unicode表示的字符串用 u'...' 表示,而把它转换成utf-8则用encode('utf-8')的方法,反过来，把UTF-8编码表示的字符串'xxx'转换为Unicode字符串u'xxx'用decode('utf-8')方法：
```python
u'ABC'.encode('utf-8')
'abc'.decode('utf-8')
```

&ensp;&ensp;&ensp;&ensp;由于字符编码问题，请保证编辑器的编码格式是utf-8

## Python的语法规则
- 注释：#
- 输出：print
- 输出：raw_input(这个返回形式是字符串)
- 字母转数字：ord(parameter)
- 数字转字母：chr(parameter)
- 字符串或list的长度：len(parameter)
- 生成一个整数序列:range()
- 数据类型检查:isinstance(var, (type))
- 判断对象类型：type()
- 获得一个对象的所有属性和方法：dir()
- 判断对象是否有这个属性或方法：hasattr()
- 获取属性或方法：getattr()
- 设置一个属性：setattr()

## Python的数组list和tuple
&ensp;&ensp;&ensp;&ensp;这两个算是Python自定义的数组吧，和java的List是有点像的。里面的一些内容就不多写了，值得写的就是list的定义是用[],而tuple的定义是用()

## 条件判断和循环
- 条件：这里的if过后一定要有空格，后面的冒号也一定不要忘掉
```python
if <条件判断1>:
    <执行1>
elif <条件判断2>:
    <执行2>
elif <条件判断3>:
    <执行3>
else:
    <执行4>
```

- 只要x是非零数值、非空字符串、非空list等，就判断为True，否则为False。
```python
if x:
    print 'True'
```

- Python的循环有两种，
    - 一种是for...in循环，依次把list或tuple中的每个元素迭代出来
    ```python
    names = ['Michael', 'Bob', 'Tracy']
    for name in names:
        print name
    ```

    - for x in ...循环就是把每个元素代入变量x，然后执行缩进块的语句
    ```python
    sum = 0
    for x in [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]:
        sum = sum + x
    print sum
    ```

    - 第二种循环是while循环，只要条件满足，就不断循环，条件不满足时退出循环
    ```python
    sum = 0
    n = 99
    while n > 0:
        sum = sum + n
        n = n - 2
    print sum
    ```

## Python函数
&ensp;&ensp;&ensp;&ensp;在Python中，定义一个函数要使用def语句，依次写出函数名、括号、括号中的参数和冒号:，然后，在缩进块中编写函数体，函数的返回值用return语句返回,Python函数能返回多个值（但其实是返回一个tuple）；定义默认参数要牢记一点：默认参数必须指向不变对象；参数定义的顺序必须是：必选参数、默认参数、可变参数和关键字参数
```python
def my_abs(x):
    if x >= 0:
        return x
    else:
        return -x
```

## 注意事项
&ensp;&ensp;&ensp;&ensp;在写Python程序时，一般在开头加上下面的代码：
```python
#!/usr/bin/env python
# -*- coding: utf-8 -*-
```
&ensp;&ensp;&ensp;&ensp;第一行注释是为了告诉Linux/OS X系统，这是一个Python可执行程序，Windows系统会忽略这个注释；

&ensp;&ensp;&ensp;&ensp;第二行注释是为了告诉Python解释器，按照UTF-8编码读取源代码，否则，你在源代码中写的中文输出可能会有乱码。

## Django学习
### 安装
&ensp;&ensp;&ensp;&ensp;在Windows上输入一般命令安装是不行的，需要加链接，上面说是墙的问题，但我一直都是开着代理的啊，不明白，但按照下面的链接做的就行了：[Windows上Django无法正常安装](http://m.blog.csdn.net/article/details?id=48793101)

### 编写你的第一个Django应用
#### 第1部分
&ensp;&ensp;&ensp;&ensp;这部分里讲了一下初始的数据库使用方法吧：在mysite/settings.py文件里可以自定义数据库（现在自己是干不了的，以后再说······）；自定义类可以通过继承特定的Python中的数据库类来达到自己使用数据库的目的（自己还不是太熟练，说的不好）；这些在交互界面操作数据库的方式和其他数据库的操作还是有想通之处的，还是稍微找到点感觉吧

#### 第2部分
&ensp;&ensp;&ensp;&ensp;这个部分好像讲的就是以管理员的视角来设计吧，里面涉及了美化、排版之类的，还有好多好多的，先有个大概了解吧

#### 第3部分
&ensp;&ensp;&ensp;&ensp;这里讲了布置特定网页的方法吧，在view里面是定义相应的视图网页，polls/url.py是匹配相应的搜索，然后在mysite/url.py里加入这个搜索路径

#### 第4部分
&ensp;&ensp;&ensp;&ensp;这一个部分内容比较多，讲了许多网页的自动生成吧，我就感觉了解了一些，不一定会写······

#### 第5部分
&ensp;&ensp;&ensp;&ensp;讲了了怎样写测试，感觉挺好玩，挺有用的

#### 第6部分
&ensp;&ensp;&ensp;&ensp;这部分讲了CSS的应用，这个网页是必不可少的，比较简单吧

## 项目实战
### Day1
&ensp;&ensp;&ensp;&ensp;环境配置中的安装第三方库直接在DOS上输入教程上的命令就行了。数据库的安装也比较简单，搜索一波教程就行了。但在安装MySQL的Python库的时候遇到点麻烦：安装教程上的输入命令时总是提示错误，安装不了，通过搜索，找到了一个简单的解决办法--输入pip install --extra-index-url https://pypi.python.org/pypi/mysql-connector-python/2.0.4 mysql-connector-python，这个给我一些提示，以后遇到相似的问题可以通过网站的直接链接来输入安装。下面就再建立相应的工程目录文件就完成了。

[MySQL安装问题](http://stackoverflow.com/questions/27394426/python-pip-install-mysql-connector-python-2-0-1-fails)



