# 结构体和类的大小问题
***
## 一个空结构体和类的大小
&ensp;&ensp;&ensp;&ensp;其大小为一，因为即使是空的结构体和类都是可以实例化的，为了让其能够实例化，系统需要让其占用一个字节来进行表示

## 一个不为空的结构体和类的大小
&ensp;&ensp;&ensp;&ensp;有一个结构体或类的定义如下

```c++
struct test {
    int a;
    char c;
}

class test {
    int a;
    char c
}
```

&ensp;&ensp;&ensp;&ensp;其两者的大小都为8，按照我们平时的理解应该是4（int）+1（char）=5才是，但是并不是这样的，原因是计算机的对齐原则，它是为了效率，具体什么的我也说不明白，并且这个在编码中用的也比较少，只是笔试和面试的时候会问一下，所有我们就不说原理，具体举例说明各种情况应该怎么去求

### 没有嵌套的结构体和没有成员函数和、继承和类嵌套的类的大小
&ensp;&ensp;&ensp;&ensp;没有嵌套是指结构体中不包含其他结构体和类中不包含其他类，至于为什么强调类中没有成员函数和继承是因为有些成员函数是占有相应大小的，继承也会影响大小，成员函数加上继承大小更复杂，这个部分在后面说，我们先从简单的开始讲。下面是计算的步骤：

- 1.找出在结构体中字节最长的
- 2.划分结构体的上下两个部分
- 3.依次求出上下两部分大小，然后相加得到总大小

&ensp;&ensp;&ensp;&ensp;小二，上个栗子

```c++
struct test {
    int a;
    char b;
    double c;
    char d;
    int e;
}
```

&ensp;&ensp;&ensp;&ensp;一个部分的大小计算原则是：为最大长度变量的最小倍数

&ensp;&ensp;&ensp;&ensp;这个结构体的计算如下：

- 1.首先找到最大长度是double类型的变量，其长度为8
- 2.把其划分为上下两个部分，上部分为int、char、double，下部分为char、int
- 3.上部分的原始大小4（int）+1（char）+8（double）=15，而8的最小倍数大于或等于15的为16，所以上部分的大小为16；下部分的原始大小为1（char）+4（int）=5，而大于或等于5的8的最小倍数为8，所以下部分的大小为8；则整个结构体的大小为16+8=24

&ensp;&ensp;&ensp;&ensp;如果有两个最大长度的变量呢？如下情况

```c++
struct test {
    int a;
    char b;
    double c;
    char d;
    int e;
    double f;
}
```

&ensp;&ensp;&ensp;&ensp;这个你可以取第一个进行划分，也可以取第二个进行划分。取第一个的话上部分如上面所示，下部分为1（char）+4（int）+8（double）=15，取8的最小倍数且其大于或等于16的为16，则下部分的大小为16。总大小为16+16=32.

&ensp;&ensp;&ensp;&ensp;如果取第二个进行划分的话就只有上部分了，大小为4（int）+1（char）+8（double）+1（char）+4（int）+8（double）=26，而8的最小倍数大于等于26的为32，所以上部分和总的大小为32了
}

&ensp;&ensp;&ensp;&ensp;如果是有数组的情况呢？它也没神马特别的，照样累加计算即可，如下面的结构体所示：

```c++
struct test {
    int a[4];
    char b;
    double d;
}
```

&ensp;&ensp;&ensp;&ensp;其中4（int）*5+1（char）+8（double）=29，则总大小为32.

### 有嵌套结构体或类的大小
&ensp;&ensp;&ensp;&ensp;上个栗子，看下面的结构：

```c++
//其大小为12
struct t1 {
    char a[8];
    int b;
}

struct t2 {
    char 1;
    t1 b;
    double c;
    int c;
}
```

&ensp;&ensp;&ensp;&ensp;面对嵌套查找最大变量时,结构体的大小并不能参与比较，我们需要用其内部的系统基本定义类型去比较。比如上面的第二天结构体的计算，最大的长度并不是t1，而是t2中的double，但计算时t1大小作为一个整体进行计算，入t2的大小为：上部分1（char）+12（t1）+8（double）=21=最小倍数24，下部分1（char）=1=最小倍数8，总大小为24+8=32。再看下面一个栗子

```c++
//其大小为16
struct t1 {
    char a;
    double b;
}

struct t2 {
    char a;
    t1 b;
    int c;
}
```

&ensp;&ensp;&ensp;&ensp;在比较时最大类型的t1里面的double，则划分为上部分char、t1，下部分int。记住计算时用的的嵌套结构体的总大小，则上部分大小为1(char)+16(t1)=17,16的最小倍数大于或等待17的是32，则上部分大小为32；下部分大小为4（int）=4,16的最小倍数大于或等于4的是16，则下部分的大小为16；总大小为32+16=48

&ensp;&ensp;&ensp;&ensp;总结下来是；查找比较时用嵌套结构体里面类型进行比较，计算时用整个嵌套结构体的大小进行计算

&ensp;&ensp;&ensp;&ensp;补充一个相等的情况：当嵌套里面的最大和本身的一样大神，选择本身的变量，如下

```c++
//大小为16
struct t1 {
    char a;
    int b;
    double c;
};
//大小为40，t1的double和t2的double一样大，但选择t2的double
struct t2 {
    char a;
    t1 b;
    double d;
    int c;
};
```

### 有static变量的类的大小
&ensp;&ensp;&ensp;&ensp;static是全局的，并不算在类的大小中，一般的函数也是不占类的大小，因为可以看成是所有此类实例化的对象共同拥有的。举例如下：

```c++
//其大小是4，static并不算在里面
class c1 {
    int a;
    static int b;
}
```

### 有虚函数的类的大小
&ensp;&ensp;&ensp;&ensp;首先我们解释一下虚函数的机制，并不全面，只讲一个和大小计算相关的部分，其它部分其实我也不太懂，嘿嘿。

&ensp;&ensp;&ensp;&ensp;当类中有虚函数时，系统就会为这个类生成一个虚函数表VTABLE，这个虚函数表相当于什么int* a之类的，不同的是它能存储虚函数的。并且这个表每个类就只有一个，所有的虚函数都存储在其中。也就是说不管有多少个虚函数，这些虚函数的总大小就是4（一个指针的大小就是4）。如下面的栗子：

```c++
//其大小为4
class c {
    virtual bool is() {};
}

//其大小也为4，多个虚函数等同于一个虚函数
class a {
    virtual bool is() {};
    virtual bool be() {};
}
```

### 有虚函数并继承的类的大小
&ensp;&ensp;&ensp;&ensp;当一个类继承了一个拥有虚函数的类的时候，子类并不会和父类同用一个虚函数表，它会唯一拥有自己的虚函数表。如下面的栗子：

```c++
//大小为8
class c1 {
    char a;
    virtual bool is() {};
    virtual bool is2() {};
    virtual bool is3() {};
    virtual bool is4() {};
    virtual bool is5() {};
    virtual bool is6() {};
    virtual bool is7() {};
};

//大小为16，最大为double的8,1（char）+4（c1的VTABLE）+8（double）=13=16
class c2 :public c1 {
    double a;
    virtual bool is() {};
};
```