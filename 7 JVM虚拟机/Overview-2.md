# 一、简介

## 1.1 简单的程序

现在我有一个JavaBean：

```java
public class Java3y {
    // 姓名
    private String name;
    // 年龄
    private int age;
    //.....各种get/set方法/toString
}
```

一个测试类：

```java
public class Java3yTest {
    public static void main(String[] args) {
        Java3y java3y = new Java3y();
        java3y.setName("Java3y");
        System.out.println(java3y);
    }
}
```

我们在初学的时候肯定用过 javac 来编译 .java 文件代码，用过 java 命令来执行编译后生成的 .class 文件。

在使用IDE点击运行的时候其实就是将这两个命令结合起来了(编译并运行)，方便我们开发。

## 1.2 编译过程

.java 文件是由 Java 源码编译器(上述所说的 javac.exe)来完成，流程图如下所示：

![img](https://segmentfault.com/img/remote/1460000015605338?w=720&h=203)

Java源码编译由以下三个过程组成：

- 分析和输入到符号表
- 注解处理
- 语义分析和生成class文件

![img](https://segmentfault.com/img/remote/1460000015605339?w=1482&h=210)

**编译时期-语法糖**

语法糖可以看做是编译器实现的一些“小把戏”，这些“小把戏”可能会使得效率“大提升”。

最值得说明的就是**泛型**了，这个语法糖可以说我们是经常会使用到的！

泛型只会在Java源码中存在，编译过后会被替换为原来的原生类型（Raw Type，也称为裸类型）了。这个过程也被称为：**泛型擦除**。

有了泛型这颗语法糖以后：

- 代码更加简洁【不用强制转换】
- 程序更加健壮【只要编译时期没有警告，那么运行时期就不会出现ClassCastException异常】
- 可读性和稳定性【在编写集合的时候，就限定了类型】

## 1.3 实现跨平台

至此，我们通过 javac.exe 编译器编译我们的 .java 源代码文件生成出 class 文件了！

class 文件很明显是不能直接运行的，它不像C语言(编译cpp后生成exe文件直接运行)

class 文件是交由 JVM 来解析运行！

- JVM 是运行在操作系统之上的，每个操作系统的指令是不同的，而 JDK 是区分操作系统的，只要你的本地系统装了 JDK，这个 JDK 就是能够和当前系统兼容的。
- 而class字节码运行在 JVM 之上，所以不用关心class字节码是在哪个操作系统编译的，只要符合JVM规范，那么，这个字节码文件就是可运行的。
- 所以 Java 就做到了跨平台：一次编译，到处运行！

## 1.4 class 文件和 JVM

### 1. 类的加载时机

生成的两个 class 文件都会直接被加载到JVM中吗？

虚拟机规范则是严格规定了有且只有5种情况必须**立即对类进行“初始化”**(class文件加载到JVM中)：

- 创建类的实例(new 的方式)。访问某个类或接口的静态变量，或者对该静态变量赋值，调用类的静态方法
- 反射的方式
- 初始化某个类的子类，则其父类也会被初始化
- Java虚拟机启动时被标明为启动类的类，直接使用java.exe命令来运行某个主类（包含main方法的那个类）
- 当使用JDK1.7的动态语言支持时(....)

所以说：Java类的加载是动态的，它并不会一次性将所有类全部加载后再运行，而是保证程序运行的基础类(像是基类)完全加载到jvm中，至于其他类，**则在需要的时候才加载**。这当然就是为了**节省内存开销**。

### 2. 如何将类加载到jvm

class文件是通过**类的加载器**装载到jvm中的！

Java**默认有三种类加载器**：

![img](https://segmentfault.com/img/remote/1460000015605342?w=386&h=384)

各个加载器的工作责任：

- **Bootstrap ClassLoader**：负责加载 $JAVA_HOME中jre/lib/rt.jar 里所有的class，由C++实现，不是ClassLoader子类
- **Extension ClassLoader**：负责加载java平台中扩展功能的一些jar包，包括$JAVA_HOME中jre/lib/ext/*.jar或-Djava.ext.dirs指定目录下的jar包
- **App ClassLoader**：负责记载classpath中指定的jar包及目录中class

工作过程：

1. 当AppClassLoader加载一个class时，它首先不会自己去尝试加载这个类，而是把类加载请求委派给父类加载器ExtClassLoader去完成。
2. 当ExtClassLoader加载一个class时，它首先也不会自己去尝试加载这个类，而是把类加载请求委派给BootStrapClassLoader去完成。
3. 如果BootStrapClassLoader加载失败（例如在$JAVA_HOME/jre/lib里未查找到该class），会使用ExtClassLoader来尝试加载；
4. 若ExtClassLoader也加载失败，则会使用AppClassLoader来加载
5. 如果AppClassLoader也加载失败，则会报出异常ClassNotFoundException

其实这就是所谓的**双亲委派模型**。简单来说：如果一个类加载器收到了类加载的请求，它首先不会自己去尝试加载这个类，而是把**请求委托给父加载器去完成，依次向上**。

**好处**：防止内存中出现多份同样的字节码(安全性角度)

特别说明：类加载器在成功加载某个类之后，会把得到的 java.lang.Class 类的实例缓存起来。下次再请求加载该类的时候，类加载器会直接使用缓存的类的实例，而不会尝试再次加载。

### 3. 类加载详细过程

加载器加载到jvm中，接下来其实又分了好几个步骤：

- 加载：查找并加载类的二进制数据，在Java堆中也创建一个java.lang.Class类的对象。
- 连接：连接又包含三块内容：验证、准备、初始化。
  - 验证：文件格式、元数据、字节码、符号引用验证；
  - 准备：为类的静态变量分配内存，并将其初始化为默认值；
  - 解析：把类中的符号引用转换为直接引用

- 初始化：为类的静态变量赋予正确的初始值。

![img](https://segmentfault.com/img/remote/1460000015605343?w=694&h=225)

### 4. JIT即时编辑器

一般我们可能会想：JVM在加载了这些class文件以后，针对这些字节码，逐条取出，逐条执行 --> 解析器解析。

但如果是这样的话，那就太慢了！

我们的JVM是这样实现的：

- 就是把这些Java字节码重新编译优化，生成机器码，让CPU直接执行。这样编出来的代码效率会更高。
- 编译也是要花费时间的，我们一般对热点代码做编译，非热点代码直接解析就好了

**热点代码**：一、多次调用的方法。二、多次执行的循环体

使用热点探测来检测是否为热点代码，热点探测有两种方式：

- 采样
- 计数器

目前HotSpot使用的是计数器的方式，它为每个方法准备了两类计数器：

- 方法调用计数器（Invocation  Counter）
- 回边计数器（Back  EdgeCounter）。

在确定虚拟机运行参数的前提下，这两个计数器都有一个确定的阈值，**当计数器超过阈值溢出了，就会触发JIT编译**。

![img](https://segmentfault.com/img/remote/1460000015605344?w=1389&h=967)

## 1.5 类加载完以后JVM干了什么

在类加载检查通过后，接下来虚拟机**将为新生对象分配内存**。

### 1. 内存结构

首先我们来了解一下JVM的内存结构的怎么样的：基于jdk1.8画的JVM的内存结构

![å¾çæè¿°](https://segmentfault.com/img/bVbibas?w=1188&h=698)

- 堆：**存放对象实例**，几乎所有的对象实例都在这里分配内存
- 虚拟机栈：虚拟机栈描述的是**Java方法执行的内存结构**：每个方法被执行的时候都会同时创建一个**栈帧**（Stack Frame）用于存储局部变量表、操作栈、动态链接、方法出口等信息
- 本地方法栈：本地方法栈则是为虚拟机使用到的**Native方法服务**。
- 方法区：存储已**被虚拟机加载的类元数据信息**(元空间)
- 程序计数器：当前线程所执行的字节码的**行号指示器**

### 2. 流程

![img](https://segmentfault.com/img/remote/1460000015605347?w=720&h=361)

**宏观简述**一下我们的例子中的工作流程：

- 1、通过`java.exe`运行`Java3yTest.class`，随后被加载到JVM中，**元空间存储着类的信息**(包括类的名称、方法信息、字段信息..)。
- 2、然后JVM找到Java3yTest的主函数入口(main)，为main函数创建栈帧，开始执行main函数
- 3、main函数的第一条命令是`Java3y java3y = new Java3y();`就是让JVM创建一个Java3y对象，但是这时候方法区中没有Java3y类的信息，所以JVM马上加载Java3y类，把Java3y类的类型信息放到方法区中(元空间)
- 4、加载完Java3y类之后，Java虚拟机做的第一件事情就是在堆区中为一个新的Java3y实例分配内存, 然后调用构造函数初始化Java3y实例，这个**Java3y实例持有着指向方法区的Java3y类的类型信息**（其中包含有方法表，java动态绑定的底层实现）的引用
- 5、当使用`java3y.setName("Java3y");`的时候，JVM**根据java3y引用找到Java3y对象**，然后根据Java3y对象持有的引用定位到方法区中Java3y类的类型信息的**方法表**，获得`setName()`函数的字节码的地址
- 6、为`setName()`函数创建栈帧，开始运行`setName()`函数

从微观上其实还做了很多东西，正如上面所说的**类加载过程**（加载-->连接(验证，准备，解析)-->初始化)，在类加载完之后jvm**为其分配内存**(分配内存中也做了非常多的事)。由于这些步骤并不是一步一步往下走，会有很多的“混沌bootstrap”的过程，所以很难描述清楚。

## 1.6 各种常量池

### 1. 各个常量池的情况

针对于jdk1.7之后：

- 运行时常量池位于**堆中**
- 字符串常量池位于**堆中**

常量池存储的是：

- 字面量(Literal)：文本字符串等---->用双引号引起来的字符串字面量都会进这里面
- 符号引用(Symbolic References)
  - 类和接口的全限定名(Full Qualified Name)
  - 字段的名称和描述符(Descriptor)
  - 方法的名称和描述符

常量池（Constant Pool Table），用于存放编译期生成的各种字面量和符号引用，这部分内容将在**类加载后进入方法区的运行时常量池中存放**

现在我们的运行时常量池只是换了一个位置(原本来方法区，现在在堆中),但可以明确的是：**类加载后，常量池中的数据会在运行时常量池中存放**！

常量池：是Class文件中的内容，还不是运行时的内容，不要理解它是个池子，其实就是Class文件中的字节码指令

字符串常量池：HotSpot VM里，记录interned string的一个全局表叫做StringTable，它本质上就是个HashSet\<String>。注意**它只存储对java.lang.String实例的引用，而不存储String对象的内容**

**字符串常量池只存储引用，不存储内容**！

再来看一下我们的intern方法：

- **如果常量池中存在当前字符串，那么直接返回常量池中它的引用**。
- **如果常量池中没有此字符串, 会将此字符串引用保存到常量池中后, 再直接返回该字符串的引用**！

### 2. 解析题目

```java
public static void main(String[] args) {
    String s = new String("1");
    s.intern();
    String s2 = "1";
    System.out.println(s == s2);// false
}
```

第一句：`String s = new String("1");`

![img](https://segmentfault.com/img/remote/1460000015616629?w=1061&h=613)

第二句：`s.intern();`发现字符串常量池中已经存在"1"字符串对象，直接**返回字符串常量池中对堆的引用(但没有接收)**-->此时s引用还是指向着堆中的对象

![img](https://segmentfault.com/img/remote/1460000015616630?w=1061&h=613)

第三句：`String s2 = "1";`发现字符串常量池**已经保存了该对象的引用**了，直接返回字符串常量池对堆中字符串的引用

![img](https://segmentfault.com/img/remote/1460000015616631?w=1179&h=656)

很容易看到，**两条引用是不一样的！所以返回false**。

```java
public static void main(String[] args) {
    String s3 = new String("1") + new String("1");
    s3.intern();
    String s4 = "11";
    System.out.println(s3 == s4); // true
}
```

第一句：`String s3 = new String("1") + new String("1");`注意：此时**"11"对象并没有在字符串常量池中保存引用**。

![img](https://segmentfault.com/img/remote/1460000015616632?w=1075&h=634)

第二句：`s3.intern();`发现"11"对象**并没有在字符串常量池中**，于是将"11"对象在字符串常量池中**保存当前字符串的引用**，并**返回**当前字符串的引用(但没有接收)

![img](https://segmentfault.com/img/remote/1460000015616633?w=1107&h=621)

第三句：`String s4 = "11";`发现字符串常量池已经存在引用了，直接返回(**拿到的也是与s3相同指向的引用**)

![img](https://segmentfault.com/img/remote/1460000015616634?w=1126&h=627)

根据上述所说的：最后会返回true~~~

```java
public static void main(String[] args) {
    String s = new String("1");
    String s2 = "1";
    s.intern();
    System.out.println(s == s2);//false

    String s3 = new String("1") + new String("1");
    String s4 = "11";
    s3.intern();
    System.out.println(s3 == s4);//false
}
```

```java
 public static void main(String[] args) {
     String s1 = new String("he") + new String("llo");
     String s2 = new String("h") + new String("ello");
     String s3 = s1.intern();
     String s4 = s2.intern();
     System.out.println(s1 == s3);// true
     System.out.println(s1 == s4);// true
}
```

## 1.7 GC垃圾回收

在C++中，我们知道创建出的对象是需要手动去delete掉的。我们Java程序运行在JVM中，JVM可以帮我们“自动”回收不需要的对象，对我们来说是十分方便的。

首先，JVM回收的是**垃圾**，垃圾就是我们程序中已经是不需要的了。垃圾收集器在对堆进行回收前，第一件事情就是要确定这些对象之中哪些还“存活”着，**哪些已经“死去”**。判断哪些对象“死去”常用有两种方式：

- 引用计数法-->这种难以解决对象之间的循环引用的问题
- **可达性分析算法**-->主流的JVM采用的是这种方式

![img](https://segmentfault.com/img/remote/1460000015605354?w=808&h=569)

现在已经可以判断哪些对象已经“死去”了，我们现在要对这些“死去”的对象进行回收，回收也有好几种算法：

- 标记-清除算法
- 复制算法
- 标记-整理算法
- 分代收集算法

无论是可达性分析算法，还是垃圾回收算法，JVM使用的都是**准确式GC**。JVM是使用一组称为**OopMap**的数据结构，来存储所有的对象引用(这样就不用遍历整个内存去查找了，空间换时间)。
并且不会将所有的指令都生成OopMap，只会在**安全点**上生成OopMap，在**安全区域**上开始GC。

在OopMap的协助下，HotSpot可以**快速且准确地**完成GC Roots枚举（可达性分析）。

上面所讲的垃圾收集算法只能算是**方法论**，落地实现的是**垃圾收集器**：

- Serial收集器
- ParNew收集器
- Parallel Scavenge收集器
- Serial Old收集器
- Parallel Old收集器
- CMS收集器
- G1收集器

上面这些收集器大部分是可以互相**组合使用**的

![img](https://segmentfault.com/img/remote/1460000015605355?w=719&h=696)



# 二、面试题

## 1. 详细jvm内存结构

根据 JVM 规范，JVM 内存共分为虚拟机栈、堆、方法区、程序计数器、本地方法栈五个部分。

![img](https://segmentfault.com/img/remote/1460000015605356?w=1108&h=546)

具体可能会聊聊jdk1.7以前的PermGen（永久代），替换成Metaspace（元空间）

- 原本永久代存储的数据：符号引用(Symbols)转移到了native heap；字面量(interned strings)转移到了java heap；类的静态变量(class statics)转移到了java heap
- Metaspace（元空间）存储的是类的元数据信息（metadata）
- 元空间的本质和永久代类似，都是**对JVM规范中方法区的实现**。不过元空间与永久代之间最大的区别在于：**元空间并不在虚拟机中，而是使用本地内存**。
- **替换的好处**：一、字符串存在永久代中，容易出现性能问题和内存溢出。二、永久代会为 GC 带来不必要的复杂度，并且回收效率偏低

![img](https://segmentfault.com/img/remote/1460000015605357?w=589&h=403)

## 2. 讲讲什么情况下回出现内存溢出，内存泄漏？

内存泄漏的原因：

- **对象是可达的**(一直被引用)
- 但是对象**不会被使用**

常见的内存泄漏例子：

```java
public static void main(String[] args) {
    Set set = new HashSet();
    for (int i = 0; i < 10; i++) {
        Object object = new Object();
        set.add(object);
        // 设置为空，这对象我不再用了
        object = null;
    }
    // 但是set集合中还维护这obj的引用，gc不会回收object对象
    System.out.println(set);
}
```

解决这个内存泄漏问题也很简单，将set设置为null，那就可以避免上述内存泄漏问题了。其他内存泄漏得一步一步分析了。

内存溢出的原因：

- 内存泄露导致堆栈内存不断增大，从而引发内存溢出。
- 大量的jar，class文件加载，装载类的空间不够，溢出
- 操作大量的对象导致堆内存空间已经用满了，溢出
- nio直接操作内存，内存过大导致溢出

解决：

- 查看程序是否存在内存泄漏的问题
- 设置参数加大空间
- 代码中是否存在死循环或循环产生过多重复的对象实体、
- 查看是否使用了nio直接操作内存。

## 3. 说说线程栈

JVM规范让**每个Java线程**拥有自己的**独立的JVM栈**，也就是Java方法的调用栈。

当方法调用的时候，会生成一个**栈帧**。栈帧是保存在虚拟机栈中的，栈帧存储了方法的**局部变量表、操作数栈**、动态连接和方法返回地址等信息

线程运行过程中，**只有一个栈帧是处于活跃状态**，称为“当前活跃栈帧”，当前活动栈帧始终是虚拟机栈的**栈顶元素**。

通过**jstack**工具查看线程状态

## 4. JVM 年轻代到年老代的晋升过程的判断条件是什么呢？

1. 部分对象会在From和To区域中复制来复制去,**如此交换15次**(由JVM参数MaxTenuringThreshold决定,这个参数默认是15),最终如果还是存活,就存入到老年代。
2. 如果**对象的大小大于Eden的二分之一会直接分配在old**，如果old也分配不下，会做一次majorGC，如果小于eden的一半但是没有足够的空间，就进行minorgc也就是新生代GC。
3. minor gc后，survivor仍然放不下，则放到老年代
4. 动态年龄判断 ，大于等于某个年龄的对象超过了survivor空间一半 ，大于等于某个年龄的对象直接进入老年代

## 5. JVM 出现 fullGC 很频繁，怎么去线上排查问题

这题就依据full GC的触发条件来做：

- 如果有perm gen的话(jdk1.8就没了)，**要给perm gen分配空间，但没有足够的空间时**，会触发full gc。

  ​	所以看看是不是perm gen区的值设置得太小了。

- `System.gc()`方法的调用

  ​    这个一般没人去调用吧~~~

-  当**统计**得到的Minor GC晋升到旧生代的平均大小**大于老年代的剩余空间**，则会触发full gc(这就可以从多个角度上看了)

  是不是**频繁创建了大对象(也有可能eden区设置过小)**(大对象直接分配在老年代中，导致老年代空间不足--->从而频繁gc)
  是不是**老年代的空间设置过小了**(Minor GC几个对象就大于老年代的剩余空间了)

## 6. 类加载为什么要使用双亲委派模式，有没有什么场景是打破了这个模式？

双亲委托模型的重要用途是为了解决类载入过程中的**安全性问题**。

- 假设有一个开发者自己编写了一个名为`java.lang.Object`的类，想借此欺骗JVM。现在他要使用自定义`ClassLoader`来加载自己编写的`java.lang.Object`类。
- 然而幸运的是，双亲委托模型不会让他成功。因为JVM会优先在`Bootstrap ClassLoader`的路径下找到`java.lang.Object`类，并载入它

Java的类加载是否一定遵循双亲委托模型？

- 在实际开发中，我们可以**通过自定义ClassLoader，并重写父类的loadClass方法**，来打破这一机制。
- SPI就是打破了双亲委托机制的(SPI：服务提供发现)。

## 7. 类的实例化顺序

- 父类静态成员和静态初始化块 ，按在代码中出现的顺序依次执行
- 子类静态成员和静态初始化块 ，按在代码中出现的顺序依次执行
- 父类实例成员和实例初始化块 ，按在代码中出现的顺序依次执行
- 父类构造方法
- 子类实例成员和实例初始化块 ，按在代码中出现的顺序依次执行
- 子类构造方法

```java
class Dervied extends Base {
    private String name = "Java3y";
    public Dervied() {
        tellName2();
        printName2();
    }
    public void tellName2() {
        System.out.println("Dervied tell name: " + name);
    }
    public void printName2() {
        System.out.println("Dervied print name: " + name);
    }
    public static void main(String[] args) {
        new Dervied();
    }
}

class Base {
    private String name = "公众号";
    public Base() {
        tellName1();
        printName1();
    }
    public void tellName1() {
        System.out.println("Base tell name: " + name);
    }
    public void printName1() {
        System.out.println("Base print name: " + name);
    }
}
```

输出数据：

```java
Base tell name: 公众号
Base print name: 公众号
Dervied tell name: Java3y
Dervied print name: Java3y
```

## 8. JVM垃圾回收机制，何时触发MinorGC等操作

当young gen中的eden区分配满的时候触发MinorGC(新生代的空间不够放的时候).

## 9. JVM 中一次完整的 GC 流程（从 ygc 到 fgc）是怎样的

- YGC ：**对新生代堆进行gc**。频率比较高，因为大部分对象的存活寿命较短，在新生代里被回收。性能耗费较小。
- FGC ：**全堆范围的gc**。默认堆空间使用到达80%(可调整)的时候会触发fgc。以我们生产环境为例，一般比较少会触发fgc，有时10天或一周左右会有一次。

什么时候执行YGC和FGC

- eden空间不足,执行 young gc
- old空间不足，perm空间不足，调用方法`System.gc()` ，ygc时的悲观策略, dump live的内存信息时(jmap –dump:live)，都会执行full gc

## 10. 各种回收算法

GC最基础的算法有三种：

- 标记 -清除算法
- 复制算法
- 标记-压缩算法
- 我们常用的垃圾回收器一般都采用**分代收集算法**(其实就是组合上面的算法，不同的区域使用不同的算法)。

具体：

- 标记-清除算法，“标记-清除”（Mark-Sweep）算法，如它的名字一样，算法分为“标记”和“清除”两个阶段：首先标记出所有需要回收的对象，在标记完成后统一回收掉所有被标记的对象。
- 复制算法，“复制”（Copying）的收集算法，它将可用内存按容量划分为大小相等的两块，每次只使用其中的一块。当这一块的内存用完了，就将还存活着的对象复制到另外一块上面，然后再把已使用过的内存空间一次清理掉。
- 标记-压缩算法，标记过程仍然与“标记-清除”算法一样，但后续步骤不是直接对可回收对象进行清理，而是让所有存活的对象都向一端移动，然后直接清理掉端边界以外的内存
- 分代收集算法，“分代收集”（Generational Collection）算法，把Java堆分为新生代和老年代，这样就可以根据各个年代的特点采用最适当的收集算法。

## 11. 各种回收器，各自优缺点，重点CMS、G1

图中**两个收集器之间有连线，说明它们可以配合使用**.

![img](https://segmentfault.com/img/remote/1460000015605359?w=508&h=527)

- Serial收集器，串行收集器是最古老，**最稳定以及效率高的收集器**，但可能会产生**较长的停顿**，只使用一个线程去回收。
- ParNew收集器，ParNew收集器其实就是Serial收集器的**多线程版本**。
- Parallel收集器，Parallel Scavenge收集器类似ParNew收集器，Parallel收集器**更关注系统的吞吐量**。
- Parallel Old收集器，Parallel Old是Parallel Scavenge收集器的老年代版本，使用多线程“标记－整理”算法
- CMS收集器，CMS（Concurrent Mark Sweep）收集器是一种以**获取最短回收停顿时间**为目标的收集器。它需要**消耗额外的CPU和内存资源**，在CPU和内存资源紧张，CPU较少时，会加重系统负担。CMS**无法处理浮动垃圾**。CMS的“标记-清除”算法，会导致大量**空间碎片的产生**。
- G1收集器，G1 (Garbage-First)是一款面向服务器的垃圾收集器,主要针对配备多颗处理器及大容量内存的机器. **以极高概率满足GC停顿时间要求的同时,还具备高吞吐量性能特征**。

## 12. stackoverflow错误，permgen space错误

stackoverflow错误主要出现在虚拟机栈中(线程请求的栈深度大于虚拟机栈锁允许的最大深度)

permgen space错误(针对jdk之前1.7版本)：

- 大量加载class文件
- 常量池内存溢出