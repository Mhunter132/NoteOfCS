## 1.索引库的添加

```java
/*向索引库中添加document对象。
第一步：先创建一个indexwriter对象
第二步：创建一个document对象
第三步：把document对象写入索引库
第四步：关闭indexwriter。*/
//添加索引
	@Test
	public void addDocument() throws Exception {
		//索引库存放路径
		Directory directory = FSDirectory.open(new File("D:\\temp\\0108\\index"));
		
		IndexWriterConfig config = new IndexWriterConfig(Version.LATEST, new IKAnalyzer());
		//创建一个indexwriter对象
		IndexWriter indexWriter = new IndexWriter(directory, config);
		//创建一个Document对象
		Document document = new Document();
		//向document对象中添加域。
		//不同的document可以有不同的域，同一个document可以有相同的域。
		document.add(new TextField("filename", "新添加的文档", Store.YES));
		document.add(new TextField("content", "新添加的文档的内容", Store.NO));
		document.add(new TextField("content", "新添加的文档的内容第二个content", Store.YES));
		document.add(new TextField("content1", "新添加的文档的内容要能看到", Store.YES));
		//添加文档到索引库
		indexWriter.addDocument(document);
		//关闭indexwriter
		indexWriter.close();
	}
/*
说白了就是往Doument对象里面添加term。第三个参数表示是否储存原文。
*/
```

## 2.Field属性

| StringField(FieldName, FieldValue,Store.YES))：不需要分析，需要索引，NY存储。（订单号,姓名等) |
| ------------------------------------------------------------ |
| **LongField(FieldName, FieldValue,Store.YES)：需要分析，需要索引，N Y存储（价格）** |
| **StoredField(FieldName, FieldValue)：不要分析，不要索引，Y存储** |
| **TextField(FieldName, FieldValue, Store.NO)：需要分析，需要索引，NY存储** |
|                                                              |

## 3.删除索引库

```java
//删除全部索引
//不可逆的删除
	@Test
	public void deleteAllIndex() throws Exception {
		IndexWriter indexWriter = getIndexWriter();
		//删除全部索引
		indexWriter.deleteAll();
		//关闭indexwriter
		indexWriter.close();
	}
```

## **4.指定查询条件删除**

```java
//根据查询条件删除索引
	@Test
	public void deleteIndexByQuery() throws Exception {
		IndexWriter indexWriter = getIndexWriter();
		//创建一个查询条件
		Query query = new TermQuery(new Term("filename", "apache"));
		//根据查询条件删除
		indexWriter.deleteDocuments(query);
		//关闭indexwriter
		indexWriter.close();
	}
/*
DeleteDocuments(Query query):根据Query条件来删除单个或多个Document
DeleteDocuments(Query[] queries):根据Query条件来删除单个或多个Document
DeleteDocuments(Term term):根据Term来删除单个或多个Document
DeleteDocuments(Term[] terms):根据Term来删除单个或多个Documen
*/
```

## 5.索引库的修改

```java
//修改索引库 ,原理就是先删除后添加。在本例中可以理解为用新的文档替换掉旧term查询出的文档，将新的进行切词。
	@Test
	public void updateIndex() throws Exception {
		IndexWriter indexWriter = getIndexWriter();
		//创建一个Document对象
		Document document = new Document();
		//向document对象中添加域。
		//不同的document可以有不同的域，同一个document可以有相同的域。
		document.add(new TextField("filename", "要更新的文档", Store.YES));
		document.add(new TextField("content", "2013年11月18日 - Lucene 简介 Lucene 是一个基于 Java 的全文信息检索工具包,它不是一个完整的搜索应用程序,而是为你的应用程序提供索引和搜索功能。", Store.YES));
		indexWriter.updateDocument(new Term("content", "java"), document);
		//关闭indexWriter
		indexWriter.close();
	}
```

## 6.lucene查询

lucene有自己的查询语法，

​	**//MatchAllDocsQuery**

```java

Test
	public void testMatchAllDocsQuery() throws Exception {
		IndexSearcher indexSearcher = getIndexSearcher();
		//创建查询条件
		Query query = new MatchAllDocsQuery();
		//执行查询
		printResult(query, indexSearcher);
	}
```

​	**//TermQuery**

```java
//使用Termquery查询
	@Test
	public void testTermQuery() throws Exception {
		IndexSearcher indexSearcher = getIndexSearcher();
		//创建查询对象
		Query query = new TermQuery(new Term("content", "lucene"));
		//执行查询
		TopDocs topDocs = indexSearcher.search(query, 10);
		//共查询到的document个数
		System.out.println("查询结果总数量：" + topDocs.totalHits);
		//遍历查询结果
		for (ScoreDoc scoreDoc : topDocs.scoreDocs) {
			Document document = indexSearcher.doc(scoreDoc.doc);
			System.out.println(document.get("filename"));
		   //System.out.println(document.get("content"));
			System.out.println(document.get("path"));
			System.out.println(document.get("size"));
		}
		//关闭indexreader
		indexSearcher.getIndexReader().close();
	}
```

​	**//NumericRangeQuery**

```java
可以根据数值范围查询。
//数值范围查询
	@Test
	public void testNumericRangeQuery() throws Exception {
		IndexSearcher indexSearcher = getIndexSearcher();
		//创建查询
		//参数：
		//1.域名
		//2.最小值
		//3.最大值
		//4.是否包含最小值
		//5.是否包含最大值
		Query query = NumericRangeQuery.newLongRange("size", 1l, 1000l, true, true);
		//执行查询
		printResult(query, indexSearcher);
	}
```

​	**//BooleanQuery**
可以组合查询条件。

```java

//组合条件查询
	@Test
	public void testBooleanQuery() throws Exception {
		IndexSearcher indexSearcher = getIndexSearcher();
		//创建一个布尔查询对象
		BooleanQuery query = new BooleanQuery();
		//创建第一个查询条件
		Query query1 = new TermQuery(new Term("filename", "apache"));
		Query query2 = new TermQuery(new Term("content", "apache"));
		//组合查询条件
		query.add(query1, Occur.MUST);
		query.add(query2, Occur.MUST);
		//执行查询
		printResult(query, indexSearcher);
	}
	/*Occur.MUST：必须满足此条件，相当于and
	Occur.SHOULD：应该满足，但是不满足也可以，相当于or
	Occur.MUST_NOT：必须不满足。相当于not	
	*/
```

## 使用queryparse（）查询

Query对象执行的查询语法可通过System.out.println(query);查询。

需要使用到分析器。建议创建索引时使用的分析器和查询索引时使用的分析器要一致

导入jar包

```java
@Test
	public void testQueryParser() throws Exception {
		IndexSearcher indexSearcher = getIndexSearcher();
		//创建queryparser对象
		//第一个参数默认搜索的域
		//第二个参数就是分析器对象
		QueryParser queryParser = new QueryParser("content", new IKAnalyzer());
		Query query = queryParser.parse("Lucene是java开发的");
		//执行查询
		printResult(query, indexSearcher);
	}
/*
3.2.1.2查询语法
1、基础的查询语法，关键词查询：
域名+“：”+搜索的关键字
例如：content:java
2、范围查询
域名+“:”+[最小值 TO 最大值]
例如：size:[1 TO 1000]
范围查询在lucene中不支持数值类型，支持字符串类型。在solr中支持数值类型。
3、组合条件查询
1）+条件1 +条件2：两个条件之间是并且的关系and
例如：+filename:apache +content:apache
2）+条件1 条件2：必须满足第一个条件，应该满足第二个条件
例如：+filename:apache content:apache
3）条件1 条件2：两个条件满足其一即可。
例如：filename:apache content:apache
4）-条件1 条件2：必须不满足条件1，要满足条件2
例如：-filename:apache content:apache
Occur.MUST 查询条件必须满足，相当于and	+（加号）
Occur.SHOULD 查询条件可选，相当于or
	空（不用符号）
Occur.MUST_NOT 查询条件不能满足，相当于not非	-（减号）

第二种写法：
条件1 AND 条件2
条件1 OR 条件2
条件1 NOT 条件2
*/
```

### MulitFieldQueryParser

```java
可以指定多个默认搜索域
@Test
	public void testMultiFiledQueryParser() throws Exception {
		IndexSearcher indexSearcher = getIndexSearcher();
		//可以指定默认搜索的域是多个,下面语句存放个的Field
		String[] fields = {"filename", "content"}; 
		//创建一个MulitFiledQueryParser对象
		MultiFieldQueryParser queryParser = new MultiFieldQueryParser(fields, new IKAnalyzer());
		Query query = queryParser.parse("java and apache");
		System.out.println(query);
		//执行查询
		printResult(query, indexSearcher);
		
	}
```

## 7.相关度排序

​	最重要的就是相关度打分

​	Term对文档的重要性称为权重，影响Term权重有两个因素：

**Term Frequency (tf)：**

指此Term在此文档中出现了多少次。tf 越大说明越重要。 

词(Term)在文档中出现的次数越多，说明此词(Term)对该文档越重要，如“Lucene”这个词，在文档中出现的次数很多，说明该文档主要就是讲Lucene技术的。

**Document Frequency (df)**

即有多少文档包含次Term。df 越大说明越不重要。 

​	比如，在一篇英语文档中，this出现的次数更多，就说明越重要吗？不是的，有越多的文档包含此词(Term), 说明此词(Term)太普通，不足以区分这些文档，因而重要性越低。

```java
/*boost是一个加权值（默认加权值为1.0f），它可以影响权重的计算。
	在索引时对某个文档的Field域设置加权值高，在搜索时匹配到这个Field就可能排在前边。lucene在执行搜索时对某个域进行加权，在进行组合域查询时，匹配到加权值高的域最后计算的相关度得分就高。
	如果希望某些文档更重要，当此文档中包含所要查询的词则应该得分较高，这样相关度排序可以排在前边，可以在创建索引时设定文档中某些域（Field）的boost值来实现，如果不进行设定，则Field Boost默认为1.0f。一旦设定，除非删除此文档，否则无法改变。
field.setBoost(XXXf); XXX即权值。
测试：
可以将springmvc.txt的file_content加权值设置为10.0f，结果搜索spring时如果内容可以匹配到关键字就可以把springmvc.txt文件排在前边。
代码：
索引时设置boost加权值：  是对	Field进行权重
//设置加权值*/
			if(file_name.equals("springmvc.txt")){
				//设置比默认值 1.0大的
				field_file_content.setBoost(20.0f);
			}
			if(file_name.equals("spring_README.txt")){
				//设置比默认值 1.0大的
				field_file_content.setBoost(30.0f);
			}		
			//向文档中添加Field
		document.add(field_file_content);

搜索时：
// 设置组合查询域，如果匹配到一个域就返回记录
		String[] fields = { "file_content" };		
		//设置评分,文件名称中包括关键字的评分高
		/*Map<String,Float> boosts = new HashMap<String,Float>();
		boosts.put("file_content", 3.0f);*/		
		// 创建查询解析器
		QueryParser queryParser = new MultiFieldQueryParser(fields,
				new StandardAnalyzer());
		// 查询文件名、文件内容中包括“java”关键字的文档
		Query query = queryParser.parse("spring");
		TopDocs topDocs = indexSearcher.search(query, 100);
		ScoreDoc[] scoreDocs = topDocs.scoreDocs;
```





