# java核心库学习记录
***
主要是大概记录一下Java核心库有那些，怎么用，自己大概有一个印象，话说自己好像大部分都是不太了解
***

# Sting类

```java
String s = "Hello ";
s.concat("World!");   //字符串连接
System.out.println(s); //输出s，还是"Hello "
s = s.concat("World!");  //把字符串对象连接的结果赋给了s引用
System.out.println(s);  //输出s，变成了"Hello World!"

/*
上述三条语句其实产生了3个String对象，"Hello "，"World!"，"Hello World!"。第2条语句确实产生了"Hello World"字符串，但是没有指定把该字符串的引用赋给谁，因此没有改变s引用。第3条语句根据不变性，并没有改变"Hello "，JVM创建了一个新的对象，把"Hello "，"World!"的连接赋给了s引用，因此引用变了，但是原对象没变。
*/
```

&ensp;&ensp;&ensp;&ensp;String 的不变性的机制显然会在 String 常量内有大量的冗余。比如我创建一个循环，使字符'1'依次连接到'n'，那么系统就得创建 n+(n+1) 个 String 对象。那有没有可变的 String 类呢？
&ensp;&ensp;&ensp;&ensp;StringBuffer 类是可变的。它是 String 的对等类，它可以增加和编写字符的可变序列，并且能够将字符插入到字符串中间或附加到字符串末尾（当然是不用创建其他对象的，这里建议大家去看一看 String 类与 StringBuffer 类的区别，理解一下他们在内存中的存储情况）

### java.lang 类 Class
&ensp;&ensp;&ensp;&ensp;class类的实例表示 Java 应用运行时的类或接口。在 Java 中，每个 class 都有一个相应的 Class 对象，即每一个类，在生成的.class文件中，就会产生一个 Class 对象，用于表示这个类的类型信息。我们获取 Class 实例有三种方法：
- 利用对象调用 getClass()方法获取该对象的 Class 实例
- 使用 Class 类的静态方法 forName(String className)，用类的名字获取一个 Class 实例
- 运用.class的方式来获取 Class 实例，对于基本数据类型的封装类，还可以采用.TYPE来获取相对应的基本数据类型的 Class 实例

## java.util 包
### Date
&ensp;&ensp;&ensp;&ensp;我们来看看类 Date 中定义的六种构造方法：
- Date():构造一个 Date 对象并对其进行初始化以反映当前时间
- Date(long date):构造一个 Date 对象，并根据相对于 GMT 1970年1月1日00:00:00的毫秒数对其进行初始化
- Date(String s):按字符串 s 产生日期对象
- Date(int year, int month, int date):year - 年份减去1900；month - 月份在0-11之间；date - 日期在1-31之间
- Date(int year, int month, int date, int hrs, int min, int sec):hrs - 小时在0-23之间；min - 分钟在0-59之间；sec - 秒在0-59之间

```java
import java.util.*;
public class DateDemo {
    public static void main(String[] args) throws ClassNotFoundException {
        String strDate, strTime = "";
        Date objDate = new Date();
        System.out.println("今天的日期是：" + objDate);
        long time = objDate.getTime();
        System.out.println("自1970年1月1日起以毫秒为单位的时间（GMT）：" + time);
        strDate = objDate.toString();
        //提取 GMT 时间
        strTime = strDate.substring(11,(strDate.length() - 4));
        //按小时、分钟和秒提取时间
        strTime = "时间：" + strTime.substring(0,8);
        System.out.println(strTime);
    }
}
```
&ensp;&ensp;&ensp;&ensp;在 Date 对象中的年、月、日、星期、时、分、秒可以用 getter 和 setter 方法进行操作。如下是方法原型：

```java
//getYear
public int getYear()
//getMonth
public int getMonth()
//getDate
public int getDate()
//getDay
public int getDay()
//getHours
public int getHours()
//getMinutes
public int getMinutes()
//getSeconds
public int getSeconds()
//setYear
public void setYear(int year)
//setMonth
public void setMonth(int month)
//setDate
public void setDate(int date)
//setHours
public void setHours(int hours)
//setMinutes
public void setMinutes(int minutes)
//setSeconds
public void setSeconds(int seconds)
```

&ensp;&ensp;&ensp;&ensp;其实，在我们有了 Calendar 后，就几乎不用 Date 类了

## Calendar 类
&ensp;&ensp;&ensp;&ensp;Calendar 类是一个抽象类，它完成 Date 类与普通日期表示法之间的转换，而我们更多的是使用 Calendar 类的子类 GregorianCalendar 类。它实现了世界上普遍使用的公历系统。当然我们也可以继承 Calendar 类，然后自己定义实现日历方法。
&ensp;&ensp;&ensp;&ensp;先来看一看 GregorianCalendar 类的构造函数:
- GregorianCalendar():创建的对象中的相关值被设置成指定时区，缺省地点的当前时间，即程序运行时所处的时区、地点的当前时间
- GregorianCalendar(TimeZone zone):创建的对象中的相关值被设置成指定时区 zone，缺省地点的当前时间
- GregorianCalendar(Locale aLocale):创建的对象中的相关值被设置成缺省时区，指定地点 aLocale 的当前时间
- GregorianCalendar(TimeZone zone,Locale aLocale):year - 创建的对象中的相关值被设置成指定时区，指定地点的当前时间

&ensp;&ensp;&ensp;&ensp;同学们在引入包时，可能发现 java.util 里面并没有 Calendar 类，windows 和 linux 系统的用户，可以在 Eclipse 菜单栏里找到window，选择preference，找到java，点击Installed JRES,将执行环境改为javaSE-1.8就可以了（Mac的在Eclipse的偏好里设置下，其他的都一样）

```java
import java.util.*;
import java.text.*;

public class CalendarDemo  
{    
  public static void main(String[] args)  
  {  

    // 得到日期格式对象  
    // Date date = fmt.parse(strDateMake);  

    System.out.println("完整显示日期时间：");
    // 字符串转换日期格式  
    DateFormat fdate = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
    String str =fdate.format(new Date());
    System.out.println(str);  

    // 创建 Calendar 对象  
    Calendar calendar = Calendar.getInstance();  
    // 初始化 Calendar 对象，但并不必要，除非需要重置时间  
    calendar.setTime(new Date());  

    // 显示年份
    System.out.println("年： " + calendar.get(Calendar.YEAR));  

    // 显示月份 (从0开始, 实际显示要加一)    
    System.out.println("月： " + calendar.get(Calendar.MONTH));


    // 当前分钟数   
    System.out.println("分钟： " + calendar.get(Calendar.MINUTE));

    // 今年的第 N 天    
    System.out.println("今年的第 " + calendar.get(Calendar.DAY_OF_YEAR) + "天");  

    // 本月第 N 天  
    System.out.println("本月的第 " + calendar.get(Calendar.DAY_OF_MONTH) + "天");  

    // 3小时以后
    calendar.add(Calendar.HOUR_OF_DAY, 3);    
    System.out.println("三小时以后的时间： " + calendar.getTime());  
    // 格式化显示  
    str = (new SimpleDateFormat("yyyy-MM-dd HH:mm:ss:SS")).format(calendar.getTime());  
    System.out.println(str);  

    // 重置 Calendar 显示当前时间  
    calendar.setTime(new Date());  
    str = (new SimpleDateFormat("yyyy-MM-dd HH:mm:ss:SS")).format(calendar.getTime());  
    System.out.println(str);

    // 创建一个 Calendar 用于比较时间  
    Calendar calendarNew = Calendar.getInstance();  

    // 设定为 5 小时以前，后者大，显示 -1  
    calendarNew.add(Calendar.HOUR, -5);  
    System.out.println("时间比较：" + calendarNew.compareTo(calendar));  

    // 设定7小时以后，前者大，显示 1  
    calendarNew.add(Calendar.HOUR, +7);  
    System.out.println("时间比较：" + calendarNew.compareTo(calendar));  

    // 退回 2 小时，时间相同，显示 0  
    calendarNew.add(Calendar.HOUR, -2);  
    System.out.println("时间比较：" + calendarNew.compareTo(calendar));  
  }  
}
```

&ensp;&ensp;&ensp;&ensp;其实 month 的含义与 Date 类相同，0代表1月，11代表12月

## Random 类
&ensp;&ensp;&ensp;&ensp;Java 实用工具类库中的类 java.util.Random 提供了产生各种类型随机数的方法。它可以产生 int、long、float、double 以及 Goussian 等类型的随机数。这也是它与 java.lang.Math 中的方法 Random() 最大的不同之处，后者只产生 double 型的随机数
- Random():产生一个随机数需要基值，这里将系统时间作为 seed
- Random(long seed)	:自定义 seed

&ensp;&ensp;&ensp;&ensp;普通方法原型：

```java
//该方法是设定基值seed
public synonronized void setSeed(long seed)

//该方法是产生一个整型随机数
public int nextInt()

//该方法是产生一个long型随机数
public long nextLong()

//该方法是产生一个Float型随机数
public float nextFloat()
　
//该方法是产生一个Double型随机数
public double nextDouble()

//该方法是产生一个double型的Goussian随机数
public synchronized double nextGoussian()

/*
synchronized  是 Java 语言的关键字，当它用来修饰一个方法或者一个代码块的时候，能够保证在同一时刻最多只有一个线程执行该段代码
*/
```

## Java 集合框架
```java
/*
    * 通过迭代器来遍历
    */
   public void testIterator() {
       //通过集合的iterator方法，取得迭代器实例
       Iterator it = students.iterator();
       System.out.println("有如下学生（通过迭代器访问）：");
       while(it.hasNext()){

           Student st = (Student)it.next();
           System.out.println("学生" + st.id + ":" +st.name);
       }
   }

   /**
     * 通过 entrySet 方法来遍历 Map
     * @param args
     */
    public void testEntrySet() {
        //通过 entrySet 方法，返回 Map 中的所有键值对
        Set<Entry<String,Course>> entrySet = courses.entrySet();
        for(Entry<String,Course> entry: entrySet) {
            System.out.println("取得键：" + entry.getKey());
            System.out.println("对应的值为：" + entry.getValue().name);
        }
    }
```

## java.io 包-字节流
### 一、I/O流概述
&ensp;&ensp;&ensp;&ensp;大部分程序都需要进行输入/输出处理，比如从键盘读取数据、从屏幕中输出数据、从文件中写数据等等。在 Java 中，把这些不同类型的输入、输出源抽象为流（Stream），而其中输入或输出的数据则称为数据流（Data Stream），用统一的接口表示，从而使程序设计简单明了。
![](.\res\photo\document-uid79144labid1113timestamp1436110985475.png)

### 二、基类：InputStream 和 OutputStream
&ensp;&ensp;&ensp;&ensp;字节流主要操作byte类型数据，以byte数组为准，java 中每一种字节流的基本功能依赖于基本类 InputStream 和 Outputstream，他们是抽象类，不能直接使用。
&ensp;&ensp;&ensp;&ensp;　InputStream 是所有表示位输入流的父类，继承它的子类要重新定义其中所定义的抽象方法。InputStream 是从装置来源地读取数据的抽象表 示，例如 System 中的标准输入流 in 对象就是一个 InputStream 类型的实例。
&ensp;&ensp;&ensp;&ensp;我们先来看看 InputStream 类的方法：
- read()throws IOException 从流中读入数据
- skip()throws IOException 跳过流中若干字节数
- available()throws IOException	返回流中可用字节数
- mark()throws IOException	在流中标记过的位置
- reset()throws IOException	返回标记过的位置
- markSupport()throws IOException	是否支持标记和复位操作
- close()throws IOException	关闭流

&ensp;&ensp;&ensp;&ensp;OutputStream类方法：
- write(int b)throws IOException	将一个整数输出到流中（只输出低位字节，为抽象方法）
- write(byte b[])throws IOException	将字节数组中的数据输出到流中
- write(byte b[], int off, int len)throws IOException	将数组 b 中从 off 指定的位置开始，长度为 len 的数据输出到流中
- flush()throws IOException	刷空输出流，并将缓冲区中的数据强制送出
- close()throws IOException	关闭流

```java
public void copy(InputStream in, OutputStream out) throws IOException {
        byte[] buf = new  byte[4096];
        int len = in.read(buf);
        //read 是一个字节一个字节地读，字节流的结尾标志是-1
        while (len != -1){
            out.write(buf, 0, len);
            len = in.read(buf);
        }
    }
```

### 三、文件流
&ensp;&ensp;&ensp;&ensp;在 I/O 处理中，最常见的就是对文件的操作。java.io 包中所提供的文件操作类包括：

- 用于读写本地文件系统中的文件：FileInputStream 和 FileOutputStream
- 描述本地文件系统中的文件或目录：File、FileDescriptor 和 FilenameFilter
- 提供对本地文件系统中文件的随机访问支持：RandomAccessFile

```java
//打开一个以 f 描述的文件作为输入
FileInputStream(File f)

//打开一个文件路径名为 name 的文件作为输入
FileInputStream(String name)

//创建一个以 f 描述的文件作为输出
//如果文件存在，则其内容被清空
FileOutputStream(File f)

//创建一个文件路径名为 name 的文件作为输出
//文件如果已经存在，则其内容被清空
FileOutputStream(String name)

//创建一个文件路径名为 name 的文件作为输出
//文件如果已经存在，则在该输出上输出的内容被接到原有内容之后
FileOutputStream(String name, boolean append)
```

### 四、缓冲流
&ensp;&ensp;&ensp;&ensp;类 BufferedInputStream 和 BufferedOutputStream 实现了带缓冲的过滤流，它提供了缓冲机制，把任意的 I/O 流“捆绑”到缓冲流上，可以提高 I/O 流的读取效率。
&ensp;&ensp;&ensp;&ensp;在初始化时，除了要指定所连接的 I/O 流之外，还可以指定缓冲区的大小。缺省时是用32字节大小的缓冲区；最优的缓冲区大小常依赖于主机操作系统、可使用的内存空间以及机器的配置等；一般缓冲区的大小为内存页或磁盘块等的整数倍。
&ensp;&ensp;&ensp;&ensp; BufferedInputStream 的数据成员 buf 是一个位数组，默认为2048字节。当读取数据来源时例如文件，BufferedInputStream 会尽量将 buf 填满。当使用 read ()方法时，实际上是先读取 buf 中的数据，而不是直接对数据来源作读取。当 buf 中的数据不足时，BufferedInputStream 才会再实现给定的 InputStream 对象的 read() 方法，从指定的装置中提取数据。
&ensp;&ensp;&ensp;&ensp; BufferedOutputStream 的数据成员 buf 是一个位数组，默认为512字节。当使用 write() 方法写入数据时，实际上会先将数据写至 buf 中，当 buf 已满时才会实现给定的 OutputStream 对象的 write() 方法，将 buf 数据写至目的地，而不是每次都对目的地作写入的动作。
&ensp;&ensp;&ensp;&ensp;构造方法：

```java
//[ ]里的内容代表选填
BufferedInputStream(InputStream in[,int size])
BufferedOutputStream(OutputStream out[,int size])

//举个例子，将缓冲流与文件流相接：
FileInputStream in = new FileInputStream("file.txt");
FileOutputStream out = new FileOutputStream("file2.txt");
//设置输入缓冲区大小为256字节
BufferedInputStream bin = new BufferedInputStream(in,256)
BufferedOutputStream bout = new BufferedOutputStream(out,256)
int len;
byte bArray[] = new byte[256];
len = bin.read(bArray); //len 中得到的是实际读取的长度，bArray 中得到的是数据

//对于 BufferedOutputStream，只有缓冲区满时，才会将数据真正送到输出流，但可以使用 flush() 方法人为地将尚未填满的缓冲区中的数据送出。
public void copy(InputStream in, OutputStream out) throw IOException {
    out = new BufferedOutputStream(out, 4096);
    byte[] buf = new byte[4096];
    int len = in.read(buf);
    while (len != -1) {
    out.write(buf, 0, len);
    len = in.read(buf);
    }
    //最后一次读取得数据可能不到4096字节
    out.flush();
}
```

### 五、数据流
&ensp;&ensp;&ensp;&ensp;接口 DataInput 和 DataOutput，设计了一种较为高级的数据输入输出方式：除了可处理字节和字节数组外，还可以处理 int、float、boolean等基本数据类型，这些数据在文件中的表示方式和它们在内存中的一样，无须转换，如 read(), readInt(), readByte()...; write(), writeChar(), writeBoolean()...此外，还可以用 readLine()方法读取一行信息。

- readBoolean()	boolean
- readByte()	byte
- readShort()	short
- readChar()	char
- readInt()	int
- readLong()	long
- readDouble()	double
- readFloat()	float
- readUnsignedByte()	int
- readUnsignedShort()	int
- readFully(byte[] b)	void	读满字节数组，不同于 InputStream。read
- reaFully(byte[] b, int off,int len)	void	读满指定长度，不同于 InputStream.read
- skipBytes(int n)	int	与 InputStream.skip 等价
- readUTF()	String	安装 UTF-8 形式从输入中读取字符串
- readLine()	String	按回车(\r)换行(\n)为分割符读取一行字符串，不完全支持 UNICODE
- writeBoolean(boolean v)	void
- writeByte(int v)	void
- writeShort(int v)	void
- writeChar(int v)	void
- writeInt(int v)	void
- writeLong(long v)	void
- writeFloat(float v)	void
- writeDouble(double v)	void
- write(byte[] b)	void	与 OutputStream.write 同义
- write(byte[] b, int off, int len)	void	与OutputStream.write 同义
- write(int b)	void	与OutputStream.write 同义
- writeBytes(String s)	void	只输出每个字符的低8位；不完全支持 UNICODE
- writeChars(String s)	void	每个字符在输出中都占两个字节

```java
//数据流可以连接一个已经建立好的数据对象，例如网络连接、文件等。数据流可以通过如下方式建立：
FileInputStream fis = new FileInputStream("file1.txt");
FileOutputStream fos = new FileOutputStream("file2.txt");
DataInputStream dis = new DataInputStream(fis);
DataOutputStream dos = new DataOutputStream(fos);
//接下来我们通过具体的代码，看一看它的用法吧：
public static void main(String[] args) throws IOException{
        // TODO Auto-generated method stub
        //向文件 a.txt 写入
        FileOutputStream fos = new FileOutputStream("a.txt");
        DataOutputStream dos = new DataOutputStream(fos);
        try {
            dos.writeBoolean(true);
            dos.writeByte((byte)123);
            dos.writeChar('J');
            dos.writeDouble(3.1415926);
            dos.writeFloat(2.122f);
            dos.writeInt(123);
        }
        finally {
            dos.close();
        }
        //从文件 a.txt 读出
        FileInputStream fis = new FileInputStream("a.txt");
        DataInputStream dis = new DataInputStream(fis);
        try {
            System.out.println("\t" + dis.readBoolean());
            System.out.println("\t" + dis.readByte());
            System.out.println("\t" + dis.readChar());
            System.out.println("\t" + dis.readDouble());
            System.out.println("\t" + dis.readFloat());
            System.out.println("\t" + dis.readInt());
        }
        finally {
            dis.close();
        }
    }
```

### 六、标准流、内存读写流、顺序输入流
- 1、标准流

　　- 语言包 java.lang 中的 System 类管理标准输入/输出流和错误流。

　　- System.in从 InputStream 中继承而来，用于从标准输入设备中获取输入数据（通常是键盘）

　　- System.out从 PrintStream 中继承而来，把输入送到缺省的显示设备（通常是显示器）

　　- System.err也是从 PrintStream 中继承而来，把错误信息送到缺省的显示设备（通常是显示器） 　　 　　每当 main 方法被执行时，就会自动生产上述三个对象。这里就不再写代码验证了。 　

- 2、内存读写流

　　- 为了支持在内存上的 I/O，java.io 中提供了类：ByteArrayInputStream、ByteArrayOutputStream 和 StringBufferInputStream

- ByteArrayInputStream 可以从指定的字节数组中读取数据
- ByteArrayOutputStream 中提供了缓冲区可以存放数据（缓冲区大小可以在构造方法中设定，缺省为32），可以用 write() 方法向其中写入数据，然后用 toByteArray() 方法将缓冲区中的有效字节写到字节数组中去。size() 方法可以知道写入的字节数；reset() 可以丢弃所有内容。
- StringBufferInputStream 与 ByteArrayInputStream 相类似，不同点在于它是从字符缓冲区 StringBuffer 中读取16位的 Unicode 数据，而不是8位的字节数据（已被 StringReader 取代）
　　　这里只做简要的介绍，有兴趣的同学可以查看一下这些类里具体的方法。

- 3、顺序输入流

　　- java.io 中提供了类 SequenceInputStream，使应用程序可以将几个输入流顺序连接起来。顺序输入流提供了将多个不同的输入流统一为一个输入流的功能，这使得程序可能变得更加简洁。

例如：
```java
FileInputStream f1,f2;
String s;
f1 = new FileInputStream("file1.txt");
f2 = new FileInputStream("file2.txt");
SequenceInputStream fs = new SequenceInputStream(f1,f2);
DataInputeStream ds = new DataInputStream(fs);
while((s = ds.readLine()) != null) {
    System.out.println(s);
}
```

## java.io 包——字符流
### 一、基类
&ensp;&ensp;&ensp;&ensp;同类 InputStream 和 OutputStream 一样，Reader 和 Writer 也是抽象类，只提供了一系列用于字符流处理的接口。它们的方法与类 InputStream 和 OutputStream 类似，只不过其中的参数换成字符或字符数组。

- close()	void
- mark (int readAheadLimit)	void
- markSupported()	boolean
- read()	int
- read(char[] cbuf, int off,int len)	int
- ready()	boolean
- reset()	void
- skip(long n)	long

- close()	void
- flush()	void
- write(char[] cbuf)	void
- write(char[] cbuf, int off,int len)	void
- write(int c)	void
- write(String str)	void
- write(String str, int off, int len)	void

&ensp;&ensp;&ensp;&ensp;InputStreamReader 和 OutputStreamWriter 是 java.io 包中用于处理字符流的最基本的类，用来在字符流和字符流之间作为中介：从字节输入流读入字节，并按编码规范转换为字符；往字节输出流写字符时先将字符按编码规范转换为字节。使用这两者进行字符处理时，在构造方法中应指定一定的平台规范，以便把以字节方式表示的流转换为特定平台上的字符表示。

```java
InputStreamReader(InputStream in); //缺省规范说明

//指定规范 enc
InputStreamReader(InputStream in, String enc);

OutputStreamReader(OutputStream out); //缺省规范说明

//指定规范 enc
OutputStreamReader(OutputStream out, String enc);
```

&ensp;&ensp;&ensp;&ensp;如果读取的字符流不是来自本地时（比如网上某处与本地编码方式不同的机器），那么在构造字符输入流时就不能简单地使用缺省编码规范，而应该指定一种统一的编码规范“ISO 8859_1”，这是一种映射到 ASCCII 码的编码方式，能够在不同平台之间正确转换字符。

```java
InputStreamReader ir = new InputStreamReader(is,"8859_1");
```

### 二、缓存流
&ensp;&ensp;&ensp;&ensp;同样的，为了提高字符流处理的效率，java.io 中也提供了缓冲流 BufferedReader 和 BufferedWrite。其构造方法与 BufferedInputStream 和 BufferedOutPutStream 相类似。另外，除了 read() 和 write() 方法外，它还提供了整行字符处理方法：
- public String readLine()：BufferedReader 的方法，从输入流中读取一行字符，行结束标志\n、\r或者两者一起（这是更具系统而定的）
- public void newLine()：BufferedWriter 的方法，向输出流中写入一个行结束标志，它不是简单地换行符\n或\r，而是系统定义的行隔离标志（line separator）。

```java
public class FileToUnicode {
    public static void main(String args[]) {
        try {
            FileInputStream fis = new FileInputStream("file1.txt");
            InputStreamReader dis = new InputStreamReader(fis);
            BufferedReader reader = new BufferedReader(dis);
            String s;
            //每次读取一行，当改行为空时结束
            while((s = reader.readLine()) != null){
                System.out.println("read:" + s);
            }
            dis.close();
        }
        catch(IOException e) {
            System.out.println(e);
        }
    }
}
```

### 三、其它字符流类
&ensp;&ensp;&ensp;&ensp;在这里我们就列举一下有哪些类，具体的木木就不再讲解了。

- 对字符数组进行处理： CharArrayReader、CharArrayWrite
- 对文本文件进行处理：FileReader、FileWriter
- 对字符串进行处理：StringReader、StringWriter
- 过滤字符流：FilterReader、FileterWriter
- 管道字符流：PipedReader、PipedWriter
- 行处理字符流：LineNumberReader
- 打印字符流：PrintWriter

## 文件操作/随机访问文件
### 一、文件操作
&ensp;&ensp;&ensp;&ensp;java.io 定义的大多数类都是流式操作，但 File 类不是。它直接处理文件和文件系统。File类没有指定信息怎样从文件读取或向文件存储；它描述了文件本身的属性。File 对象用来获取或处理与磁盘文件相关的信息，例如权限，时间，日期和目录路径。此外，File还浏览子目录层次结构。Java 中的目录当成 File 对待，它具有附加的属性——一个可以被 list( )方法检测的文件名列表。

```java
//根据 parent 抽象路径名和 child 路径名字符串创建一个新 File 实例。
File(File parent, String child)

//通过将给定路径名字符串转换为抽象路径名来创建一个新 File 实例       
File(String pathname)

// 根据 parent 路径名字符串和 child 路径名字符串创建一个新 File 实例
File(String parent, String child)

//通过将给定的 file: URI 转换为一个抽象路径名来创建一个新的 File 实例
File(URI uri)

//一个目录路径参数
File f1 = new File("/Users/mumutongxue/");

//对象有两个参数——路径和文件名
File f2 = new File("/Users/mumutongxue/","a.bat");

//指向f1文件的路径及文件名
File f3 = new File(f1,"a.bat");
```

- boolean canExecute()	测试应用程序是否可以执行此抽象路径名表示的文件
- boolean canRead()	测试应用程序是否可以读取此抽象路径名表示的文件
- boolean canWrite()	测试应用程序是否可以修改此抽象路径名表示的文件
- int compareTo(File pathname)	按字母顺序比较两个抽象路径名
- boolean createNewFile()	当且仅当不存在具有此抽象路径名指定名称的文件时，不可分地创建一个新的空文件
- static File createTempFile(String prefix, String suffix)	在默认临时文件目录中创建一个空文件，使用给定前缀和后缀生成其名称
- static File createTempFile(String prefix, String suffix, File directory)	在指定目录中创建一个新的空文件，使用给定的前缀和后缀字符串生成其名称
- boolean delete()	删除此抽象路径名表示的文件或目录
- void deleteOnExit()	在虚拟机终止时，请求删除此抽象路径名表示的文件或目录
- boolean equals(Object obj)	测试此抽象路径名与给定对象是否相等
- boolean exists()	测试此抽象路径名表示的文件或目录是否存在
- File getAbsoluteFile()	返回此抽象路径名的绝对路径名形式
- String getAbsolutePath()	返回此抽象路径名的绝对路径名字符串
- File getCanonicalFile()	返回此抽象路径名的规范形式
- String getCanonicalPath()	返回此抽象路径名的规范路径名字符串
- long getFreeSpace()	返回此抽象路径名指定的分区中未分配的字节数
- String getName()	返回由此抽象路径名表示的文件或目录的名称
- String getParent()	返回此抽象路径名父目录的路径名字符串；如果此路径名没有指定父目录，则返回 null
- File getParentFile()	返回此抽象路径名父目录的抽象路径名；如果此路径名没有指定父目录，则返回 null
- String getPath()	将此抽象路径名转换为一个路径名字符串
- long getTotalSpace()	返回此抽象路径名指定的分区大小
- long getUsableSpace()	返回此抽象路径名指定的分区上可用于此虚拟机的字节数
- int hashCode()	计算此抽象路径名的哈希码
- boolean isAbsolute()	测试此抽象路径名是否为绝对路径名
- boolean isDirectory()	测试此抽象路径名表示的文件是否是一个目录
- boolean isFile()	测试此抽象路径名表示的文件是否是一个标准文件
- boolean isHidden()	测试此抽象路径名指定的文件是否是一个隐藏文件
- long lastModified()	返回此抽象路径名表示的文件最后一次被修改的时间
- long length()	返回由此抽象路径名表示的文件的长度
- String[] list()	返回一个字符串数组，这些字符串指定此抽象路径名表示的目录中的文件和目录
- String[] list(FilenameFilter filter)	返回一个字符串数组，这些字符串指定此抽象路径名表示的目录中满足指定过滤器的文件和目录
- File[] listFiles()	返回一个抽象路径名数组，这些路径名表示此抽象路径名表示的目录中的文件
- File[] listFiles(FileFilter filter)	返回抽象路径名数组，这些路径名表示此抽象路径名表示的目录中满足指定过滤器的文件和目录
- File[] listFiles(FilenameFilter filter)	返回抽象路径名数组，这些路径名表示此抽象路径名表示的目录中满足指定过滤器的文件和目录
- static File[] listRoots()	列出可用的文件系统根
- boolean mkdir()	创建此抽象路径名指定的目录
- boolean mkdirs()	创建此抽象路径名指定的目录，包括所有必需但不存在的父目录
- boolean renameTo(File dest)	重新命名此抽象路径名表示的文件
- boolean setExecutable(boolean executable)	设置此抽象路径名所有者执行权限的一个便捷方法
- boolean setExecutable(boolean executable, boolean ownerOnly)	设置此抽象路径名的所有者或所有用户的执行权限
- boolean setLastModified(long time)	设置此抽象路径名指定的文件或目录的最后一次修改时间
- boolean setReadable(boolean readable)	设置此抽象路径名所有者读权限的一个便捷方法
- boolean setReadable(boolean readable, boolean ownerOnly)	设置此抽象路径名的所有者或所有用户的读权限
- boolean setReadOnly()	标记此抽象路径名指定的文件或目录，从而只能对其进行读操作
- boolean setWritable(boolean writable)	设置此抽象路径名所有者写权限的一个便捷方法
- boolean setWritable(boolean writable, boolean ownerOnly)	设置此抽象路径名的所有者或所有用户的写权限
- String toString()	返回此抽象路径名的路径名字符串
- URI toURI()	构造一个表示此抽象路径名的 file: URI
- URL toURL()	已过时。 此方法不会自动转义 URL 中的非法字符。建议新的代码使用以下方式将抽象路径名转换为 URL：首先通过 toURI 方法将其转换为 URI，然后通过 URI.toURL 方法将 URI 装换为 URL

```java
public static void main(String[] args){
       //这是我电脑上的路径，同学们可以直接在桌上生成文件。桌面路径为"/root/Desktop"
       //构造函数File(String pathname)
       File f1 =new
       File("/Users/mumutongxue/1.txt");
       //File(String parent,String child)
       File f2 =new File("/Users/mumutongxue","2.txt");
       //separator 跨平台分隔符
       File f3 =new File("/Users"+File.separator+"mumutongxue");
       File f4 =new File(f3,"3.txt");

       try {
            System.out.println(f1);
               //当文件存在时返回 false；不存在时返回 true
               System.out.println(f2.createNewFile());
               //当文件不存在时返回 false
               System.out.println(f3.delete());
       }catch(IOException e) {
               e.printStackTrace();
       }

       //列出磁盘下的文件和文件夹
       File[] files =File.listRoots();
       for(File file:files){
           System.out.println(file);
           if(file.length()>0){
               String[] filenames =file.list();
               for(String filename:filenames){
                   System.out.println(filename);
               }
           }
       }

   }
```

### 二、随机访问文件
&ensp;&ensp;&ensp;&ensp;对于 FileInputStream/FileOutputStream、FileReader/FileWriter 来说，它们的实例都是顺序访问流，即只能进行顺序读/写。而类 RandomAccessFile 则允许文件内容同时完成读和写操作，它直接继承 object，并且同时实现了接口 DataInput 和 DataOutput，提供了支持随机文件操作的方法：

- readXXX()或者 writeXXX():如 ReadInt(),ReadLine(),WriteChar(),WriteDouble()等
- int skipBytes(int n):将指针向下移动若干字节
- length():返回文件长度
- long getFilePointer():返回指针当前位置
- void seek(long pos):将指针调用所需位置

&ensp;&ensp;&ensp;&ensp;在生成一个随机文件对象时，除了要指明文件对象和文件名之外，还需要指明访问文件的模式。
&ensp;&ensp;&ensp;&ensp;我们来看看 RandomAccessFile 的构造函数：

```java
RandomAccessFile(File file,String mode)
RandomAccessFile(String name,String mode)
```

&ensp;&ensp;&ensp;&ensp;mode 的取值：

- r只读，任何写操作都讲抛出 IOException
- rw读写，文件不存在时会创建该文件，文件存在是，原文件内容不变，通过写操作改变文件内容。
- rws同步读写，等同于读写，但是任何写操作的内容都被直接写入物理文件，包括文件内容和文件属性
- rwd数据同步读写，等同于读写，但任何内容写操作都直接写到物理文件，但对文件属性内容的修改不是这样

```java
public static void main(String[] args){

           int data_arr[] = {12, 32, 43, 45, 1, 5};
           try {
               RandomAccessFile randf=new RandomAccessFile("temp.dat","rw");
               for(int i = 0; i < data_arr.length; i++){
                   randf.writeInt(data_arr[i]);
               }
               for(int i = data_arr.length-1 ; i >= 0; i--){
                   //int 数据占4个字节
                   randf.seek(i * 4L);
                   System.out.println(randf.readInt());
               }
               randf.close();
           }catch(IOException e){
               System.out.println("File access error" + e);
           }
   }
```

## 泛型
&ensp;&ensp;&ensp;&ensp;我们在集合的类型后面添加上一对<>，尖括号中填上我们需要规范的集合里对象的类型，我们可以指定一个或多个类型参数的名字，同时也可以对类型参数的取值范围进行限定，多个类型参数之间用,分隔。这样编辑器便会在编译时帮我们检查出不符合规范的元素添加。

- 泛型的类型参数只能是类类型（包括自定义类），不能是简单类型。
- 同一种泛型可以对应多个版本（因为参数类型是不确定的），不同版本的泛型类实例是不兼容的。
- 泛型的类型参数可以有多个。
- 泛型的参数类型可以使用 extends 语句，例如 <T extends superclass>。习惯上称为“有界类型”。
- 泛型的参数类型还可以是通配符类型。例如 Class<?> classType = Class.forName("java.lang.String");

&ensp;&ensp;&ensp;&ensp;
&ensp;&ensp;&ensp;&ensp;
&ensp;&ensp;&ensp;&ensp;
