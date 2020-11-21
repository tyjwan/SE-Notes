# Java 单例模式

***

## 什么是单例模式

&ensp;&ensp;&ensp;&ensp;保证一个类仅创建一个实例，并提供一个全局访问点

&ensp;&ensp;&ensp;&ensp;Java推荐使用静态内部类或者枚举实现

## 创建模式

### 饿汉模式

- 线程安全：static修饰变量，在类初始化过程中能保证唯一执行
- 优点：保证唯一性，没有锁和其他判断，性能高
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

    private Singleton() {//构造函数
    }
    public static Singleton getInstance(){
        return INSTANCE;//返回已存在的对象
    }
}
```

### 懒汉模式
- 线程安全：需要使用锁来保障线程安全
- 优点：需要的时候进行加载
- 缺点：初次使用需要加载

```java
// 前三种只是列举出来，不线程安全
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

// 线程安全
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

//懒汉模式 静态内部类实现
// 第一次加载Singleton类时并不会初始化sInstance，只有第一次调用getInstance方法时虚拟机加载SingletonHolder 并初始化sInstance ，这样不仅能确保线程安全也能保证Singleton类的唯一性，所以推荐使用静态内部类单例模式。
public class Singleton { 
    private Singleton(){
    }
    public static Singleton getInstance(){  
        return SingletonHolder.sInstance;  
    }  
    private static class SingletonHolder {  
        private static final Singleton sInstance = new Singleton();  
    }  
} 

//懒汉模式 枚举实现
// 推荐使用
public class Singleton {
    private enum EnumSingleton {
        /**
         * 懒汉枚举单例
         */
        INSTANCE;
        private Singleton instance;

        EnumSingleton(){
            instance = new Singleton();
        }
        public Singleton getSingleton(){
            return instance;
        }
    }

    public static Singleton getInstance(){
        return EnumSingleton.INSTANCE.getSingleton();
    }
}
```

## 参考链接
- [为什么用枚举类来实现单例模式越来越流行？](https://juejin.im/post/6844903925783461896)
- [Java单例模式怎么用？看这篇就够了](https://zhuanlan.zhihu.com/p/150004430)
- [设计模式（二）单例模式的七种写法](https://blog.csdn.net/itachi85/article/details/50510124)