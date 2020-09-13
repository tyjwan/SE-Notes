# Python3 常用记录

***

## list

```python
# extend() 函数用于在列表末尾一次性追加另一个序列中的多个值（用新列表扩展原来的列表）。
list.extend(seq)
```

### 将list作为key，并取默认值为空list再添加元素

```python
ansDict[tuple(count)] = ansDict.get(tuple(count), []) + [s]
```

### list作为队列

```python
list.pop(0)
```

### list作为栈

```python
list.pop()
```

### 列表拼接:["1", "2", "3"] --> "1->2->3"

```python
"->".join(["1", "2", "3"])
```

### 数组初始化

```python
# 一维
parents = [i for i in range(0, len(M))]

# 二维
N = [[0]*10 for i in range(10)]
```


### 双端队列：deque

```python
quque = deque([[start, 1]])
node = quque.popleft()
```

## Dict

### 将list作为key，并取默认值为空list再添加元素

```python
ansDict[tuple(count)] = ansDict.get(tuple(count), []) + [s]
```


### 将dict作为二维list返回

```python
list(dict.values())
```

### 列表统计构造哈希映射

```python
index = {element: i for i, element in enumerate(inorder)}
```

### 字典排序

```sh
sorted(dataOrigin.items(), key=lambda k: k[1], reverse=False)
```

### 列表字典排序

```sh
import operator
x = [{'name':'Homer', 'age':39}, {'name':'Bart', 'age':10}]
sorted_x = sorted(x, key=operator.itemgetter('age'),reverse=True) # True 是倒叙  默认是False
print sorted_x
```

### 使用堆排序排序字典

```python
class Solution:
    def topKFrequent(self, nums: List[int], k: int) -> List[int]:
        statistics = {}
        for num in nums:
            statistics[num] = statistics.get(num, 0) + 1
        result = Counter(statistics).most_common(k)
        ans = []
        for key, value in result:
            ans.append(key)
        return ans
```

## String

```python
# convert
str='Runoob'
print(str[::-1])

str='Runoob'
print(''.join(reversed(str)))
```

### 字符转数字统计：

```python
count = [0] * 26
count[ord(c) - ord('a')] += 1
```

## if

```python
i = 5 if a > 7 else 0
```

## 装饰器

### @lru_cache(None)

&ensp;&ensp;&ensp;&ensp;把耗时的函数的结果保存起来，避免传入相同的参数时重复计算

```python
@lru_cache(None)
def fib_with_cache(n):
    if n < 2:
        return n
    return fib_with_cache(n - 2) + fib_with_cache(n - 1)
```

## 其他

### 四则运算

```python
eval("1*2")
```

### 输入与进制转换

```python
print(int(input(), 16))

dec = int(input("输入数字："))
print("十进制数为：", dec)
print("转换为二进制为：", bin(dec))
print("转换为八进制为：", oct(dec))
print("转换为十六进制为：", hex(dec))
```