# Numpy简单入门
***
&ensp;&ensp;&ensp;&ensp;在Numpy中有两个重要的对象:ndarray和ufunc,下面进行简单的介绍和使用

## ndarray
&ensp;&ensp;&ensp;&ensp;ndarray实际就是多维数组,用于解决多维数组的问题.在Numpy数组中,维数称为秩,一维数组的秩为1,二维数组的秩为2,以此类推.在Numpy中每一个线性的数组称为一个轴,其实秩就是描述轴的数量.

### 创建简单数组
&ensp;&ensp;&ensp;&ensp;下面代码是使用示例:

```python
# 导入numpy包
import numpy as np
# 定义一个一维数组
a = np.array([1, 2, 3])
# 定义一个二维数组
b = np.array([[1, 2, 3], [4, 5, 6], [7, 8, 9]])

# 改变b二维数组的值
b[1, 1] = 0

print(a)
print(b)

"""
[1 2 3]
[[1 2 3]
 [4 0 6]
 [7 8 9]]
"""
```

### 创建结构数组
&ensp;&ensp;&ensp;&ensp;如果你想统计一个班级里面学生的姓名、年龄，以及语文、英语、数学成绩之类的,使用上面的下标也可以,但是不够便利,使用结构数组就会更直观和方便.

&ensp;&ensp;&ensp;&ensp;下面代码是使用示例:

```python
import numpy as np

# 定义dtype结构类型,formats是数据类型:字符串,整型,整型,整型,浮点数
studenttype = np.dtype({
    "names":["name", "age", "chinese", "math", "english"],
    "formats":["S32", "int", "int", "int", "float32"]
})

# 通过设置数组的dtype=studenttype,生成结构数组
students = np.array([("zhangFei", 32, 75, 100, 90), ("GuanYu", 24, 85, 96, 88), ("ZhaoYun", 28, 85, 92, 96)], studenttype)

# 通过结构体中的关键字分别获取所有的名字列表,年龄列表,语文成绩列表,数学成绩列表,英语成绩列表
names = students["name"]
ages = students["age"]
chineses = students["chinese"]
maths = students["math"]
englishs = students["english"]

print(names)
print(ages)
print(chineses)
print(maths)
print(englishs)

# 使用numpy平均数函数计算年龄平均数
print(np.mean(ages))

"""result
[b'zhangFei' b'GuanYu' b'ZhaoYun']
[32 24 28]
[75 85 85]
[100  96  92]
[90. 88. 96.]
28.0
"""
```

## ufunc运算
&ensp;&ensp;&ensp;&ensp;ufunc就是numpy中预先实现好的各种运算函数,其采用C语言进行实现,速度非常快.

### 连续数组的创建
&ensp;&ensp;&ensp;&ensp;Numpy可以很方便的创建连续数组,比如下面的示例代码使用arange和linspace函数进行创建

```python
import numpy as np

# 指定初始值,终值,步长来创建等差数列的一维数组,默认不包含终值
x1 = np.arange(1, 11, 2)

# 指定初始值,终值,元素个数来创建等差数列的一维数组
x2 = np.linspace(1, 9, 5)

print(x1)
print(x2)

"""result
[1 3 5 7 9]
[1. 3. 5. 7. 9.]
"""
```

### 算数运算
&ensp;&ensp;&ensp;&ensp;通过Numpy可以进行加减乘除,求N次方和取余数,示例代码如下:

```python
import numpy as np
x1 = np.arange(1, 11, 2)
x2 = np.linspace(1, 9, 5)

print(np.add(x1, x2))
print(np.subtract(x1, x2))
print(np.multiply(x1, x2))
print(np.divide(x1, x2))
print(np.power(x1, x2))
print(np.remainder(x1, x2))

"""result
[ 2.  6. 10. 14. 18.]
[0. 0. 0. 0. 0.]
[ 1.  9. 25. 49. 81.]
[1. 1. 1. 1. 1.]
[1.00000000e+00 2.70000000e+01 3.12500000e+03 8.23543000e+05
 3.87420489e+08]
[0. 0. 0. 0. 0.]
"""
```

### 统计函数
&ensp;&ensp;&ensp;&ensp;如果你想要对一堆数据有更清晰的认识,就需要对这些数据进行描述性的统计分析,如最大值,最小值,平均值,是否符合正态分布,方差和标准差等等,这些数据可以让你对这堆数据有更清晰的认知

&ensp;&ensp;&ensp;&ensp;下面是使用示例:

#### 最大值函数amax(),最小值函数amin()
```python
import numpy as np
a = np.array([[1, 2, 3], [4, 5, 6], [7, 8, 9]])

# 计算整个数组中的最大最小值
print(np.amax(a))
print(np.amin(a))

# 将一行数据视为一组数据计算最大最小值
print(np.amax(a, 0))
print(np.amin(a, 0))

# 将一列数据视为一组数据计算最大最小值
print(np.amax(a, 1))
print(np.amin(a, 1))

"""result
9
1
[7 8 9]
[1 2 3]
[3 6 9]
[1 4 7]
"""
```

#### 统计最大值与最小值之差ptp()
```python
import numpy as np
a = np.array([[1, 2, 3], [4, 5, 6], [7, 8, 9]])
print(np.ptp(a))
print(np.ptp(a, 0))
print(np.ptp(a, 1))

"""result
8
[6 6 6]
[2 2 2]
"""
```

#### 统计数组的百分位数percentile()
```python
import numpy as np
a = np.array([[1, 2, 3], [4, 5, 6], [7, 8, 9]])
# 第二个参数是百分位参数设置,代表第p个百分位数,这个P的取值范围为0-100,如果是0就是求最小值,50是求平均数,100是求最大值
print(np.percentile(a, 50))
print(np.percentile(a, 50, 0))
print(np.percentile(a, 50, 1))

"""result
5.0
[4. 5. 6.]
[2. 5. 8.]
"""
```

#### 统计数组中的中位数median(),平均数mean()
```python
import numpy as np
a = np.array([[1, 2, 3], [4, 5, 6], [7, 8, 9]])

print(np.median(a))
print(np.median(a, 0))
print(np.median(a, 1))

print(np.mean(a))
print(np.mean(a, 0))
print(np.mean(a, 1))

"""result
5.0
[4. 5. 6.]
[2. 5. 8.]
5.0
[4. 5. 6.]
[2. 5. 8.]
"""
```

#### 统计数组中的加权平均值average()
```python
import numpy as np
a = np.array([1, 2, 4, 5, 6])
# 不设置权重,默认每个值的权重都是相同的:(1+2+4+5+6)/5
print(np.average(a))

# 设置权重:(1*1+2*2+4*3+2*5+2*6)/(1+2+3+2+2)
wts = np.array([1, 2, 3, 2, 2])
print(np.average(a, weights=wts))

"""result
3.6
3.9
"""
```

#### 统计数组中的标准差std(),方差var()
```python
import numpy as np
a = np.array([1, 2, 4, 5, 6])
print(np.std(a))
print(np.var(a))

"""result
1.8547236990991407
3.44
"""
```

#### Numpy排序
&ensp;&ensp;&ensp;&ensp;直接使用sort(a, axis=1, kind="quicksort", order=None),其中kind参数标识排序算法,可以填入quicksort(快速排序),mergesort(合并排序),heapsort(堆排序);order字段是按照某个字段进行排序

&ensp;&ensp;&ensp;&ensp;代码示例如下:

```python
import numpy as np
a = np.array([[1, 9, 3], [4, 5, 6], [7, 8, 2]])
print(np.sort(a))
print(np.sort(a, 0))

"""result
[[1 3 9]
 [4 5 6]
 [2 7 8]]
[[1 5 2]
 [4 8 3]
 [7 9 6]]
"""
```

## 参考资料
- 建立了一个jupyterlab的GitHub工程,可以下载运行:[https://github.com/lw1243925457/BigDataLearn](https://github.com/lw1243925457/BigDataLearn)
- 极客时间:数据分析实战45讲,感兴趣的可以同分享的海报进行订阅

![数据分析实战45讲](./picture/数据分析实战45讲.jpg)
