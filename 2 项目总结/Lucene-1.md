## 1 索引创建

创建索的关键类：

![åå»ºç´¢å¼çå³é®ç±»](https://segmentfault.com/img/bVnaWw)

创建索引的示例代码：

```java
/**
 * 索引创建
 */
@Test
public void createIndex() {
	
	// 创建一个分词器（指定Lucene版本）
	Analyzer analyzer = new StandardAnalyzer(Version.LUCENE_43);	
	// IndexWriter配置信息（指定Lucene版本和分词器）
	IndexWriterConfig indexWriterConfig = new IndexWriterConfig(Version.LUCENE_43, analyzer); 
	// 设置索引的打开方式
	indexWriterConfig.setOpenMode(OpenMode.CREATE_OR_APPEND);
	// 创建Directory对象和IndexWriter对象
	Directory directory = null;
	IndexWriter indexWriter = null;
	try {
		directory = FSDirectory.open(new File("Lucene_index/test"));
		// 检查Directory对象是否处于锁定状态（如果锁定则进行解锁）
		if (IndexWriter.isLocked(directory)) {
			IndexWriter.unlock(directory);
		}
		indexWriter = new IndexWriter(directory, indexWriterConfig);
	} catch (IOException e) {
		e.printStackTrace();
	}
	// 创建测试文档并为其添加域
	Document doc1 = new Document();
	doc1.add(new StringField("id", "abcde", Store.YES)); // 添加一个id域，域值为abcde
	doc1.add(new TextField("content", "使用Lucene实现全文检索", Store.YES)); // 文本域
	doc1.add(new IntField("num", 1, Store.YES));	// 添加数值域
	
	// 将文档写入索引
	try {
		indexWriter.addDocument(doc1);
	} catch (IOException e) {
		e.printStackTrace();
	}
	
	Document doc2 = new Document();
	doc2.add(new StringField("id", "yes", Store.YES)); 				 
	doc2.add(new TextField("content", "Docker容器技术简介", Store.YES)); 
	doc2.add(new IntField("num", 2, Store.YES));						 
	try {
		indexWriter.addDocument(doc2);
	} catch (IOException e) {
		e.printStackTrace();
	}
	
	// 将IndexWriter提交
	try {
		indexWriter.commit();
	} catch (IOException e) {
		e.printStackTrace();
	} finally{
		try {
			indexWriter.close();
			directory.close();
		} catch (IOException e) {
			e.printStackTrace();
		}
	}
}
```

## 2 索引检索

索引检索关键类:

![ç´¢å¼æ£ç´¢å³é®ç±»](https://segmentfault.com/img/bVnaXI)

利用以下的检索程序对前面创建的索引进行检索：

```java
/**
 * 索引检索
 */
@Test
public void searchIndex(){
	
	Directory directory = null;
	DirectoryReader dReader = null;
	try {
		directory = FSDirectory.open(new File("Lucene_index/test"));// 索引文件
		dReader = DirectoryReader.open(directory);	// 读取索引文件
		IndexSearcher searcher = new IndexSearcher(dReader);// 创建IndexSearcher对象
		
		Analyzer analyzer = new StandardAnalyzer(Version.LUCENE_43);    // 指定分词技术（标准分词-与创建索引时使用的分词技术一致）
		
		// 创建查询字符串（指定搜索域和采用的分词技术）
		QueryParser parser = new QueryParser(Version.LUCENE_43, "content", analyzer);
		Query query = parser.parse("Docker"); // 创建Query对象（指定搜索词）
		
		// 检索索引(指定前10条)
		TopDocs topDocs = searcher.search(query, 10);
		if (topDocs != null) {
			System.out.println("符合条件的文档总数为：" + topDocs.totalHits);
			for (int i = 0; i < topDocs.scoreDocs.length; i++) {
				Document doc = searcher.doc(topDocs.scoreDocs[i].doc);
				System.out.println("id = " + doc.get("id") + ",content = " + doc.get("content") + ",num = " + doc.get("num"));
			}
		}
	} catch (IOException e) {
		e.printStackTrace();
	} catch (ParseException e) {
		e.printStackTrace();
	} finally{
		try {
			dReader.close();
			directory.close();
		} catch (IOException e) {
			e.printStackTrace();
		}
	}
}
```

运行结果：

![ä»ç´¢å¼ä¸­è¿è¡æ£ç´¢](https://segmentfault.com/img/bVnaYD)

## 3 Lucene分词器

常见分词器

其中IKAnalyzer需要下载专门的jar包

![å¸¸è§åè¯å¨](https://segmentfault.com/img/bVnaYL)

```java
/**
 * 常见分词器
 */
@Test
public void testAnalyzer(){
	
	final String str = "利用Lucene 实现全文检索";
	Analyzer analyzer = null;
	
	analyzer = new StandardAnalyzer(Version.LUCENE_43); 	// 标准分词
	print(analyzer, str);
	analyzer = new IKAnalyzer();							// 第三方中文分词
	print(analyzer, str);
	analyzer = new WhitespaceAnalyzer(Version.LUCENE_43); 	// 空格分词
	print(analyzer, str);
	analyzer = new SimpleAnalyzer(Version.LUCENE_43); 		// 简单分词
	print(analyzer, str);
	analyzer = new CJKAnalyzer(Version.LUCENE_43);			// 二分法分词
	print(analyzer, str);
	analyzer = new KeywordAnalyzer();						// 关键字分词
	print(analyzer, str);
	analyzer = new StopAnalyzer(Version.LUCENE_43); 		//被忽略词分词器
	print(analyzer, str);

}

/**
 * 该方法用于打印分词器及其分词结果
 * @param analyzer 分词器
 * @param str 需要分词的字符串
 */
public void print(Analyzer analyzer,String str){
	
	StringReader stringReader = new StringReader(str);
	try {
		TokenStream tokenStream = analyzer.tokenStream("", stringReader); // 分词
		tokenStream.reset();
		
		CharTermAttribute term = tokenStream.getAttribute(CharTermAttribute.class); // 获取分词结果的CharTermAttribute
		System.out.println("分词技术:" + analyzer.getClass());
		while (tokenStream.incrementToken()) {
			System.out.print(term.toString() + "|");
		}
		System.out.println();
	} catch (IOException e) {
		e.printStackTrace();
	}
}
```

运行结果：

![各种分词器对比](https://segmentfault.com/img/bVnaZ7)

## 4 Query创建

![Queryåå»º1](https://segmentfault.com/img/bVna0c)

![Queryåå»º2](https://segmentfault.com/img/bVna0f)

![Queryåå»º3](https://segmentfault.com/img/bVna0l)

```java
/**
 * 创建Query
 */
@Test
public void createQuery(){
	
	String key = "JAVA EE Lucene案例开发";
	String field = "name";
	String[] fields = {"name","content"};
	Analyzer analyzer = new StandardAnalyzer(Version.LUCENE_43); // 分词器
	
	// 单域查询
	QueryParser parser1 = new QueryParser(Version.LUCENE_43, field, analyzer);
	Query query1 = null;
	try {
		query1 = parser1.parse(key); // 使用QueryParser创建Query
	} catch (ParseException e) {
		e.printStackTrace();
	}
	System.out.println(QueryParser.class + query1.toString());
	
	// 多域查询
	MultiFieldQueryParser parser2 = new MultiFieldQueryParser(Version.LUCENE_43, fields, analyzer);
	try {
		query1 = parser2.parse(key);
	} catch (ParseException e) {
		e.printStackTrace();
	}
	System.out.println(QueryParser.class + query1.toString());
	
	// 短语查询
	query1 = new TermQuery(new Term(field, key));
	System.out.println(TermQuery.class + query1.toString());
	
	// 前缀查询
	query1 = new PrefixQuery(new Term(field, key));
	System.out.println(PrefixQuery.class + query1.toString());
	
	// 短语查询
	PhraseQuery query2 = new PhraseQuery();
	query2.setSlop(2); // 设置短语间的最大距离是2
	query2.add(new Term(field, "Lucene"));
	query2.add(new Term(field, "案例"));
	System.out.println(PhraseQuery.class + query2.toString());
	
	// 通配符查询
	query1 = new WildcardQuery(new Term(field, "Lucene?"));
	System.out.println(WildcardQuery.class + query1.toString());
	
	// 字符串范围搜索
	query1 = TermRangeQuery.newStringRange(field, "abc", "azz", false, false);
	System.out.println(TermRangeQuery.class + query1.toString());
	
	// 布尔条件查询
	BooleanQuery query3 = new BooleanQuery();
	query3.add(new TermQuery(new Term(field, "Lucene")),Occur.SHOULD); // 添加条件
	query3.add(new TermQuery(new Term(field, "案例")),Occur.MUST); 
	query3.add(new TermQuery(new Term(field, "案例")),Occur.MUST_NOT); 
	System.out.println(BooleanQuery.class + query3.toString());
}
```

运行结果：

![query的子类](https://segmentfault.com/img/bVna1C)

## 5 IndexSearcher常用方法

检索关键类

![检索关键类](https://segmentfault.com/img/bVna1O)

