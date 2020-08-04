## 一.线程和进程

***进程*是操作系统中进行保护和资源分配的基本单位**，操作系统分配资源以进程为基本单位。**而线程是进程的组成部分，它代表了一条顺序的执行流。**

由于所有的线程共享进程的内存地址空间，所以线程间的通信就容易的多，通过共享进程级全局变量即可实现。



### （CPU给进程使用时间，进程给线程分配）

同时，在没有引入多线程概念之前，所谓的『并发』是发生在进程之间的，每一次的进程上下文切换都将导致系统调度算法的运行，以及各种 CPU 上下文的信息保存，非常耗时。而线程级并发没有系统调度这一步骤，进程分配到 CPU 使用时间，并给其内部的各个线程使用。

在分时系统中，***进程中的每个线程都拥有一个时间片***，时间片结束时保存 CPU 及寄存器中的线程上下文并交出 CPU，完成一次线程间切换。当然，当进程的 CPU 时间使用结束时，所有的线程必然被阻塞。

## 二、锁

有些业务逻辑在执行过程中要求对数据进行排他性的访问，于是需要通过一些机制保证在此过程中数据被锁住不会被外界修改，这就是所谓的锁机制。

![image-20200518080602677](C:\Users\wangdamei\AppData\Roaming\Typora\typora-user-images\image-20200518080602677.png)

## 三、线程的基本操作



### 2.1 创建



Java中创建多线程类两种方法：



**方法1、继承java.lang.Thread**



*Java示例：*



```
public class MyThread extends Thread {
    public void run() {
        for (int i = 0; i < 10000; i++) {
            System.out.print(i + " ");
        }
    }
}
public class MultiThread {
    public static void main(String[] args) {
        MyThread t = new MyThread();
        t.start();    //启动子线程
        //主线程继续同时向下执行
        for (int i = 0; i < 10000; i++) {
            System.out.print(i + " ");
        }
    }
}
```



*上述代码中，`MyThread`类继承了类`java.lang.Thread`，并覆写了`run`方法。主线程从`main`方法开始执行，当主线程执行至`t.start()`时，启动新线程（注意此处是调用`start`方法，不是`run`方法），新线程会并发执行自身的`run`方法。*



**方法2、实现java.lang.Runnable接口**



*Java示例：*



​                        

```
public class MyThread implements Runnable {
    public void run() {
        for (int i = 0; i < 10000; i++) {
            System.out.print(i + " ");
        }
    }
}
public class MultiThread {
    public static void main(String[] args) {
        Thread t = new Thread(new MyThread());
        t.start();    //启动子线程
        //主线程继续同时向下执行
        for (int i = 0; i < 10000; i++) {
            System.out.print(i + " ");
        }
    }
}
```

注意：主线程执行完成后，如果还有子线程正在执行，程序也不会结束。只有当所有线程都结束时（不含Daemon Thread），程序才会结束。



### 2.2 暂停



Java中线程的暂停是调用`java.lang.Thread`类的`sleep`方法（注意是**类方法**）。该方法会使**当前正在执行的线程**暂停指定的时间，如果线程持有锁，`sleep`方法结束前并不会释放该锁。

### 2.3 互斥



Java中线程的共享互斥操作，会使用synchronized关键字。线程共享互斥的架构称为监视（monitor），而获取锁有时也称为“持有(own)监视”。



每个锁在同一时刻，只能由一个线程持有。
*注意：`synchronized`方法或声明执行期间，如程序遇到任何异常或return，线程都会释放锁。*



### 2.4 中断



java.lang.Thread类有一个`interrupt`方法，该方法直接对线程调用。当被interrupt的线程正在sleep或wait时，会抛出`InterruptedException`异常。
事实上，`interrupt`方法只是改变目标线程的中断状态（interrupt status），而那些会抛出`InterruptedException`异常的方法，如wait、sleep、join等，都是在方法内部不断地检查中断状态的值。



- **interrupt方法**



Thread实例方法：必须由其它线程获取被调用线程的实例后，进行调用。实际上，只是改变了被调用线程的内部中断状态；



- **Thread.interrupted方法**



Thread类方法：必须在当前执行线程内调用，该方法返回当前线程的内部中断状态，然后清除中断状态（置为false） ；



- **isInterrupted方法**



Thread实例方法：用来检查指定线程的中断状态。当线程为中断状态时，会返回true；否则返回false。



### 2.5 协调



**1、wait  set / wait方法**
wait set是一个虚拟的概念，每个Java类的实例都有一个wait  set，当对象执行wait方法时，当前线程就会暂停，并进入该对象的wait  set。
当发生以下事件时，线程才会退出wait  set：
①有其它线程以notify方法唤醒该线程
②有其它线程以notifyAll方法唤醒该线程
③有其它线程以interrupt方法唤醒该线程
④wait方法已到期
*注：当前线程若要执行`obj.wait()`，则必须先获取该对象锁。当线程进入wait set后，就已经释放了该对象锁。*



下图中线程A先获得对象锁，然后调用wait()方法（此时线程B无法获取锁，只能等待）。当线程A调用完wait()方法进入wait set后会自动释放锁，线程B获得锁。



**2、notify方法**
notify方法相当于从wait set中从挑出一个线程并唤醒。
下图中线程A在当前实例对象的wait set中等待，此时线程B必须拿到同一实例的对象锁，才能调用notify方法唤醒wait set中的任意一个线程。
*注：线程B调用notify方法后，并不会立即释放锁，会有一段时间差。*





**3、notifyAll方法**
notifyAll方法相当于将wait set中的所有线程都唤醒。



**4、总结**
wait、notify、notifyAll这三个方法都是java.lang.Object类的方法（注意，不是Thread类的方法）。
若线程没有拿到当前对象锁就直接调用对象的这些方法，都会抛出java.lang.IllegalMonitorStateException异常。



- `obj.wait()`是把当前线程放到obj的wait set；
- `obj.notify()`是从obj的wait set里唤醒1个线程；
- `obj.notifyAll()`是唤醒所有在obj的wait set里的线程。

## 四、线程的6种状态

Java线程`Thread`在`package java.lang;`中可以找到，通过源码，我们可以看到其状态有如下6种

- **NEW**
- **RUNNABLE**
- **BLOCKED**
- **WAITING**
- **TIMED_WAITING**
- **TERMINATED**

下面分别解释一下各种状态

### NEW

顾名思义，这个状态，只存在于线程刚创建，未`start`之前，例如



```java
        MyThread thread = new MyThread();
        System.out.println(thread.getState());
```

此时打印出来的状态就是NEW

### RUNNABLE

这个状态的线程，其正在JVM中执行，但是这个"执行"，不一定是真的在运行， 也有可能是在等待CPU资源。所以，在网上，有人把这个状态区分为READY和RUNNING两个，一个表示的start了，资源一到位随时可以执行，另一个表示真正的执行中，例如



```java
        MyThread thread = new MyThread(lock);
        thread.start();
        System.out.println(thread.getState());
```

### BLOCKED

这个状态，***<u>一般是线程等待获取一个锁，来继续执行下一步的操作，</u>***比较经典的就是`synchronized`关键字，这个关键字修饰的代码块或者方法，均需要获取到对应的锁，在未获取之前，其线程的状态就一直未BLOCKED，如果线程长时间处于这种状态下，我们就是当心看是否出现死锁的问题了。例如



```java
public class MyThread extends Thread {
    private byte[] lock = new byte[0];

    public MyThread(byte[] lock) {
        this.lock = lock;
    }

    @Override
    public void run() {
        synchronized (lock){
            try {
                Thread.sleep(10000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println("done");

        }
    }
}
```



```java
    public static void main(String[] args) throws InterruptedException {
        byte[] lock = new byte[0];
        MyThread thread1 = new MyThread(lock);
        thread1.start();
        MyThread thread2 = new MyThread(lock);
        thread2.start();
        Thread.sleep(1000);//等一会再检查状态
        System.out.println(thread2.getState());
    }
```

此时我们看到的输出的第二个线程的状态就是BLOCKED

### WAITING

一个线程会进入这个状态，一定是执行了如下的一些代码，例如



wait和join不带时间。

- **Object.wait()**
- **Thread.join()**
- **LockSupport.park()**
  ***当一个线程执行了Object.wait()的时候，它一定在等待另一个线程执行Object.notify()或者Object.notifyAll()。***
  或者一个线程thread，其在主线程中被执行了thread.join()的时候，主线程即会等待该线程执行完成。当一个线程执行了LockSupport.park()的时候，其在等待执行LockSupport.unpark(thread)。当该线程处于这种等待的时候，其状态即为WAITING。需要关注的是，这边的等待是没有时间限制的，当发现有这种状态的线程的时候，若其长时间处于这种状态，也需要关注下程序内部有无逻辑异常。例如
  **LockSupport.park()** 



```java
public class MyThread extends Thread {
    private byte[] lock = new byte[0];

    public MyThread(byte[] lock) {
        this.lock = lock;
    }

    @Override
    public void run() {
        LockSupport.park();
    }
}
```



```java
    public static void main(String[] args) throws InterruptedException {
        byte[] lock = new byte[0];
        MyThread thread1 = new MyThread(lock);
        thread1.start();
        Thread.sleep(100);
        System.out.println(thread1.getState());
        LockSupport.unpark(thread1);
        Thread.sleep(100);
        System.out.println(thread1.getState());
    }
```

输出WAITING和TERMINATED

**Object.wait()**



```java
public class MyThread extends Thread {
    private byte[] lock = new byte[0];

    public MyThread(byte[] lock) {
        this.lock = lock;
    }

    @Override
    public void run() {
        synchronized (lock){
            try {
                lock.wait(); //wait并允许其他线程同步lock
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}
```



```java
    public static void main(String[] args)
        throws InterruptedException {
        byte[] lock = new byte[0];
        MyThread thread1 = new MyThread(lock);
        thread1.start();
        Thread.sleep(100);
        System.out.println(thread1.getState()); //这时候线程状态应为WAITING
        synchronized (lock){
            lock.notify(); //notify通知wait的线程
        }
        Thread.sleep(100);
        System.out.println(thread1.getState());
    }
```

输出WAITING和TERMINATED

**Thread.join()**



```java
public class MyThread extends Thread {
    private byte[] lock = new byte[0];

    public MyThread(byte[] lock) {
        this.lock = lock;
    }

    @Override
    public void run() {
        try {
            Thread.sleep(10000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```



```java
public class MyThread1 extends Thread {

    Thread thread;

    public MyThread1(Thread thread) {
        this.thread = thread;
    }

    @Override
    public void run() {
        try {
            thread.join();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```



```java
public class Main {

    public static void main(String[] args)
        throws InterruptedException {
        byte[] lock = new byte[0];
        MyThread thread = new MyThread(lock);
        thread.start();
        MyThread1 thread1 = new MyThread1(thread);
        thread1.start();
        Thread.sleep(100);
        System.out.println(thread1.getState());
    }
}
```

输出为WAITING

### TIMED_WAITING

这个状态和**WAITING**状态的区别就是，这个状态的等待是有一定时效的，**`即可以理解为WAITING状态等待的时间是永久的，即必须等到某个条件符合才能继续往下走，否则线程不会被唤醒。但是TIMED_WAITING，等待一段时间之后，会唤醒线程去重新获取锁。`**当执行如下代码的时候，对应的线程会进入到**TIMED_WAITING**状态

- Thread.sleep(long)
- Object.wait(long)
- Thread.join(long)
- LockSupport.parkNanos()
- LockSupport.parkUntil()

代码示例
**Thread.sleep**



```java
public class MyThread3 extends Thread {
    @Override
    public void run() {
        try {
            Thread.sleep(10000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```



```java
        Thread thread = new MyThread3();
        thread.start();
        Thread.sleep(100);
        System.out.println(thread.getState());
```

输出为TIMED_WAITING

**Object.wait**



```java
public class MyThread4 extends Thread {
    private Object lock;

    public MyThread4(Object lock) {
        this.lock = lock;
    }

    @Override
    public void run() {

        synchronized (lock){
            try {
                lock.wait(1000);//注意，此处1s之后线程醒来，会重新尝试去获取锁，如果拿不到，后面的代码也不执行
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println("lock end");
        }
    }
}
```



```java
        byte[] lock = new byte[0];
        MyThread4 thread = new MyThread4(lock);
        thread.start();
        Thread.sleep(100);
        System.out.println(thread.getState());
        Thread.sleep(2000);
        System.out.println(thread.getState());
```

输出
TIMED_WAITING
lock end
TERMINATED

其余方法类似

**TERMINATED**
这个状态很好理解，即为线程执行结束之后的状态

## 状态之间的切换

![img](https:////upload-images.jianshu.io/upload_images/4840092-f85e70e2262b7878.png?imageMogr2/auto-orient/strip|imageView2/2/w/1155)

线程状态切换.png



作者：两句挽联
链接：https://www.jianshu.com/p/ec94ed32895f
来源：简书
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。