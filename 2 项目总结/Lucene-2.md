## 1 索引和搜索流程图

![img](https://images2017.cnblogs.com/blog/1227182/201710/1227182-20171011190654543-1014690139.png)

绿色表示索引过程，对要搜索的原始内容进行索引构建一个索引库，索引过程包括：

1. 确定原始内容即要搜索的内容
2. 采集文档
3. 创建文档
4. 分析文档
5. 索引文档

红色表示搜索过程，从索引库中搜索内容，搜索过程包括：

1. 用户通过搜索界面
2. 创建查询
3. 执行搜索，从索引库搜索
4. 渲染搜索结果

## 2 创建索引

对文档索引的过程，将用户要搜索的文档内容进行索引，索引存储在索引库（index）中。

### 2.1 获得原始文档

**原始文档**是指要索引和搜索的内容。原始内容包括互联网上的网页、数据库中的数据、磁盘上的文件等。

### 2.2 创建文档对象

获取原始内容的目的是为了索引，在索引前需要将原始内容创建成文档（Document），文档中包括一个一个的域（Field），域中存储内容。

这里我们可以将磁盘上的一个文件当成一个document，Document中包括一些Field（file_name文件名称、file_path文件路径、file_size文件大小、file_content文件内容），如下图：

![img](https://images2017.cnblogs.com/blog/1227182/201710/1227182-20171011192234699-115366628.png)

注意：

- 每个Document可以有多个Field
- 不同的Document可以有不同的Field
- 同一个Document可以有相同的Field（域名和域值都相同）
- 每个文档都有一个唯一的编号，就是文档id。

### 2.3 分析文档

将原始内容创建为包含域（Field）的文档（document），需要再对域中的内容进行分析，分析的过程是经过对原始文档提取单词、将字母转为小写、去除标点符号、去除停用词等过程生成最终的语汇单元，可以将语汇单元理解为一个一个的单词。

比如下边的文档经过分析如下：

原文档内容：Lucene is a Java full-text search engine.  

分析后得到的**语汇单元**：lucene、java、full、search、engine

每个单词叫做一个**Term**，不同的域中拆分出来的相同的单词是不同的term。term中包含两部分一部分是文档的域名，另一部分是单词的内容。

例如：文件名中包含apache和文件内容中包含的apache是不同的term。

### 2.4 创建索引

对所有文档分析得出的语汇单元进行索引，索引的目的是为了搜索，最终要实现只搜索被索引的语汇单元从而找到Document（文档）。

![img](https://images2017.cnblogs.com/blog/1227182/201710/1227182-20171011194345059-873127678.png)

![img](https://images2017.cnblogs.com/blog/1227182/201710/1227182-20171011193525215-791530589.png)

注意：

1. 创建索引是对语汇单元索引，通过词语找文档，这种索引的结构叫**倒排索引结构**。
2. 传统方法是根据文件找到该文件的内容，在文件内容中匹配搜索关键字，这种方法是顺序扫描方法，数据量大、搜索慢。
3. 倒排索引结构是根据内容（词语）找文档，如下图：

![img](https://images2017.cnblogs.com/blog/1227182/201710/1227182-20171011193758934-50928958.png)

倒排索引结构也叫反向索引结构，包括索引和文档两部分，索引即词汇表，它的规模较小，而文档集合较大。

**创建索引代码实例：**

**具体步骤：**

第一步：创建一个indexwriter对象。

1. 指定索引库的存放位置Directory对象
2. 指定一个分析器，对文档内容进行分析。

第二步：创建document对象。

第三步：创建field对象，将field添加到document对象中。

第四步：使用indexwriter对象将document对象写入索引库，此过程进行索引创建。并将索引和document对象写入索引库。

第五步：关闭IndexWriter对象。

```java
//创建索引
public void testCreateIndex() throws IOException{
    //指定索引库的存放位置Directory对象
    Directory directory = FSDirectory.open(new File("E:\\programme\\test"));
    //索引库还可以存放到内存中
    //Directory directory = new RAMDirectory();

    //指定一个标准分析器，对文档内容进行分析
    Analyzer analyzer = new StandardAnalyzer();

    //创建indexwriterCofig对象
    //第一个参数：Lucene的版本信息，可以选择对应的lucene版本也可以使用LATEST
    //第二根参数：分析器对象
    IndexWriterConfig config = new IndexWriterConfig(Version.LATEST, analyzer);

    //创建一个indexwriter对象
    IndexWriter indexWriter = new IndexWriter(directory, config);

    //原始文档的路径
    File file = new File("E:\\programme\\searchsource");
    File[] fileList = file.listFiles();
    for (File file2 : fileList) {
        //创建document对象
        Document document = new Document();

        //创建field对象，将field添加到document对象中

        //文件名称
        String fileName = file2.getName();
        //创建文件名域
        //第一个参数：域的名称
        //第二个参数：域的内容
        //第三个参数：是否存储
        Field fileNameField = new TextField("fileName", fileName, Store.YES);
        //文件的大小
        long fileSize  = FileUtils.sizeOf(file2);
        //文件大小域
        Field fileSizeField = new LongField("fileSize", fileSize, Store.YES);
        //文件路径
        String filePath = file2.getPath();
        //文件路径域（不分析、不索引、只存储）
        Field filePathField = new StoredField("filePath", filePath);
        //文件内容
        String fileContent = FileUtils.readFileToString(file2);
        //String fileContent = FileUtils.readFileToString(file2, "utf-8");
        //文件内容域
        Field fileContentField = new TextField("fileContent", fileContent, Store.YES);

        document.add(fileNameField);
        document.add(fileSizeField);
        document.add(filePathField);
        document.add(fileContentField);
        //使用indexwriter对象将document对象写入索引库，此过程进行索引创建。并将索引和document对象写入索引库。
        indexWriter.addDocument(document);
    }
    //关闭IndexWriter对象。
    indexWriter.close();
}
```

Field域的属性概述

1. **是否分析**：是否对域的内容进行分词处理。前提是我们要对域的内容进行查询。

2. **是否索引**：将Field分析后的词或整个Field值进行索引，只有索引方可搜索到。

   比如：商品名称、商品简介分析后进行索引，订单号、身份证号不用分析但也要索引，这些将来都要作为查询条件。

3. **是否存储**：将Field值存储在文档中，存储在文档中的Field才可以从Document中获取

   比如：商品名称、订单号，凡是将来要从Document中获取的Field都要存储。

## 3 查询索引

查询索引也是搜索的过程。搜索就是用户输入关键字，从索引（index）中进行搜索的过程。根据关键字搜索索引，根据索引找到对应的文档，从而找到要搜索的内容（这里指磁盘上的文件）。

对要搜索的信息创建Query查询对象，Lucene会根据Query查询对象生成最终的查询语法，类似关系数据库Sql语法一样Lucene也有自己的查询语法，比如：“name:lucene”表示查询Field的name为“lucene”的文档信息。

### 3.1.   用户查询接口

全文检索系统提供用户搜索的界面供用户提交搜索的关键字，搜索完成展示搜索结果。

比如： 百度搜索

Lucene不提供制作用户搜索界面的功能，需要根据自己的需求开发搜索界面。

### 3.2 创建查询

用户输入查询关键字执行搜索之前需要先构建一个查询对象，查询对象中可以指定查询要搜索的Field文档域、查询关键字等，查询对象会生成具体的查询语法，

例如： 语法 “fileName:lucene”表示要搜索Field域的内容为“lucene”的文档

### 3.3 执行查询

搜索索引过程：根据查询语法在倒排索引词典表中分别找出对应搜索词的索引，从而找到索引所链接的文档链表。

比如搜索语法为“fileName:lucene”表示搜索出fileName域中包含Lucene的文档。

搜索过程就是在索引上查找域为fileName，并且关键字为Lucene的term，并根据term找到文档id列表。

可通过两种方法创建查询对象：

**(1) 使用Lucene提供Query子类**

Query是一个抽象类，lucene提供了很多查询对象，比如TermQuery项精确查询，NumericRangeQuery数字范围查询等。

```java
Query query = new TermQuery(new Term("name", "lucene"));
```

**(2) 使用QueryParse解析查询表达式**

QueryParse会将用户输入的查询表达式解析成Query对象实例。

```java
QueryParser queryParser = new QueryParser("name", new IKAnalyzer());
Query query = queryParser.parse("name:lucene");
```

#### 3.3.1 使用query的子类查询

**实现步骤**

1. 创建一个Directory对象，也就是索引库存放的位置。
2. 创建一个indexReader对象，需要指定Directory对象。
3. 创建一个indexsearcher对象，需要指定IndexReader对象
4. 创建一个Query的子类对象，指定查询的域和查询的关键词。
5. 执行查询。
6. 返回查询结果。遍历查询结果并输出。
7. 关闭IndexReader对象

##### MatchAllDocsQuery

使用MatchAllDocsQuery查询索引目录中的所有文档

```java
@Test
public void testMatchAllDocsQuery() throws Exception {
    //创建一个Directory对象，指定索引库存放的路径
    Directory directory = FSDirectory.open(new File("E:\\programme\\test"));
    //创建IndexReader对象，需要指定Directory对象
    IndexReader indexReader = DirectoryReader.open(directory);
    //创建Indexsearcher对象，需要指定IndexReader对象
    IndexSearcher indexSearcher = new IndexSearcher(indexReader);

    //创建查询条件
    //使用MatchAllDocsQuery查询索引目录中的所有文档
    Query query = new MatchAllDocsQuery();
    //执行查询
    //第一个参数是查询对象，第二个参数是查询结果返回的最大值
    TopDocs topDocs = indexSearcher.search(query, 10);

    //查询结果的总条数
    System.out.println("查询结果的总条数："+ topDocs.totalHits);
    //遍历查询结果
    //topDocs.scoreDocs存储了document对象的id
    //ScoreDoc[] scoreDocs = topDocs.scoreDocs;
    for (ScoreDoc scoreDoc : topDocs.scoreDocs) {
        //scoreDoc.doc属性就是document对象的id
        //int doc = scoreDoc.doc;
        //根据document的id找到document对象
        Document document = indexSearcher.doc(scoreDoc.doc);
        //文件名称
        System.out.println(document.get("fileName"));
        //文件内容
        System.out.println(document.get("fileContent"));
        //文件大小
        System.out.println(document.get("fileSize"));
        //文件路径
        System.out.println(document.get("filePath"));
        System.out.println("----------------------------------");
    }
    //关闭indexreader对象
    indexReader.close();
}
```

##### TermQuery（精准查询）

TermQuery通过项查询，TermQuery不使用分析器所以建议匹配不分词的Field域查询，比如订单号、分类ID号等。

指定要查询的域和要查询的关键词。

```java
//创建一个TermQuery（精准查询）对象，指定查询的域与查询的关键词
//创建查询
Query query = new TermQuery(new Term("fileName", "apache"));
//执行查询
//第一个参数是查询对象，第二个参数是查询结果返回的最大值
TopDocs topDocs = indexSearcher.search(query, 10);
```

##### NumericRangeQuery

可以根据数值范围查询。

```java
//创建查询
//参数：
//1.域名
//2.最小值
//3.最大值
//4.是否包含最小值
//5.是否包含最大值
Query query = NumericRangeQuery.newLongRange("fileSize", 41L, 2055L, true, true);
//执行查询
//第一个参数是查询对象，第二个参数是查询结果返回的最大值
TopDocs topDocs = indexSearcher.search(query, 10);
```

##### BooleanQuery

可以组合查询条件。

```java
//创建一个布尔查询对象
BooleanQuery query = new BooleanQuery();
//创建第一个查询条件
Query query1 = new TermQuery(new Term("fileName", "apache"));
Query query2 = new TermQuery(new Term("fileName", "lucene"));
//组合查询条件
query.add(query1, Occur.MUST);
query.add(query2, Occur.MUST);
//执行查询
//第一个参数是查询对象，第二个参数是查询结果返回的最大值
TopDocs topDocs = indexSearcher.search(query, 10);
```

- Occur.MUST：必须满足此条件，相当于and
- Occur.SHOULD：应该满足，但是不满足也可以，相当于or
- Occur.MUST_NOT：必须不满足。相当于not

#### 3.3.2 使用queryparser查询

通过QueryParser也可以创建Query，QueryParser提供一个Parse方法，此方法可以直接根据查询语法来查询。Query对象执行的查询语法可通过System.out.println(query);查询。

这个操作需要使用到分析器。建议创建索引时使用的分析器和查询索引时使用的分析器要一致。

##### queryparser

```java
@Test
public void testQueryParser() throws Exception {
    //创建一个Directory对象，指定索引库存放的路径
    Directory directory = FSDirectory.open(new File("E:\\programme\\test"));
    //创建IndexReader对象，需要指定Directory对象
    IndexReader indexReader = DirectoryReader.open(directory);
    //创建Indexsearcher对象，需要指定IndexReader对象
    IndexSearcher indexSearcher = new IndexSearcher(indexReader);

    //创建queryparser对象
    //第一个参数默认搜索的域
    //第二个参数就是分析器对象
    QueryParser queryParser = new QueryParser("fileName", new IKAnalyzer());
    //使用默认的域,这里用的是语法，下面会详细讲解一下
    Query query = queryParser.parse("apache");
    //不使用默认的域，可以自己指定域
    //Query query = queryParser.parse("fileContent:apache");
    
    //执行查询
    //第一个参数是查询对象，第二个参数是查询结果返回的最大值
    TopDocs topDocs = indexSearcher.search(query, 10);

    //查询结果的总条数
    System.out.println("查询结果的总条数："+ topDocs.totalHits);
    //遍历查询结果
    //topDocs.scoreDocs存储了document对象的id
    //ScoreDoc[] scoreDocs = topDocs.scoreDocs;
    for (ScoreDoc scoreDoc : topDocs.scoreDocs) {
        //scoreDoc.doc属性就是document对象的id
        //int doc = scoreDoc.doc;
        //根据document的id找到document对象
        Document document = indexSearcher.doc(scoreDoc.doc);
        //文件名称
        System.out.println(document.get("fileName"));
        //文件内容
        System.out.println(document.get("fileContent"));
        //文件大小
        System.out.println(document.get("fileSize"));
        //文件路径
        System.out.println(document.get("filePath"));
        System.out.println("----------------------------------");
    }
    //关闭indexreader对象
    indexReader.close();        
}
```

##### 查询语法

（1）基础的查询语法，关键词查询：

域名+“：”+搜索的关键字，例如：content:java

（2）范围查询

域名+“:”+[最小值 TO 最大值]

例如：size:[1 TO 1000]

范围查询在lucene中支持数值类型，不支持字符串类型。在solr中支持字符串类型。

（3）组合条件查询

1. +条件1 +条件2：两个条件之间是并且的关系and

   例如：+filename:apache +content:apache

2. +条件1 条件2：必须满足第一个条件，应该满足第二个条件

   例如：+filename:apache content:apache

3. 条件1 条件2：两个条件满足其一即可。

   例如：filename:apache content:apache

4. -条件1 条件2：必须不满足条件1，要满足条件2

   例如：-filename:apache content:apache

| Occur.MUST 查询条件必须满足，相当于and       | +（加号）      |
| -------------------------------------------- | -------------- |
| Occur.SHOULD 查询条件可选，相当于or          | 空（不用符号） |
| Occur.MUST_NOT 查询条件不能满足，相当于not非 | -（减号）      |

第二种写法：

1. 条件1 AND 条件2
2. 条件1 OR 条件2
3. 条件1 NOT 条件2

##### MultiFieldQueryParser

可以指定多个默认搜索域

```java
//可以指定默认搜索的域是多个
String[] fields = {"fileName", "fileContent"};
//创建一个MulitFiledQueryParser对象
MultiFieldQueryParser queryParser = new MultiFieldQueryParser(fields, new IKAnalyzer());
Query query = queryParser.parse("apache");
System.out.println(query);
//执行查询
//第一个参数是查询对象，第二个参数是查询结果返回的最大值
TopDocs topDocs = indexSearcher.search(query, 10);
```

### 3.4 IndexSearcher搜索方法

| 方法                                                | 说明                                                         |
| --------------------------------------------------- | ------------------------------------------------------------ |
| indexSearcher.search(query, n)                      | 根据Query搜索，返回评分最高的n条记录                         |
| indexSearcher.search(query, filter, n)              | 根据Query搜索，添加过滤策略，返回评分最高的n条记录           |
| indexSearcher.search(query, n, sort)                | 根据Query搜索，添加排序策略，返回评分最高的n条记录           |
| indexSearcher.search(booleanQuery, filter, n, sort) | 根据Query搜索，添加过滤策略，添加排序策略，返回评分最高的n条记录 |

### 3.5 TopDocs

Lucene搜索结果可通过TopDocs遍历，TopDocs类提供了少量的属性，如下：

| 方法或属性 | 说明                   |
| ---------- | ---------------------- |
| totalHits  | 匹配搜索条件的总记录数 |
| scoreDocs  | 顶部匹配记录           |

注意：

1. Search方法需要指定匹配记录数量n：indexSearcher.search(query, n)
2. TopDocs.totalHits：是匹配索引库中所有记录的数量
3. TopDocs.scoreDocs：匹配相关度高的前边记录数组，scoreDocs的长度小于等于search方法指定的参数n

## 4 删除索引

### 4.1 删除全部索引

说明：将索引目录的索引信息全部删除，直接彻底删除，无法恢复。**此方法慎用！！**

```java
//删除全部索引
@Test
public void testDeleteAllIndex() throws Exception {
    Directory directory = FSDirectory.open(new File("E:\\programme\\test"));
    Analyzer analyzer = new IKAnalyzer();
    IndexWriterConfig config = new IndexWriterConfig(Version.LATEST, analyzer);
    IndexWriter indexWriter = new IndexWriter(directory, config);
    //删除全部索引
    indexWriter.deleteAll();
    //关闭indexwriter
    indexWriter.close();
}
```

### 4.2 指定查询条件删除

```java
//根据查询条件删除索引
@Test
public void deleteIndexByQuery() throws Exception {
    Directory directory = FSDirectory.open(new File("E:\\programme\\test"));
    Analyzer analyzer = new IKAnalyzer();
    IndexWriterConfig config = new IndexWriterConfig(Version.LATEST, analyzer);
    IndexWriter indexWriter = new IndexWriter(directory, config);
    //创建一个查询条件
    Query query = new TermQuery(new Term("fileContent", "apache"));
    //根据查询条件删除
    indexWriter.deleteDocuments(query);
    //关闭indexwriter
    indexWriter.close();
}
```

## 5 索引库的修改

更新的原理就是先删除在添加

```java
//修改索引库
@Test
public void updateIndex() throws Exception {
    Directory directory = FSDirectory.open(new File("E:\\programme\\test"));
    Analyzer analyzer = new IKAnalyzer();
    IndexWriterConfig config = new IndexWriterConfig(Version.LATEST, analyzer);
    IndexWriter indexWriter = new IndexWriter(directory, config);
    //创建一个Document对象
    Document document = new Document();
    //向document对象中添加域。
    //不同的document可以有不同的域，同一个document可以有相同的域。
    document.add(new TextField("fileXXX", "要更新的文档", Store.YES));
    document.add(new TextField("contentYYY", "简介 Lucene 是一个基于 Java 的全文信息检索工具包。", Store.YES));
    indexWriter.updateDocument(new Term("fileName", "apache"), document);
    //关闭indexWriter
    indexWriter.close();
}
```

