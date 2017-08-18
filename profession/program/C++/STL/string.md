# string 详解
***
### 构造与析构
&ensp;&ensp;&ensp;&ensp;相应的参数及作用如下图：

![string1.png](./picture/string1.png)

&ensp;&ensp;&ensp;&ensp;在其中常用的要记住的是空串、字符串的子串、用单个字符重复填充，需要注意的是不能用单个字符进行只有一个参数的初始化，如 string（‘a') 这样是不允许的。其中的string（beg， end）的用法的用ASCII码为 end 的字符 beg 初始化字符串，例如

```c++
string s(5, 97);
cout << s << endl;
//结果为 aaaaa,因为 a 的ASCII码为97，并重复五个
```

### 更改内容
&ensp;&ensp;&ensp;&ensp;赋值=，assign；清除clear、erase；安插insert,在无符号整数时有时需强制转换；移除removing；

&ensp;&ensp;&ensp;&ensp;swap交互内容的复杂的为O（n）,当分配器相同时，直接交换控制信息

```c++
赋值
string& string::assign(const string& str)
string& string::assign(const char* cstr)
string& string::assign(const char* chars, size_type chars_len)
string& string::assign(size_type num, char c)
void string::swap(string& str)

添加字符
string & string::append(const string& str)
stirng& string::append(const string& str, size_type str_idx, size_type str_num)
string& string::append(const char* cstr)
string& string::append(const char* chars, size_type chars_len)
string& string::append(size_type num, char c)
string& string::append(InputIteracor beg, InputIterator end)

void string::push_back(char c)

安插
string& string::insert(size_type idx, const string& str)
string& string::insert(size_type idx, const& str, size_type str_idx, size_type str_num)
string& string::insert(size_type idx, const char* cstr)
string& string::insert(size_type idx, const char* chars, size_type chars_len)
string& string::insert(size_type idx, size_type num, char c)
void string::insert(iterator pos, size_type num, char c)
iterrator string::insert(iterator pos, char c)
void string::insert(iterator pos, InputIterator beg, InputIterator end)

删除
void string::clear()
string& string::erase()
string& string::erase(size_type idx)
string& string::erase(size_type idx, size_type len)
string& string::erase(iterator pos)
string& string::erase(iterator beg, iterator end)

改变容量大小
void string::resize(size_type num)
void string::resize(size_type num, char c)

替换
string& string::replace(size_type idx, size_type len, const string& str)
string& string::replace(iterator beg, iterator end, const string& str)
string& string::replace(size_type idx, size_type len, const string& str, size_type stri_icx, size_type str_num)
string& string::replace(size_type idx, size_type len, const char* cstr)
string& string::replace(iterator beg, iterator end, const char* cstr)
string& string::replace(size_type idx, size_type len, const char* chars, size_types chars_len)
string& string::replace(itertor beg, iterator end,const char* chars, size_type chars_len)
string& string::replace(size_type idx, size_type len, size_type num, char c)
string& string::replace(iterator bet, iterator end, size_type num, char c)
string& string::replace(iterator beg, iterator end, InputIerrator newBeg, InputIterator newEnd)
```


### 查
#### 查大小
&ensp;&ensp;&ensp;&ensp;其成员函数max_size()查询在物理机基础上能使用的大小，capacity（）查询分配的能使用的大小，size（）和length（）查询现在使用了的大小；试验了一下看到其大小分配类似于动态数组，但其增长规律不了解

#### 查字符
&ensp;&ensp;&ensp;&ensp;string的查询可以单个字符查询，多个字符查询，多个字符中任意一个查询；前向和后向查询；任意位置开始查询，所有的函树如下图：

![string2.png](./picture/string2.png)

### 比较
&ensp;&ensp;&ensp;&ensp;除常规比较< 、> 、 <= 、 >= 、 == 这些，提供了成员函数compare（）来扩展，它可以拥有子串的比较，还可以定制,具体如下：

```c++
函数的返回值是0，表示两字符相等；<0,表示当前字符串小于比较字符串；>0,表示当前字符串打印比较字符串

当前字符串与str进行比较
int string::compare(const string &str) const

把当前字符串从idx开始的最多len个字符与str进行比较，其中的str可为char*类型
int string::compare(size_type idx, size_type len, const string &str) const

把当前字符串从idx开始的最多len个字符与str从str_len开始的最多str_len个字符进行比较，其中的str可为char*类型
int string::compare(size_type idx, size_type len, const string &str, size_type str_idx, size_type str_len) const
```

### 子串
&ensp;&ensp;&ensp;&ensp;substr（）；

### 对迭代器的支持
- 大小写转换
- 无视大小写的比较、搜索
- 翻转
- 排序

### 大小及容量
```c++
查询现有字符的个数，下面两个函数是等效的
size_type string::size() const
size_type string::length() const
判断字符串是否为空
bool string::empty() const
查询字符串可含有的最大字符数，与计算机的物理内存相关吧
size_type string::max_size() const
查询重新分配前能拥有的最大字符串，和动态数组类似
size_type string::capacity() const
重新分配内存时可以调用此函数来加快速度
void string::reserve()
void string::reserve(size_type num)
```

### string 与 C-String的转换
&ensp;&ensp;&ensp;&ensp;存在string向C-String的隐式转换，但不存在C-String向string的隐式转换。

&ensp;&ensp;&ensp;&ensp;三个转换函数data、c_str、copy中，data和copy在转换后并不在其末尾添加结束符，c_str在其末尾添加结束符

&ensp;&ensp;&ensp;&ensp;使用指针引用转换后的c_str时有注意，如果在之后string进行了增删改以后，指针的有效性就没有，也就是说指针所指的内容也发生了变化

## 需要注意的细节点
### c++ string 与 C_string的转换，在初始化或赋值过程中的结尾问题
&ensp;&ensp;&ensp;&ensp;在string中没有结束符一说，但是其是存在的，因为结束符等价于string：：npos，为了在搜索的时候表示搜索到结束也没有。也就是说你查找‘\t’时是永远能查找到的
&ensp;&ensp;&ensp;&ensp;在初始化过程中，总是会在字符串的末尾添加结束符，但其不会算在string的大小中，
在单参数版本中才将字符串结束符当特殊符号处理，其他不视为特殊符号,使用【】取值并不会越界，而使用at会报错越界。是与npos的设计有关，往往在其最后添加npos，已便于查询。详细如下面的情况：

```c++
    char* p = "test";
    cout << (int)('a') << endl;
    string test(p);
    cout << test << endl;
    cout << test.size() << endl;//在单参数传入中将忽略结束符，结果为4
    //下面的两行用【】取值很奇怪，平时也会遇到这样的情况
    cout << test[test.size()] << endl;//但是使用【】取结束符却是可以的
    cout << test[test.size()+1] << endl;//在下一就越界报错了
    cout << test.at(test.size()) << endl;//at需要检查越界，因为越界了所以报错
    string s2("test", 5);
    cout << s2.size() << endl;//双参数并指定了结束符，所以结果为5
    cout << s2[4] << endl;//合法
    cout << s2[5] << endl;//这个也可以，是结束符合法
    cout << s2[6] << endl;//不合法，越界
```

### 它的内存机制是什么样的，是二倍动态扩展，还是其他的如链表之类的，还是reserve（）重新分配内存使用场景是什么样的，使用后的作用体现
&ensp;&ensp;&ensp;&ensp;经过查资料和试验，证明其动态分配的规律如下（在win10，VS2015下试验）：其初始大小为15，在前2次分配中每次增加16，之后每次增强其一般的值，试验的代码如下：

```c++
int main() {
    string s;
    cout << s.size() << ' ' << s.capacity() << endl;
    s += "1234567890123456";
    cout << s.size() << ' ' << s.capacity() << endl;
    s += "1234567890123456";
    cout << s.size() << ' ' << s.capacity() << endl;
    s += "1234567890123456";
    cout << s.size() << ' ' << s.capacity() << endl;
    s += "12345678901234567890123";
    cout << s.size() << ' ' << s.capacity() << endl;

    system("pause");
    return 0;
}

//输出如下：
0 15
16 31
32 47
48 70
71 105
```

### 元素存取【】与at的区别，在初始过程中最后索引元素的结束符，所以是有效的？
&ensp;&ensp;&ensp;&ensp;【】存取中不会检查越界，而at会检查；但【】的效率比at高。在const string 中【】是无效的，会出错，而不是const string是可以的，其指向的值的结束符

### size和length有区别没？
&ensp;&ensp;&ensp;&ensp;基本等效，只是size是STL版本的，length是C的


