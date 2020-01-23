# UML
****

## 类的表示
&ensp;&ensp;&ensp;&ensp;类用三层矩形框来表示：第一层显示类的名称，第二场是类的特性（字段和属性），第三是类的操作（方法和行为）；在特性和操作前面的‘+’表示 public， ‘-’表示 private， ‘#’表示 protected

&ensp;&ensp;&ensp;&ensp;示例如下：

![](./picture/1.jpg)

## 接口的表示
&ensp;&ensp;&ensp;&ensp;接口有两种表示方法：一是矩形表示法，二是棒棒糖表示法，三层的意思与类的表示是类似的，基本意思相同，只是第一层有点区别

矩形表示法：

![](./picture/2.jpg)

棒棒糖表示法：

![](./picture/3.jpg)

## 继承
&ensp;&ensp;&ensp;&ensp;继承用空心三角形+实线表示，如下：

![](./picture/4.jpg)

## 实现接口
&ensp;&ensp;&ensp;&ensp;实现接口用空心三角形+虚线：

![](./picture/5.jpg)

## 关联
&ensp;&ensp;&ensp;&ensp;关联关系用实线箭头表示：

![](./picture/6.jpg)

## 聚合
&ensp;&ensp;&ensp;&ensp;聚合关系（聚合表示一种弱的‘拥有’关系，体现的是A对象可以包含B对象，而B对象不是A对象的一部分（B对象不能包含A对象）用菱形+实线箭头表示：

![](./picture/7.jpg)

## 组合（合成）
&ensp;&ensp;&ensp;&ensp;合成（组合）是一种强的‘拥有’关系，体现了严格的部分和整体的关系，部分和整体的生命周期是一样的（强弱应该是体现在数量和生命周期上），表示如下：

![](./picture/9.jpg)

## 依赖
&ensp;&ensp;&ensp;&ensp;依赖关系用虚线箭头表示：

![](./picture/10.jpg)