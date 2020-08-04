
# 一、NIO 概览

## 1 NIO简介

**Java NIO** 是 **java 1.4** 之后新出的一套IO接口，这里的的新是相对于原有标准的Java IO和Java Networking接口。NIO提供了一种完全不同的操作方式。

**NIO中的N可以理解为Non-blocking，不单纯是New。**

**它支持面向缓冲的，基于通道的I/O操作方法。** 随着JDK 7的推出，NIO系统得到了扩展，为文件系统功能和文件处理提供了增强的支持。 由于NIO文件类支持的这些新的功能，NIO被广泛应用于文件处理。

## 2 NIO的特性

![img](https://mmbiz.qpic.cn/mmbiz_png/hvUCbRic69sAdeibvW8NVWOIZ7l70qiaNPurvicq1oztlP2gDufky36qPcxRleWrCCdLFrMicwkJtkjeyOePLv2BbFg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

### 2.1 Channels and Buffers

**IO是面向流的，NIO是面向缓冲区的**

- 标准的IO编程接口是面向字节流和字符流的。而NIO是面向通道和缓冲区的，数据总是从通道中读到buffer缓冲区内，或者从buffer缓冲区写入到通道中；（ NIO中的所有I/O操作都是通过一个通道开始的。）
- Java IO面向流意味着每次从流中读一个或多个字节，直至读取所有字节，它们没有被缓存在任何地方；
- Java NIO是面向缓存的I/O方法。 将数据读入缓冲器，使用通道进一步处理数据。 在NIO中，使用通道和缓冲区来处理I/O操作。

### 2.2 Non-blocking IO

**IO流是阻塞的，NIO流是不阻塞的**。

- Java NIO使我们可以进行非阻塞IO操作。比如说，单线程中从通道读取数据到buffer，同时可以继续做别的事情，当数据读取到buffer中后，线程再继续处理数据。写数据也是一样的。另外，非阻塞写也是如此。一个线程请求写入一些数据到某通道，但不需要等待它完全写入，这个线程同时可以去做别的事情。
- Java IO的各种流是阻塞的。这意味着，当一个线程调用read() 或 write()时，该线程被阻塞，直到有一些数据被读取，或数据完全写入。该线程在此期间不能再干任何事情了

### 2.3 Selectors

**NIO有选择器，而IO没有。**

- 选择器用于使用单个线程处理多个通道。因此，它需要较少的线程来处理这些通道。
- 线程之间的切换对于操作系统来说是昂贵的。 因此，为了提高系统效率选择器是有用的。

## 3 读数据和写数据的方式

通常来说NIO中的所有IO都是从 **Channel（通道）** 开始的。

**从通道进行数据读取** ：创建一个缓冲区，然后请求通道读取数据。

**从通道进行数据写入** ：创建一个缓冲区，填充数据，并要求通道写入数据。

数据读取和写入操作图示：

![img](https://mmbiz.qpic.cn/mmbiz_png/hvUCbRic69sAdeibvW8NVWOIZ7l70qiaNPu5TCby8wTt5rtWLy7yglvn6E8eFgLcCezY8tgQVUPibUbW3o4DqwRXKw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

## 4 NIO核心组件

NIO包含下面几个核心的组件：

- **Channels**
- **Buffers**
- **Selectors**

整个NIO体系包含的类远远不止这三个，只能说这三个是NIO体系的“核心API”。

### 4.1 通道

在Java NIO中，主要使用的通道如下（涵盖了UDP 和 TCP 网络IO，以及文件IO）：

- DatagramChannel
- SocketChannel
- FileChannel
- ServerSocketChannel

### 4.2 缓冲区

在Java NIO中使用的核心缓冲区如下（覆盖了通过I/O发送的基本数据类型：byte, char、short, int, long, float, double ，long）：

- ByteBuffer
- CharBuffer
- ShortBuffer
- IntBuffer
- FloatBuffer
- DoubleBuffer
- LongBuffer

### 4.3 选择器

Java NIO提供了“选择器”的概念。这是一个可以用于监视多个通道的对象，如数据到达，连接打开等。因此，单线程可以监视多个通道中的数据。

如果应用程序有多个通道(连接)打开，但每个连接的流量都很低，则可考虑使用它。 例如：在聊天服务器中。

下面是一个单线程中Slector维护3个Channel的示意图：

![img](https://mmbiz.qpic.cn/mmbiz_png/hvUCbRic69sAdeibvW8NVWOIZ7l70qiaNPucziaKxIh2rjTWhmo7WiaZUrrHgDrEXviaicxD4SOTIZ1dh6IicictTkay5uw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

**要使用Selector的话，我们必须把Channel注册到Selector上，然后就可以调用Selector的select()方法。这个方法会进入阻塞，直到有一个channel的状态符合条件。当方法返回后，线程可以处理这些事件。**



# 二、NIO 之 Buffer

## 1 Buffer 介绍

**Java NIO Buffers用于和NIO Channel交互。 我们从Channel中读取数据到buffers里，从Buffer把数据写入到Channels.**

**Buffer本质上就是一块内存区**，可以用来写入数据，并在稍后读取出来。这块内存被NIO Buffer包裹起来，对外提供一系列的读写方便开发的接口。

在Java NIO中使用的核心缓冲区如下（覆盖了通过I/O发送的基本数据类型：byte, char、short, int, long, float, double ，long）：

- **ByteBuffer**
- **CharBuffer**
- **ShortBuffer**
- **IntBuffer**
- **FloatBuffer**
- **DoubleBuffer**
- **LongBuffer**

![img](https://mmbiz.qpic.cn/mmbiz_png/hvUCbRic69sAtESZIMSs3Gotl5mLMJWotIcPQdJQSVGZyZMpJ2kUYTnOWic5k27aSm7LArp1rNRCcotZjI6ejbFQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



**利用Buffer读写数据，通常遵循四个步骤：**

1. 把数据写入buffer；
2. 调用flip；
3. 从Buffer中读取数据；
4. 调用buffer.clear()或者buffer.compact()。

当写入数据到buffer中时，buffer会记录已经写入的数据大小。当需要读数据时，通过 **flip()** 方法把buffer从写模式调整为读模式；在读模式下，可以读取所有已经写入的数据。

当读取完数据后，需要清空buffer，以满足后续写入操作。清空buffer有两种方式：调用 **clear()** 或 **compact()** 方法。**clear会清空整个buffer，compact则只清空已读取的数据**，未被读取的数据会被移动到buffer的开始位置，写入位置则近跟着未读数据之后。



**Buffer的容量，位置，上限（Buffer Capacity, Position and Limit）**

**Buffer缓冲区实质上就是一块内存**，用于写入数据，也供后续再次读取数据。这块内存被NIO Buffer管理，并提供一系列的方法用于更简单的操作这块内存。

一个Buffer有三个属性是必须掌握的，分别是：

- **capacity容量**
- **position位置**
- **limit限制**

position和limit的具体含义取决于当前buffer的模式。capacity在两种模式下都表示容量。

**读写模式下position和limit的含义：**

![img](https://mmbiz.qpic.cn/mmbiz_png/hvUCbRic69sAtESZIMSs3Gotl5mLMJWotpZyyPmYeywGiaq8ictezCy7RAsibqndic7tKnADtO1gjIZFhMzvJYwByicQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

**容量（Capacity）**

作为一块内存，buffer有一个固定的大小，叫做capacit（容量）。也就是最多只能写入容量值得字节，整形等数据。一旦buffer写满了就需要清空已读数据以便下次继续写入新的数据。

**位置（Position）**

**当写入数据到Buffer的时候需要从一个确定的位置开始**，默认初始化时这个位置position为0，一旦写入了数据比如一个字节，整形数据，那么position的值就会指向数据之后的一个单元，position最大可以到capacity-1.

**当从Buffer读取数据时，也需要从一个确定的位置开始。buffer从写入模式变为读取模式时，position会归零，每次读取后，position向后移动。**

**上限（Limit）**

在写模式，limit的含义是我们所能写入的最大数据量，它等同于buffer的容量。

一旦切换到读模式，limit则代表我们所能读取的最大数据量，他的值等同于写模式下position的位置。换句话说，您可以读取与写入数量相同的字节数（限制设置为写入的字节数，由位置标记）。

## 2 Buffer 的常见方法

|               方法               |                             介绍                             |
| :------------------------------: | :----------------------------------------------------------: |
|     abstract Object array()      |             返回支持此缓冲区的数组 （可选操作）              |
|    abstract int arrayOffset()    | 返回该缓冲区的缓冲区的第一个元素的在数组中的偏移量 （可选操作） |
|          int capacity()          |                      返回此缓冲区的容量                      |
|          Buffer clear()          |   清除此缓存区。将position = 0;limit = capacity;mark = -1;   |
|          Buffer flip()           | flip()方法可以吧Buffer从写模式切换到读模式。调用flip方法会把position归零，并设置limit为之前的position的值。 也就是说，现在position代表的是读取位置，limit标示的是已写入的数据位置。 |
|   abstract boolean hasArray()    |             告诉这个缓冲区是否由可访问的数组支持             |
|      boolean hasRemaining()      |        return position < limit，返回是否还有未读内容         |
|   abstract boolean isDirect()    |                  判断个缓冲区是否为 direct                   |
|  abstract boolean isReadOnly()   |                判断告知这个缓冲区是否是只读的                |
|           int limit()            |                      返回此缓冲区的限制                      |
| Buffer position(int newPosition) |                     设置这个缓冲区的位置                     |
|         int remaining()          |  return limit - position; 返回limit和position之间相对位置差  |
|         Buffer rewind()          |         把position设为0，mark设为-1，不改变limit的值         |
|          Buffer mark()           |                 将此缓冲区的标记设置在其位置                 |

## 3 Buffer 的使用方法

### 3.1 分配缓冲区

为了获得缓冲区对象，我们必须首先分配一个缓冲区。在每个Buffer类中，allocate()方法用于分配缓冲区。

下面来看看ByteBuffer分配容量为28字节的例子：

```java
ByteBuffer buf = ByteBuffer.allocate(28);
```

下面来看看另一个示例：CharBuffer分配空间大小为2048个字符。

```java
CharBuffer buf = CharBuffer.allocate(2048);
```

### 3.2 写入数据到缓冲区

**写数据到Buffer有两种方法：**

- 从Channel中写数据到Buffer
- 手动写数据到Buffer，调用put方法

下面是一个实例，演示从Channel写数据到Buffer：

```java
int bytesRead = inChannel.read(buf); //read into buffer.
```

通过put写数据：

```java
buf.put(127);
```

put方法有很多不同版本，对应不同的写数据方法。例如把数据写到特定的位置，或者把一个字节数据写入buffer。看考JavaDoc文档可以查阅的更多数据。

### 3.3 翻转

flip()方法可以把Buffer从写模式切换到读模式。调用flip方法会把position归零，并设置limit为之前的position的值。 也就是说，现在position代表的是读取位置，limit标示的是已写入的数据位置。

### 3.4 从Buffer读取数据

从Buffer读数据也有两种方式。

- 从buffer读数据到channel
- 从buffer直接读取数据，调用get方法

读取数据到channel的例子：

```java
int bytesWritten = inChannel.write(buf);
```

调用get读取数据的例子：

```java
byte aByte = buf.get();
```

get也有诸多版本，对应了不同的读取方式。

### 3.5 rewind()

Buffer.rewind()方法将position置为0，这样我们可以重复读取buffer中的数据。limit保持不变。

### 3.6 clear() 和 compact()

一旦我们从buffer中读取完数据，需要复用buffer为下次写数据做准备。只需要调用clear（）或compact（）方法。

如果调用的是clear()方法，position将被设回0，limit被设置成 capacity的值。换句话说，Buffer 被清空了。Buffer中的数据并未清除，只是这些标记告诉我们可以从哪里开始往Buffer里写数据。

如果Buffer还有一些数据没有读取完，调用clear就会导致这部分数据被“遗忘”，因为我们没有标记这部分数据未读。

针对这种情况，如果需要保留未读数据，那么可以使用compact。 因此 **compact()** 和 **clear()** 的区别就在于: **对未读数据的处理，是保留这部分数据还是一起清空** 。

### 3.7 mark() 和 reset()

通过调用Buffer.mark()方法，可以标记Buffer中的一个特定position。之后可以通过调用Buffer.reset()方法恢复到这个position。例如：

```java
buffer.mark();//call buffer.get() a couple of times, e.g. during parsing.buffer.reset();  //set position back to mark.    
```

### 3.8 equals() 和 compareTo()

可以用eqauls和compareTo比较两个buffer

**equals():**

判断两个buffer相等，需满足：

- 类型相同
- buffer中剩余字节数相同
- 所有剩余字节相等

从上面的三个条件可以看出，equals只比较buffer中的部分内容，并不会去比较每一个元素。

**compareTo():**

compareTo也是比较buffer中的剩余元素，只不过这个方法适用于比较排序的

## 4 Buffer 常用方法测试

这里以ByteBuffer为例子说明抽象类Buffer的实现类的一些常见方法的使用：

```java
import java.nio.ByteBuffer;

public class Test {

	public static void main(String[] args) {
		// 分配缓冲区
		ByteBuffer buffer = ByteBuffer.allocate(33);
		System.out.println("-------------Test reset-------------");
		// clear()方法，position将被设回0，limit被设置为capacity的值
		buffer.clear();
		// 设置这个缓冲区的位置
		buffer.position(5);
		// 将此缓冲区的标记设置在其位置，没有buffer.mark()这句话会报错。
		buffer.mark();
		buffer.position(10);
		System.out.println("before reset:" + buffer);
		// 将此缓冲区的位置重置为先前标记的位置。（buffer.position(5)）
		buffer.reset();
		System.out.println("after reset:" + buffer);

		System.out.println("-------------Test rewind-------------");
		buffer.clear();
		buffer.position(10);
		// 返回此缓冲区的限制
		buffer.limit(15);
		System.out.println("before rewind:" + buffer);
		// 把position设为0，mark设为-1，不改变limit的值
		buffer.rewind();
		System.out.println("after rewind:" + buffer);

		System.out.println("-------------Test compact-------------");
		buffer.clear();
		buffer.put("abcd".getBytes());
		System.out.println("before compact:" + buffer);
		System.out.println(new String(buffer.array()));
		// limit = position;position = 0;mark = -1;
		// 翻转也就是让flip之后的position到limit这块区域变成之前的0到position这块，
		// 翻转就是将一个处于存数据状态的缓冲区变为一个处于准备取数据的状态
		buffer.flip();
		System.out.println("after flip:" + buffer);
		System.out.println((char) buffer.get());
		System.out.println((char) buffer.get());
		System.out.println((char) buffer.get());
		System.out.println("after three gets:" + buffer);
		System.out.println("\t" + new String(buffer.array()));
		// 把从position到limit中的内容移到0到limit-position的区域内，position和limit的取值也分别变成limit-position、capacity。
		// 如果先将positon设置到limit，再compact，那么相当于clear()
		buffer.compact();
		System.out.println("after compact:" + buffer);
		System.out.println("\t" + new String(buffer.array()));

		System.out.println("-------------Test get-------------");
		buffer = ByteBuffer.allocate(32);
		buffer.put((byte) 'a').put((byte) 'b').put((byte) 'c').put((byte) 'd').put((byte) 'e').put((byte) 'f');
		System.out.println("before flip:" + buffer);
		// 转换成读取模式
		buffer.flip();
		System.out.println("before get:" + buffer);
		System.out.println((char) buffer.get());
		System.out.println("after get:" + buffer);
		// get(index)不影响position的值
		System.out.println((char) buffer.get(2));
		System.out.println("after get(index):" + buffer);
		byte[] dst = new byte[10];
		buffer.get(dst, 0, 2);
		System.out.println("after get(dst,0,2):" + buffer);
		System.out.println("\t dst:" + new String(dst));
		System.out.println("buffer now is:" + buffer);
		System.out.println("\t" + new String(buffer.array()));

		System.out.println("-------------Test put-------------");
		ByteBuffer bb = ByteBuffer.allocate(32);
		System.out.println("before put(byte):" + bb);
		System.out.println("after put(byte):" + bb.put((byte) 'z'));
		System.out.println("\t" + bb.put(2, (byte) 'c'));
		// put(2, (byte) 'c')不改变position的位置
		System.out.println("after put(2, (byte) 'c'):" + bb);
		System.out.println("\t" + new String(bb.array()));
		// 这里的buffer是 abcdef[pos=3 lim=6 cap=32]
		bb.put(buffer);
		System.out.println("after put(buffer):" + bb);
		System.out.println("\t" + new String(bb.array()));
	}

}
```



# 三、NIO 之 Channel

## 1 Channel 介绍

**通常来说NIO中的所有IO都是从 Channel（通道） 开始的。**

- **从通道进行数据读取** ：创建一个缓冲区，然后请求通道读取数据。
- **从通道进行数据写入** ：创建一个缓冲区，填充数据，并要求通道写入数据。

**数据读取和写入操作图示：**

![img](https://mmbiz.qpic.cn/mmbiz_png/hvUCbRic69sAlWOQIdK09StRpvYGMKZBn74j91CGPgWlMUKngCRaOKSaf4MzxMqHEVzQpy6Lo2yicl3Q1rpOk9pw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

**Java NIO Channel通道和流非常相似，主要有以下几点区别：**

- 通道可以读也可以写，流一般来说是单向的（只能读或者写，所以之前我们用流进行IO操作的时候需要分别创建一个输入流和一个输出流）。
- 通道可以异步读写。
- 通道总是基于缓冲区Buffer来读写。

**Java NIO中最重要的几个Channel的实现：**

- **FileChannel：** 用于文件的数据读写
- **DatagramChannel：** 用于UDP的数据读写
- **SocketChannel：** 用于TCP的数据读写，一般是客户端实现
- **ServerSocketChannel:** 允许我们监听TCP链接请求，每个请求会创建会一个SocketChannel，一般是服务器实现

**类层次结构：**

![img](https://mmbiz.qpic.cn/mmbiz_png/hvUCbRic69sAlWOQIdK09StRpvYGMKZBnCOs8Tic8ANsRpZF4t1rAD4nSSgQ8IaEYNFY14BF3pldrsQbCIAMNibicw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

## 2 FileChannel 的使用

**使用FileChannel读取数据到Buffer（缓冲区）以及利用Buffer（缓冲区）写入数据到FileChannel：**

```java
import java.io.IOException;
import java.io.RandomAccessFile;
import java.nio.ByteBuffer;
import java.nio.channels.FileChannel;

public class Test {

	public static void main(String[] args) throws IOException {
		// 1.创建一个RandomAccessFile（随机访问文件）对象
		RandomAccessFile raf = new RandomAccessFile("D:\\test.txt", "rw");
		// 通过RandomAccessFile对象的getChannel()方法。FileChannel是抽象类。
		FileChannel inChannel = raf.getChannel();
		// 2.创建一个读数据缓冲区对象
		ByteBuffer buf = ByteBuffer.allocate(48);
		// 3.从通道中读取数据
		int bytesRead = inChannel.read(buf);
		// 创建一个写数据缓冲区对象
		ByteBuffer buf2 = ByteBuffer.allocate(48);
		// 写入数据
		buf2.put("filechannel test".getBytes());
		buf2.flip();
		inChannel.write(buf2);
		while (bytesRead != -1) {
			System.out.println("Read " + bytesRead);
			// Buffer有两种模式，写模式和读模式。在写模式下调用flip()之后，Buffer从写模式变成读模式。
			buf.flip();
			// 如果还有未读内容
			while (buf.hasRemaining()) {
				System.out.print((char) buf.get());
			}
			// 清空缓存区
			buf.clear();
			bytesRead = inChannel.read(buf);
		}
		// 关闭RandomAccessFile（随机访问文件）对象
		raf.close();
	}

}
```

通过上述实例代码，我们可以大概总结出FileChannel的一般使用规则：

1. 开启FileChannel

**使用之前，FileChannel必须被打开** ，但是你无法直接打开FileChannel（FileChannel是抽象类）。需要通过 **InputStream** ， **OutputStream** 或 **RandomAccessFile** 获取FileChannel。

我们上面的例子是通过RandomAccessFile打开FileChannel的：

```java
// 1.创建一个RandomAccessFile（随机访问文件）对象
RandomAccessFile raf = new RandomAccessFile("D:\\test.txt", "rw");
// 通过RandomAccessFile对象的getChannel()方法。FileChannel是抽象类。
FileChannel inChannel = raf.getChannel();
```

2. 从FileChannel读取数据/写入数据

从FileChannel中读取数据/写入数据之前首先要创建一个Buffer（缓冲区）对象，Buffer（缓冲区）对象的使用我们在上一篇文章中已经详细说明了，如果不了解的话可以看我的上一篇关于Buffer的文章。

**使用FileChannel的read()方法读取数据：**

```java
// 2.创建一个读数据缓冲区对象
ByteBuffer buf = ByteBuffer.allocate(48);
// 3.从通道中读取数据
int bytesRead = inChannel.read(buf);
```

**使用FileChannel的write()方法写入数据：**

```Java
// 创建一个写数据缓冲区对象
ByteBuffer buf2 = ByteBuffer.allocate(48);
// 写入数据
buf2.put("filechannel test.".getBytes());
buf2.flip();
inChannel.write(buf2);
```

3. 关闭FileChannel

完成使用后，FileChannel您必须关闭它。

```java
channel.close();
```

## 3 SocketChannel和ServerSocketChannel的使用

**利用SocketChannel和ServerSocketChannel实现客户端与服务器端简单通信：**

**SocketChannel** 用于创建基于tcp协议的客户端对象，因为SocketChannel中不存在accept()方法，所以，它不能成为一个服务端程序。通过 **connect()方法** ，SocketChannel对象可以连接到其他tcp服务器程序。

客户端:

```java
import java.io.IOException;
import java.net.InetSocketAddress;
import java.nio.ByteBuffer;
import java.nio.channels.SocketChannel;

public class Test {

	public static void main(String[] args) throws IOException {
		// 1.通过SocketChannel的open()方法创建一个SocketChannel对象
		SocketChannel socketChannel = SocketChannel.open();
		// 2.连接到远程服务器（连接此通道的socket）
		socketChannel.connect(new InetSocketAddress("127.0.0.1", 3333));
		// 3.创建写数据缓存区对象
		ByteBuffer writeBuffer = ByteBuffer.allocate(128);
		writeBuffer.put("hello WebServer this is from WebClient".getBytes());
		writeBuffer.flip();
		socketChannel.write(writeBuffer);
		// 创建读数据缓存区对象
		ByteBuffer readBuffer = ByteBuffer.allocate(128);
		socketChannel.read(readBuffer);
		// String 字符串常量，不可变；StringBuffer 字符串变量（线程安全），可变；StringBuilder 字符串变量（非线程安全），可变
		StringBuilder stringBuffer = new StringBuilder();
		// 4.将Buffer从写模式变为可读模式
		readBuffer.flip();
		while (readBuffer.hasRemaining()) {
			stringBuffer.append((char) readBuffer.get());
		}
		System.out.println("从服务端接收到的数据：" + stringBuffer);
		socketChannel.close();
	}

}
```

**ServerSocketChannel** 允许我们监听TCP链接请求，通过ServerSocketChannelImpl的 **accept()方法** 可以创建一个SocketChannel对象用户从客户端读/写数据。

服务端：

```java
import java.io.IOException;
import java.net.InetSocketAddress;
import java.nio.ByteBuffer;
import java.nio.channels.ServerSocketChannel;
import java.nio.channels.SocketChannel;

public class Test {

	public static void main(String[] args) throws IOException {
		try {
			// 1.通过ServerSocketChannel的open()方法创建一个ServerSocketChannel对象，open方法的作用：打开套接字通道
			ServerSocketChannel ssc = ServerSocketChannel.open();
			// 2.通过ServerSocketChannel绑定ip地址和port(端口号)
			ssc.socket().bind(new InetSocketAddress("127.0.0.1", 3333));
			// 通过ServerSocketChannelImpl的accept()方法创建一个SocketChannel对象用户从客户端读/写数据
			SocketChannel socketChannel = ssc.accept();
			// 3.创建写数据的缓存区对象
			ByteBuffer writeBuffer = ByteBuffer.allocate(128);
			writeBuffer.put("hello WebClient this is from WebServer".getBytes());
			writeBuffer.flip();
			socketChannel.write(writeBuffer);

			// 创建读数据的缓存区对象
			ByteBuffer readBuffer = ByteBuffer.allocate(128);
			// 读取缓存区数据
			socketChannel.read(readBuffer);
			StringBuilder stringBuffer = new StringBuilder();

			// 4.将Buffer从写模式变为可读模式
			readBuffer.flip();
			while (readBuffer.hasRemaining()) {
				stringBuffer.append((char) readBuffer.get());
			}
			System.out.println("从客户端接收到的数据：" + stringBuffer);
			socketChannel.close();
			ssc.close();
		} catch (IOException e) {
			e.printStackTrace();
		}
	}

}
```

通过上述实例代码，我们可以大概总结出SocketChannel和ServerSocketChannel的使用的一般使用规则：

**客户端**

1. 通过SocketChannel连接到远程服务器
2. 创建读数据/写数据缓冲区对象来读取服务端数据或向服务端发送数据
3. 关闭SocketChannel

**服务端**

1. 通过ServerSocketChannel 绑定ip地址和端口号
2. 通过ServerSocketChannelImpl的accept()方法创建一个SocketChannel对象用户从客户端读/写数据
3. 创建读数据/写数据缓冲区对象来读取客户端数据或向客户端发送数据
4. 关闭SocketChannel和ServerSocketChannel

## 4 DatagramChannel 的使用

DataGramChannel，类似于java 网络编程的DatagramSocket类；使用UDP进行网络传输， **UDP是无连接，面向数据报文段的协议，对传输的数据不保证安全与完整** ；和上面介绍的SocketChannel和ServerSocketChannel的使用方法类似，所以这里就简单介绍一下如何使用。

1. 获取DataGramChannel

```java
// 1.通过DatagramChannel的open()方法创建一个DatagramChannel对象
DatagramChannel datagramChannel = DatagramChannel.open();
// 绑定一个port（端口）
datagramChannel.bind(new InetSocketAddress(1234));
```

上面代码表示程序可以在1234端口接收数据报。

2. 接收/发送消息

**接收消息：**

先创建一个缓存区对象，然后通过receive方法接收消息，这个方法返回一个SocketAddress对象，表示发送消息方的地址：

```java
ByteBuffer buf = ByteBuffer.allocate(48);
buf.clear();
channel.receive(buf);
```

**发送消息：**

由于UDP下，服务端和客户端通信并不需要建立连接，只需要知道对方地址即可发出消息，但是是否发送成功或者成功被接收到是没有保证的;发送消息通过send方法发出，改方法返回一个int值，表示成功发送的字节数：

```java
ByteBuffer buf = ByteBuffer.allocate(48);
buf.clear();
buf.put("datagramchannel".getBytes());
buf.flip();
int send = channel.send(buffer, new InetSocketAddress("localhost", 1234));
```

这个例子发送一串字符：“datagramchannel”到主机名为”localhost”服务器的端口1234上。

## 5 Scatter / Gather

Channel 提供了一种被称为 Scatter/Gather 的新功能，也称为本地矢量 I/O。Scatter/Gather 是指在多个缓冲区上实现一个简单的 I/O 操作。正确使用 Scatter / Gather可以明显提高性能。

大多数现代操作系统都支持本地矢量I/O（native vectored I/O）操作。当您在一个通道上请求一个Scatter/Gather操作时，该请求会被翻译为适当的本地调用来直接填充或抽取缓冲区，减少或避免了缓冲区拷贝和系统调用；

Scatter/Gather应该使用直接的ByteBuffers以从本地I/O获取最大性能优势。

**Scatter/Gather功能是通道(Channel)提供的 并不是Buffer。**

- **Scatter:** 从一个Channel读取的信息分散到N个缓冲区中(Buufer).
- **Gather:** 将N个Buffer里面内容按照顺序发送到一个Channel.



**Scattering Reads**

"scattering read"是把数据从单个Channel写入到多个buffer,如下图所示：

![img](https://mmbiz.qpic.cn/mmbiz_png/hvUCbRic69sAlWOQIdK09StRpvYGMKZBn2GfeqkBEQa2jvIfVicxk8ia4MyUibJpaecvd8r5AXibHPkbEeHe9Dawciag/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

示例代码：

```java
ByteBuffer header = ByteBuffer.allocate(128);
ByteBuffer body = ByteBuffer.allocate(1024);
ByteBuffer[] bufferArray = { header, body };
channel.read(bufferArray);
```

read()方法内部会负责把数据按顺序写进传入的buffer数组内。一个buffer写满后，接着写到下一个buffer中。

举个例子，假如通道中有200个字节数据，那么header会被写入128个字节数据，body会被写入72个字节数据；

**注意：**

无论是scatter还是gather操作，都是按照buffer在数组中的顺序来依次读取或写入的；



**Gathering Writes**

"gathering write"把多个buffer的数据写入到同一个channel中，下面是示意图：

![img](https://mmbiz.qpic.cn/mmbiz_png/hvUCbRic69sAlWOQIdK09StRpvYGMKZBn1TR51MFuqvNU4p4iblWhPaWkOR4MBg68rZJmDkpT11uXOdmC4Xxyianw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

示例代码：

```java
ByteBuffer header = ByteBuffer.allocate(128);
ByteBuffer body = ByteBuffer.allocate(1024);
// write data into buffers
ByteBuffer[] bufferArray = { header, body };
channel.write(bufferArray);
```

write()方法内部会负责把数据按顺序写入到channel中。

**注意：**

并不是所有数据都写入到通道，写入的数据要根据position和limit的值来判断，只有position和limit之间的数据才会被写入；

举个例子，假如以上header缓冲区中有128个字节数据，但此时position=0，limit=58；那么只有下标索引为0-57的数据才会被写入到通道中。

## 6 通道之间的数据传输

在Java NIO中如果一个channel是FileChannel类型的，那么他可以直接把数据传输到另一个channel。

- **transferFrom()** :transferFrom方法把数据从通道源传输到FileChannel
- **transferTo()** :transferTo方法把FileChannel数据传输到另一个channel



# 四、NIO 之 Selector

## 1 Selector 介绍

**Selector** 一般称为**选择器** ，当然你也可以翻译为**多路复用器** 。它是Java NIO核心组件中的一个，用于检查一个或多个NIO Channel（通道）的状态是否处于可读、可写。如此可以实现单线程管理多个channels,也就是可以管理多个网络链接。

**使用Selector的好处在于：** 使用更少的线程来就可以来处理通道了， 相比使用多个线程，避免了线程上下文切换带来的开销。



**选择器（Selector)**

Selector选择器类管理着一个被注册的通道集合的信息和它们的就绪状态。通道是和选择器一起被注册的，并且使用选择器来更新通道的就绪状态。当这么做的时候，可以选择将被激发的线程挂起，直到有就绪的的通道。

**可选择通道(SelectableChannel)**

SelectableChannel这个抽象类提供了实现通道的可选择性所需要的公共方法。它是所有支持就绪检查的通道类的父类。因为FileChannel类没有继承SelectableChannel因此是不是可选通道，而所有socket通道都是可选择的，包括从管道(Pipe)对象的中获得的通道。SelectableChannel可以被注册到Selector对象上，同时可以指定对那个选择器而言，那种操作是感兴趣的。一个通道可以被注册到多个选择器上，但对每个选择器而言只能被注册一次。

**选择键(SelectionKey)**

选择键封装了特定的通道与特定的选择器的注册关系。选择键对象被SelectableChannel.register()返回并提供一个表示这种注册关系的标记。 

## 2 Selector 的使用方法介绍

### 2.1 Selector 的创建

一个selector的创建，都是通过简单open静态方法获取：

```java
Selector selector = Selector.open();
```

### 2.2 注册 Channel 到 Selector

通道要通过selector管理，必须先将通道注册到一个selector上：

```java
serverChannel.configureBlocking(false);
SelectionKey key = serverChannel.register(selector, SelectionKey.OP_ACCEPT);
```

register方法是在SelectableChannel抽象类中定义的，所以只有继承了SelectableChannel类的通道类型才可注册到selector，如ServerSocketChannel、SocketChannel，而且通道必须是设置为非阻塞模式，所以像FileChannel通道，是不能与selector配合使用的； 另外通道一旦被注册，将不能再回到阻塞状态，此时若调用通道的configureBlocking(true)将抛出BlockingModeException异常。



register方法中的第二个参数，表示这个通道注册所感兴趣的事件， 它实际上是一个表示选择器在检查通道就绪状态时需要关心的操作的比特掩码。比如一个选择器对通道的read和write操作感兴趣，那么选择器在检查该通道时，只会检查通道的read和write操作是否已经处在就绪状态。 它有以下四种操作类型：

- Connect 连接
- Accept 接受
- Read 读
- Write 写

需要注意并非所有的操作在所有的可选择通道上都能被支持，比如ServerSocketChannel支持Accept，而SocketChannel中不支持。我们可以通过通道上的validOps()方法来获取特定通道下所有支持的操作集合。

Java中定义了四个常量来表示这四种操作类型：

```java
SelectionKey.OP_CONNECT
SelectionKey.OP_ACCEPT
SelectionKey.OP_READ
SelectionKey.OP_WRITE
```

如果你对不止一种事件感兴趣，使用或运算符即可，如下：

```java
int interestSet = SelectionKey.OP_READ | SelectionKey.OP_WRITE;
```

当通道触发了某个操作之后，表示该通道的某个操作已经就绪，可以被操作。因此，某个SocketChannel成功连接到另一个服务器称为“连接就绪”(OP_CONNECT)。一个ServerSocketChannel准备好接收新进入的连接称为“接收就绪”（OP_ACCEPT）。一个有数据可读的通道可以说是“读就绪”(OP_READ)。等待写数据的通道可以说是“写就绪”(OP_WRITE)。



register方法的返回值是一个SelectionKey对象，我们称之为键对象。该对象包含了以下四种属性：

- interest集合

- read集合
- Channel
- Selector

**interest集合**是Selector感兴趣的集合，用于指示选择器对通道关心的操作，可通过SelectionKey对象的interestOps()获取。最初，该兴趣集合是通道被注册到Selector时传进来的值。该集合不会被选择器改变，但是可通过interestOps()改变。我们可以通过以下方法来判断Selector是否对Channel的某种事件感兴趣：

```java
int interestSet = selectionKey.interestOps();
boolean isInterestedInAccept  = (interestSet & SelectionKey.OP_ACCEPT) == SelectionKey.OP_ACCEPT；
```

**read集合**是通道已经就绪的操作的集合，表示一个通道准备好要执行的操作了,可通过SelctionKey对象的readyOps()来获取相关通道已经就绪的操作。它是interest集合的子集，并且表示了interest集合中从上次调用select()以后已经就绪的那些操作。（比如选择器对通道的read,write操作感兴趣，而某时刻通道的read操作已经准备就绪可以被选择器获知了，前一种就是interest集合，后一种则是read集合。）。JAVA中定义以下几个方法用来检查这些操作是否就绪：

```java
//int readSet=selectionKey.readOps();
selectionKey.isAcceptable();//等价于selectionKey.readyOps()&SelectionKey.OP_ACCEPT
selectionKey.isConnectable();
selectionKey.isReadable();
selectionKey.isWritable();
```

需要注意的是，通过相关的选择键的readyOps()方法返回的就绪状态指示只是一个提示，底层的通道在任何时候都会不断改变，而其他线程也可能在通道上执行操作并影响到它的就绪状态。另外，我们不能直接修改read集合。

**取出SelectionKey所关联的Selector和Channel**

```java
Channel channel = selectionKey.channel();
Selector selector = selectionKey.selector();
```

**取消SelectionKey对象**

我们可以通过SelectionKey对象的cancel()方法来取消特定的注册关系。该方法调用之后，该SelectionKey对象将会被”拷贝”至已取消键的集合中，该键此时已经失效，但是该注册关系并不会立刻终结。在下一次select()时，已取消键的集合中的元素会被清除，相应的注册关系也真正终结。

### 2.3 为SelectionKey绑定附加对象

可以将一个或者多个附加对象绑定到SelectionKey上，以便容易的识别给定的通道。通常有两种方式： 

1. 在注册的时候直接绑定： 

```java
SelectionKey key=channel.register(selector,SelectionKey.OP_READ,theObject); 
```

2. 在绑定完成之后附加： 

```java
selectionKey.attach(theObject);//绑定
```

绑定之后，可通过对应的SelectionKey取出该对象: 

```java
selectionKey.attachment();
```

如果要取消该对象，则可以通过该种方式: 

```java
selectionKey.attach(null).
```

需要注意的是如果附加的对象不再使用，一定要人为清除，因为垃圾回收器不会回收该对象，若不清除的话会成内存泄漏。

一个单独的通道可被注册到多个选择器中，有些时候我们需要通过isRegistered（）方法来检查一个通道是否已经被注册到任何一个选择器上。 通常来说，我们并不会这么做。

### 2.4 从 Selector 中选择 channel

选择器维护注册过的通道的集合，并且这种注册关系都被封装在SelectionKey当中.

**Selector维护的三种类型SelectionKey集合：**

- **已注册的键的集合(Registered key set)**

所有与选择器关联的通道所生成的键的集合称为已经注册的键的集合。并不是所有注册过的键都仍然有效。这个集合通过 **keys()** 方法返回，并且可能是空的。这个已注册的键的集合不是可以直接修改的；试图这么做的话将引发java.lang.UnsupportedOperationException。

- **已选择的键的集合(Selected key set)**

已注册的键的集合的子集。这个集合的每个成员都是相关的通道被选择器(在前一个选择操作中)判断为已经准备好的，并且包含于键的interest集合中的操作。这个集合通过selectedKeys()方法返回(并有可能是空的)。 

不要将已选择的键的集合与ready集合弄混了。这是一个键的集合，每个键都关联一个已经准备好至少一种操作的通道。每个键都有一个内嵌的ready集合，指示了所关联的通道已经准备好的操作。键可以直接从这个集合中移除，但不能添加。试图向已选择的键的集合中添加元素将抛出java.lang.UnsupportedOperationException。

- **已取消的键的集合(Cancelled key set)**

已注册的键的集合的子集，这个集合包含了 **cancel()** 方法被调用过的键(这个键已经被无效化)，但它们还没有被注销。这个集合是选择器对象的私有成员，因而无法直接访问。

**注意：**当键被取消（ 可以通过**isValid( )** 方法来判断）时，它将被放在相关的选择器的已取消的键的集合里。注册不会立即被取消，但键会立即失效。当再次调用 **select( )**方法时（或者一个正在进行的select()调用结束时），已取消的键的集合中的被取消的键将被清理掉，并且相应的注销也将完成。通道会被注销，而新的SelectionKey将被返回。当通道关闭时，所有相关的键会自动取消（记住，一个通道可以被注册到多个选择器上）。当选择器关闭时，所有被注册到该选择器的通道都将被注销，并且相关的键将立即被无效化（取消）。一旦键被无效化，调用它的与选择相关的方法就将抛出CancelledKeyException。



在刚初始化的Selector对象中，这三个集合都是空的。 **通过Selector的select（）方法可以选择已经准备就绪的通道** （这些通道包含你感兴趣的的事件）。比如你对读就绪的通道感兴趣，那么select（）方法就会返回读事件已经就绪的那些通道。下面是Selector几个重载的select()方法：

```java
int select(); // 阻塞到至少有一个通道在你注册的事件上就绪了。
int select(long timeout); // 和select()一样，但最长阻塞时间为timeout毫秒。
int selectNow(); // 非阻塞，只要有通道就绪就立刻返回。
```

**select()方法返回的int值表示有多少通道已经就绪,是自上次调用select()方法后有多少通道变成就绪状态。之前在select（）调用时进入就绪的通道不会在本次调用中被记入，而在前一次select（）调用进入就绪但现在已经不在处于就绪的通道也不会被记入**。例如：首次调用select()方法，如果有一个通道变成就绪状态，返回了1，若再次调用select()方法，如果另一个通道就绪了，它会再次返回1。如果对第一个就绪的channel没有做任何操作，现在就有两个就绪的通道，但在每次select()方法调用之间，只有一个通道就绪了。

**一旦调用select()方法，并且返回值不为0时，则可以通过调用Selector的selectedKeys()方法来访问已选择键集合** 。如下：

```java
Set selectedKeys = selector.selectedKeys(); 
```

进而可以放到和某SelectionKey关联的Selector和Channel。如下所示：

```java
Set selectedKeys = selector.selectedKeys();
Iterator keyIterator = selectedKeys.iterator();
while (keyIterator.hasNext()) {
    SelectionKey key = keyIterator.next();
    if (key.isAcceptable()) {
        // a connection was accepted by a ServerSocketChannel.
    } else if (key.isConnectable()) {
        // a connection was established with a remote server.
    } else if (key.isReadable()) {
        // a channel is ready for reading
    } else if (key.isWritable()) {
        // a channel is ready for writing
    }
    keyIterator.remove();
}
```



**Selector执行选择的过程**

我们知道调用select（）方法进行通道选择，现在我们再来深入一下选择的过程，也就是select（）执行过程。当select（）被调用时将执行以下几步：

1. 首先检查已取消键集合，也就是通过cancle()取消的键。如果该集合不为空，则清空该集合里的键，同时该集合中每个取消的键也将从已注册键集合和已选择键集合中移除。（一个键被取消时，并不会立刻从集合中移除，而是将该键“拷贝”至已取消键集合中，这种取消策略就是我们常提到的“延迟取消”。）
2. 再次检查已注册键集合（准确说是该集合中每个键的interest集合）。系统底层会依次询问每个已经注册的通道是否准备好选择器所感兴趣的某种操作，一旦发现某个通道已经就绪了，则会首先判断该通道是否已经存在在已选择键集合当中，如果已经存在，则更新该通道在已注册键集合中对应的键的ready集合，如果不存在，则首先清空该通道的对应的键的ready集合，然后重设ready集合，最后将该键存至已注册键集合中。这里需要明白，当更新ready集合时，在上次select（）中已经就绪的操作不会被删除，也就是ready集合中的元素是累积的，比如在第一次的selector对某个通道的read和write操作感兴趣，在第一次执行select（）时，该通道的read操作就绪，此时该通道对应的键中的ready集合存有read元素，在第二次执行select()时，该通道的write操作也就绪了，此时该通道对应的ready集合中将同时有read和write元素。



**深入已注册键集合的管理**

到现在我们已经知道一个通道的的键是如何被添加到已选择键集合中的，下面我们来继续了解对已选择键集合的管理 。首先要记住：选择器不会主动删除被添加到已选择键集合中的键，而且被添加到已选择键集合中的键的ready集合只能被设置，而不能被清理。如果我们希望清空已选择键集合中某个键的ready集合该怎么办？我们知道一个键在新加入已选择键集合之前会首先置空该键的ready集合，这样的话我们可以人为的将某个键从已注册键集合中移除最终实现置空某个键的ready集合。被移除的键如果在下一次的select（）中再次就绪，它将会重新被添加到已选择的键的集合中。这就是为什么要在每次迭代的末尾调用keyIterator.remove()。

### 2.5 停止选择的方法

选择器执行选择的过程，系统底层会依次询问每个通道是否已经就绪，这个过程可能会造成调用线程进入阻塞状态,那么我们有以下三种方式可以唤醒在select（）方法中阻塞的线程。

- **wakeup()方法** ：通过调用Selector对象的wakeup（）方法让处在阻塞状态的select()方法立刻返回。该方法使得选择器上的第一个还没有返回的选择操作立即返回。如果当前没有进行中的选择操作，那么下一次对select()方法的一次调用将立即返回。
- **close()方法** ：通过close（）方法关闭Selector， 该方法使得任何一个在选择操作中阻塞的线程都被唤醒（类似wakeup（）），同时使得注册到该Selector的所有Channel被注销，所有的键将被取消，但是Channel本身并不会关闭。
- **interrupt()方法**：调用该方法会使睡眠的线程抛出InterruptException异常，捕获该异常并在调用wakeup()

## 3 模板代码

**一个服务端的模板代码：**

有了模板代码我们在编写程序时，大多数时间都是在模板代码中添加相应的业务代码

```java
ServerSocketChannel ssc = ServerSocketChannel.open();
ssc.socket().bind(newInetSocketAddress("localhost", 8080));
ssc.configureBlocking(false);
Selector selector = Selector.open();
ssc.register(selector, SelectionKey.OP_ACCEPT);
while (true) {
    int readyNum = selector.select();
    if (readyNum == 0) {
        continue;
    }
    Set<SelectionKey> selectedKeys = selector.selectedKeys();
    Iterator<SelectionKey> it = selectedKeys.iterator();
    while (it.hasNext()) {
        SelectionKey key = it.next();
        if (key.isAcceptable()) {
            // 接受连接
        } else if (key.isReadable()) {
            // 通道可读
        } else if (key.isWritable()) {
            // 通道可写
        }
        it.remove();
    }
}
```

## 4 客户端与服务端简单交互实例

**服务端：**

```java
import java.io.IOException;
import java.net.InetSocketAddress;
import java.nio.ByteBuffer;
import java.nio.channels.SelectionKey;
import java.nio.channels.Selector;
import java.nio.channels.ServerSocketChannel;
import java.nio.channels.SocketChannel;
import java.util.Iterator;
import java.util.Set;

public class WebServer {

	public static void main(String[] args) {
		try {
			ServerSocketChannel ssc = ServerSocketChannel.open();
			ssc.socket().bind(new InetSocketAddress("127.0.0.1", 8000));
			ssc.configureBlocking(false);

			Selector selector = Selector.open();
			// 注册channel，并且指定感兴趣的事件是Accept
			ssc.register(selector, SelectionKey.OP_ACCEPT);

			ByteBuffer readBuff = ByteBuffer.allocate(1024);
			ByteBuffer writeBuff = ByteBuffer.allocate(128);
			writeBuff.put("received".getBytes());
			writeBuff.flip();

			while (true) {
				int nReady = selector.select();
				Set<SelectionKey> keys = selector.selectedKeys();
				Iterator<SelectionKey> it = keys.iterator();

				while (it.hasNext()) {
					SelectionKey key = it.next();
					it.remove();
					if (key.isAcceptable()) {
					// 创建新的连接，并且把连接注册到selector上，而且声明这个channel只对读感兴趣
						SocketChannel socketChannel = ssc.accept();
						socketChannel.configureBlocking(false);
						socketChannel.register(selector, SelectionKey.OP_READ);
					} else if (key.isReadable()) {
						SocketChannel socketChannel = (SocketChannel) key.channel();
						readBuff.clear();
						socketChannel.read(readBuff);
						readBuff.flip();
						System.out.println("received：" + new String(readBuff.array()));
						key.interestOps(SelectionKey.OP_WRITE);
					} else if (key.isWritable()) {
						writeBuff.rewind();
						SocketChannel socketChannel = (SocketChannel) key.channel();
						socketChannel.write(writeBuff);
						key.interestOps(SelectionKey.OP_READ);
					}
				}
			}
		} catch (IOException e) {
			e.printStackTrace();
		}
	}

}
```

**客户端：**

```java
import java.io.IOException;
import java.net.InetSocketAddress;
import java.nio.ByteBuffer;
import java.nio.channels.SocketChannel;

public class WebClient {

	public static void main(String[] args) {
		try {
			SocketChannel socketChannel = SocketChannel.open();
			socketChannel.connect(new InetSocketAddress("127.0.0.1", 8000));

			ByteBuffer writeBuffer = ByteBuffer.allocate(32);
			ByteBuffer readBuffer = ByteBuffer.allocate(32);
			writeBuffer.put("hello".getBytes());
			writeBuffer.flip();

			while (true) {
				writeBuffer.rewind();
				socketChannel.write(writeBuffer);
				readBuffer.clear();
				socketChannel.read(readBuffer);
			}
		} catch (IOException e) {
			e.printStackTrace();
		}
	}
}
```



# 五、NIO 之 Path 和 Files

## 1 Path 类

Java7中文件IO发生了很大的变化，专门引入了很多新的类来取代原来的基于java.io.File的文件IO操作方式：

我们将从下面几个方面来学习Path类:

- 创建一个Path
- File和Path之间的转换，File和URI之间的转换
- 获取Path的相关信息
- 移除Path中的冗余项

### 1.1 创建一个Path

创建Path实例可以通过 **Paths工具类** 的 **get（）方法：**

```java
// 使用绝对路径
Path path = Paths.get("c:\\data\\myfile.txt");
// 使用相对路径
Path path = Paths.get("/home/jakobjenkov/myfile.txt");
```

下面这种创建方式和上面等效：

```java
Path path = FileSystems.getDefault().getPath("c:\\data\\myfile.txt");
```

### 1.2 File和Path之间的转换，File和URI之间的转换

```java
File file = newFile("C:/my.ini");
Path p1 = file.toPath();
p1.toFile();
file.toURI();
```

### 1.3 获取Path的相关信息

```java
// 使用Paths工具类的get()方法创建
Path path = Paths.get("D:\\XMind\\bcl-java.txt");
/*
* //使用FileSystems工具类创建
* 
* Path path2 = FileSystems.getDefault().getPath("c:\\data\\myfile.txt");
*/
System.out.println("文件名：" + path.getFileName());
System.out.println("名称元素的数量：" + path.getNameCount());
System.out.println("父路径：" + path.getParent());
System.out.println("根路径：" + path.getRoot());
System.out.println("是否是绝对路径：" + path.isAbsolute());
// startsWith()方法的参数既可以是字符串也可以是Path对象
System.out.println("是否是以为给定的路径D:开始：" + path.startsWith("D:\\"));
System.out.println("该路径的字符串形式：" + path.toString());
```

**结果：**

```java
文件名：bcl-java.txt
名称元素的数量：2
父路径：D:\XMind
根路径：D:\
是否是绝对路径：true
是否是以为给定的路径D:开始：true
该路径的字符串形式：D:\XMind\bcl-java.txt
```

### 1.4 移除冗余项

某些时候在我们需要处理的Path路径中可能会有一个或两个点

- . 表示的是当前目录
- .. 表示父目录或者说是上一级目录：

下面通过实例来演示一下使用Path类的normalize()和toRealPath()方法把.和..去除。

- **normalize()** : 返回一个路径，该路径是冗余名称元素的消除。
- **toRealPath()** : 融合了toAbsolutePath()方法和normalize()方法

```java
// .表示的是当前目录
Path currentDir = Paths.get(".");
System.out.println(currentDir.toAbsolutePath());
// 输出C:\Users\Administrator\NIODemo\.
Path currentDir2 = Paths.get(".\\NIODemo.iml");
System.out.println("原始路径格式：" + currentDir2.toAbsolutePath());
System.out.println("执行normalize（）方法之后：" + currentDir2.toAbsolutePath().normalize());
System.out.println("执行toRealPath()方法之后：" + currentDir2.toRealPath());
// ..表示父目录或者说是上一级目录：
Path currentDir3 = Paths.get("..");
System.out.println("原始路径格式：" + currentDir3.toAbsolutePath());
System.out.println("执行normalize（）方法之后：" + currentDir3.toAbsolutePath().normalize());
System.out.println("执行toRealPath()方法之后：" + currentDir3.toRealPath());
```

**结果：**

```java
C:\Users\Administrator\NIODemo\.
原始路径格式：C:\Users\Administrator\NIODemo\.\NIODemo.iml
执行 normalize()方法之后：C:\Users\Administrator\NIODemo\NIODemo.iml
执行 toRealPath()方法之后：C:\Users\Administrator\NIODemo\NIODemo.iml
原始路径格式：C:\Users\Administrator\NIODemo\..
执行 normalize()方法之后：C:\Users\Administrator
执行 toRealPath()方法之后：C:\Users\Administrator
```

## 2 Files 类

Java NIO中的Files类（java.nio.file.Files）提供了多种操作文件系统中文件的方法。本节教程将覆盖大部分方法。Files类包含了很多方法，所以如果本文没有提到的你也可以直接查询JavaDoc文档。

java.nio.file.Files类是和java.nio.file.Path相结合使用的

### 2.1 检查给定的Path在文件系统中是否存在

通过 **Files.exists()** 检测文件路径是否存在：

```java
Path path = Paths.get("D:\\XMind\\bcl-java.txt");
boolean pathExists = Files.exists(path, new LinkOption[] { LinkOption.NOFOLLOW_LINKS });
System.out.println(pathExists);// true
```

注意Files.exists()的的第二个参数。它是一个数组，这个参数直接影响到Files.exists()如何确定一个路径是否存在。在本例中，这个数组内包含了LinkOptions.NOFOLLOW_LINKS，表示检测时不包含符号链接文件。

### 2.2 创建文件/文件夹

**创建文件：**

通过 **Files.createFile()** 创建文件

```java
Path target2 = Paths.get("C:\\mystuff.txt");
try {
    if (!Files.exists(target2))
        Files.createFile(target2);
} catch (IOException e) {
    e.printStackTrace();
}
```

**创建文件夹：**

Files.createDirectories()会首先创建所有不存在的父目录来创建目录，而Files.createDirectory()方法只是创建目录，如果它的上级目录不存在就会报错。比如下面的程序使用**Files.createDirectory()** 方法创建就会报错，这是因为我的D盘下没有data文件夹，加入存在data文件夹的话则没问题。

通过 **Files.createDirectory()** 创建文件夹

通过 **Files.createDirectories()** 创建文件夹

```java
Path path = Paths.get("D://data//test");
try {
    Path newDir = Files.createDirectories(path);
} catch (FileAlreadyExistsException e) {
    // the directory already exists.
} catch (IOException e) {
    // something else went wrong
    e.printStackTrace();
}
```

### 2.3 删除文件或目录

通过 **Files.delete()方法** 可以删除一个文件或目录：

```java
Path path = Paths.get("data/subdir/logging-moved.properties");
try {
    Files.delete(path);
} catch (IOException e) {
    // deleting file failed
    e.printStackTrace();
}
```

### 2.4 把一个文件从一个地址复制到另一个位置

通过Files.copy()方法可以把一个文件从一个地址复制到另一个位置

```java
Path sourcePath = Paths.get("data/logging.properties");
Path destinationPath = Paths.get("data/logging-copy.properties");
try {
    Files.copy(sourcePath, destinationPath);
} catch (FileAlreadyExistsException e) {
    // destination file already exists
} catch (IOException e) {
    // something else went wrong
    e.printStackTrace();
}
```

copy操作还可可以强制覆盖已经存在的目标文件，只需要将上面的copy()方法改为如下格式：

```java
Files.copy(sourcePath, destinationPath, StandardCopyOption.REPLACE_EXISTING);
```

### 2.5 获取文件属性

```java
Path path = Paths.get("D:\\XMind\\bcl-java.txt");
System.out.println(Files.getLastModifiedTime(path));
System.out.println(Files.size(path));
System.out.println(Files.isSymbolicLink(path));
System.out.println(Files.isDirectory(path));
System.out.println(Files.readAttributes(path, "*"));
```

结果：

```java
2019-06-23T02:40:16.076701Z
15
false
false
{lastAccessTime=2019-06-23T02:40:24.708508Z, lastModifiedTime=2019-06-23T02:40:16.076701Z, size=15, creationTime=2019-06-23T02:15:58.715551Z, isSymbolicLink=false, isRegularFile=true, fileKey=null, isOther=false, isDirectory=false}
```

### 2.6 遍历一个文件夹

```java
Path dir = Paths.get("C:\\");
try (DirectoryStream<Path> stream = Files.newDirectoryStream(dir)) {
    for (Path e : stream) {
        System.out.println(e.getFileName());
    }
} catch (IOException e) {

}
```

结果：

```java
$Recycle.Bin
$WINRE_BACKUP_PARTITION.MARKER
AMTAG.BIN
Config.Msi
Documents and Settings
hiberfil.sys
inetpub
pagefile.sys
Program Files
Program Files (x86)
ProgramData
Recovery
swapfile.sys
System Volume Information
Users
Windows
```

上面是遍历单个目录，它不会遍历整个目录。遍历整个目录需要使用：Files.walkFileTree() 方法具有递归遍历目录的功能。

### 2.7 遍历整个文件目录：

walkFileTree接受一个Path和FileVisitor作为参数。Path对象是需要遍历的目录，FileVistor则会在每次遍历中被调用。

FileVisitor需要调用方自行实现，然后作为参数传入walkFileTree()。FileVisitor的每个方法会在遍历过程中被调用多次。如果不需要处理每个方法，那么可以继承它的默认实现类SimpleFileVisitor，它将所有的接口做了空实现。

```java
public class WorkFileTree {

	public static void main(String[] args) throws Exception {
		Path startingDir = Paths.get("D:\\apache-tomcat-9.0.0.M17");
		List<Path> result = new LinkedList<Path>();
		Files.walkFileTree(startingDir, new FindJavaVisitor(result));
		System.out.println("result.size()=" + result.size());
	}

	private static class FindJavaVisitor extends SimpleFileVisitor<Path> {
		private List<Path> result;

		public FindJavaVisitor(List<Path> result) {
			this.result = result;
		}

		@Override
		public FileVisitResult visitFile(Path file, BasicFileAttributes attrs) {
			if (file.toString().endsWith(".java")) {
				result.add(file.getFileName());
			}
			return FileVisitResult.CONTINUE;
		}
	}
}
```

上面这个例子输出了我的D:\apache-tomcat-9.0.0.M17也就是我的Tomcat安装目录下以.java结尾文件的数量。

Files类真的很强大，除了这些操作之外还有其他很多操作比如：读取和设置文件权限、更新文件所有者等等操作。



# 六、NIO 总结及 NIO 新特性

## 1. NIO的新特性

总的来说java 中的IO 和NIO的区别主要有3点：

- IO是面向流的，NIO是面向缓冲的；
- IO是阻塞的，NIO是非阻塞的；
- IO是单线程的，NIO 是通过选择器来模拟多线程的；

NIO在基础的IO流上发展处新的特点，分别是：内存映射技术，字符及编码，非阻塞I/O和文件锁定。下面我们分别就这些技术做一些说明。

## 2. 内存映射

这个功能主要是为了提高大文件的读写速度而设计的。内存映射文件(memory-mappedfile)能让你创建和修改那些大到无法读入内存的文件。有了内存映射文件，你就可以认为文件已经全部读进了内存，然后把它当成一个非常大的数组来访问了。将文件的一段区域映射到内存中，比传统的文件处理速度要快很多。内存映射文件它虽然最终也是要从磁盘读取数据，但是它并不需要将数据读取到OS内核缓冲区，而是直接将进程的用户私有地址空间中的一部分区域与文件对象建立起映射关系，就好像直接从内存中读、写文件一样，速度当然快了。

NIO中内存映射主要用到以下两个类：

- java.nio.MappedByteBuffer
- java.nio.channels.FileChannel

调用FileChannel类的map方法进行内存映射，第一个参数设置映射模式,现在支持3种模式：

- **FileChannel.MapMode.READ_ONLY**：只读缓冲区，在缓冲区中如果发生写操作则会产生ReadOnlyBufferException；
- **FileChannel.MapMode.READ_WRITE**：读写缓冲区，任何时刻如果通过内存映射的方式修改了文件则立刻会对磁盘上的文件执行相应的修改操作。别的进程如果也共享了同一个映射，则也会同步看到变化。而不是像标准IO那样每个进程有各自的内核缓冲区，比如JAVA代码中，没有执行IO输出流的 flush() 或者 close() 操作，那么对文件的修改不会更新到磁盘去，除非进程运行结束；
- **FileChannel.MapMode.PRIVATE** ：这个比较狠，可写缓冲区，但任何修改是缓冲区私有的，不会回到文件中。所以尽情的修改吧，结局跟突然停电是一样的。

我们注意到FileChannel类中有map方法来建立内存映射，按理说是否应用的有相应的unmap方法来卸载映射内存呢。但是竟然没有找到该方法。一旦建立映射保持有效，直到MappedByteBuffer对象被垃圾收集。 此外，映射缓冲区不会绑定到创建它们的通道。 关闭相关的FileChannel不会破坏映射; 只有缓冲对象本身的处理打破了映射。

**内存映射文件的优点：**

1. 用户进程将文件数据视为内存，因此不需要发出read()或write()系统调用。
2. 当用户进程触摸映射的内存空间时，将自动生成页面错误，以从磁盘引入文件数据。 如果用户修改映射的内存空间，受影响的页面将自动标记为脏，并随后刷新到磁盘以更新文件。
3. 操作系统的虚拟内存子系统将执行页面的智能缓存，根据系统负载自动管理内存。
4. 数据始终是页面对齐的，不需要缓冲区复制。
5. 可以映射非常大的文件，而不消耗大量内存来复制数据。

## 3. 字符及编码

说到字符和编码，我们的先说一个概念，**字符编码方案**：

编码方案定义了如何把字符编码的序列表达为字节序列。字符编码的数值不需要与编码字节相同，也不需要是一对一或一对多个的关系。原则上，把字符集编码和解码近似视为对象的序列化和反序列化。

通常字符数据编码是用于网络传输或文件存储。编码方案不是字符集，它是映射；但是因为它们之间的紧密联系，大部分编码都与一个独立的字符集相关联。例如，UTF-8，仅用来编码Unicode字符集。尽管如此，用一个编码方案处理多个字符集还是可能发生的。例如，EUC可以对几个亚洲语言的字符进行编码。

目前字符编码方案有US-ASCII,UTF-8,GB2312, BIG5,GBK,GB18030,UTF-16BE, UTF-16LE, UTF-16,UNICODE。其中Unicode试图把全世界所有语言的字符集统一到全面的映射之中。虽然占有一定的市场份额，但是目前其余的字符方案仍然广被采用。大部分的操作系统在I/O与文件存储方面仍是以字节为导向的，所以无论使用何种编码，Unicode或其他编码，在字节序列和字符集编码之间仍需要进行转化。

由java.nio.charset包组成的类满足了这个需求。这不是Java平台第一次处理字符集编码，但是它是最系统、最全面、以及最灵活的解决方式。

**字符集编码器和解码器**

字符的编码和解码是使用很频繁的，试想如果使用UTF-8字符集进行编码，但是却是用UTF-16字符集进行解码，那么这条信息对于用户来说其实是无用的。因为没人能看得懂。

在NIO中提供了两个类**CharsetEncoder**和**CharsetDecoder**来实现编码转换方案。

CharsetEncoder类是一个状态编码引擎。实际上，编码器有状态意味着它们不是线程安全的：CharsetEncoder对象不应该在线程中共享。CharsetEncoder对象是一个状态转换引擎：字符进去，字节出来。一些编码器的调用可能需要完成转换。编码器存储在调用之间转换的状态。

字符集解码器是编码器的逆转。通过特殊的编码方案把字节编码转化成16-位Unicode字符的序列。与CharsetEncoder类似的, CharsetDecoder也是状态转换引擎。

## 4. 非阻塞IO

一般来说 I/O 模型可以分为：同步阻塞，同步非阻塞，异步阻塞，异步非阻塞 四种IO模型。

**同步阻塞 IO ：**

在此种方式下，用户进程在发起一个 IO 操作以后，必须等待 IO 操作的完成，只有当真正完成了 IO 操作以后，用户进程才能运行。 JAVA传统的 IO 模型属于此种方式！

**同步非阻塞 IO:**

在此种方式下，用户进程发起一个 IO 操作以后可以返回做其它事情，但是用户进程需要时不时的询问 IO 操作是否就绪，这就要求用户进程不停的去询问，从而引入不必要的 CPU 资源浪费。其中目前 JAVA 的 NIO 就属于同步非阻塞 IO 。

**异步阻塞 IO ：**

此种方式下是指应用发起一个 IO 操作以后，不等待内核 IO 操作的完成，等内核完成 IO 操作以后会通知应用程序，这其实就是同步和异步最关键的区别，同步必须等待或者主动的去询问 IO 是否完成，那么为什么说是阻塞的呢？因为此时是通过 select 系统调用来完成的，而 select 函数本身的实现方式是阻塞的，而采用 select 函数有个好处就是它可以同时监听多个文件句柄，从而提高系统的并发性！

**异步非阻塞 IO:**

在此种模式下，用户进程只需要发起一个 IO 操作然后立即返回，等 IO 操作真正的完成以后，应用程序会得到 IO 操作完成的通知，此时用户进程只需要对数据进行处理就好了，不需要进行实际的 IO 读写操作，因为 真正的 IO读取或者写入操作已经由内核完成了。目前 Java 中还没有支持此种 IO 模型。

上面我们说到nio是使用了同步非阻塞模型。我们知道典型的非阻塞IO模型一般如下：

```java
while(true){
    data = socket.read();
    if(data!= error){
        处理数据
        break;
    }
}
```

但是对于非阻塞IO就有一个非常严重的问题，在while循环中需要不断地去询问内核数据是否就绪，这样会导致CPU占用率非常高，因此一般情况下很少使用while循环这种方式来读取数据。所以这就不得不说到下面这个概念–多路复用IO模型。

**多路复用IO模型**

在多路复用IO模型中，会有一个线程不断去轮询多个socket的状态，只有当socket真正有读写事件时，才真正调用实际的IO读写操作。因为在多路复用IO模型中，只需要使用一个线程就可以管理多个socket，系统不需要建立新的进程或者线程，也不必维护这些线程和进程，并且只有在真正有socket读写事件进行时，才会使用IO资源，所以它大大减少了资源占用。

NIO 的非阻塞 I/O 机制是围绕 选择器和 通道构建的。 Channel 类表示服务器和客户机之间的一种通信机制。Selector 类是 Channel 的多路复用器。 Selector 类将传入客户机请求多路分用并将它们分派到各自的请求处理程序。NIO 设计背后的基石是**反应器(Reactor)设计模式。**

Reactor负责IO事件的响应，一旦有事件发生，便广播发送给相应的handler去处理。而NIO的设计则是完全按照Reactor模式来设计的。Selector发现某个channel有数据时，会通过SelectorKey来告知，然后实现事件和handler的绑定。

在Reactor模式中，包含如下角色：

- Reactor 将I/O事件发派给对应的Handler
- Acceptor 处理客户端连接请求
- Handlers 执行非阻塞读/写

利用多路复用机制避免了线程的阻塞，提高了连接的数量。一个线程就可以管理多个socket，只有当socket真正有读写事件发生才会占用资源来进行实际的读写操作。虽然多线程+ 阻塞IO达到类似的效果，但是由于在多线程 + 阻塞IO 中，每个socket对应一个线程，这样会造成很大的资源占用，并且尤其是对于长连接来说，线程的资源一直不会释放，如果后面陆续有很多连接的话，就会造成性能上的瓶颈。

另外多路复用IO为何比非阻塞IO模型的效率高是因为在非阻塞IO中，不断地询问socket状态时通过用户线程去进行的，而在多路复用IO中，轮询每个socket状态是内核在进行的，这个效率要比用户线程要高的多。

## 5. 文件锁定

NIO中的文件通道（FileChannel）在读写数据的时候主要使用了阻塞模式，它不能支持非阻塞模式的读写，而且FileChannel的对象是不能够直接实例化的， 他的实例只能通过getChannel()从一个打开的文件对象上边读取（RandomAccessFile、 FileInputStream、FileOutputStream），并且通过调用getChannel()方法返回一个 Channel对象去连接同一个文件，也就是针对同一个文件进行读写操作。

文件锁的出现解决了很多Java应用程序和非Java程序之间共享文件数据的问题，在以前的JDK版本中，没有文件锁机制使得Java应用程序和其他非Java进程程序之间不能够针对同一个文件共享数据，有可能造成很多问题，JDK1.4里面有了FileChannel，它的锁机制使得文件能够针对很多非 Java应用程序以及其他Java应用程序可见。但是Java里面的文件锁机制主要是基于共享锁模型，在不支持共享锁模型的操作系统上，文件锁本身也起不了作用，JDK1.4使用文件通道读写方式可以向一些文件发送锁请求，FileChannel的锁模型主要针对的是每一个文件，并不是每一个线程和每一个读写通道，也就是以文件为中心进行共享以及独占，也就是文件锁本身并不适合于同一个JVM的不同线程之间。

我们简要看一下相关API：

```java
// 如果请求的锁定范围是有效的，阻塞直至获取锁
public final FileLock lock()  
// 尝试获取锁非阻塞，立刻返回结果  
public final FileLock tryLock()  
// 第一个参数：要锁定区域的起始位置  
// 第二个参数：要锁定区域的尺寸,  
// 第三个参数：true为共享锁，false为独占锁  
public abstract FileLock lock (long position, long size, boolean shared)  
public abstract FileLock tryLock (long position, long size, boolean shared) 
```

锁定区域的范围不一定要限制在文件的size值以内，锁可以扩展从而超出文件尾。因此，我们可以提前把待写入数据的区域锁定，我们也可以锁定一个不包含任何文件内容的区域，比如文件最后一个字节以外的区域。如果之后文件增长到达那块区域，那么你的文件锁就可以保护该区域的文件内容了。相反地，如果你锁定了文件的某一块区域，然后文件增长超出了那块区域，那么新增加的文件内容将不会受到您的文件锁的保护。

# 七、AIO

## 1 AIO 简介

Jdk7中新增了一些与文件(网络)I/O相关的一些api。这些API被称为NIO.2，或称为AIO(Asynchronous I/O)。AIO最大的一个特性就是异步能力，这种能力对socket与文件I/O都起作用。AIO其实是一种在读写操作结束之前允许进行其他操作的I/O处理。AIO是对JDK1.4中提出的同步非阻塞I/O(NIO)的进一步增强。

jdk7主要增加了三个新的异步通道:

- AsynchronousFileChannel: 用于文件异步读写；
- AsynchronousSocketChannel: 客户端异步socket；
- AsynchronousServerSocketChannel: 服务器异步socket。

因为AIO的实施需充分调用OS参与，IO需要操作系统支持、并发也同样需要操作系统的支持，所以性能方面不同操作系统差异会比较明显。

## 2 前提概念

### 2.1 Unix中的I/O模型

Unix定义了五种 I/O 模型

- 阻塞I/O
- 非阻塞I/O
- I/O复用（select、poll、linux 2.6种改进的epoll）
- 信号驱动IO（SIGIO）
- 异步I/O（POSIX的aio_系列函数）

> 如果你想吃一份宫保鸡丁盖饭：
>
> - 同步阻塞：你到饭馆点餐，然后在那等着，还要一边喊：好了没啊！
> - 同步非阻塞：在饭馆点完餐，就去遛狗了。不过溜一会儿，就回饭馆喊一声：好了没啊！
> - 异步阻塞：遛狗的时候，接到饭馆电话，说饭做好了，让您亲自去拿。
> - 异步非阻塞：饭馆打电话说，我们知道您的位置，一会给你送过来，安心遛狗就可以了。

### 2.2 Reactor与Proactor

- 两种IO多路复用方案:Reactor and Proactor。
- Reactor模式是基于同步I/O的，而Proactor模式是和异步I/O相关的。
- reactor：能收了你跟俺说一声。proactor: 你给我收十个字节，收好了跟俺说一声。

## 3 异步的处理

异步无非是通知系统做一件事情。然后忘掉它，自己做其他事情去了。很多时候系统做完某一件事情后需要一些后续的操作。怎么办？这时候就是告诉异步调用如何做后续处理。通常有两种方式：

- 将来式: 当你希望主线程发起异步调用，并轮询等待结果的时候使用将来式;
- 回调式: 常说的异步回调就是它。

以文件读取为例

**将来式**

![img](https://img-blog.csdn.net/20180714132908968?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21vYWt1bg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

将来式异步读取

将来式用现有的Java.util.concurrent技术声明一个Future,用来保存异步操作的处理结果。通常用Future.get()方法（带或不带超时参数）在异步IO操作完成时获取其结果。

AsynchronousFileChannel会关联线程池，它的任务是接收IO处理事件，并分发给负责处理通道中IO操作结果的结果处理器。跟通道中发起的IO操作关联的结果处理器确保是由线程池中的某个线程产生。

**将来式例子**：

```java
Path path = Paths.get("/data/code/github/1log4j.properties");
AsynchronousFileChannel channel = AsynchronousFileChannel.open(path);
ByteBuffer buffer = ByteBuffer.allocate(1024);
Future<Integer> future = channel.read(buffer,0);
//        while (!future.isDone()){
//            System.out.println("I'm idle");
//        }
Integer readNumber = future.get();

buffer.flip();
CharBuffer charBuffer = CharBuffer.allocate(1024);
CharsetDecoder decoder = Charset.defaultCharset().newDecoder();
decoder.decode(buffer,charBuffer,false);
charBuffer.flip();
String data = new String(charBuffer.array(),0, charBuffer.limit());
System.out.println("read number:" + readNumber);
System.out.println(data);
```

**回调式**

![img](https://img-blog.csdn.net/2018071413302529?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21vYWt1bg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

回调式异步读取

回调式所采用的事件处理技术类似于Swing UI编程采用的机制。基本思想是主线程会派一个侦查员CompletionHandler到独立的线程中执行IO操作。这个侦查员将带着IO的操作的结果返回到主线程中，这个结果会触发它自己的completed或failed方法（要重写这两个方法）。在异步IO活动结束后，接口java.nio.channels.CompletionHandler会被调用，其中V是结果类型，A是提供结果的附着对象。此时必须已经有了该接口completed（V,A）和failed(V,A)方法的实现，你的程序才能知道异步IO操作成功或失败时该如何处理。

**回调式例子**：

```java
Path path = Paths.get("/data/code/github/java_practice/src/main/resources/1log4j.properties");
AsynchronousFileChannel channel = AsynchronousFileChannel.open(path);
ByteBuffer buffer = ByteBuffer.allocate(1024);
channel.read(buffer, 0, buffer, new CompletionHandler<Integer, ByteBuffer>() {
    @Override
    public void completed(Integer result, ByteBuffer attachment) {
        System.out.println(Thread.currentThread().getName() + " read success!");
    }

    @Override
    public void failed(Throwable exc, ByteBuffer attachment) {
        System.out.println("read error");
    }
});

while (true){
    System.out.println(Thread.currentThread().getName() + " sleep");
    Thread.sleep(1000);
}
```

## 4 异步 Socket

**服务端**

```java
final AsynchronousServerSocketChannel channel = AsynchronousServerSocketChannel
            .open()
    .bind(new InetSocketAddress("0.0.0.0",8888));
channel.accept(null, new CompletionHandler<AsynchronousSocketChannel, Void>() {
    @Override
    public void completed(final AsynchronousSocketChannel client, Void attachment) {
        channel.accept(null, this);

        ByteBuffer buffer = ByteBuffer.allocate(1024);
        client.read(buffer, buffer, new CompletionHandler<Integer, ByteBuffer>() {
            @Override
            public void completed(Integer result_num, ByteBuffer attachment) {
                attachment.flip();
                CharBuffer charBuffer = CharBuffer.allocate(1024);
                CharsetDecoder decoder = Charset.defaultCharset().newDecoder();
                decoder.decode(attachment,charBuffer,false);
                charBuffer.flip();
                String data = new String(charBuffer.array(),0, charBuffer.limit());
                System.out.println("read data:" + data);
                try{
                    client.close();
                }catch (Exception e){}
            }

            @Override
            public void failed(Throwable exc, ByteBuffer attachment) {
                System.out.println("read error");
            }
        });
    }

    @Override
    public void failed(Throwable exc, Void attachment) {
        System.out.println("accept error");
    }
});

while (true){
    Thread.sleep(1000);
}
```

**客户端**

```java
AsynchronousSocketChannel channel = AsynchronousSocketChannel.open();
channel.connect(new InetSocketAddress("127.0.0.1",8888)).get();
ByteBuffer buffer = ByteBuffer.wrap("中文,你好".getBytes());
Future<Integer> future = channel.write(buffer);

future.get();
System.out.println("send ok");
```

