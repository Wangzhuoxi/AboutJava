AQS

https://youzhixueyuan.com/aqs.html

AQS（AbstractQueuedSynchronizer）就是一个抽象的队列同步器，AQS定义了一套多线程访问共享资源的同步器框架，许多同步类实现都依赖于它。

AQS的主要作用是为Java中的并发同步组件提供统一的底层支持，比如大家熟知的：

-  ReentrantLock
-  Semaphore
-  CountDownLatch
-  CyclicBarrier
- ![image-20200525194153126](C:\Users\wangdamei\AppData\Roaming\Typora\typora-user-images\image-20200525194153126.png)

![image-20200525194220871](C:\Users\wangdamei\AppData\Roaming\Typora\typora-user-images\image-20200525194220871.png)

**2.AQS中state状态的变更是基于CAS实现的**

主要有三种方法：

-  getState()
-  setState()
-  compareAndSetState()
- state状态通过volatile保证共享变量的可见性，再由CAS 对该同步状态进行原子操作，从而保证原子性和可见性。



## AQS资源共享方式 

AQS定义两种资源共享方式：

![深度源码剖析AQS的实现原理](https://youzhixueyuan.com/blog/wp-content/uploads/2020/05/20200501143153-ad4e7.jpeg)

**1.独占锁Exclusive**

独占模式下时，其他线程试图获取该锁将无法取得成功，只有一个线程能执行，如ReentrantLock采用独占模式。

ReentrantLock还可以分为公平锁和非公平锁：

-  公平锁：按照线程在队列中的排队顺序，先到者先拿到锁
-  非公平锁：当线程要获取锁时，无视队列顺序直接去抢锁，谁抢到就是谁的

**2.共享锁shared**

多个线程获取某个锁可能会获得成功，多个线程可同时执行，如：Semaphore、CountDownLatch。

## AQS的锁获取与释放原理 

![深度源码剖析AQS的实现原理](https://youzhixueyuan.com/blog/wp-content/uploads/2020/05/20200501143154-bbba8.jpeg)

**1.线程获取锁流程：**

-  线程A获取锁，state将0置为1，线程A占用
-  在A没有释放锁期间，线程B也来获取锁，线程B获取state为1，表示线程被占用，线程B创建Node节点放入队尾(tail)，并且阻塞线程B
-  同理线程C获取state为1，表示线程被占用，线程C创建Node节点，放入队尾，且阻塞线程

**2.线程释放锁流程：**

-  线程A执行完，将state从1置为0
-  唤醒下一个Node B线程节点，然后再删除线程A节点
-  线程B占用，获取state状态位，执行完后唤醒下一个节点 Node C,再删除线程B节点

更加详细的锁获取和释放过程，建议通过查看源码的方式学习AQS独占模式和共享模式下的获取锁过程。

# AQS总结 

本文主要介绍AQS的数据模型、CLH队列、资源共享方式、以及锁的获取与释放流程，来介绍AQS的实现原理

让大家能对AQS有一个整体的了解，只有对整体的设计方向有清晰了解，再去跟踪学习源码就会比较轻松了。



## Condition

#### java.util.concurrent.locks.Condition

Condition接口主要是用来对持有锁的线程进行同步控制。Condition接口中有以下3个比较重要的方法如下：



```java
    Condition.await() == Object.wait()
    Condition.signal() == Object.notify()
    Condition.signalAll() == Object.notifyAll()
```

用Object.wait()来写代码

```java
/生产者(写入线程)
public class SynchronizedProviderDemo implements Runnable {

    public static List<String> cache = new LinkedList<String>();

    @Override
    public void run() {
        try {
            while (!Thread.interrupted()) {
                Thread.sleep(10);
                synchronized (cache) {
                    if (cache.size() < 1) {
                        System.out.println("生产bread");
                        cache.add(new String("bread"));
                        cache.notifyAll();
                    } else {
                        System.out.println("bread生产超过总量");
                        cache.wait();
                    }
                }
            }
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }

}



-------------------------------------------------------------------


//消费者(读取线程)
public class SynchronizedConsumerDemo implements Runnable {

    @Override
    public void run() {
        try {
            while (!Thread.interrupted()) {
                Thread.sleep(10);
                synchronized (SynchronizedProviderDemo.cache) {
                    if (SynchronizedProviderDemo.cache.size() == 0) {
                        System.out.println("bread全部消费完毕");
                        SynchronizedProviderDemo.cache.wait();
                    } else {
                        System.out.println("消费bread");
                        SynchronizedProviderDemo.cache.get(0);
                        SynchronizedProviderDemo.cache.remove(0);
                        SynchronizedProviderDemo.cache.notifyAll();
                        
                    }
                }
            }
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

    }

}
```

```java
public class ExecuteConsumerProviderDemoClass {

    public static void main(String[] args) {
        Thread d1 = new Thread(new SynchronizedConsumerDemo());
        Thread d2 = new Thread(new SynchronizedProviderDemo());
        // 产生10个生产消费者线程
        for (int i = 0; i < 10; i++) {
            new Thread(new SynchronizedProviderDemo()).start();
            new Thread(new SynchronizedProviderDemo()).start();
        }
        d1.start();
        d2.start();
    }

}


----------
生产bread
bread生产超过总量
消费bread
bread全部消费完毕
生产bread
bread生产超过总量
消费bread
生产bread
消费bread
生产bread
```

代码依然按照预想的结果执行了。

但是需要注意，每当线程在某个锁上调用notifyAll()时，所有持有这个锁的处于阻塞状态的线程都会被唤醒。
 虽然在上面的例子中，我们通过逻辑避免了“生产者调用notfiyAll()时连同生产者一起唤醒”这样的情况出现，
 但是，这样的处理方式实在是十分“低级”，且不易阅读。

试想，我们是否能够显示的在代码中只唤醒“对方”处于阻塞状态的线程呢？

 **<u><u>******<u>换句话说，在持有同一个锁的情况下，生产者只唤醒消费者，消费者只唤醒生产者，这样的需求是否能实现？</u>***



 **<u>答案是可以的。使用Condition配合Lock，就能解决这样的需求了</u>**。**</u>**</u>*

让我们来看看Condition，如何在多生产者消费者模型中实现交替唤醒功能。
 下面是一个实际项目(web application)中比较常见的，多生产者单消费者模型示例。



```java
import java.util.LinkedList;
import java.util.List;
import java.util.concurrent.locks.Condition;
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

public class ReentrantLockConditionDemo {

    private Lock theLock = new ReentrantLock();
    // 消费者用判断条件
    private Condition full = theLock.newCondition();
    // 生产者用判断条件
    private Condition empty = theLock.newCondition();

    private static List<String> cache = new LinkedList<String>();

    // 生产者线程任务
    public void put(String str) throws InterruptedException {
        Thread.sleep(200);
        // 获取线程锁
        theLock.lock();
        try {
            while (cache.size() != 0) {
                System.out.println("超出缓存容量.暂停写入.");
                // 生产者线程阻塞
                full.await();
                System.out.println("生产者线程被唤醒");
            }
            System.out.println("写入数据");
            cache.add(str);
            // 唤醒消费者
            empty.signal();
        } finally {
            // 锁使用完毕后不要忘记释放
            theLock.unlock();
        }
    }

    // 消费者线程任务
    public void get() throws InterruptedException {
        try {
            while (!Thread.interrupted()) {
                // 获取锁
                theLock.lock();
                while (cache.size() == 0) {
                    System.out.println("缓存数据读取完毕.暂停读取");
                    // 消费者线程阻塞
                    empty.await();
                }
                System.out.println("读取数据");
                cache.remove(0);
                // 唤醒生产者线程
                full.signal();
            }

        } finally {
            // 锁使用完毕后不要忘记释放
            theLock.unlock();
        }
    }

}




-------------------------------------------------------------------

public class ExecuteReentrantLockConditionDemo {

    public static void main(String[] args) {
        final ReentrantLockConditionDemo rd = new ReentrantLockConditionDemo();
        // 创建1个消费者线程
        for (int i = 0; i < 1; i++) {
            
            new Thread(new Runnable() {
                public void run() {
                    try {
                        rd.get();
                    } catch (InterruptedException e) {

                        e.printStackTrace();
                    }
                }
            }).start();
        }

        // 创建10个生产者线程
        for (int i = 0; i < 10; i++) {
            new Thread(new Runnable() {
                public void run() {
                    try {
                        rd.put("bread");
                    } catch (InterruptedException e) {

                        e.printStackTrace();
                    }
                }
            }).start();
        }

    }

}
-------------
缓存数据读取完毕.暂停读取
写入数据
超出缓存容量.暂停写入.
超出缓存容量.暂停写入.
超出缓存容量.暂停写入.
超出缓存容量.暂停写入.
超出缓存容量.暂停写入.
读取数据
缓存数据读取完毕.暂停读取
写入数据
超出缓存容量.暂停写入.
超出缓存容量.暂停写入.
超出缓存容量.暂停写入.
生产者线程被唤醒
超出缓存容量.暂停写入.
读取数据
缓存数据读取完毕.暂停读取
生产者线程被唤醒
写入数据
```

通过Condition与Lock结合运用写出来的代码，相对notifyAll()来说更加易读。阻塞，唤醒，同步读写的过程，体现的更加清楚了。



# 

## 共享模式原理（`Semaphore`、`CountDownLatch`,）

他们内部都继承了AQS并实现了`tryAcquireShared`,`tryReleaseShared`方法

### 共享模式逻辑

线程调用`acquireShared`方法获取锁
如果失败则创建共享类型的节点放入FIFO队列，等待唤醒
有线程释放锁后唤醒队列最前端的节点，然后唤醒所有后面的共享节点

### AQS acquireShared方法

acquireShared方法是AQS共享模式的入口