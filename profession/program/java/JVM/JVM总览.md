# JVM 总览
***
## 总览
&ensp;&ensp;&ensp;&ensp;Java内存模型、Java字节码、GC相关知识、GC调优等模块的学习。其中Java内存模型、GC等相关知识是需要掌握的重点，在工作中排除问题、优化性能的时候会用到这部分知识。其次是字节码相关知识，对于编译相关的，有个大概的了解即可，感觉不必太深入，但其中的类加载机制及应用需要掌握，这个在工作中也会用到；其他的知识有个大概的了解即可。关于调试工具的时候和GC日志分析，自己需要多动手实践。

&ensp;&ensp;&ensp;&ensp;涉及内容大致如下：

![](https://github.com/lw1243925457/JAVA-000/blob/main/Week_02/picture/JVM.png)

### 一、Java背景知识
&ensp;&ensp;&ensp;&ensp;这部分设计Java技术体系、发展史，做一个了解即可。老师在课上也做了相关的介绍，这里推荐扩展阅读：《深入理解Java虚拟机：JVM高级特性与最佳实践》的第一部分。

### 二、Java字节码
&ensp;&ensp;&ensp;&ensp;这部分知识主要设计Java机器码的相关和Java类加载机制的应用。Java机器码的相关有个大概的了解即可，感觉在工作中不做底层这部分知识相对而言应用的比较少，如果用到了，回过头来深入，掌握。

&ensp;&ensp;&ensp;&ensp;大致的内容如下

- 四种指令类型：
- 基于栈的计算机器
- 方法调用的指用

### 三、类加载器
&ensp;&ensp;&ensp;&ensp;类加载中需要掌握的内容有：双亲委派机制和自定义类加载器。类加载机制这个在工作中应用还是比较多了，可能写的比较少，但明里暗里打交道却不少，下面是一个举例

&ensp;&ensp;&ensp;&ensp;在开发中我们接触到的主流Java Web服务器，如Tomcat、Jetty、WebLogic、WebSphere等服务器，都实现了自己的多个类加载器，相应的需求如下：

- 部署在同一个服务器上的两个Web应用程序所使用的Java类库可以实现相互隔离。这是最基本的需求，两个不同的应用程序可能会依赖同一个第三方类库的不同版本，不能要求每个类库在一个服务器中只能有一份，服务器应当能够保证两个独立应用程序的类库可以互相独立使用。
- 部署在同一个服务器上的两个Web应用程序所使用的Java类库可以互相共享。这个需求与前面一点正好相反，但是也很常见，例如用户可能有10个使用Spring组织的应用程序部署在同一台服务器上，如果把10份Spring分别存放在各个应用程序的隔离目录中，将会是很大的资源浪费——这主要倒不是浪费磁盘空间的问题，而是指类库在使用时都要被加载到服务器内存，如果类库不能共享，虚拟机的方法区就会很容易出现过度膨胀的风险。
- 服务器需要尽可能地保证自身的安全不受部署的Web应用程序影响。目前，有许多主流的Java Web服务器自身也是使用Java语言来实现的。因此服务器本身也有类库依赖的问题，一般来说，基于安全考虑，服务器所使用的类库应该与应用程序的类库互相独立。
- 支持JSP应用的Web服务器，十有八九都需要支持HotSwap功能。我们知道JSP文件最终要被编译成Java的Class文件才能被虚拟机执行，但JSP文件由于其纯文本存储的特性，被运行时修改的概率远大于第三方类库或程序自己的Class文件。而且ASP、PHP和JSP这些网页应用也把修改后无须重启作为一个很大的“优势”来看待，因此“主流”的Web服务器都会支持JSP生成类的热替换，当然也有“非主流”的，如运行在生产模式（Production Mode）下的WebLogic服务器默认就不会处理JSP文件的变化。

&ensp;&ensp;&ensp;&ensp;上面举例的这个可详细参考：《深入理解Java虚拟机：JVM高级特性与最佳实践》的第9章类加载及执行子系统的案例与实战。

&ensp;&ensp;&ensp;&ensp;课程中涉及到的大致内容如下：

- 类的7个生命周期（步骤）：加载、链接（效验、准备、解析）、初始化、使用、卸载
- 类的8个加载时机：main、new、遭到调用静态方法的类、遭到访问静态字段的类、子类触发父类、default、发射、MethodHandle
- 不会触发类初始化：6种
- 三类加载器：启动类加载器、扩展类加载器、应用类加载器
- 加载器特点：双亲委托、负责依赖、缓存加载
- 显示当前Classloader加载了哪些Jar
- 自定义Classloader
- 添加引用类的几种方

#### 自定义类加载器
&ensp;&ensp;&ensp;&ensp;这个是要重点掌握的，核心点就是一个函数：defineClass。这个函数读取class的字节流，生成对应的类。核心的使用方法就是，读取转化class为字节流，调用函数，生成。掌握了这个就对类的动态生成加载有了大致的了解。

```java
import java.io.*;
import java.lang.reflect.InvocationTargetException;

/**
 * 自定义类加载
 * 关键点是defineClass,读取字节码的字节流，生成class，思路就是转化class文件为字节流，传入defineClass中
 * 思路：
 * 1.继承ClassLoader，重写findClass方法
 * 2.从文件中读取转化成字节流
 * 3.传入defineClass进行加载
 * 4.生成实例，调用方法
 */
public class HelloClassLoader extends ClassLoader {
    @Override
    public Class findClass(String name) {
        byte[] b = new byte[0];
        try {
            b = loadClassFromFile(name);
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        }
        // name 就是加载的类名称，这里注意要填写正确
        return defineClass("Hello", b, 0, b.length);
    }

    /**
     * 读取class文件，转化内容为字节流
     * @param fileName 文件路径
     * @return
     * @throws FileNotFoundException
     */
    private byte[] loadClassFromFile(String fileName) throws FileNotFoundException {
        System.out.println(fileName);
        File file = new File(fileName);
        FileInputStream inputStream = new FileInputStream(file);
        byte[] buffer;
        ByteArrayOutputStream byteStream = new ByteArrayOutputStream();
        int nextValue = 0;
        try {
            while ( (nextValue = inputStream.read()) != -1 ) {
                // 注意字节还原
                byteStream.write(255 - nextValue);
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
        buffer = byteStream.toByteArray();
        return buffer;
    }

    public static void main(String[] args) throws ClassNotFoundException, NoSuchMethodException, IllegalAccessException, InstantiationException, InvocationTargetException {
        // 文件读取，字节流转换，加载
        String path = "C:\\Users\\12439\\Documents\\Code\\Java\\JAVA-000\\Week_01\\code\\src\\main\\resources\\Hello.xlass".replace("\\", "/");
        HelloClassLoader loader = new HelloClassLoader();
        Class hello = loader.loadClass(path);
        System.out.println(hello.getName());
        // 生成实例，调用方法
        Object instance = hello.newInstance();
        System.out.println(hello.getMethod("hello").invoke(instance));
    }
}
```

#### 双亲委派机制
&ensp;&ensp;&ensp;&ensp;这个也是要重点掌握的，了解各个类加载器的关系，特别是父类加载器持有的类是能被子类看见的这一特性。

![](https://github.com/lw1243925457/JAVA-000/raw/main/Week_02/picture/classLoad.png)

#### 类加载过程
![](https://github.com/lw1243925457/JAVA-000/raw/main/Week_02/picture/classlife.png)

&ensp;&ensp;&ensp;&ensp;了解如上图所示的生命周期，其中有两个点需要注意：

- 准备阶段：会触发静态字典的初始化，包含static变量和代码块，这个时候变量知识赋予系统初始值
- 初始化阶段：构造函数运行和变量初始化

&ensp;&ensp;&ensp;&ensp;一个实例如下：

```java
// 代码
public class InitExample {
    public static int var = 1;
    static {
         var = 2;
         System.out.println("static print");
    }

    InitExample() {
        System.out.println("init print");
    }

    public static void main(String[] args) {
        InitExample example = new InitExample();
        System.out.println(InitExample.var);
    }
}

// 输出结果
static print
init print
2
```

&ensp;&ensp;&ensp;&ensp;从中可以看到，静态先于构造函数，而静态代码块执行先于静态变量语句。

### 四、自动内存管理
&ensp;&ensp;&ensp;&ensp;这部分的内容可以说是重中之重了，有过C/C++开发工作的人应该知道内存管理的重要性和难度。虽然Java自己实现了内存管理，不用开发人员去操心，但其内存管理还是有不足之处，常常也会出内存泄漏和内存溢出等问题。当我们进行这些问题排查的时候，没有掌握相关的JVM内存管理知识，那就是盲目，没有方向，掌握这部分知识在解决问题的时候才能有所依据。

&ensp;&ensp;&ensp;&ensp;这部分内存主要涉及两块，一个是内存模型，内存模型是基础，影响了后面的内存管理，其中还涉及到了多线程竞争的问题，并发这块也是一个大问题，这里就不详述。第二个是垃圾回收的基础知识和各种GC算法，了解掌握GC算法有助于问题的分析和性能调优，这块也是相当重要，但感觉也不用深入到GC算法代码细节部分，了解其主要的算法思想即可，有需要在去深入。

&ensp;&ensp;&ensp;&ensp;GC工具的使用和日志分析就需要自己多动手实践了。

#### 内存模型
![](https://github.com/lw1243925457/JAVA-000/raw/main/Week_02/picture/savemodel.png)

&ensp;&ensp;&ensp;&ensp;上图是Java虚拟机运行时的数据库，需要掌握其五个部分：方法区、堆、虚拟机栈、本地方法栈、程序计数器

- 程序计数器：字节码的行号指示器；线程私有
- Java虚拟机栈：Java执行的线程内存模型，存放基本数据类型和对象引用；线程私有
- 本地方法栈：为Java方法服务，与Java虚拟机栈类似；线程私有
- 堆：存放对象实例；线程共享
- 方法区：加载的类型信息、常量、静态变量、即时编译其编译后的代码缓存等数据；线程共享
    - 运行时常量池：存放类的版本、字段、方法、接口等描述信息，还有常量池表（存放编译期生成的各种字面量与符号引用）

&ensp;&ensp;&ensp;&ensp;还有非运行时的数据区：直接内存。这部分也会被频繁使用

&ensp;&ensp;&ensp;&ensp;上面这些哪些会有OOM现象：Java虚拟机栈（变量不断存放）、本地方法栈（不断递归）、堆（对象不断存放）、方法区（类信息等不断存放）、直接内存

&ensp;&ensp;&ensp;&ensp;OOM示例这块可以参考：深入理解Java虚拟机：JVM高级特性与最佳实践》的2.4　实战：OutOfMemoryError异常

#### 垃圾回收基础知识
&ensp;&ensp;&ensp;&ensp;垃圾回收这块有些前置知识需要了解：回收的判断标准（可达性分析算法）、分代收集理论、垃圾收集的三个基本算法

##### 可达性分析算法
&ensp;&ensp;&ensp;&ensp;可达性分析算法：主要是通过枚举GC Roots作为根节点出发，能够遍历到的就是任然可以存活的对象

![](https://github.com/lw1243925457/JAVA-000/raw/main/Week_02/picture/canAccess.png)

&ensp;&ensp;&ensp;&ensp;在Java技术体系里面，固定可作为GC Roots的对象包括以下几种：

- 在虚拟机栈（栈帧中的本地变量表）中引用的对象，譬如各个线程被调用的方法堆栈中使用到的参数、局部变量、临时变量等。
- 在方法区中类静态属性引用的对象，譬如Java类的引用类型静态变量。
- 在方法区中常量引用的对象，譬如字符串常量池（String Table）里的引用。
- 在本地方法栈中JNI（即通常所说的Native方法）引用的对象。
- Java虚拟机内部的引用，如基本数据类型对应的Class对象，一些常驻的异常对象（比如NullPointExcepiton、OutOfMemoryError）等，还有系统类加载器。
- 所有被同步锁（synchronized关键字）持有的对象。
- 反映Java虚拟机内部情况的JMXBean、JVMTI中注册的回调、本地代码缓存等。

##### 分代收集理论
&ensp;&ensp;&ensp;&ensp;重要的分代假设里面，在ZGC之前，GC算法都有围绕这个假设进行算法设计

- 1）弱分代假说（Weak Generational Hypothesis）：绝大多数对象都是朝生夕灭的。
- 2）强分代假说（Strong Generational Hypothesis）：熬过越多次垃圾收集过程的对象就越难以消亡。
- 3）跨代引用假说（Intergenerational Reference Hypothesis）：跨代引用相对于同代引用来说仅占极少数。

##### 垃圾收集算法
&ensp;&ensp;&ensp;&ensp;这个是相当重要的，目前为止的GC算法设计中都起码有其中一个算法的思想

###### 标记-清除算法
![](https://github.com/lw1243925457/JAVA-000/raw/main/Week_02/picture/typeclean.png)

&ensp;&ensp;&ensp;&ensp;如它的名字一样，分为标记和清除两个阶段：首先标记出需要清除的对象，标记完成后进行清除

- 优点：基础简单
- 缺点：
    - 第一个是执行效率不稳定，如果Java堆中包含大量对象，而且其中大部分是需要被回收的，这时必须进行大量标记和清除的动作，导致标记和清除两个过程的执行效率都随对象数量增长而降低
    - 第二个是内存空间的碎片化问题，标记、清除之后会产生大量不连续的内存碎片，空间碎片太多可能会导致当以后在程序运行过程中需要分配较大对象时无法找到足够的连续内存而不得不提前触发另一次垃圾收集动作

###### 标记-复制算法
![](https://github.com/lw1243925457/JAVA-000/raw/main/Week_02/picture/flagcopy.png)

&ensp;&ensp;&ensp;&ensp;它将可用内存按容量划分为大小相等的两块，每次只使用其中的一块。当这一块的内存用完了，就将还存活着的对象复制到另外一块上面，然后再把已使用过的内存空间一次清理掉。如果内存中多数对象都是存活的，这种算法将会产生大量的内存间复制的开销，但对于多数对象都是可回收的情况，算法需要复制的就是占少数的存活对象，而且每次都是针对整个半区进行内存回收，分配内存时也就不用考虑有空间碎片的复杂情况，只要移动堆顶指针，按顺序分配即可。

&ensp;&ensp;&ensp;&ensp;G1及前面的算法的年轻代都是使用这个思想来进行垃圾的回收。因为存活对象少，复制所消耗的时间少

- 优点：实现简单，运行高效，没有空间碎片
- 缺点：将可用内存缩小为了原来的一半，空间浪费未免太多了一点

###### 标记-整理算法
![](https://github.com/lw1243925457/JAVA-000/raw/main/Week_02/picture/flagmake.png)

&ensp;&ensp;&ensp;&ensp;标记-复制算法在对象存活率较高时就要进行较多的复制操作，效率将会降低。更关键的是，如果不想浪费50%的空间，就需要有额外的空间进行分配担保，以应对被使用的内存中所有对象都100%存活的极端情况，所以在老年代一般不能直接选用这种算法。

&ensp;&ensp;&ensp;&ensp;其中的标记过程仍然与“标记-清除”算法一样，但后续步骤不是直接对可回收对象进行清理，而是让所有存活的对象都向内存空间一端移动，然后直接清理掉边界以外的内存，“标记-整理”算法的示意图如上图所示。

&ensp;&ensp;&ensp;&ensp;整理还有灵活调整空间，可以及时整理，也可以当碎片化到一定程度再进行整理

- 优点：没有空间碎片，空间利用率高
- 缺点：移动存活对象并更新所有引用这些对象的地方将会是一种极为负重的操作，而且这种对象移动操作必须全程暂停用户应用程序才能进行

#### Serial收集器
![](https://github.com/lw1243925457/JAVA-000/raw/main/Week_02/picture/serial.png)

&ensp;&ensp;&ensp;&ensp;如上图所示，Serial收集其是一个单线程的，在其进行垃圾回收时，必须停止业务代码，让其专心进行回收，书中有个形象的比喻：“你妈妈在给你打扫房间的时候，肯定也会让你老老实实地在椅子上或者房间外待着，如果她一边打扫，你一边乱扔纸屑，这房间还能打扫完？”

&ensp;&ensp;&ensp;&ensp;其新生代使用标记-复制算法，老年代使用标记-整理算法

#### ParNew收集器
![](https://github.com/lw1243925457/JAVA-000/raw/main/Week_02/picture/parnew.png)

&ensp;&ensp;&ensp;&ensp;如上图所示，ParNew是基于Serial的并行版本，其他的基本没有任何改变，其思想就是利用多线程加快垃圾回收速度

&ensp;&ensp;&ensp;&ensp;其新生代使用标记-复制算法，老年代使用标记-整理算法

#### Parallel Scavenge收集器
&ensp;&ensp;&ensp;&ensp;Parallel Scavenge收集器的特点是它的关注点与其他收集器不同，CMS等收集器的关注点是尽可能地缩短垃圾收集时用户线程的停顿时间，而Parallel Scavenge收集器的目标则是达到一个可控制的吞吐量（Throughput）。所谓吞吐量就是处理器用于运行用户代码的时间与处理器总消耗时间的比值

&ensp;&ensp;&ensp;&ensp;这个收集器设计目标是达到设定吞吐量，还能智能调节

#### Serial Old收集器
![](https://github.com/lw1243925457/JAVA-000/raw/main/Week_02/picture/SerialOld.png)

&ensp;&ensp;&ensp;&ensp;Serial Old是Serial收集器的老年代版本，它同样是一个单线程收集器，使用标记-整理算法。这个收集器的主要意义也是供客户端模式下的HotSpot虚拟机使用。如果在服务端模式下，它也可能有两种用途：一种是在JDK 5以及之前的版本中与Parallel Scavenge收集器搭配使用，另外一种就是作为CMS收集器发生失败时的后备预案，在并发收集发生Concurrent Mode Failure时使用。

#### Parallel Old收集器
![](https://github.com/lw1243925457/JAVA-000/raw/main/Week_02/picture/ParallelOld.png)

&ensp;&ensp;&ensp;&ensp;直到Parallel Old收集器出现后，“吞吐量优先”收集器终于有了比较名副其实的搭配组合，在注重吞吐量或者处理器资源较为稀缺的场合，都可以优先考虑Parallel Scavenge加Parallel Old收集器这个组合。

#### CMS收集器
![](https://github.com/lw1243925457/JAVA-000/raw/main/Week_02/picture/CMS.png)

&ensp;&ensp;&ensp;&ensp;CMS是一个突破的节点，其突破点在于将垃圾回收的节点进行细分，达到了缩短STW时间，部分回收过程可以和业务代码一起运行。

&ensp;&ensp;&ensp;&ensp;CMS（Concurrent Mark Sweep）收集器是一种以获取最短回收停顿时间为目标的收集器。目前很大一部分的Java应用集中在互联网网站或者基于浏览器的B/S系统的服务端上，这类应用通常都会较为关注服务的响应速度，希望系统停顿时间尽可能短，以给用户带来良好的交互体验。CMS收集器就非常符合这类应用的需求。

&ensp;&ensp;&ensp;&ensp;CMS是基于标记-清楚算法的，如上图所示，一共分为四个步骤：

- 1）初始标记：需要STW；标记GC Roots能直接关联到的对象，速度很快
- 2）并发标记：不需要STW；标记GC Roots的直接关联对象开始遍历标记（并发）
- 3）重新标记：需要STW；修正阶段2并发标记中因业务运行导致变动的部分
- 4）并发清除：不需要STW；清理删除死亡对象，有内存碎片

&ensp;&ensp;&ensp;&ensp;CMS有如下优缺点：

- 优点：并发收集、低停顿，适合如今互联网服务器的快响应需求
- 缺点：
    - 对资源敏感，核心4或以上还行，一下影响很大，强占资源
    - 由于浮点垃圾，需预留一部分空间给并发收集时的程序使用，过低过高都不行
    - 有空间碎片

#### G1 GC

![](https://github.com/lw1243925457/JAVA-000/raw/main/Week_02/picture/G1.png)

&ensp;&ensp;&ensp;&ensp;G1 GC说是里程牌式的成果，其突破点在于对于内存布局的重新设计（Region），可控的最大停顿时间。可以说这些设计和改进，从CMS开始都是针对追求高响应的服务端的。

&ensp;&ensp;&ensp;&ensp;G1大致也分为下面四个阶段：

- 1）初始标记（Initial Marking）：仅仅只是标记一下GC Roots能直接关联到的对象，并且修改TAMS指针的值，让下一阶段用户线程并发运行时，能正确地在可用的Region中分配新对象。这个阶段需要停顿线程，但耗时很短，而且是借用进行Minor GC的时候同步完成的，所以G1收集器在这个阶段实际并没有额外的停顿。
- 2）并发标记（Concurrent Marking）：从GC Root开始对堆中对象进行可达性分析，递归扫描整个堆里的对象图，找出要回收的对象，这阶段耗时较长，但可与用户程序并发执行。当对象图扫描完成以后，还要重新处理SATB记录下的在并发时有引用变动的对象。
- 3）最终标记（Final Marking）：对用户线程做另一个短暂的暂停，用于处理并发阶段结束后仍遗留下来的最后那少量的SATB记录。
- 4）筛选回收（Live Data Counting and Evacuation）：负责更新Region的统计数据，对各个Region的回收价值和成本进行排序，根据用户所期望的停顿时间来制定回收计划，可以自由选择任意多个Region构成回收集，然后把决定回收的那一部分Region的存活对象复制到空的Region中，再清理掉整个旧Region的全部空间。这里的操作涉及存活对象的移动，是必须暂停用户线程，由多条收集器线程并行完成的。

&ensp;&ensp;&ensp;&ensp;关于G1算法是否有STW停顿，这有一个小偷团伙作案的例子：

- 1）大家一起在地图标记：首先在地图上标记出比较富有的人家，准备踩点，在地图上标记，地图时静止的，对应需要STW
- 2）分工进行踩点：需要到具体地方查看，人来人往的，这个时候不是静止的，不需要STW
- 3）碰头在地图上确认：大家碰头确认哪些人家比较富有和好偷，在地图上标记出来，对应需要STW
- 4）分头行动开工：这个时候肯定是月黑风高，要么没人在家，要么酣然入睡，对应需要STW

&ensp;&ensp;&ensp;&ensp;G1的优缺点：

- 优点：可控的、低延迟的、基本无碎片，6G-8G较为合适
- 缺点：跨代之间的回收产生的卡表，占较多内存，处理较为复杂，6G以下可能CMS好

#### Shenandoah
![](https://github.com/lw1243925457/JAVA-000/raw/main/Week_02/picture/shenandoah.png)

&ensp;&ensp;&ensp;&ensp;shennadoah感觉是在G1的基础上最进一步的改进，大致的步骤如下，这里就不详细分析了：

- 初始标记（Initial Marking）：与G1一样，首先标记与GC Roots直接关联的对象，这个阶段仍是“Stop The World”的，但停顿时间与堆大小无关，只与GC Roots的数量相关。
- 并发标记（Concurrent Marking）：与G1一样，遍历对象图，标记出全部可达的对象，这个阶段是与用户线程一起并发的，时间长短取决于堆中存活对象的数量以及对象图的结构复杂程度。
- 最终标记（Final Marking）：与G1一样，处理剩余的SATB扫描，并在这个阶段统计出回收价值最高的Region，将这些Region构成一组回收集（Collection Set）。最终标记阶段也会有一小段短暂的停顿。
- 并发清理（Concurrent Cleanup）：这个阶段用于清理那些整个区域内连一个存活对象都没有找到的Region（这类Region被称为Immediate Garbage Region）。
- 并发回收（Concurrent Evacuation）：并发回收阶段是Shenandoah与之前HotSpot中其他收集器的核心差异。在这个阶段，Shenandoah要把回收集里面的存活对象先复制一份到其他未被使用的Region之中。复制对象这件事情如果将用户线程冻结起来再做那是相当简单的，但如果两者必须要同时并发进行的话，就变得复杂起来了。其困难点是在移动对象的同时，用户线程仍然可能不停对被移动的对象进行读写访问，移动对象是一次性的行为，但移动之后整个内存中所有指向该对象的引用都还是旧对象的地址，这是很难一瞬间全部改变过来的。对于并发回收阶段遇到的这些困难，Shenandoah将会通过读屏障和被称为“Brooks Pointers”的转发指针来解决（讲解完Shenandoah整个工作过程之后笔者还要再回头介绍它）。并发回收阶段运行的时间长短取决于回收集的大小。
- 初始引用更新（Initial Update Reference）：并发回收阶段复制对象结束后，还需要把堆中所有指向旧对象的引用修正到复制后的新地址，这个操作称为引用更新。引用更新的初始化阶段实际上并未做什么具体的处理，设立这个阶段只是为了建立一个线程集合点，确保所有并发回收阶段中进行的收集器线程都已完成分配给它们的对象移动任务而已。初始引用更新时间很短，会产生一个非常短暂的停顿。
- 并发引用更新（Concurrent Update Reference）：真正开始进行引用更新操作，这个阶段是与用户线程一起并发的，时间长短取决于内存中涉及的引用数量的多少。并发引用更新与并发标记不同，它不再需要沿着对象图来搜索，只需要按照内存物理地址的顺序，线性地搜索出引用类型，把旧值改为新值即可。
- 最终引用更新（Final Update Reference）：解决了堆中的引用更新后，还要修正存在于GC Roots中的引用。这个阶段是Shenandoah的最后一次停顿，停顿时间只与GC Roots的数量相关。
- 并发清理（Concurrent Cleanup）：经过并发回收和引用更新之后，整个回收集中所有的Region已再无存活对象，这些Region都变成Immediate Garbage Regions了，最后再调用一次并发清理过程来回收这些Region的内存空间，供以后新对象分配使用。

#### ZGC
![](https://github.com/lw1243925457/JAVA-000/raw/main/Week_02/picture/zgc.png)

&ensp;&ensp;&ensp;&ensp;ZGC收集器是一款基于Region内存布局的，（暂时）不设分代的，使用了读屏障、染色指针和内存多重映射等技术来实现可并发的标记-整理算法的，以低延迟为首要目标的一款垃圾收集器。

&ensp;&ensp;&ensp;&ensp;首先从ZGC的内存布局说起。与Shenandoah和G1一样，ZGC也采用基于Region的堆内存布局，但与它们不同的是，ZGC的Region（在一些官方资料中将它称为Page或者ZPage，本章为行文一致继续称为Region）具有动态性——动态创建和销毁，以及动态的区域容量大小。在x64硬件平台下，ZGC的Region可以具有如图3-19所示的大、中、小三类容量：

- 小型Region（Small Region）：容量固定为2MB，用于放置小于256KB的小对象。
- 中型Region（Medium Region）：容量固定为32MB，用于放置大于等于256KB但小于4MB的对象。
- 大型Region（Large Region）：容量不固定，可以动态变化，但必须为2MB的整数倍，用于放置4MB或以上的大对象。每个大型Region中只会存放一个大对象，这也预示着虽然名字叫作“大型Region”，但它的实际容量完全有可能小于中型Region，最小容量可低至4MB。大型Region在ZGC的实现中是不会被重分配（重分配是ZGC的一种处理动作，用于复制对象的收集器阶段，稍后会介绍到）的，因为复制一个大对象的代价非常高昂。

&ensp;&ensp;&ensp;&ensp;接下来是ZGC的核心问题——并发整理算法的实现。染色指针技术

- 染色指针可以使得一旦某个Region的存活对象被移走之后，这个Region立即就能够被释放和重用掉，而不必等待整个堆中所有指向该Region的引用都被修正后才能清理。这点相比起Shenandoah是一个颇大的优势，使得理论上只要还有一个空闲Region，ZGC就能完成收集，而Shenandoah需要等到引用更新阶段结束以后才能释放回收集中的Region，这意味着堆中几乎所有对象都存活的极端情况，需要1∶1复制对象到新Region的话，就必须要有一半的空闲Region来完成收集。至于为什么染色指针能够导致这样的结果，笔者将在后续解释其“自愈”特性的时候进行解释。
- 染色指针可以大幅减少在垃圾收集过程中内存屏障的使用数量，设置内存屏障，尤其是写屏障的目的通常是为了记录对象引用的变动情况，如果将这些信息直接维护在指针中，显然就可以省去一些专门的记录操作。实际上，到目前为止ZGC都并未使用任何写屏障，只使用了读屏障（一部分是染色指针的功劳，一部分是ZGC现在还不支持分代收集，天然就没有跨代引用的问题）。内存屏障对程序运行时性能的损耗在前面章节中已经讲解过，能够省去一部分的内存屏障，显然对程序运行效率是大有裨益的，所以ZGC对吞吐量的影响也相对较低。
- 染色指针可以作为一种可扩展的存储结构用来记录更多与对象标记、重定位过程相关的数据，以便日后进一步提高性能。现在Linux下的64位指针还有前18位并未使用，它们虽然不能用来寻址，却可以通过其他手段用于信息记录。如果开发了这18位，既可以腾出已用的4个标志位，将ZGC可支持的最大堆内存从4TB拓展到64TB，也可以利用其余位置再存储更多的标志，譬如存储一些追踪信息来让垃圾收集器在移动对象时能将低频次使用的对象移动到不常访问的内存区域。

&ensp;&ensp;&ensp;&ensp;其大致步骤如下：

- 并发标记（Concurrent Mark）：与G1、Shenandoah一样，并发标记是遍历对象图做可达性分析的阶段，前后也要经过类似于G1、Shenandoah的初始标记、最终标记（尽管ZGC中的名字不叫这些）的短暂停顿，而且这些停顿阶段所做的事情在目标上也是相类似的。与G1、Shenandoah不同的是，ZGC的标记是在指针上而不是在对象上进行的，标记阶段会更新染色指针中的Marked 0、Marked 1标志位。
- 并发预备重分配（Concurrent Prepare for Relocate）：这个阶段需要根据特定的查询条件统计得出本次收集过程要清理哪些Region，将这些Region组成重分配集（Relocation Set）。重分配集与G1收集器的回收集（Collection Set）还是有区别的，ZGC划分Region的目的并非为了像G1那样做收益优先的增量回收。相反，ZGC每次回收都会扫描所有的Region，用范围更大的扫描成本换取省去G1中记忆集的维护成本。因此，ZGC的重分配集只是决定了里面的存活对象会被重新复制到其他的Region中，里面的Region会被释放，而并不能说回收行为就只是针对这个集合里面的Region进行，因为标记过程是针对全堆的。此外，在JDK 12的ZGC中开始支持的类卸载以及弱引用的处理，也是在这个阶段中完成的。
- 并发重分配（Concurrent Relocate）：重分配是ZGC执行过程中的核心阶段，这个过程要把重分配集中的存活对象复制到新的Region上，并为重分配集中的每个Region维护一个转发表（Forward Table），记录从旧对象到新对象的转向关系。得益于染色指针的支持，ZGC收集器能仅从引用上就明确得知一个对象是否处于重分配集之中，如果用户线程此时并发访问了位于重分配集中的对象，这次访问将会被预置的内存屏障所截获，然后立即根据Region上的转发表记录将访问转发到新复制的对象上，并同时修正更新该引用的值，使其直接指向新对象，ZGC将这种行为称为指针的“自愈”（Self-Healing）能力。这样做的好处是只有第一次访问旧对象会陷入转发，也就是只慢一次，对比Shenandoah的Brooks转发指针，那是每次对象访问都必须付出的固定开销，简单地说就是每次都慢，因此ZGC对用户程序的运行时负载要比Shenandoah来得更低一些。还有另外一个直接的好处是由于染色指针的存在，一旦重分配集中某个Region的存活对象都复制完毕后，这个Region就可以立即释放用于新对象的分配（但是转发表还得留着不能释放掉），哪怕堆中还有很多指向这个对象的未更新指针也没有关系，这些旧指针一旦被使用，它们都是可以自愈的。
- 并发重映射（Concurrent Remap）：重映射所做的就是修正整个堆中指向重分配集中旧对象的所有引用，这一点从目标角度看是与Shenandoah并发引用更新阶段一样的，但是ZGC的并发重映射并不是一个必须要“迫切”去完成的任务，因为前面说过，即使是旧引用，它也是可以自愈的，最多只是第一次使用时多一次转发和修正操作。重映射清理这些旧引用的主要目的是为了不变慢（还有清理结束后可以释放转发表这样的附带收益），所以说这并不是很“迫切”。因此，ZGC很巧妙地把并发重映射阶段要做的工作，合并到了下一次垃圾收集循环中的并发标记阶段里去完成，反正它们都是要遍历所有对象的，这样合并就节省了一次遍历对象图的开销。一旦所有指针都被修正之后，原来记录新旧对象关系的转发表就可以释放掉了。

### GC算法的演进
&ensp;&ensp;&ensp;&ensp;在GC算法的发展的，个人感觉始终围绕着一个点：减少单次的STW时间。

&ensp;&ensp;&ensp;&ensp;如使用多线程加速回收，以减少串行GC的整个一次回收的STW时间；将回收过程细化，分阶段，更长的一次STW被分成了小部分，分散在各个回收处理阶段，也达到了减少单次STW时间。
 
&ensp;&ensp;&ensp;&ensp;而且GC算法的演进方向是为了满足服务端的快速响应。

&ensp;&ensp;&ensp;&ensp;算法演进图大致如下：

![](https://github.com/lw1243925457/JAVA-000/raw/main/Week_02/picture/gcroute.png)

### GC调优思路
&ensp;&ensp;&ensp;&ensp;此部分仅个人想法，感觉还是实践有点少，经验不足，有待完善

&ensp;&ensp;&ensp;&ensp;感觉调优思路可以分为一条线，三个点

&ensp;&ensp;&ensp;&ensp;一条线：减少STW时间。因为代码运行总时间=GC STW时间 + 业务运行时间，减少STW时间，业务时间相对多了，系统就能处理更多的请求

&ensp;&ensp;&ensp;&ensp;三个点：内存占用、吞吐量、延迟，这三个构成了不可能三角（有点类似CAP理论），开发者需要在其中进行权衡，看当前系统需要的是什么，针对性的选择GC或者进行调优。当然，如今大部分开发人员都是WEB服务端了，基本上延迟是必须的了，内存这块好像也不缺，所以可以说后面基本是G1类GC的天下了吧。但肯定也有其他应用方面，这个时候还是需要权衡的。

#### JVM调优经验
##### 1、高分配速率(High Allocation Rate) 

分配速率(Allocation rate)表示单位时间内分配的内存量。通常 使用MB/sec作为单位。上一次垃圾收集之后，与下一次GC开 始之前的年轻代使用量，两者的差值除以时间,就是分配速率。
分配速率过高就会严重影响程序的性能，在JVM中可能会导致巨 大的GC开销。

- 正常系统：分配速率较低~ 回收速率-> 健康 
- 内存泄漏：分配速率持续大于回收速率-> OOM 
- 性能劣化：分配速率较高~ 回收速率-> 压健康

##### 2、过早提升(Premature Promotion) 
提升速率（promotion rate）用于衡量单位时间内从年轻代提 升到老年代的数据量。一般使用MB/sec 作为单位, 和分配速率 类似。
JVM会将长时间存活的对象从年轻代提升到老年代。根据分代假 设，可能存在一种情况，老年代中不仅有存活时间长的对象,， 也可能有存活时间短的对象。这就是过早提升：对象存活时间还 不够长的时候就被提升到了老年代。
major GC 不是为频繁回收而设计的，但major GC 现在也要清 理这些生命短暂的对象，就会导致GC暂停时间过长。这会严重 影响系统的吞吐量。

一般来说过早提升的症状表现为以下形式： 

- 1. 短时间内频繁地执行full GC 
- 2. 每次full GC 后老年代的使用率都很低，在1020%或以下 
- 3. 提升速率接近于分配速率 要演示这种情况稍微有点麻烦，所以我们使用特殊 手段，让对象提升到老年代的年龄比默认情况小很 多。指定GC参数-Xmx24m -XX:NewSize=16m -XX:MaxTenuringThreshold=1，运行程序之后， 可以看到下面的GC日志。

解决这类问题，需要让年轻代存放得下暂存的数据，有两种简单 的方法： 
- 一是增加年轻代的大小，设置JVM启动参数，类似这样：Xmx64m -XX:NewSize=32m，程序在执行时，Full GC 的次数 自然会减少很多，只会对minor GC的持续时间产生影响。 
- 二是减少每次批处理的数量，也能得到类似的结果。

至于选用哪个方案，要根据业务需求决定。在某些情况下，业务 逻辑不允许减少批处理的数量，那就只能增加堆内存，或者重新 指定年轻代的大小。如果都不可行，就只能优化数据结构，减少 内存消耗。 但总体目标依然是一致的：让临时数据能够在年轻代存放得下。

#### GC疑难情况问题分析
- 1、查询业务日志，可以发现这类问题：请求压力大，波峰，遭遇降级，熔断等等，基础服务、外部API 依赖。 
- 2、查看系统资源和监控信息： 硬件信息、操作系统平台、系统架构； 排查CPU 负载、内存不足，磁盘使用量、硬件故障、磁盘分区用满、IO 等待、IO 密集、丢数据、并发竞 争等情况； 排查网络：流量打满，响应超时，无响应，DNS 问题，网络抖动，防火墙问题，物理故障，网络参数调整、 超时、连接数。 
- 3、查看性能指标，包括实时监控、历史数据。可以发现假死，卡顿、响应变慢等现象； 排查数据库，并发连接数、慢查询、索引、磁盘空间使用量、内存使用量、网络带宽、死锁、TPS、查询 数据量、redo日志、undo、binlog 日志、代理、工具BUG。可以考虑的优化包括：集群、主备、只读 实例、分片、分区； 大数据，中间件，JVM 参数。 
- 4、排查系统日志，比如重启、崩溃、Kill 。 
- 5、APM，比如发现有些链路请求变慢等等。
- 6、排查应用系统 排查配置文件: 启动参数配置、Spring 配置、JVM 监控参数、数据库参数、Log 参数、APM 配置、 内存问题，比如是否存在内存泄漏，内存溢出、批处理导致的内存放大、GC 问题等等； GC 问题，确定GC 算法、确定GC 的KPI，GC 总耗时、GC 最大暂停时间、分析GC 日志和监控指标：内存 分配速度，分代提升速度，内存使用率等数据。适当时修改内存配置； 排查线程, 理解线程状态、并发线程数，线程Dump，锁资源、锁等待，死锁； 排查代码，比如安全漏洞、低效代码、算法优化、存储优化、架构调整、重构、解决业务代码BUG、第三方 库、XSS、CORS、正则； 单元测试：覆盖率、边界值、Mock 测试、集成测试。 
- 7、排除资源竞争、坏邻居效应 
- 8、疑难问题排查分析手段 DUMP 线程\内存； 抽样分析\调整代码、异步化、削峰填谷。


## 参考链接
- [High performance at low cost – choose the best JVM and the best Garbage Collector for your needs](https://blog.oio.de/2020/01/13/high-performance-at-low-cost-choose-the-best-jvm-and-the-best-garbage-collector-for-your-needs/)
- [Using the OkHttp library for HTTP requests - Tutorial - Tutorial](https://www.vogella.com/tutorials/JavaLibrary-OkHttp/article.html)
- [com.squareup.okhttp3:okhttp:4.9.0](https://search.maven.org/artifact/com.squareup.okhttp3/okhttp/4.9.0/jar)
- [OkHttp: NoSuchMethodError copyInto in TlsUtil](https://stackoverflow.com/questions/57330923/okhttp-nosuchmethoderror-copyinto-in-tlsutil)
- [aliostad/SuperBenchmarker](https://github.com/aliostad/SuperBenchmarker)