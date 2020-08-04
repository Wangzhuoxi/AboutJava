# 一、概述

## 1 按操作方式分类

![按操作方式分类结构图：](https://user-gold-cdn.xitu.io/2018/5/16/16367d4fd1ce1b46?w=720&h=1080&f=jpeg&s=69522)

**字节流和字符流：**

- 字节流：以字节为单位，每次次读入或读出是8位数据。可以读任何类型数据。
- 字符流：以字符为单位，每次次读入或读出是16位数据。其只能读取字符类型数据。

**输出流和输入流：**

- 输出流：从内存读出到文件。只能进行写操作。
- 输入流：从文件读入到内存。只能进行读操作。

**注意：** 这里的出和入，都是相对于系统内存而言的。

**节点流和处理流：**

- 节点流：直接与数据源相连，读入或读出。
- 处理流：与节点流一块使用，在节点流的基础上，再套接一层，套接在节点流上的就是处理流。

**为什么要有处理流？**直接使用节点流，读写不方便，为了更快的读写文件，才有了处理流。



**分类说明：**

**1） 输入字节流InputStream**：

- **ByteArrayInputStream、StringBufferInputStream、FileInputStream** 是三种基本的介质流，它们分别从Byte 数组、StringBuffer、和本地文件中读取数据。
- **PipedInputStream** 是从与其它线程共用的管道中读取数据。PipedInputStream的一个实例要和PipedOutputStream的一个实例共同使用，共同完成管道的读取写入操作。主要用于线程操作。
- **DataInputStream**： 将基础数据类型读取出来
- **ObjectInputStream** 和所有 **FilterInputStream** 的子类都是装饰流（装饰器模式的主角）。

**2）输出字节流OutputStream：**

- **ByteArrayOutputStream**、**FileOutputStream**： 是两种基本的介质流，它们分别向- Byte 数组、和本地文件中写入数据。
- **PipedOutputStream** 是向与其它线程共用的管道中写入数据。
- **DataOutputStream** 将基础数据类型写入到文件中
- **ObjectOutputStream** 和所有 **FilterOutputStream** 的子类都是装饰流。

**节流的输入和输出类结构图：**

![img](https://mmbiz.qpic.cn/mmbiz_jpg/hvUCbRic69sDYrnicZ2Ykczp7I69GbFp2XUacOvJ9pQtTnXe9snLkv5npIGZnhphFRW0MNedet3xwxA24cWga8gw/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

**3）字符输入流Reader：：**

- **FileReader、CharReader、StringReader** 是三种基本的介质流，它们分在本地文件、Char 数组、String中读取数据。
- **PipedReader**：是从与其它线程共用的管道中读取数据
- **BufferedReader** ：加缓冲功能，避免频繁读写硬盘
- **InputStreamReader**： 是一个连接字节流和字符流的桥梁，它将字节流转变为字符流。

**4）字符输出流Writer：**

- **StringWriter**:向String 中写入数据。
- **CharArrayWriter**：实现一个可用作字符输入流的字符缓冲区
- **PipedWriter**:是向与其它线程共用的管道中写入数据
- **BufferedWriter** ： 增加缓冲功能，避免频繁读写硬盘。
- **PrintWriter** 和**PrintStream** 将对象的格式表示打印到文本输出流。 极其类似，功能和使用也非常相似
- **OutputStreamWriter**： 是OutputStream 到Writer 转换的桥梁，它的子类FileWriter 其实就是一个实现此功能的具体类（具体可以研究一SourceCode）。功能和使用和OutputStream 极其类似，后面会有它们的对应图。

**字符流的输入和输出类结构图：**

![img](https://mmbiz.qpic.cn/mmbiz_jpg/hvUCbRic69sDYrnicZ2Ykczp7I69GbFp2XbvPniczIeTaicxVLrlxCicMaltvmLAhzib9Y8cmrc6231Ya0UXoCIiboxPA/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



## 2 按操作对象分类

![按操作对象分类结构图](https://user-gold-cdn.xitu.io/2018/5/16/16367d673b0e268d?w=720&h=535&f=jpeg&s=46081)



**分类说明**：

**对文件进行操作（节点流）：**

- FileInputStream（字节输入流），
- FileOutputStream（字节输出流），
- FileReader（字符输入流），
- FileWriter（字符输出流）

**对管道进行操作（节点流）：**

- PipedInputStream（字节输入流）,
- PipedOutStream（字节输出流），
- PipedReader（字符输入流），
- PipedWriter（字符输出流）。
- PipedInputStream的一个实例要和PipedOutputStream的一个实例共同使用，共同完成管道的读取写入操作。主要用于线程操作。

**字节/字符数组流（节点流）：**

- ByteArrayInputStream，
- ByteArrayOutputStream，
- CharArrayReader，
- CharArrayWriter；

**除了上述三种是节点流，其他都是处理流，需要跟节点流配合使用。**

**Buffered缓冲流（处理流）：**带缓冲区的处理流，缓冲区的作用的主要目的是：避免每次和硬盘打交道，提高数据访问的效率。

- BufferedInputStream，
- BufferedOutputStream，
- BufferedReader,
- BufferedWriter,

**转化流（处理流）：**

- InputStreamReader：把字节转化成字符；
- OutputStreamWriter：把字节转化成字符。

**基本类型数据流（处理流）：用于操作基本数据类型值。**

因为平时若是我们输出一个8个字节的long类型或4个字节的float类型，那怎么办呢？可以一个字节一个字节输出，也可以把转换成字符串输出，但是这样转换费时间，若是直接输出该多好啊，因此这个数据流就解决了我们输出数据类型的困难。数据流可以直接输出float类型或long类型，提高了数据读写的效率。

- DataInputStream，
- DataOutputStream。

**打印流（处理流）：**

一般是打印到控制台，可以进行控制打印的地方。

- PrintStream，
- PrintWriter，

**对象流（处理流）：**

把封装的对象直接输出，而不是一个个在转换成字符串再输出。

- ObjectInputStream，对象反序列化；
- ObjectOutputStream，对象序列化；

**合并流（处理流）：**

- SequenceInputStream：可以认为是一个工具类，将两个或者多个输入流当成一个输入流依次读取。



# 二、学习总结

## 1 概念

java的 io 是实现输入和输出的基础，可以方便的实现数据的输入和输出操作。在java中把不同的输入/输出源（键盘，文件，网络连接等）抽象表述为“流”(stream)。通过流的形式允许java程序使用相同的方式来访问不同的输入/输出源。stream是从起源（source）到接收的（sink）的有序数据。

java把所有的传统的流类型都放到在java io包下，用于实现输入和输出功能。

## 2 分类

### 2.1 按照流的流向分

- 输入流： 只能从中读取数据，而不能向其写入数据。
- 输出流：只能向其写入数据，而不能向其读取数据。

java的输入流主要是 InputStream 和 Reader 作为基类，而输出流则是主要由 outputStream 和 Writer 作为基类。它们都是一些抽象基类，无法直接创建实例。

### 2.2 按照操作单元划分

字节流和字符流的用法几乎完成全一样，区别在于字节流和字符流所操作的数据单元不同，字节流操作的单元是数据单元是8位的字节，字符流操作的是数据单元为16位的字符。

字节流主要是由 InputStream 和 outputStream 作为基类，而字符流则主要有 Reader 和 Writer 作为基类。

### 2.3 按照流的角色划分

可以从/向一个特定的IO设备（如磁盘，网络）读/写数据的流，称为**节点流**。节点流也被称为低级流。

当使用节点流进行输入和输出时，程序直接连接到实际的数据源，和实际的输入/输出节点连接。 

**处理流**则用于对一个已存在的流进行连接和封装，通过封装后的流来实现数据的读/写功能。处理流也被称为高级流。

当使用处理流进行输入/输出时，程序并不会直接连接到实际的数据源，没有和实际的输入和输出节点连接。使用处理流的一个明显的好处是，只要使用相同的处理流，程序就可以采用完全相同的输入/输出代码来访问不同的数据源，随着处理流所包装的节点流的变化，程序实际所访问的数据源也相应的发生变化。

## 3 原理

 java Io流共涉及40多个类，这些类看上去很杂乱，但实际上很有规则，而且彼此之间存在非常紧密的联系， Java Io流的40多个类都是从如下4个抽象类基类中派生出来的。

- **InputStream/Reader**：所有的输入流的基类，前者是字节输入流，后者是字符输入流。
- **OutputStream/Writer**:：所有输出流的基类，前者是字节输出流，后者是字符输出流。



对于InputStream和Reader而言，它们把输入设备抽象成为一个”水管“，这个水管的每个“水滴”依次排列。

字节流和字符流的处理方式其实很相似，只是它们处理的输入/输出单位不同而已。输入流使用隐式的记录指针来表示当前正准备从哪个“水滴”开始读取，每当程序从InputStream或者Reader里面取出一个或者多个“水滴”后，记录指针自定向后移动；除此之外，InputStream和Reader里面都提供了一些方法来控制记录指针的移动。

对于OutputStream和Writer而言，它们同样把输出设备抽象成一个”水管“，只是这个水管里面没有任何水滴。

当执行输出时，程序相当于依次把“水滴”放入到输出流的水管中，输出流同样采用隐示指针来标识当前水滴即将放入的位置，每当程序向OutputStream或者Writer里面输出一个或者多个水滴后，记录指针自动向后移动。

Java的处理流模型则体现了Java输入和输出流设计的灵活性。处理流的功能主要体现在以下两个方面。

- 性能的提高：主要以增加缓冲的方式来提供输入和输出的效率。
- 操作的便捷：处理流可能提供了一系列便捷的方法来一次输入和输出大批量的内容，而不是输入/输出一个或者多个“水滴”。

处理流可以“嫁接”在任何已存在的流的基础之上，这就允许Java应用程序采用相同的代码，透明的方式来访问不同的输入和输出设备的数据流。

## 4 分类表

|    分类    |        字节输出流         |        字节输入流        |     字符输入流      |     字符输出流      |
| :--------: | :-----------------------: | :----------------------: | :-----------------: | :-----------------: |
|  抽象基类  |      *OutputStream*       |      *InputStream*       |      *Reader*       |      *Writer*       |
|  访问文件  |   **FileOutputStream**    |   **FileInputStream**    |   **FileReader**    |   **FileWriter**    |
|  访问数组  | **ByteArrayOutputStream** | **ByteArrayInputStream** | **CharArrayReader** | **CharArrayWriter** |
|  访问管道  |   **PipedOutputStream**   |   **PipedInputStream**   |   **PipedReader**   |   **PipedWriter**   |
| 访问字符串 |                           |                          |  **StringReader**   |  **StringWriter**   |
|   缓冲流   |   BufferedOutputStream    |   BufferedInputStream    |   BufferedReader    |   BufferedWriter    |
|   转换流   |                           |                          |  InputStreamReader  | OutputStreamWriter  |
|   对象流   |     ObjectInputStream     |    ObjectOutputStream    |                     |                     |
|  抽象基类  |    *FilterInputStream*    |   *FilterOutputStream*   |   *FilterReader*    |   *FilterWriter*    |
|   打印流   |                           |       PrintStream        |                     |     PrintWriter     |
| 推回输入流 |    PushbackInputStream    |                          |   PushbackReader    |                     |
|   特殊流   |      DataInputStream      |     DataOutputStream     |                     |                     |

**注**：表中粗体字所标出的类代表节点流，必须直接与指定的物理节点关联：斜体字标出的类代表抽象基类，无法直接创建实例。

## 5 NIO与传统IO的区别

我们使用InputStream从输入流中读取数据时，如果没有读取到有效的数据，程序将在此处阻塞该线程的执行。其实传统的输入里和输出流都是阻塞式的进行输入和输出。 不仅如此，传统的输入流、输出流都是通过字节的移动来处理的（即使我们不直接处理字节流，但底层实现还是依赖于字节处理），也就是说，面向流的输入和输出一次只能处理一个字节，因此面向流的输入和输出系统效率通常不高。 

从JDk1.4开始，java提供了一系列改进的输入和输出处理的新功能，这些功能被统称为新IO(NIO)。新增了许多用于处理输入和输出的类，这些类都被放在java.nio包及其子包下，并且对原io的很多类都以NIO为基础进行了改写。新增了满足NIO的功能。 

NIO采用了内存映射对象的方式来处理输入和输出，NIO将文件或者文件的一块区域映射到内存中，这样就可以像访问内存一样来访问文件了。通过这种方式来进行输入/输出比传统的输入和输出要快的多。

**JDk1.4使用NIO改写了传统Io后，传统Io的读写速度和NIO差不了太多。**

## 6 正确使用Io流

​	了解了java io的整体类结构和每个类的一下特性后，我们可以在开发的过程中根据需要灵活的使用不同的Io流进行开发。下面是我整理2点原则:

- 如果是操作二进制文件那我们就使用字节流，如果操作的是文本文件那我们就使用字符流。
- 尽可能的多使用处理流，这会使我们的代码更加灵活，复用性更好。



# 三、常用IO流用法

## 1 IO体系的基类

字节流和字符流的操作方式基本一致，只是操作的数据单元不同——字节流的操作单元是字节，字符流的操作单元是字符。所以字节流和字符流就整理在一起了。

**InputStream/Reader**

InputStream和Reader是所有输入流的抽象基类，本身并不能创建实例来执行输入，但它们将成为所有输入流的模板，所以它们的方法是所有输入流都可使用的方法。

在InputStream里面包含如下3个方法。

```java
int read(); //从输入流中读取单个字节（相当于从水管中取出一滴水），返回所读取的字节数据（字节数据可直接转换为int类型）。

int read(byte[] b); //从输入流中最多读取b.length个字节的数据，并将其存储在字节数组b中，返回实际读取的字节数。

int read(byte[] b,int off,int len); //从输入流中最多读取len个字节的数据，并将其存储在数组b中，放入数组b中时，并不是从数组起点开始，而是从off位置开始，返回实际读取的字节数。
```

在Reader中包含如下3个方法。

```java
int read(); //从输入流中读取单个字符（相当于从水管中取出一滴水），返回所读取的字符数据（字节数据可直接转换为int类型）。

int read(char[] b); //从输入流中最多读取b.length个字符的数据，并将其存储在字节数组b中，返回实际读取的字符数。

int read(char[] b,int off,int len); //从输入流中最多读取len个字符的数据，并将其存储在数组b中，放入数组b中时，并不是从数组起点开始，而是从off位置开始，返回实际读取的字符数。
```

对比InputStream和Reader所提供的方法，就不难发现这两个基类的功能基本是一样的。

InputStream和Reader都是将输入数据抽象成水管，所以程序即可以通过read()方法每次读取一个”水滴“，也可以通过read（char[] chuf）或者read（byte[] b）方法来读取多个“水滴”。

当使用数组作为read（）方法中的参数, 我们可以理解为使用一个“竹筒”到水管中取水，read(char[] cbuf)方法的参数可以理解成一个”竹筒“，程序每次调用输入流read(char[] cbuf)或read（byte[] b）方法，就相当于用“竹筒”从输入流中取出一筒“水滴”，程序得到“竹筒”里面的”水滴“后，转换成相应的数据即可；程序多次重复这个“取水”过程，直到最后。

程序如何判断取水取到了最后呢？直到read（char[] chuf）或者read（byte[] b）方法返回-1，即表明到了输入流的结束点。

InputStream和Reader提供的一些移动指针的方法：

```java
void mark(int readAheadLimit);  //在记录指针当前位置记录一个标记（mark）。
boolean markSupported();  //判断此输入流是否支持mark()操作，即是否支持记录标记。
void reset(); //将此流的记录指针重新定位到上一次记录标记（mark）的位置。
long skip(long n); //记录指针向前移动n个字节/字符。
```

**OutputStream和Writer**

OutputStream和Writer的用法也非常相似，两个流都提供了如下三个方法：

```java
void write(int c);  // 将指定的字节/字符输出到输出流中，其中c即可以代表字节，也可以代表字符。
void write(byte[]/char[] buf);  // 将字节数组/字符数组中的数据输出到指定输出流中。
void write(byte[]/char[] buf, int off,int len );  // 将字节数组/字符数组中从off位置开始，长度为len的字节/字符输出到输出流中。
```

因为字符流直接以字符作为操作单位，所以Writer可以用字符串来代替字符数组，即以String对象作为参数。Writer里面还包含如下两个方法。

```java
void write(String str);  // 将str字符串里包含的字符输出到指定输出流中。
void write (String str, int off, int len); // 将str字符串里面从off位置开始，长度为len的字符输出到指定输出流中。
```

## 2 IO体系的基类文件流

前面说过InputStream和Reader都是抽象类，本身不能创建实例，但它们分别有一个用于读取文件的输入流：FileInputStream和FileReader，它们都是节点流——会直接和指定文件关联。下面程序示范使用FileInputStream和FileReader。 

**FileInputStream**

```java
public class MyClass {
  public  static void main(String[] args)throws IOException{
      FileInputStream fis=null;
      try {
          //创建字节输入流
          fis=new FileInputStream("E:\\learnproject\\Iotest\\lib\\src\\main\\java\\com\\Test.txt");
          //创建一个长度为1024的竹筒
          byte[] b=new byte[1024];
          //用于保存的实际字节数
          int hasRead=0;
          //使用循环来重复取水的过程
          while((hasRead=fis.read(b))>0){
              //取出竹筒中的水滴（字节），将字节数组转换成字符串进行输出
            System.out.print(new String(b,0,hasRead));
          }
      }catch (IOException e){
        e.printStackTrace();
      }finally {
          fis.close();
      }
  }
}
```

**注**：上面程序最后使用了fis.close()来关闭该文件的输入流，与JDBC编程一样，程序里面打开的文件IO资源不属于内存的资源，垃圾回收机制无法回收该资源，所以应该显示的关闭打开的IO资源。Java 7改写了所有的IO资源类，它们都实现了AntoCloseable接口，因此都可以通过自动关闭资源的try语句来关闭这些Io流。

**FileReader**

```java
public class FileReaderTest {
    public  static void main(String[] args)throws IOException{
        FileReader fis=null;
        try {
            //创建字节输入流
            fis=new FileReader("E:\\learnproject\\Iotest\\lib\\src\\main\\java\\com\\Test.txt");
            //创建一个长度为1024的竹筒
            char[] b=new char[1024];
            //用于保存的实际字节数
            int hasRead=0;
            //使用循环来重复取水的过程
            while((hasRead=fis.read(b))>0){
                //取出竹筒中的水滴（字节），将字节数组转换成字符串进行输出
                System.out.print(new String(b,0,hasRead));
            }
        }catch (IOException e){
            e.printStackTrace();
        }finally {
            fis.close();
        }
    }
}
```

可以看出使用FileInputStream和FileReader进行文件的读写并没有什么区别，只是操作单元不同而且。

FileOutputStream/FileWriter是IO中的文件输出流，下面介绍这两个类的用法。

**FileOutputStream**

```java
public class FileOutputStreamTest {
    public  static void main(String[] args)throws IOException {
        FileInputStream fis=null;
        FileOutputStream fos=null;
        try {
            //创建字节输入流
            fis=new FileInputStream("E:\\learnproject\\Iotest\\lib\\src\\main\\java\\com\\Test.txt");
            //创建字节输出流
            fos=new FileOutputStream("E:\\learnproject\\Iotest\\lib\\src\\main\\java\\com\\newTest.txt");

            byte[] b=new byte[1024];
            int hasRead=0;

            //循环从输入流中取出数据
            while((hasRead=fis.read(b))>0){
                //每读取一次，即写入文件输入流，读了多少，就写多少。
                fos.write(b,0,hasRead);
            }
        }catch (IOException e){
            e.printStackTrace();
        }finally {
            fis.close();
            fos.close();
        }
    }
}
```

运行程序可以看到输出流指定的目录下多了一个文件：newTest.txt, 该文件的内容和Test.txt文件的内容完全相同。FileWriter的使用方式和FileOutputStream基本类似，这里就带过。

**注**： 使用java的io流执行输出时，不要忘记关闭输出流，关闭输出流除了可以保证流的物理资源被回收之外，可能还可以将输出流缓冲区中的数据flush到物理节点中里（因为在执行close（）方法之前，自动执行输出流的flush（）方法）。java很多输出流默认都提供了缓存功能，其实我们没有必要刻意去记忆哪些流有缓存功能，哪些流没有，只有正常关闭所有的输出流即可保证程序正常。

## 3 字节缓存流

**BufferedInputStream/BufferedReader, BufferedOutputStream/BufferedWriter**

下面介绍字节缓存流的用法（字符缓存流的用法和字节缓存流一致就不介绍了）

```java
public class BufferedStreamTest {
    public  static void main(String[] args)throws IOException {
        FileInputStream fis=null;
        FileOutputStream fos=null;
        BufferedInputStream bis=null;
        BufferedOutputStream bos=null;
        try {
            //创建字节输入流
            fis=new FileInputStream("E:\\learnproject\\Iotest\\lib\\src\\main\\java\\com\\Test.txt");
            //创建字节输出流
            fos=new FileOutputStream("E:\\learnproject\\Iotest\\lib\\src\\main\\java\\com\\newTest.txt");
            //创建字节缓存输入流
            bis=new BufferedInputStream(fis);
            //创建字节缓存输出流
            bos=new BufferedOutputStream(fos);

            byte[] b=new byte[1024];
            int hasRead=0;
            //循环从缓存流中读取数据
            while((hasRead=bis.read(b))>0){
                //向缓存流中写入数据，读取多少写入多少
                bos.write(b,0,hasRead);
            }
        }catch (IOException e){
            e.printStackTrace();
        }finally {
            bis.close();
            bos.close();
        }
    }
}
```

可以看到使用字节缓存流读取和写入数据的方式和文件流（FileInputStream,FileOutputStream）并没有什么不同，只是把处理流套接到文件流上进行读写。

上面代码中我们使用了缓存流和文件流，但是我们只关闭了缓存流。这个需要注意一下，当我们使用处理流套接到节点流上的使用的时候，只需要关闭最上层的处理就可以了。java会自动帮我们关闭下层的节点流。

## 4 转换流

**InputStreamReader/OutputStreamWriter**

下面以获取键盘输入为例来介绍转换流的用法。java使用System.in代表输入，即键盘输入，但这个标准输入流是InputStream类的实例，使用不太方便，而且键盘输入内容都是文本内容，所以可以使用InputStreamReader将其包装成BufferedReader,利用BufferedReader的readLine()方法可以一次读取一行内容，如下代码所示:

```java
public class InputStreamReaderTest {
    public  static void main(String[] args)throws IOException {
        try {
           	// 将System.in对象转化为Reader对象
           	InputStreamReader reader=new InputStreamReader(System.in);
           	// 将普通的Reader包装成BufferedReader
           	BufferedReader bufferedReader=new BufferedReader(reader);
           	String buffer=null;
           	while ((buffer=bufferedReader.readLine())!=null){
            	// 如果读取到的字符串为“exit”,则程序退出
               	if(buffer.equals("exit")){
                   	System.exit(1);
               	}
               	//打印读取的内容
               	System.out.print("输入内容："+buffer);
           	}
        }catch (IOException e){
            e.printStackTrace();
        }finally {
        }
    }
}
```

上面程序将System.in包装成BufferedReader,BufferedReader流具有缓存功能，它可以一次读取一行文本——以换行符为标志，如果它没有读到换行符，则程序堵塞。等到读到换行符为止。运行上面程序可以发现这个特征，当我们在控制台执行输入时，只有按下回车键，程序才会打印出刚刚输入的内容。

## 5 对象流

**ObjectInputStream/ObjectOutputStream**

写入对象：

```java
public static void writeObject(){
	OutputStream outputStream=null;
	BufferedOutputStream buf=null;
	ObjectOutputStream obj=null;
	try {
		//序列化文件輸出流
		outputStream=new 		FileOutputStream("E:\\learnproject\\Iotest\\lib\\src\\main\\java\\com\\myfile.tmp");
		//构建缓冲流
		buf=new BufferedOutputStream(outputStream);
		//构建字符输出的对象流
        obj=new ObjectOutputStream(buf);
        //序列化数据写入
        obj.writeObject(new Person("A", 21));//Person对象
        //关闭流
        obj.close();
    } catch (FileNotFoundException e) {
   		e.printStackTrace();
    } catch (IOException e) {
    	e.printStackTrace();
    }
}
```

读取对象：

```java
public static void readObject() throws IOException {
    try {
        InputStream inputStream=new FileInputStream("E:\\learnproject\\Iotest\\lib\\src\\main\\java\\com\\myfile.tmp");
        //构建缓冲流
        BufferedInputStream buf=new BufferedInputStream(inputStream);
        //构建字符输入的对象流
        ObjectInputStream obj=new ObjectInputStream(buf);
        Person tempPerson=(Person)obj.readObject();
        System.out.println("Person对象为："+tempPerson);
        //关闭流
        obj.close();
        buf.close();
        inputStream.close();
    } catch (FileNotFoundException e) {
        e.printStackTrace();
    } catch (IOException e) {
        e.printStackTrace();
    } catch (ClassNotFoundException e) {
        e.printStackTrace();
    }
}
```

使用对象流的一些注意事项 

1. 读取顺序和写入顺序一定要一致，不然会读取出错。 
2. 在对象属性前面加transient关键字，则该对象的属性不会被序列化。



# 四、常见面试题

![img](https://mmbiz.qpic.cn/mmbiz_png/hvUCbRic69sDCqjBUCMtjHPGZxLJ8wNQVSoTZ879BHDY12389iatr5yzqjPc7fSm3PibgjbaW2EfG9saruNHicm3iaA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

![img](https://mmbiz.qpic.cn/mmbiz_png/hvUCbRic69sDCqjBUCMtjHPGZxLJ8wNQV2q1PQ2vouPvGBv46RB6ONl7zVNibPibYNX2BrmsBC0YIOFF9Vu54iagag/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

## 1 什么是IO流

它是一种数据的流从源头流到目的地。比如文件拷贝，输入流和输出流都包括了。输入流从文件中读取数据存储到进程(process)中，输出流从进程中读取数据然后写入到目标文件。

## 2 字节流和字符流的区别

字节流在JDK1.0中就被引进了，用于操作包含ASCII字符的文件。JAVA也支持其他的字符如Unicode，为了读取包含Unicode字符的文件，JAVA语言设计者在JDK1.1中引入了字符流。ASCII作为Unicode的子集，对于英语字符的文件，可以可以使用字节流也可以使用字符流。

## 3 流类的超类主要有哪些

- java.io.InputStream
- java.io.OutputStream
- java.io.Reader
- java.io.Writer

## 4 FileInputStream和FileOutputStream

这是在拷贝文件操作的时候，经常用到的两个类。在处理小文件的时候，它们性能表现还不错，在大文件的时候，最好使用BufferedInputStream (或 BufferedReader) 和 BufferedOutputStream (或 BufferedWriter)

## 5 字节流和字符流更喜欢用哪一个

个人来说，更喜欢使用字符流，因为他们更新一些。许多在字符流中存在的特性，字节流中不存在。比如使用BufferedReader而不是BufferedInputStreams或DataInputStream，使用newLine()方法来读取下一行，但是在字节流中我们需要做额外的操作。

## 6 System.out.println()是什么

`println`是PrintStream的一个方法。`out`是一个静态PrintStream类型的成员变量，`System`是一个java.lang包中的类，用于和底层的操作系统进行交互。

## 7 什么是Filter流

Filter Stream是一种IO流主要作用是用来对存在的流增加一些额外的功能，像给目标文件增加源文件中不存在的行数，或者增加拷贝的性能。

## 8 有哪些可用的Filter流

在java.io包中主要由4个可用的filter Stream。两个字节filter stream，两个字符filter stream. 分别是FilterInputStream, FilterOutputStream, FilterReader and FilterWriter.这些类是抽象类，不能被实例化的。

**有些Filter流的子类:**

- LineNumberInputStream 给目标文件增加行号
- DataInputStream 有些特殊的方法如`readInt()`, `readDouble()`和`readLine()` 等可以读取一个 int, double和一个string一次性的,
- BufferedInputStream 增加性能
- PushbackInputStream 推送要求的字节到系统中

## 9 SequenceInputStream的作用

这个类的作用是将多个输入流合并成一个输入流，通过SequenceInputStream类包装后形成新的一个总的输入流。在拷贝多个文件到一个目标文件的时候是非常有用的。可用使用很少的代码实现

## 10 说说PrintStream和PrintWriter

他们两个的功能相同，但是属于不同的分类。字节流和字符流。他们都有println()方法。

## 11 在文件拷贝的时候，那一种流可用提升更多的性能

在字节流的时候，使用BufferedInputStream和BufferedOutputStream。
在字符流的时候，使用BufferedReader 和 BufferedWriter

## 12 说说管道流(Piped Stream)

有四种管道流， PipedInputStream, PipedOutputStream, PipedReader 和 PipedWriter.在多个线程或进程中传递数据的时候管道流非常有用。

## 13 说说File类

它不属于 IO流，也不是用于文件操作的，它主要用于知道一个文件的属性，读写权限，大小等信息。注意：Java7中文件IO发生了很大的变化，专门引入了很多新的类（Path和Files）来取代原来的基于java.io.File的文件IO操作方式。

## 14 说说RandomAccessFile

它在java.io包中是一个特殊的类，既不是输入流也不是输出流，它两者都可以做到。他是Object的直接子类。通常来说，一个流只有一个功能，要么读，要么写。但是RandomAccessFile既可以读文件，也可以写文件。 DataInputStream 和 DataOutStream有的方法，在RandomAccessFile中都存在。

