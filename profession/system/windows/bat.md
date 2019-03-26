# Bat 脚本记录
***
### 脚本编写
#### 关闭指令输入
&ensp;&ensp;&ensp;&ensp;在文件开始加上输出关闭

```bat
@echo off
```

#### 获取用户输入
&ensp;&ensp;&ensp;&ensp;使用/p标准，等号和前后请不要使用间隔，不然后有错误，等号后面的输入的提示

```bat
set /p var=please input
```

#### 生成随机数
&ensp;&ensp;&ensp;&ensp;%random% %%最大数 +最小数

```bat
set /a num=%random% %%100 +1
```

#### 睡眠指定时间
&ensp;&ensp;&ensp;&ensp;number为数字，指定时间

```bat
set /a number = 1
timeout /t %number% /nobreak > nul
```

#### 执行后不退出
&ensp;&ensp;&ensp;&ensp;末尾加上pause即可

#### 无限循环
```bat
:loop
echo loop
goto loop
```