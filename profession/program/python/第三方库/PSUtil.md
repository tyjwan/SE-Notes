# Python PSUtil
***
## Code
```python
import os,psutil

process = psutil.Process(os.getpid())
print('Used Memory:',process.memory_info().rss / 1024 / 1024,'MB')
```

## 参考链接
- [python查看进程使用内存信息 psutil](https://blog.csdn.net/silent56_th/article/details/81320271)