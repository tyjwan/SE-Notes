# 《Python高手之路》 阅读笔记
***
## 第一章
### 1.1 Python版本问题
&ensp;&ensp;&ensp;&ensp;python在十几年后将被舍弃，新的开发请使用python3

### 1.2 目录结构
&ensp;&ensp;&ensp;&ensp;项目目录结构的规范性是很有必要的，一个好的项目结构应该如下
pip freeze > requirements.txt
```
# 比如项目的名称是：Test
- Foo/                       #项目名
	- bin/                    #可执行文件目录
		- test/               #可执行程序

	- foo/                   #主程序目录
		- tests/             #测试代码目录
			- __init__.py
			- app.py
		- __init__.py
		- main.py

	- conf/
		- __init__.py
		- setting.py

	- logs/

	- docs/
		- conf.py
		- abs.rst

	- setup.py
	- requirements.txt
	- README
```

## 第二章
### 2.1 导入系统
&ensp;&ensp;&ensp;&ensp;python的模块导入和sys息息相关，如果遇到导入问题，可用尝试使用sys进行解决。sys还可以导入费pyc的模块文件

### 2.2 标准库
&ensp;&ensp;&ensp;&ensp;有一些常用到的功能，在python的标准库中已经实现了，有时候没有必要重复的造轮子，在特定时候可以省去自己的很多麻烦和时间，特别是标准库中的multiprocessing，这些标准库有空自己需要整理和研究一波

#### 任务：整理研究python3标准库

### 2.6 管理API变化
&ensp;&ensp;&ensp;&ensp;写代码是们艺术，对于不断调整和变化的部分我们应该给与相应的提示