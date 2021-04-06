---
title: Lucene的Field域和索引维护
tags:
  - Lucene
category: Lucene
abbrlink: 40393
date: 2017-11-30 14:30:36
---
![image](http://ovi3ob9p4.bkt.clouddn.com/TIETU/CT0054.jpg)

一、Field域
1.Field属性
Field是文档中的域，包括Field名和Field值两部分，一个文档可以包括多个Field，Document只是Field的一个承载体，Field值即为要索引的内容，也是要搜索的内容。
<!--more-->
是否分词(tokenized)
是：作分词处理，即将Field值进行分词，分词的目的是为了索引。
比如：商品名称、商品简介等，这些内容用户要输入关键字搜索，由于搜索的内容格式大、内容多需要分词后将语汇单元索引。


否：不作分词处理
比如：商品id、订单号、身份证号等

是否索引(indexed)
是：进行索引。将Field分词后的词或整个Field值进行索引，索引的目的是为了搜索。
比如：商品名称、商品简介分析后进行索引，订单号、身份证号不用分析但也要索引，这些将来都要作为查询条件。

否：不索引。该域的内容无法搜索到
比如：商品id、文件路径、图片路径等，不用作为查询条件的不用索引。

是否存储(stored)
是：将Field值存储在文档中，存储在文档中的Field才可以从Document中获取。
比如：商品名称、订单号，凡是将来要从Document中获取的Field都要存储。

否：不存储Field值，不存储的Field无法通过Document获取
比如：商品简介，内容较大不用存储。如果要向用户展示商品简介可以从系统的关系数据库中获取商品简介。

如果需要商品描述，则根据搜索出的商品ID去数据库中查询，然后显示出商品描述信息即可。

2.Field常用类型
开发中常用 的Filed类型，注意Field的属性，根据需求选择：


Field常用类型.PNG
3.Field改进代码
图书id：

是否分词：不用分词，因为不会根据商品id来搜索商品 
是否索引：不索引，因为不需要根据图书ID进行搜索
是否存储：要存储，因为查询结果页面需要使用id这个值。
图书名称：

是否分词：要分词，因为要将图书的名称内容分词索引，根据关键搜索图书名称抽取的词。
是否索引：要索引。
是否存储：要存储。
图书价格：

是否分词：要分词，lucene对数字型的值只要有搜索需求的都要分词和索
引，因为lucene对数字型的内容要特殊分词处理，本例子可能要根据价格范
围搜索，需要分词和索引。
是否索引：要索引
是否存储：要存储
图书图片地址：

是否分词：不分词
是否索引：不索引
是否存储：要存储
图书描述：

是否分词：要分词
是否索引：要索引
是否存储：因为图书描述内容量大，不在查询结果页面直接显示，不存储。
不存储是来不在lucene的索引文件中记录，节省lucene的索引文件空间，
如果要在详情页面显示描述，思路：
从lucene中取出图书的id，根据图书的id查询关系数据库中book表
得到描述信息。
代码：

```java
@Test
public void createIndex() throws Exception {
// 采集数据
BookDao dao = new BookDaoImpl();
List<Book> list = dao.queryBooks();

// 将采集到的数据封装到Document对象中
List<Document> docList = new ArrayList<>();
Document document;
for (Book book : list) {
    document = new Document();
    // store:如果是yes，则说明存储到文档域中
    // 图书ID
    // 不分词、索引、存储 StringField
    Field id = new StringField("id", book.getId().toString(), Store.YES);
    // 图书名称
    // 分词、索引、存储 TextField
    Field name = new TextField("name", book.getName(), Store.YES);
    // 图书价格
    // 分词、索引、存储 但是是数字类型，所以使用FloatField
    Field price = new FloatField("price", book.getPrice(), Store.YES);
    // 图书图片地址
    // 不分词、不索引、存储 StoredField
    Field pic = new StoredField("pic", book.getPic());
    // 图书描述
    // 分词、索引、不存储 TextField
    Field description = new TextField("description",
            book.getDescription(), Store.NO);

    // 设置boost值
    if (book.getId() == 4)
        description.setBoost(100f);

    // 将field域设置到Document对象中
    document.add(id);
    document.add(name);
    document.add(price);
    document.add(pic);
    document.add(description);

    docList.add(document);
}
```
二、索引维护
需求：

管理人员通过电商系统更改图书信息，这时更新的是数据库，如果使用lucene搜索图书信息需要在数据库表book信息变化时及时更新lucene索引库。

1.添加索引
调用 indexWriter.addDocument（doc）添加索引。

```java
@Test

public void createIndex() throws Exception {
// 采集数据
BookDao dao = new BookDaoImpl();
List<Book> list = dao.queryBooks();

// 将采集到的数据封装到Document对象中
List<Document> docList = new ArrayList<>();
Document document;
for (Book book : list) {
    document = new Document();
    // store:如果是yes，则说明存储到文档域中
    // 图书ID
    Field id = new TextField("id", book.getId().toString(), Store.YES);
    // 图书名称
    Field name = new TextField("name", book.getName(), Store.YES);
    // 图书价格
    Field price = new TextField("price", book.getPrice().toString(),
            Store.YES);
    // 图书图片地址
    Field pic = new TextField("pic", book.getPic(), Store.YES);
    // 图书描述
    Field description = new TextField("description",
            book.getDescription(), Store.YES);

    // 将field域设置到Document对象中
    document.add(id);
    document.add(name);
    document.add(price);
    document.add(pic);
    document.add(description);

    docList.add(document);
}

// 创建分词器，标准分词器
Analyzer analyzer = new StandardAnalyzer();

// 创建IndexWriter
IndexWriterConfig cfg = new IndexWriterConfig(Version.LUCENE_4_10_3,
        analyzer);
// 指定索引库的地址
File indexFile = new File("E:\\11-index\\hm19\\");
Directory directory = FSDirectory.open(indexFile);
IndexWriter writer = new IndexWriter(directory, cfg);

// 通过IndexWriter对象将Document写入到索引库中
for (Document doc : docList) {
    writer.addDocument(doc);
}

// 关闭writer
writer.close();
}
```

2.删除索引
2.1删除指定索引

根据Term项删除索引，满足条件的将全部删除。

Term是索引域中最小的单位。根据条件删除时，建议根据唯一键来进行删除。在solr中就是根据ID来进行删除和修改操作的。


```java
@Test

public void deleteIndex() throws Exception {
// 创建分词器，标准分词器
Analyzer analyzer = new StandardAnalyzer();

// 创建IndexWriter
IndexWriterConfig cfg = new IndexWriterConfig(Version.LUCENE_4_10_3,
        analyzer);
Directory directory = FSDirectory
        .open(new File("E:\\11-index\\hcx\\"));
// 创建IndexWriter
IndexWriter writer = new IndexWriter(directory, cfg);

// Terms
writer.deleteDocuments(new Term("id", "1"));

writer.close();
}
```

2.2删除全部索引（慎用）

将索引目录的索引信息全部删除，直接彻底删除，无法恢复。慎用！


```java
// 删除索引

@Test

public void deleteIndex() throws Exception {
// 1、指定索引库目录
Directory directory = FSDirectory.open(new File("E:\\11-index\\0720"));
// 2、创建IndexWriterConfig
IndexWriterConfig cfg = new IndexWriterConfig(Version.LATEST,
        new StandardAnalyzer());
// 3、 创建IndexWriter
IndexWriter writer = new IndexWriter(directory, cfg);
// 4、通过IndexWriter来删除索引
// a)、删除全部索引
writer.deleteAll();
// 5、关闭IndexWriter
writer.close();
}
```
建议参照关系数据库基于主键删除方式，所以在创建索引时需要创建一个主键Field，删除时根据此主键Field删除。

索引删除后将放在Lucene的回收站中，Lucene3.X版本可以恢复删除的文档，3.X之后无法恢复。

3.修改索引
更新索引是先删除再添加，建议对更新需求采用此方法并且要保证对已存在的索引执行更新，可以先查询出来，确定更新记录存在执行更新操作。


```java
@Test

public void updateIndex() throws Exception {
// 创建分词器，标准分词器
Analyzer analyzer = new StandardAnalyzer();

// 创建IndexWriter
IndexWriterConfig cfg = new IndexWriterConfig(Version.LUCENE_4_10_3,
        analyzer);

Directory directory = FSDirectory
        .open(new File("E:\\11-index\\hcx\\"));
// 创建IndexWriter
IndexWriter writer = new IndexWriter(directory, cfg);

// 第一个参数：指定查询条件
// 第二个参数：修改之后的对象
// 修改时如果根据查询条件，可以查询出结果，则将以前的删掉，然后覆盖新的Document对象，如果没有查询出结果，则新增一个Document
// 修改流程即：先查询，再删除，在添加
Document doc = new Document();
doc.add(new TextField("name", "lisi", Store.YES));
writer.updateDocument(new Term("name", "zhangsan"), doc);

writer.close();
```
}
