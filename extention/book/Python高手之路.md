# 《Python高手之路》 阅读笔记
***
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