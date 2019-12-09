# python CSV文件读取
***
## 用CSV标准库读取：
```
import csv
csv_reader = csv.reader(open("fileName.csv"))
for row in csv_reader:
    print row
```