# 决策树ID3与C4.5
***
*参考极客时间<数据分析实战45讲>17章*

## 决策树的生成
&ensp;&ensp;&ensp;&ensp;决策树的生成会经历两个阶段:构造和剪枝

### 构造
&ensp;&ensp;&ensp;&ensp;构造就是生成一颗完整的决策树.简单来说,构造的过程就是选择什么属性作为节点的过程,在构造过程中会存在三种节点:

- 根节点:决策树最开始的节点
- 内部节点:决策树中间的节点
- 叶节点:决策树最底部的节点

&ensp;&ensp;&ensp;&ensp;在构造的过程中需要解决三个重要的问题:

- 1.选择哪个属性作为根节点
- 2.选择哪些属性作为子节点
- 3.什么时候停止并得到目标节点状态,即叶节点

### 剪枝
&ensp;&ensp;&ensp;&ensp;剪枝就是给决策树瘦身,使不需要太多的判断的同时能得到不错的结果,防止过拟合现象的发生.剪枝又分为预剪枝和后剪枝.

&ensp;&ensp;&ensp;&ensp;预剪枝是在决策树构造时就进行剪枝.方法是在构造的过程中对节点进行评估,如果对某个节点进行划分,在验证集中不能带来准确性的提升,那么对这个节点进行划分就没有意义,这时就会把当前节点当做叶节点,不对其进行划分.

&ensp;&ensp;&ensp;&ensp;后剪枝就是在生成决策树之后再进行剪枝,通常会从决策树的叶节点开始,逐层向上对每个节点进行评估.如果减掉这个节点子树,与保留该节点子树在分类上准确性差别不大或有所提升,那么就可以把该节点子树进行剪枝.方法是:用这个节点子树的叶子节点来替代该节点,类标记为这个节点子树中最频繁的那个类.

### 节点选择
&ensp;&ensp;&ensp;&ensp;给出一个打篮球的数据集如下:

|天气|温度|湿度|刮风|是否打篮球|
|----|:--:|:--:|:--:|--------|
|晴|高|中|否|否|
|晴|高|中|是|否|
|阴|高|高|否|是|
|小雨|高|高|否|是|
|小雨|低|高|否|是|
|晴天|中|中|是|是|
|阴天|中|高|是|否|

&ensp;&ensp;&ensp;&ensp;在这个探讨将那个属性(天气,温度,湿度,刮风)作为根节点的关键问题,这里是根据纯度和信息熵进行选择的,在纯度和信息熵之间有关联关系:纯度越低,信息熵越大;纯度越高,信息熵越小.

&ensp;&ensp;&ensp;&ensp;关于的纯度和信息熵的具体信息和数学公式在极客时间-数据分析实战45讲-17讲里面有,这里不叙述了,数学公式之类太繁琐,可能会说不清楚,极客时间里面讲的还行,需要的可以去看看.

&ensp;&ensp;&ensp;&ensp;在构造决策树时会基于纯度来构建,而经典的"不纯度"的指标有三种,分别是信息增益(ID3算法),信息增益率(C4.5算法)以及基尼指数(Cart算法),这篇文件介绍ID3算法.

&ensp;&ensp;&ensp;&ensp;ID3算法计算的是信息增益,信息增益指的是划分可以带来纯度的提高,信息熵的下降.它的计算公式是父亲节点的信息熵减去所有子节点的信息熵.在计算的过程中,会计算每个子节点的归一化信息熵,即按照每个子节点在父节点中出现的概率,来计算这些子节点的信息熵.

&ensp;&ensp;&ensp;&ensp;在构造的过程中需要计算两个东西:信息熵,信息增益.下面以打篮球的数据集为例如何计算:

#### 信息熵
##### 根节点信息熵
&ensp;&ensp;&ensp;&ensp;信息熵的计算公式如下:

$$Ent(D) = - \sum p_k \log_2 p_k$$

&ensp;&ensp;&ensp;&ensp;其中的累加次数为结果数量,这个就是2(去打篮球和不去打篮球).$p_k$表示的是每个累加情况的概率,在上面的数据集中有7条数据,3个打篮球,4个不打篮球,那么不去打篮球的$p_k$为$\frac{4}{7}$,去打篮球为 $\frac{3}{7}$.则在根节点为空的情况下,根节点信息熵为:

$$Ent(D) = - \sum p_k \log_2 p_k = - (\frac{4}{7} \log_2 \frac{4}{7} + \frac{3}{7} \log_2 \frac{3}{7}) = 0.985$$

##### 归一化信息熵
&ensp;&ensp;&ensp;&ensp;如果将天气作为属性划分,分别会有三个叶节点D1(晴天),D2(阴天),D3(小雨),用+代表去打篮球,-代表不去打篮球,+-符号前面是数据序号,那D1,D2,D3可以表示如下:

- D1(天气=晴天)={1-,2-,6+}
- D2(天气=阴天)={3+,7-}
- D3(天气=小雨)={4+,5-}

&ensp;&ensp;&ensp;&ensp;三个叶节点的信息熵为:

$$Ent(D1) = - (\frac{1}{3} log_2 \frac{1}{3} + \frac{2}{3} log_2 \frac{2}{3}) = 0.918$$
$$Ent(D2) = - (\frac{1}{2} log_2 \frac{1}{2} + \frac{1}{2} log_2 \frac{1}{2}) = 1.0$$
$$Ent(D3) = - (\frac{1}{2} log_2 \frac{1}{2} + \frac{1}{2} log_2 \frac{1}{2}) = 1.0$$

&ensp;&ensp;&ensp;&ensp;D为天气,D1有3个记录,D2有2个记录,D3有2个记录,一个7条记录.D1在D中的概率为3/7,D2在D中的概率为2/7,D3在D中的概率为2/7.那么作为子节点的归一化信息熵为:$3/7*0.918+2/7*1.0+2/7*1.0=0.965$

#### 信息增益
&ensp;&ensp;&ensp;&ensp;在上面的信息熵的结果上可以得到天气作为属性节点的信息增益为根节点信息熵-归一化信息熵:$Gain(D,天气)=0.985-0.965=0.020$

#### 根据信息熵和信息增益选择节点
&ensp;&ensp;&ensp;&ensp;通过上面的例子,可以得到下面的节点的信息增益:

- Gain(D,天气)=0.020
- Gain(D,温度)=0.128
- Gain(D,湿度)=0.020
- Gain(D,刮风)=0.020

&ensp;&ensp;&ensp;&ensp;可以看出这里的温度的信息增益最大,所有温度作为整个决策树的根节点.后面根据温度的高中低会生成三个中间节点,而这三个中间节点的选择算法同根节点,如果只有一种结果那这个节点就是叶节点.这样就生成了一颗决策树.

#### 信息增益率
*在文章中这部分好像描述的比较少,自己读起来也是比较懵,在网上找了一篇讲的不错的作为参考:[C4.5算法详解](https://blog.csdn.net/zjsghww/article/details/51638126)

&ensp;&ensp;&ensp;&ensp;信息增益率是C4.5算法对ID3改进使用的,它由信息增益和属性熵获取(属性分裂信息度量)

##### 属性熵(属性分裂信息度量)
&ensp;&ensp;&ensp;&ensp;上栗子吧,就不搞公式了,防止我理解错误,上面表格中各个属性熵为:

H(天气)= -(3/7 * log2(3/7) + 2/7 * log2(2/7) + 2/7 * log2(2/7)) 天气有晴阴雨,各占3/7,2/7,2/7
H(温度)= -(4/7 * log2(4/7) + 2/7 * log2(2/7) + 1/7 * log2(1/7)) 温度有高中低
H(湿度)= -(4/7 * log2(4/7) + 3/7 * log2(3/7)) 湿度有两种情况
H(刮风)= -(4/7 * log2(4/7) + 3/7 * log2(3/7)) 刮风有两种情况

## ID3和C4.5算法实现(Python3)
```python
# -*- coding: utf-8 -*-
from copy import copy

import math


def calculateInfoEntropy(trainData, attributeIndex):
    """

    :param trainData:
    :param attributeIndex:
    :return:
    {
        status: {
            count: value,
            value: value,
        },
        ......
    }
    """
    statusStatistics = {}
    for item in trainData:
        if item[attributeIndex] not in statusStatistics:
            statusStatistics[item[attributeIndex]] = {}
        if trainData[item] not in statusStatistics[item[attributeIndex]]:
            statusStatistics[item[attributeIndex]][trainData[item]] = 1
        else:
            statusStatistics[item[attributeIndex]][trainData[item]] = statusStatistics[item[attributeIndex]][trainData[item]] + 1

    infoEntropyMap = {}
    for status in statusStatistics:
        amount = 0
        for result in statusStatistics[status]:
            amount = amount + statusStatistics[status][result]

        infoEntropy = 0.0
        for result in statusStatistics[status]:
            probability = statusStatistics[status][result] / amount
            infoEntropy = infoEntropy + (probability * math.log(probability, 2))

        infoEntropyMap[status] = {}
        infoEntropyMap[status]["count"] = amount
        infoEntropyMap[status]["value"] = infoEntropy * -1
    return infoEntropyMap


def getInfoEntropy(trainData, properties, propertiesIndex):
    """

    :param trainData:
    :param properties:
    :param propertiesIndex:
    :return:
    {
        attribute: {
            status: {
                count: value,
                value: value,
            },
            ......
        },
        ......
    }
    """
    infoEntropy = {}
    for attribute in properties:
        infoEntropy[attribute] = calculateInfoEntropy(trainData, propertiesIndex[attribute])
    return infoEntropy


def calculateNormalizedInfoEntropy(attributeInfoEntropy):
    amount = 0
    for status in attributeInfoEntropy:
        amount = amount + attributeInfoEntropy[status]["count"]

    normalizedInfoEntropy = 0.0
    for status in attributeInfoEntropy:
        probability = attributeInfoEntropy[status]["count"] / amount
        normalizedInfoEntropy = normalizedInfoEntropy + probability * attributeInfoEntropy[status]["value"]
    return normalizedInfoEntropy


def getNormalizedInfoEntropy(infoEntropy):
    normalizedInfoEntropy = {}
    for attribute in infoEntropy:
        normalizedInfoEntropy[attribute] = calculateNormalizedInfoEntropy(infoEntropy[attribute])
    return normalizedInfoEntropy


def getAttributeEntropy(trainData, properties, propertiesIndex):
    attributeEntropy = {}
    for attribute in properties:
        attributeEntropy[attribute] = 0.0
        index = propertiesIndex[attribute]
        attributeStatistics = {}
        for item in trainData:
            if item[index] not in attributeStatistics:
                attributeStatistics[item[index]] = 1
            else:
                attributeStatistics[item[index]] = attributeStatistics[item[index]] + 1

        for item in attributeStatistics:
            probability = attributeStatistics[item] / len(trainData)
            attributeEntropy[attribute] = attributeEntropy[attribute] + probability * math.log(probability, 2)
        attributeEntropy[attribute] = attributeEntropy[attribute] * -1
    return attributeEntropy


def getInfoGainRate(infoGain, attributeEntropy):
    infoGainRate = {}
    for attribute in attributeEntropy:
        if attributeEntropy[attribute] == 0.0:
            continue
        infoGainRate[attribute] = infoGain[attribute] / attributeEntropy[attribute]
    return infoGainRate


def selectNode(trainData, properties, propertiesIndex, model):
    """

    :param trainData:
    :param properties:
    :param propertiesIndex:
    :return:
    node(当前选择的节点属性信息):{
        name: 节点属性名称
        status: { 各个状态的信息熵
            key: {
                keys: []
                value: value
            }
        }
    }
    """
    print("\n当前训练集为:", trainData)
    print("当前的属性列表为:", properties)
    resultStatistics = {}
    for item in trainData:
        if trainData[item] not in resultStatistics:
            resultStatistics[trainData[item]] = 1
        else:
            resultStatistics[trainData[item]] = resultStatistics[trainData[item]] + 1

    rootInfoEntropy = 0.0
    for result in resultStatistics:
        probability = resultStatistics[result] / len(trainData)
        rootInfoEntropy = rootInfoEntropy + (probability * math.log(probability, 2))
    rootInfoEntropy = rootInfoEntropy * -1
    print("当前训练集的根节点的信息熵:", rootInfoEntropy)

    infoEntropy = getInfoEntropy(trainData, properties, propertiesIndex)
    print("当前训练集的信息熵:", infoEntropy)

    normalizedInfoEntropy = getNormalizedInfoEntropy(infoEntropy)
    print("当前训练集的归一化信息熵:", normalizedInfoEntropy)

    infoGain = {}
    for attribute in properties:
        infoGain[attribute] = rootInfoEntropy - normalizedInfoEntropy[attribute]
    print("当前训练集的信息增益:", infoGain)

    name = None
    for attribute in infoGain:
        if name is None:
            name = attribute
        elif infoGain[attribute] > infoGain[name]:
            name = attribute

    # C4.5添加start
    attributeEntropy = {}
    infoGainRate = {}
    if model == 1:
        attributeEntropy = getAttributeEntropy(trainData, properties, propertiesIndex)
        print("当前属性熵：", attributeEntropy)

        infoGainRate = getInfoGainRate(infoGain, attributeEntropy)
        print("当前信息增益率:", infoGainRate)

        name = None
        for attribute in infoGainRate:
            if name is None:
                name = attribute
            elif infoGainRate[attribute] > infoGainRate[name]:
                name = attribute

    statusMap = {}
    for status in infoEntropy[name]:
        statusMap[status] = {}
        statusMap[status]["value"] = infoEntropy[name][status]["value"]
        statusMap[status]["keys"] = []
        for item in trainData:
            if item[propertiesIndex[name]] == status:
                statusMap[status]["keys"].append(item)

    node = {
        "name": name,
        "status": statusMap,
    }
    return node


def getID3Tree(trainData, properties, propertiesIndex, model):
    """
    获取ID3决策树
    :param trainData:
    :param properties:
    :param propertiesIndex:
    :return:

    node(当前选择的节点属性信息):{
        name: 节点属性名称
        status: { 各个状态的信息熵
            key: {
                keys: []
                value: value
            }
        }
    }
    """
    node = selectNode(trainData, properties, propertiesIndex, model)
    print("当前选择的最优节点为:", node)
    tree = {
        node["name"]: {},
    }

    tempProperties = copy(properties)
    print("进行属性列表裁剪:", tempProperties, node["name"])
    if node["name"] in tempProperties:
        tempProperties.remove(node["name"])

    for status in node["status"]:
        if node["status"][status]["value"] == 0.0:
            tree[node["name"]][status] = trainData[node["status"][status]["keys"][0]]
        else:
            tempTrainData = {}
            for item in trainData:
                if item[propertiesIndex[node["name"]]] == status:
                    tempTrainData[item] = trainData[item]

            tree[node["name"]][status], nodeName = getID3Tree(tempTrainData, tempProperties, propertiesIndex, model)
            print("进行属性列表裁剪:", tempProperties, nodeName)
            if nodeName in tempProperties:
                tempProperties.remove(nodeName)

    return tree, node["name"]


def printTree(tree, interval):
    newInterval = interval + "\t"
    for item in tree:
        if type(tree[item]) is not dict:
            print(interval + item, tree[item])
        else:
            print(interval + item)
            printTree(tree[item], newInterval)


if __name__ == "__main__":
    # 设置算法模式: 0->ID3 1->C4.5
    model = 0

    properties = ["weather", "temperature", "humidity", "windy"]
    propertiesIndex = {"weather": 0, "temperature": 1, "humidity": 2, "windy": 3}
    # 0:不打篮球 1:打篮球
    trainData = {
        ("sun", "high", "middle", "no"): 0,
        ("sun", "high", "middle", "yes"): 0,
        ("cloud", "high", "high", "no"): 1,
        ("rain", "high", "high", "no"): 1,
        ("rain", "low", "high", "no"): 0,
        ("sun", "middle", "middle", "yes"): 1,
        ("cloud", "middle", "high", "yes"): 0,
    }
    # for key in trainData:
    #     print(key, trainData[key])

    tree, name = getID3Tree(trainData, properties, propertiesIndex, model)
    print(str(tree).replace("'", '"'))
    printTree(tree, "")

```

运行结果为:
```
当前训练集为: {('sun', 'high', 'middle', 'no'): 0, ('sun', 'high', 'middle', 'yes'): 0, ('cloud', 'high', 'high', 'no'): 1, ('rain', 'high', 'high', 'no'): 1, ('rain', 'low', 'high', 'no'): 0, ('sun', 'middle', 'middle', 'yes'): 1, ('cloud', 'middle', 'high', 'yes'): 0}
当前的属性列表为: ['weather', 'temperature', 'humidity', 'windy']
当前训练集的根节点的信息熵: 0.9852281360342516
当前训练集的信息熵: {'weather': {'sun': {'count': 3, 'value': 0.9182958340544896}, 'cloud': {'count': 2, 'value': 1.0}, 'rain': {'count': 2, 'value': 1.0}}, 'temperature': {'high': {'count': 4, 'value': 1.0}, 'low': {'count': 1, 'value': -0.0}, 'middle': {'count': 2, 'value': 1.0}}, 'humidity': {'middle': {'count': 3, 'value': 0.9182958340544896}, 'high': {'count': 4, 'value': 1.0}}, 'windy': {'no': {'count': 4, 'value': 1.0}, 'yes': {'count': 3, 'value': 0.9182958340544896}}}
当前训练集的归一化信息熵: {'weather': 0.9649839288804954, 'temperature': 0.8571428571428571, 'humidity': 0.9649839288804954, 'windy': 0.9649839288804954}
当前训练集的信息增益: {'weather': 0.02024420715375619, 'temperature': 0.12808527889139454, 'humidity': 0.02024420715375619, 'windy': 0.02024420715375619}
当前选择的最优节点为: {'name': 'temperature', 'status': {'high': {'value': 1.0, 'keys': [('sun', 'high', 'middle', 'no'), ('sun', 'high', 'middle', 'yes'), ('cloud', 'high', 'high', 'no'), ('rain', 'high', 'high', 'no')]}, 'low': {'value': -0.0, 'keys': [('rain', 'low', 'high', 'no')]}, 'middle': {'value': 1.0, 'keys': [('sun', 'middle', 'middle', 'yes'), ('cloud', 'middle', 'high', 'yes')]}}}
进行属性列表裁剪: ['weather', 'temperature', 'humidity', 'windy'] temperature

当前训练集为: {('sun', 'high', 'middle', 'no'): 0, ('sun', 'high', 'middle', 'yes'): 0, ('cloud', 'high', 'high', 'no'): 1, ('rain', 'high', 'high', 'no'): 1}
当前的属性列表为: ['weather', 'humidity', 'windy']
当前训练集的根节点的信息熵: 1.0
当前训练集的信息熵: {'weather': {'sun': {'count': 2, 'value': -0.0}, 'cloud': {'count': 1, 'value': -0.0}, 'rain': {'count': 1, 'value': -0.0}}, 'humidity': {'middle': {'count': 2, 'value': -0.0}, 'high': {'count': 2, 'value': -0.0}}, 'windy': {'no': {'count': 3, 'value': 0.9182958340544896}, 'yes': {'count': 1, 'value': -0.0}}}
当前训练集的归一化信息熵: {'weather': 0.0, 'humidity': 0.0, 'windy': 0.6887218755408672}
当前训练集的信息增益: {'weather': 1.0, 'humidity': 1.0, 'windy': 0.31127812445913283}
当前选择的最优节点为: {'name': 'weather', 'status': {'sun': {'value': -0.0, 'keys': [('sun', 'high', 'middle', 'no'), ('sun', 'high', 'middle', 'yes')]}, 'cloud': {'value': -0.0, 'keys': [('cloud', 'high', 'high', 'no')]}, 'rain': {'value': -0.0, 'keys': [('rain', 'high', 'high', 'no')]}}}
进行属性列表裁剪: ['weather', 'humidity', 'windy'] weather
进行属性列表裁剪: ['weather', 'humidity', 'windy'] weather

当前训练集为: {('sun', 'middle', 'middle', 'yes'): 1, ('cloud', 'middle', 'high', 'yes'): 0}
当前的属性列表为: ['humidity', 'windy']
当前训练集的根节点的信息熵: 1.0
当前训练集的信息熵: {'humidity': {'middle': {'count': 1, 'value': -0.0}, 'high': {'count': 1, 'value': -0.0}}, 'windy': {'yes': {'count': 2, 'value': 1.0}}}
当前训练集的归一化信息熵: {'humidity': 0.0, 'windy': 1.0}
当前训练集的信息增益: {'humidity': 1.0, 'windy': 0.0}
当前选择的最优节点为: {'name': 'humidity', 'status': {'middle': {'value': -0.0, 'keys': [('sun', 'middle', 'middle', 'yes')]}, 'high': {'value': -0.0, 'keys': [('cloud', 'middle', 'high', 'yes')]}}}
进行属性列表裁剪: ['humidity', 'windy'] humidity
进行属性列表裁剪: ['humidity', 'windy'] humidity
{"temperature": {"high": {"weather": {"sun": 0, "cloud": 1, "rain": 1}}, "low": 0, "middle": {"humidity": {"middle": 1, "high": 0}}}}
temperature
    high
        weather
            sun 0
            cloud 1
            rain 1
    low 0
    middle
        humidity
            middle 1
            high 0
```