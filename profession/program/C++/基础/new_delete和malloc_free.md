# new/delete 和 malloc/free的区别
***

&ensp;&ensp;&ensp;&ensp;前者自动计算需要分配的内存大小；而后者需要自己计算所需的内存大小.

## 疑问
### struck和class的大小是怎么计算的？
&ensp;&ensp;&ensp;&ensp;使用最大对齐原则，所以的变量长度都与变量中最长的相等；在类中方法并没有大小；在使用基础时，父类和子类不影响