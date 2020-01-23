# Kaggle初体验之泰坦尼特生存预测
***
*学习完了决策树的ID3、C4.5、CART算法，找一个试手的地方，Kaggle的练习赛泰坦尼特很不错，记录下*

## 流程
&ensp;&ensp;&ensp;&ensp;首先注册一个账号，然后在顶部菜单栏Competitions里面搜索Titanic，找到Titanic练习赛，练习赛就用用于帮助新手入门的，在比赛的页面有很多的入门推荐，很值得去一看。

- 获取数据集
- 探索数据集
- 清洗数据集
- 特征选择
- 训练数据集
- 预测数据集
- 提交结果文件

### 获取数据集
&ensp;&ensp;&ensp;&ensp;数据集在比赛面板菜单栏的Data里面，有三个数据集

- train.csv:训练数据集
- test.csv:需要预测结果的数据集
- gender_submission.csv:预测结果提交模板（直接提交这个都能达到77%的准确率，而自己写的Python调用机器学习算法的才75%，有意思）

### 探索数据集
&ensp;&ensp;&ensp;&ensp;拿到数据集以后我们需要看看里面是写什么内容，数据完整不完整之类的。在网站上对数据列的说明如下：

- PassengerId:乘客编号
- Survived:是否幸存
- Pclass：船票等级
- Name：乘客姓名
- Sex：乘客性别
- SibSp：亲戚数量（兄妹、配偶数）
- Parch：亲戚数量（父母、子女数）
- Ticket：船票号码
- Fare：船票价格
- Cabin：船舱
- Embarked：登录港口

&ensp;&ensp;&ensp;&ensp;使用的Python代码如下：

```python
import pandas as pd

train_data = pd.read_csv("../docs/train.csv")
test_data = pd.read_csv("../docs/test.csv")
# 了解数据表的基本情况：行数、列数、每列的数据类型、数据完整度;
print(train_data.info())
print("_"*30)
# 了解数据表的统计情况：总数、平均值、标准差、最小值、最大值
print(train_data.describe())
print("_"*30)
# 查看字符串类型（非数字）的整体情况
print(train_data.describe(include=['O']))
print("_"*30)
# 查看前五行数据
print(train_data.head())
print("_"*30)
# 查看后五行数据
print(train_data.tail())
print("_"*30)
```

&ensp;&ensp;&ensp;&ensp;运行结果大致如下：

```bash
RangeIndex: 891 entries, 0 to 890
Data columns (total 12 columns):
PassengerId    891 non-null int64
Survived       891 non-null int64
Pclass         891 non-null int64
Name           891 non-null object
Sex            891 non-null object
Age            714 non-null float64
SibSp          891 non-null int64
Parch          891 non-null int64
Ticket         891 non-null object
Fare           891 non-null float64
Cabin          204 non-null object
Embarked       889 non-null object
dtypes: float64(2), int64(5), object(5)
memory usage: 83.6+ KB
None
______________________________
       PassengerId    Survived      Pclass  ...       SibSp       Parch        Fare
count   891.000000  891.000000  891.000000  ...  891.000000  891.000000  891.000000
mean    446.000000    0.383838    2.308642  ...    0.523008    0.381594   32.204208
std     257.353842    0.486592    0.836071  ...    1.102743    0.806057   49.693429
min       1.000000    0.000000    1.000000  ...    0.000000    0.000000    0.000000
25%     223.500000    0.000000    2.000000  ...    0.000000    0.000000    7.910400
50%     446.000000    0.000000    3.000000  ...    0.000000    0.000000   14.454200
75%     668.500000    1.000000    3.000000  ...    1.000000    0.000000   31.000000
max     891.000000    1.000000    3.000000  ...    8.000000    6.000000  512.329200

[8 rows x 7 columns]
______________________________
                            Name   Sex  Ticket    Cabin Embarked
count                        891   891     891      204      889
unique                       891     2     681      147        3
top     Allen, Mr. William Henry  male  347082  B96 B98        S
freq                           1   577       7        4      644
______________________________
   PassengerId  Survived  Pclass  ...     Fare Cabin  Embarked
0            1         0       3  ...   7.2500   NaN         S
1            2         1       1  ...  71.2833   C85         C
2            3         1       3  ...   7.9250   NaN         S
3            4         1       1  ...  53.1000  C123         S
4            5         0       3  ...   8.0500   NaN         S

[5 rows x 12 columns]
______________________________
     PassengerId  Survived  Pclass  ...   Fare Cabin  Embarked
886          887         0       2  ...  13.00   NaN         S
887          888         1       1  ...  30.00   B42         S
888          889         0       3  ...  23.45   NaN         S
889          890         1       1  ...  30.00  C148         C
890          891         0       3  ...   7.75   NaN         Q

[5 rows x 12 columns]
```

### 清洗数据集
&ensp;&ensp;&ensp;&ensp;通过探索发现Age、Fare和Cabin这三个数据有所缺失，Age和Fare都为数值型，简单的使用平均数进行补齐，Cabin为字符串型，其中S最多，简单将缺失的填为S吧

```python
train_data["Age"].fillna(train_data["Age"].mean(), inplace=True)
test_data["Age"].fillna(test_data["Age"].mean(), inplace=True)

train_data["Fare"].fillna(train_data["Fare"].mean(), inplace=True)
test_data["Fare"].fillna(test_data["Fare"].mean(), inplace=True)

train_data["Embarked"].fillna("S", inplace=True)
test_data["Embarked"].fillna("S", inplace=True)
```

### 特征选择
&ensp;&ensp;&ensp;&ensp;通过数据探索，PassengerId为乘客编号，对分类没用，Name为乘客姓名，也没用；Cabin字段缺失值太多，暂时放弃；Ticket为船票号码，杂乱无章且无规律，放弃；剩下的字段就是：Plass、Sex、Age、SibSp、Parch、Fare、Embarked，接下来就将这些特征作为训练数据，并将数据中的符号化对象转换成数字对象进行表示。

```python
from sklearn.feature_extraction import DictVectorizer

features = ['Pclass', 'Sex', 'Age', 'SibSp', 'Parch', 'Fare', 'Embarked']
train_features = train_data[features]
train_labels = train_data['Survived']
test_features = test_data[features]

dvec=DictVectorizer(sparse=False)
train_features = dvec.fit_transform(train_features.to_dict(orient='record'))
```

### 训练数据集
&ensp;&ensp;&ensp;&ensp;使用Python的机器学习库中决策树训练一个模型,

```python
from sklearn.tree import DecisionTreeClassifier
clf = DecisionTreeClassifier(criterion="entropy")
clf.fit(train_features, train_labels)
```

### 预测数据集
&ensp;&ensp;&ensp;&ensp;对测试数据集里面的数据进行测试，并将结果输出到csv文件中，用于Kaggle的提交

```
test_features = dvec.transform(test_features.to_dict(orient="record"))
    pred_labels = clf.predict(test_features)
    print(test_features)
    print(pred_labels)
    print("_"*30)

with open("submission.csv", encoding="utf-8", mode="w", newline="") as f:
    write = csv.writer(f, delimiter=",", quotechar='"', quoting=csv.QUOTE_MINIMAL)
    write.writerow(["PassengerId", "Survived"])
    count = 0
    for item in test_data.values:
        print(item[0])
        write.writerow([item[0], pred_labels[count]])
        count = count + 1
```

&ensp;&ensp;&ensp;&ensp;可以做一些验证，前者是简单的使用训练数据进行验证，后者是K折交叉验证

```python
import numpy as np
from sklearn.model_selection import cross_val_score

acc_decision_tree = round(clf.score(train_features, train_labels), 6)
print(acc_decision_tree)
print("_"*30)

print(np.mean(cross_val_score(clf, train_features, train_labels, cv=10)))
```

### 提交结果文件
&ensp;&ensp;&ensp;&ensp;点击Kaggle泰坦尼特比赛页面的Submit Predictions，提交上一步生成的结果文件submission.csv，比赛名次在1万多名左右，嘿嘿，名次不是关键，这次的尝试还是有点意思，简单了解了整个预测过程和Kaggle的流程。

### 后面自己进行的一些尝试
- Age和Fare改用中位数补全：没有平均数好，预测才69%，平均数是73%

## 参考链接
- [Reading and Writing CSV Files in Python](https://realpython.com/python-csv/)
- [python3 writerow CSV文件多一个空行](https://blog.csdn.net/pfm685757/article/details/47806469)
- [机器学习系列(3)_逻辑回归应用之Kaggle泰坦尼克之灾](https://blog.csdn.net/han_xiaoyang/article/details/49797143)
- [Kaggle入门，看这一篇就够了](https://zhuanlan.zhihu.com/p/25686876)
- [Titanic: Machine Learning from Disaster](https://www.kaggle.com/c/titanic/overview)
