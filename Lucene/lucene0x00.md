# lucene

## 01.Lucene基本使用



![流程](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\70)

**执行流程：**设置索引目标文件路径，读取源文件读取他们的域（Field），将field和分词器传入写出器写进索引库。

**原始文档：**可以是互联网上任意想要检索的资源。

**创建文档对象：**Document doc= new Document( );

**域（Field）:**每个Docment都包含一个或多个Field，例如文件内容，文件名字，文件路径。

**文档 I D：**每个文档都有一个唯一的编号，就是文档id。

**倒排序索引结构：**通过词语（索引）找文档

**term：**分词处理之后的单词的单词叫term.

![term](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\20160527111259428)



**jar包：** 

Lucene包：

lucene-core-4.10.3.jar

lucene-analyzers-common-4.10.3.jar

其它：

commons-io-2.4.jar

junit-4.9.jar

## 02.入门程序

```java
//第一步：创建一个java工程，并导入jar包。
//第二步：创建一个indexwriter对象。
//1）指定索引库的存放位置Directory对象
//2）指定一个分析器，对文档内容进行分析。
//第二步：创建document对象。
//第三步：创建field对象，将field添加到document对象中。
//第四步：使用indexwriter对象将document对象写入索引库，此过程进行索引创建。并将索引和document对象写入索引
//库。
//第五步：关闭IndexWriter对象。
//创建索引
	@Test
	public void createIndex() throws Exception {
		
		//指定索引库存放的路径
		//D:\temp\0108\index
		Directory directory = FSDirectory.open(new File("D:\\temp\\0108\\index"));
		//索引库还可以存放到内存中
		//Directory directory = new RAMDirectory();
		//创建一个标准分析器
		Analyzer analyzer = new StandardAnalyzer();
		//创建indexwriterCofig对象
		//第一个参数： Lucene的版本信息，可以选择对应的lucene版本也可以使用LATEST
		//第二根参数：分析器对象
		IndexWriterConfig config = new IndexWriterConfig(Version.LATEST, analyzer);
		//创建indexwriter对象
		IndexWriter indexWriter = new IndexWriter(directory, config);//将分词器和索引库存放进indexwriter中
		//原始文档的路径D:\传智播客\01.课程\04.lucene\01.参考资料\searchsource
		File dir = new File("D:\\传智播客\\01.课程\\04.lucene\\01.参考资料\\searchsource");
		for (File f : dir.listFiles()) {
			//文件名
			String fileName = f.getName();
			//文件内容
			String fileContent = FileUtils.readFileToString(f);//工具类读内容
			//文件路径
			String filePath = f.getPath();
			//文件的大小
			long fileSize  = FileUtils.sizeOf(f);
			//创建文件名域
			//第一个参数：域的名称
			//第二个参数：域的内容
			//第三个参数：是否存储
			Field fileNameField = new TextField("filename", fileName, Store.YES);
			//文件内容域
			Field fileContentField = new TextField("content", fileContent, Store.YES);
			//文件路径域（不分析、不索引、只存储）
			Field filePathField = new TextField("path", filePath);
			//文件大小域
			Field fileSizeField = new TextField("size", fileSize + "", Store.YES);
			
			//创建document对象
			Document document = new Document();
			document.add(fileNameField);
			document.add(fileContentField);
			document.add(filePathField);
			document.add(fileSizeField);
			//创建索引，并写入索引库
			indexWriter.addDocument(document);
		}
		//关闭indexwriter
		indexWriter.close();
	}
```

## 查询索引

```java
/*-第一步：创建一个Directory对象，也就是索引库存放的位置。
第二步：创建一个indexReader对象，需要指定Directory对象。
第三步：创建一个indexsearcher对象，需要指定IndexReader对象
第四步：创建一个TermQuery对象，指定查询的域和查询的关键词。
第五步：执行查询。
第六步：返回查询结果。遍历查询结果并输出。
第七步：关闭IndexReader对象*/
//查询索引库
	@Test
	public void searchIndex() throws Exception {
		//指定索引库存放的路径
		//D:\temp\0108\index
		Directory directory = FSDirectory.open(new File("D:\\temp\\0108\\index"));
		//创建indexReader对象
		IndexReader indexReader = DirectoryReader.open(directory);
		//创建indexsearcher对象
		IndexSearcher indexSearcher = new IndexSearcher(indexReader);
		//创建查询
		Query query = new TermQuery(new Term("filename", "apache"));
		//执行查询
		//第一个参数是查询对象，第二个参数是查询结果返回的最大值
		TopDocs topDocs = indexSearcher.search(query, 10);
		//查询结果的总条数
		System.out.println("查询结果的总条数："+ topDocs.totalHits);
		//遍历查询结果
		//topDocs.scoreDocs存储了document对象的id
		for (ScoreDoc scoreDoc : topDocs.scoreDocs) {
			//scoreDoc.doc属性就是document对象的id
			//根据document的id找到document对象
			Document document = indexSearcher.doc(scoreDoc.doc);
			System.out.println(document.get("filename"));
			//System.out.println(document.get("content"));
			System.out.println(document.get("path"));
			System.out.println(document.get("size"));
		}
		//关闭indexreader对象
		indexReader.close();
	}
/* TopDocs
ucene搜索结果可通过TopDocs遍历，TopDocs类提供了少量的属性，如下：
┌---------------------------------┐
|方法或属性|	说明				   |
-----------------------------------
|totalHits|	匹配搜索条件的总记录数  |
-----------------------------------
|scoreDocs|	顶部匹配记录			|
└---------------------------------┘
```

注意：
**Search**方法需要指定匹配记录数量：indexSearcher.search(query, n)
**TopDocs.totalHits：**是匹配索引库中所有记录的数量
**TopDocs.scoreDocs：**匹配相关度高的前边记录数组，scoreDocs的长度小于等于search方法指定的参数n

## 分词器

### 1.Lucene自带中文分词器

**StandardAnalyzer：**

单字分词：就是按照中文一个字一个字地进行分词。如：“我爱中国”，效果：“我”、“爱”、“中”、“国”。

**CJKAnalyzer**

二分法分词：按两个字进行切分。如：“我是中国人”，效果：“我是”、“是中”、“中国”“国人”。上边两个分词器无法满足需求。

**SmartChineseAnalyzer**

对中文支持较好，但扩展性差，扩展词库，禁用词库和同义词库等不好处理

### 2.第三方中文分析器

paoding： 庖丁解牛最新版在 <https://code.google.com/p/paoding/> 中最多支持Lucene 3.0，且最新提交的代码在 2008-06-03，在svn中最新也是2010年提交，已经过时，不予考虑。

mmseg4j：最新版已从 <https://code.google.com/p/mmseg4j/> 移至 <https://github.com/chenlb/mmseg4j-solr>，支持Lucene 4.10，且在github中最新提交代码是2014年6月，从09年～14年一共有：18个版本，也就是一年几乎有3个大小版本，有较大的活跃度，用了mmseg算法。

IK-analyzer（**建议使用**）： 最新版在https://code.google.com/p/ik-analyzer/上，支持Lucene 4.10从2006年12月推出1.0版开始， IKAnalyzer已经推出了4个大版本。最初，它是以开源项目Luence为应用主体的，结合词典分词和文法分析算法的中文分词组件。从3.0版本开 始，IK发展为面向Java的公用分词组件，独立于Lucene项目，同时提供了对Lucene的默认优化实现。在2012版本中，IK实现了简单的分词 歧义排除算法，标志着IK分词器从单纯的词典分词向模拟语义分词衍化。 但是也就是2012年12月后没有在更新。

ansj_seg：最新版本在 <https://github.com/NLPchina/ansj_seg> tags仅有1.1版本，从2012年到2014年更新了大小6次，但是作者本人在2014年10月10日说明：“可能我以后没有精力来维护ansj_seg了”，现在由”nlp_china”管理。2014年11月有更新。并未说明是否支持Lucene，是一个由CRF（条件随机场）算法所做的分词算法。

imdict-chinese-analyzer：最新版在 <https://code.google.com/p/imdict-chinese-analyzer/> ， 最新更新也在2009年5月，下载源码，不支持Lucene 4.10 。是利用HMM（隐马尔科夫链）算法。

Jcseg：最新版本在git.oschina.net/lionsoul/jcseg，支持Lucene 4.10，作者有较高的活跃度。利用mmseg算法。

### 3.分词器使用时机

​	1.被查询的内容需要分词

​	2.查询时候也要分词

