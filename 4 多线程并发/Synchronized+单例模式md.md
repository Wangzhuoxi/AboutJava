# Synchronized

.synchronized是Java中的关键字，是一种同步锁。它修饰的对象有以下几种： 
（1） 修饰一个代码块，

被修饰的代码块称为同步语句块，

其作用的范围是大括号{}括起来的代码，

作用的对象是调用这个代码块的对象； 
（2）修饰一个方法，被修饰的方法称为同步方法，

其作用的范围是整个方法，

作用的对象是调用这个方法的对象； 
（3）修改一个静态的方法，

其作用的范围是整个静态方法，

作用的对象是这个类的所有对象； 
（4）修改一个类，其作用的范围是synchronized后面括号括起来的部分，作用主的对象是这个类的所有对象。

举例：

```java
（1）修饰代码块

  @Override
    public  void run() {
        synchronized(this) {
            for (int i = 0; i < 5; i++) {
                try {
                    System.out.println(Thread.currentThread().getName() + ":" + (count++));
                    Thread.sleep(100);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }
    }
```

————————————————

synchronized只锁定对象，每个对象只有一个锁（lock）与之相关联，这时会有两把锁分别锁定syncThread1对象和syncThread2对象，而这两把锁是互不干扰的，不形成互斥，所以两个线程可以同时执行。

2.修饰普通方法：

Synchronized作用于整个方法的写法。 
写法一：

public synchronized void method()
{
  // todo
}



```java
public class SyncThread2 {
private static int count;
 
public synchronized void test1(){
    for (int i = 0; i < 5; i ++) {
        try {
            System.out.println(Thread.currentThread().getName() + ":" + (count++));
            Thread.sleep(100);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```

————————————————

### 单例模式

单例模式（Singleton Pattern）是 Java 中最简单的设计模式之一。这种类型的设计模式属于创建型模式，它提供了一种创建对象的最佳方式。

这种模式涉及到一个单一的类，该类负责创建自己的对象，同时确保只有单个对象被创建。这个类提供了一种访问其唯一的对象的方式，可以直接访问，不需要实例化该类的对象。

- 1、单例类只能有一个实例。
- 2、单例类必须自己创建自己的唯一实例。
- 3、单例类必须给所有其他对象提供这一实例。

```java
public class SingleObject {
 
   //创建 SingleObject 的一个对象
   private static SingleObject instance = new SingleObject();
 
   //让构造函数为 private，这样该类就不会被实例化
   private SingleObject(){}
 
   //获取唯一可用的对象
   public static SingleObject getInstance(){
      return instance;
   }
 
   public void showMessage(){
      System.out.println("Hello World!");
   }
}
```

## 介绍

**意图：*****<u>保证一个类仅有一个实例</u>***，并提供一个访问它的全局访问点。

**主要解决：**一个全局使用的类频繁地创建与销毁。

**关键代码：**构造函数是私有的。

**应用实例：**

- 1、一个班级只有一个班主任。
- 2、Windows 是多进程多线程的，在操作一个文件的时候，就不可避免地出现多个进程或线程同时操作一个文件的现象，所以所有文件的处理必须通过唯一的实例来进行。
- 3、一些设备管理器常常设计为单例模式，比如一个电脑有两台打印机，在输出的时候就要处理不能两台打印机打印同一个文件。

## 实现

我们将创建一个 *SingleObject* 类。*SingleObject* 类有:1、私有构造函  2、本身的一个静态实例。

**<u>*SingleObject* 类提供了一个静态方法，供外界获取它的静态实例。</u>**

*SingletonPatternDemo文件*：我们的演示类使用 *SingleObject* 类来获取 *SingleObject* 对象。

## SingleObject.java

```java
public class SingleObject {
 
   //创建 SingleObject 的一个对象
   private static SingleObject instance = new SingleObject();
 
   //让构造函数为 private，这样该类就不会被实例化
   private SingleObject(){}
 
   //获取唯一可用的对象
   public static SingleObject getInstance(){
      return instance;
   }
 
   public void showMessage(){
      System.out.println("Hello World!");
   }
}
```

## SingletonPatternDemo.java

```java

public class SingletonPatternDemo {
   public static void main(String[] args) {
 
      //不合法的构造函数
      //编译时错误：构造函数 SingleObject() 是不可见的
      //SingleObject object = new SingleObject();
 
      //获取唯一可用的对象
      SingleObject object = SingleObject.getInstance();
 
      //显示消息
      object.showMessage();
   }
}
```



# 单例模式实现方式：

### 1.懒汉式，线程安全

**是否 Lazy 初始化：**是

**是否多线程安全：**是

**实现难度：**易

**描述：**这种方式具备很好的 lazy loading，能够在多线程中很好的工作，但是，效率很低，99% 情况下不需要同步。
优点：第一次调用才初始化，避免内存浪费。
缺点：必须加锁 synchronized 才能保证单例，但加锁会影响效率。
getInstance() 的性能对应用程序不是很关键（该方法使用不太频繁）。



注意synchronized 关键字的使用。

## 实例

```java
public Class Singleton{

private static Singleton single;
 private Singleton(){}
 public static synchronized Singleton getInstance()
 {
     if(single==null)
         single=new Singeton(); 
     return single;
 }

}
```

### 3、饿汉式

**是否 Lazy 初始化：**否

**是否多线程安全：**是

**实现难度：**易

**描述：**这种方式比较常用，但容易产生垃圾对象。
**优点：没有加锁，执行效率会提高。**
**缺点：类加载时就初始化，浪费内存。**
instance 在类装载时就实例化，这时候初始化 instance 显然没有达到 lazy loading 的效果。

```java
public class Singleton {  
    private static Singleton instance = new Singleton();  
    private Singleton (){}  
    public static Singleton getInstance() {  
    return instance;  
    }  
}
```

### 4、双检锁/双重校验锁（DCL，即 double-checked locking）

**JDK 版本：**JDK1.5 起

**是否 Lazy 初始化：**是

**是否多线程安全：**是

**实现难度：**较复杂

**描述：**这种方式采用双锁机制，安全且在多线程情况下能保持高性能。
getInstance() 的性能对应用程序很关键。

```java
public class Singleton {  
   private volatile static Singleton instance;
    private static Singleton(){};
    private static Singleton getInstance()
    {
        if(instance==null)
        {
        synchronized(Singleton.class)
        {
            if(instance==null){
            instance=new Singleton();
            }
        }
        }
        return instance;
    }
    }  
}
```

