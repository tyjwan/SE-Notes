# Java 单例模式

***

## 什么是单例模式

&ensp;&ensp;&ensp;&ensp;保证一个类仅创建一个实例，并提供一个全局访问点

## 创建模式

### 饿汉模式

- 线程安全：static修饰变量，在类初始化过程中能保证唯一执行
- 有点：保证唯一性，没有锁和其他判断，性能高
- 缺点：在类成员比较多或变量比较大时，在没有使用之前或初始化时就会一直暂用堆内存

```java

//饿汉模式
public final class Singleton {
    private static Singleton instance=new Singleton();//自行创建实例
    private Singleton(){}//构造函数
    public static Singleton getInstance(){//通过该函数向整个系统提供实例
        return instance;
    }
}


//饿汉模式 枚举实现
public enum Singleton {
    INSTANCE;//不实例化
    public List<String> list = null;// list属性

  private Singleton() {//构造函数
    list = new ArrayList<String>();
  }
    public static Singleton getInstance(){
        return INSTANCE;//返回已存在的对象
    }
}
```

### 懒汉模式

```java
//懒汉模式
public final class Singleton {
    private static Singleton instance= null;//不实例化
    private Singleton(){}//构造函数
    public static Singleton getInstance(){//通过该函数向整个系统提供实例
        if(null == instance){//当instance为null时，则实例化对象，否则直接返回对象
            instance = new Singleton();//实例化对象
        }
        return instance;//返回已存在的对象
    }
}

//懒汉模式 + synchronized同步锁
public final class Singleton {
    private static Singleton instance= null;//不实例化
    private Singleton(){}//构造函数
    public static synchronized Singleton getInstance(){//加同步锁，通过该函数向整个系统提供实例
        if(null == instance){//当instance为null时，则实例化对象，否则直接返回对象
            instance = new Singleton();//实例化对象
        }
        return instance;//返回已存在的对象
    }
}

//懒汉模式 + synchronized同步锁
public final class Singleton {
    private static Singleton instance= null;//不实例化
    private Singleton(){}//构造函数
    public static Singleton getInstance(){//加同步锁，通过该函数向整个系统提供实例
        if(null == instance){//当instance为null时，则实例化对象，否则直接返回对象
          synchronized (Singleton.class){
              instance = new Singleton();//实例化对象
          }
        }
        return instance;//返回已存在的对象
    }
}

//懒汉模式 + synchronized同步锁 + double-check
public final class Singleton {
    private static Singleton instance= null;//不实例化
    private Singleton(){}//构造函数
    public static Singleton getInstance(){//加同步锁，通过该函数向整个系统提供实例
        if(null == instance){//第一次判断，当instance为null时，则实例化对象，否则直接返回对象
          synchronized (Singleton.class){//同步锁
             if(null == instance){//第二次判断
                instance = new Singleton();//实例化对象
             }
          }
        }
        return instance;//返回已存在的对象
    }
}


//懒汉模式 + synchronized同步锁 + double-check
public final class Singleton {
    private static Singleton instance= null;//不实例化
    public List<String> list = null;//list属性
    private Singleton(){
      list = new ArrayList<String>();
    }//构造函数
    public static Singleton getInstance(){//加同步锁，通过该函数向整个系统提供实例
        if(null == instance){//第一次判断，当instance为null时，则实例化对象，否则直接返回对象
          synchronized (Singleton.class){//同步锁
             if(null == instance){//第二次判断
                instance = new Singleton();//实例化对象
             }
          }
        }
        return instance;//返回已存在的对象
    }
}

//懒汉模式 内部类实现
public final class Singleton {
  public List<String> list = null;// list属性

  private Singleton() {//构造函数
    list = new ArrayList<String>();
  }

  // 内部类实现
  public static class InnerSingleton {
    private static Singleton instance=new Singleton();//自行创建实例
  }

  public static Singleton getInstance() {
    return InnerSingleton.instance;// 返回内部类中的静态变量
  }
}


//懒汉模式 枚举实现
public class Singleton {
    //不实例化
    public List<String> list = null;// list属性

  private Singleton(){//构造函数
    list = new ArrayList<String>();
  }
    //使用枚举作为内部类
    private enum EnumSingleton {
        INSTANCE;//不实例化
        private Singleton instance = null;

        private EnumSingleton(){//构造函数
        instance = new Singleton();
       }
        public Singleton getSingleton(){
            return instance;//返回已存在的对象
        }
    }

    public static Singleton getInstance(){
        return EnumSingleton.INSTANCE.getSingleton();//返回已存在的对象
    }
}
```
