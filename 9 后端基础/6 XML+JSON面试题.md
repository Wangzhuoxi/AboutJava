## 1 什么是JSON和XML

JSON：JavaScript Object Notation 【JavaScript 对象表示法】.

XML:extensiable markup language 被称作可扩展标记语言.

JSON和XML都是数据交换语言，完全独立于任何程序语言的文本格式。

## 2 JSON与XML区别是什么？ 有什么共同点？

共同点：

- 用于RPC远程调用数据交换格式，RPC远程调用简单理解：调用本地服务一样调用远程服务。

区别：

- XML文件格式复杂，比较占宽带，服务器端与客户端解析xml话费较多的资源和时间.
- JSON文件格式压缩，占宽带小

## 3 JSON、XML解析有那些方式？

- JSON解析方式(阿里巴巴fastjson、谷歌gson,jackJson)
- XML解析方式(dom、sax、pul)

## 4 XML和JSON优缺点

XML的优点

- 格式统一，符合标准；
- 容易与其他系统进行远程交互，数据共享比较方便

XML的缺点

- XML文件庞大，文件格式复杂，传输占带宽；
- 服务器端和客户端都需要花费大量代码来解析XML，导致服务器端和客户端代码变得异常复杂且不易维护；
- 客户端不同浏览器之间解析XML的方式不一致，需要重复编写很多代码；
- 服务器端和客户端解析XML花费较多的资源和时间。

JSON的优点

- 数据格式比较简单，易于读写，格式都是压缩的，占用带宽小；
- 易于解析，客户端JavaScript可以简单的通过eval_r()进行JSON数据的读取；
- 支持多种语言，包括ActionScript, C, C#, ColdFusion, Java, JavaScript, Perl, PHP, Python, Ruby等服务器端语言，便于服务器端的解析；
- 因为JSON格式能直接为服务器端代码使用，大大简化了服务器端和客户端的代码开发量，且完成任务不变，并且易于维护

JSON的缺点

- 没有XML格式这么推广的深入人心和喜用广泛，没有XML那么通用性；
- JSON片段的创建和验证过程比一般的XML稍显复杂。

## 5 XPath 是什么

XPath 是用于从 XML 文档检索元素的 XML 技术。XML 文档是结构化的，因此 XPath 可以从 XML 文件定位和检索元素、属性或值。从数据检索方面来说，XPath与 SQL 很相似，但是它有自己的语法和规则。了解更多查看怎样使用 XPath 从 XML 文档中检索数据.

## 6 XML 命名空间是什么？它为什么很重要？

XML 命名空间与 Java 的 package 类似，用来避免不同来源名称相同的标签发生冲突。XML 命名空间在 XML 文档顶部使用 xmlns 属性定义，语法为 xmlns:prefix=’URI’。prefix 与XML 文档中实际标签一起使用。

```xml
<root xmlns:inst="http://instruments.com/inst"
<inst:phone>
<inst:number>837363223</inst:number>
</inst:phone>
</root>
```

## 7 DOM 和 和 SAX 解析器有什么区别?

- DOM解析读取整个XML文档，在内存中形成DOM树，很方便地对XML文档的内容进行增删改。但如果XML文档的内容过大，那么就会导致内存溢出！
- SAX解析采用部分读取的方式，可以处理大型文件，但只能对文件按顺序从头到尾解析一遍，不支持文件的增删改操作.

1. DOM是基于内存的，不管文件有多大，都会将所有的内容预先装载到内存中。从而消耗很大的内存空间。而SAX是基于事件的。当某个事件被触发时，才获取相应的XML的部分数据，从而不管XML文件有多大，都只占用了少量的内存空间。
2. DOM可以读取XML也可以向XML文件中插入数据，而SAX却只能对XML进行读取，而不能在文件中插入数据。这也是SAX的一个缺点。
3. SAX的另一个缺点：DOM我们可以指定要访问的元素进行随机访问，而SAX则不行。SAX是从文档开始执行遍历的。并且只能遍历一次。也就是说我们不能随机的访问XML文件，只能从头到尾的将XML文件遍历一次（当然也可以中间截断遍历）。

## 8 XSLT 是什么?

XSLT 也是常用的 XML 技术， 用于将一个 XML 文件转换为另一种 XML，HTML 或者其他的格式。XSLT 为转换 XML 文件详细定义了自己的语法，函数和操作符。通常由 XSLT 引擎完成转换，XSLT 引擎读取 XSLT 语法编写的 XML 样式表或者 XSL 文件的指令。XSLT 大量使用递归来执行转换。一个常见 XSLT 使用就是将 XML 文件中的数据作为 HTML 页面显示。XSLT 也可以很方便地把一种 XML 文件转换为另一种 XML 文档.