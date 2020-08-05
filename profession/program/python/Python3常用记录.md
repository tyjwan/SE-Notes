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

## Dict
### 将list作为key，并取默认值为空list再添加元素
```python
ansDict[tuple(count)] = ansDict.get(tuple(count), []) + [s]
```


### 将dict作为二维list返回
```python
list(dict.values())
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




