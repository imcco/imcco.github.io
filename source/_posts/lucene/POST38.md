---
title: Lucene全文搜索解析
tags:
  - Lucene
copyright: true
category: Lucene
abbrlink: 3536
date: 2017-11-30 13:34:36
---
![image](http://ovi3ob9p4.bkt.clouddn.com/TIETU/CT0052.jpg)

一、创建查询对象的方式
对要搜索的信息创建Query查询对象，Lucene会根据Query查询对象生成最终的查询语法。类似关系数据库Sql语法一样，Lucene也有自己的查询语法，比如：“name:lucene”表示查询Field的name为“lucene”的文档信息。
<!--more-->
可通过两种方法创建查询对象：

使用Lucene提供Query子类
Query是一个抽象类，lucene提供了很多查询对象，比如TermQuery项精确查询，NumericRangeQuery数字范围查询等。

如下代码：

Query query = new TermQuery(new Term("name", "lucene"));
使用QueryParse解析查询表达式
QueryParser会将用户输入的查询表达式解析成Query对象实例。
如下代码：

QueryParser queryParser = new QueryParser("name", new IKAnalyzer());
Query query = queryParser.parse("name:lucene");
二、通过Query子类搜索
2.1 TermQuery
TermQuery项查询，TermQuery不使用分析器，搜索关键词作为整体来匹配Field域中的词进行查询，比如订单号、分类ID号等。

private void doSearch(Query query) {
```java
IndexReader reader = null;
try {
    // a) 指定索引库目录
    Directory indexdirectory = FSDirectory.open(new File(
            "E:\\11-index\\0720"));
    // b) 创建IndexReader对象
    reader = DirectoryReader.open(indexdirectory);
    // c) 创建IndexSearcher对象
    IndexSearcher searcher = new IndexSearcher(reader);
    // d) 通过IndexSearcher对象执行查询索引库，返回TopDocs对象
    // 第一个参数：查询对象
    // 第二个参数：最大的n条记录
    TopDocs topDocs = searcher.search(query, 10);
    // e) 提取TopDocs对象中的文档ID，如何找出对应的文档
    ScoreDoc[] scoreDocs = topDocs.scoreDocs;
    System.out.println("总共查询出的结果总数为：" + topDocs.totalHits);
    Document doc;
    for (ScoreDoc scoreDoc : scoreDocs) {
        // 文档对象ID
        int docId = scoreDoc.doc;
        doc = searcher.doc(docId);
        // f) 输出文档内容
        System.out.println(doc.get("filename"));
        System.out.println(doc.get("path"));
        System.out.println(doc.get("size"));
    }
} catch (IOException e) {
    e.printStackTrace();
} finally {
    if (reader != null) {
        try {
            reader.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```
}

@Test
public void testTermQuery() throws Exception {
```java
// 1、 创建查询（Query对象）
Query query = new TermQuery(new Term("filename", "apache"));
// 2、 执行搜索
doSearch(query);
```
}
2.2 NumericRangeQuery
NumericRangeQuery，指定数字范围查询.

@Test
public void testNumbericRangeQuery() throws Exception {
```java
// 创建查询
// 第一个参数：域名
// 第二个参数：最小值
// 第三个参数：最大值
// 第四个参数：是否包含最小值
// 第五个参数：是否包含最大值
Query query = NumericRangeQuery.newLongRange("size", 1l, 100l, true,true);
// 2、 执行搜索
doSearch(query);
```
}
2.3 BooleanQuery
BooleanQuery，布尔查询，实现组合条件查询。

@Test
public void booleanQuery() throws Exception {
```java
BooleanQuery query = new BooleanQuery();
Query query1 = new TermQuery(new Term("id", "3"));
Query query2 = NumericRangeQuery.newFloatRange("price", 10f, 200f,
        true, true);

//MUST：查询条件必须满足，相当于AND
//SHOULD:查询条件可选，相当于OR
//MUST_NOT：查询条件不能满足，相当于NOT非
query.add(query1, Occur.MUST);
query.add(query2, Occur.SHOULD);

System.out.println(query);

search(query);
```
}
组合关系代表的意思如下:

1、MUST和MUST表示“与”的关系，即“交集”。 
2、MUST和MUST_NOT前者包含后者不包含。 
3、MUST_NOT和MUST_NOT没意义 
4、SHOULD与MUST表示MUST，SHOULD失去意义； 
5、SHOUlD与MUST_NOT相当于MUST与MUST_NOT。 
6、SHOULD与SHOULD表示“或”的概念。
三、通过QueryParser搜索
通过QueryParser也可以创建Query，QueryParser提供一个Parse方法，此方法可以直接根据查询语法来查询。Query对象执行的查询语法可通过System.out.println(query);查询。

3.1 QueryParser
代码实现：

@Test
public void testQueryParser() throws Exception {
```java
// 创建QueryParser
// 第一个参数：默认域名
// 第二个参数：分词器
QueryParser queryParser = new QueryParser("name", new IKAnalyzer());
// 指定查询语法 ，如果不指定域，就搜索默认的域
Query query = queryParser.parse("lucene");
System.out.println(query);
// 2、 执行搜索
doSearch(query);
```
}
查询语法：

1、基础的查询语法，关键词查询：

域名+“：”+搜索的关键字
例如：content:java
2、范围查询

域名+“:”+[最小值 TO 最大值]
例如：size:[1 TO 1000]
注意：QueryParser不支持对数字范围的搜索，它支持字符串范围。
数字范围搜索建议使用NumericRangeQuery。
3、组合条件查询


组合条件查询.PNG
1）+条件1 +条件2：两个条件之间是并且的关系and
例如：+filename:apache +content:apache

2）+条件1 条件2：必须满足第一个条件，忽略第二个条件
例如：+filename:apache content:apache

3）条件1 条件2：两个条件满足其一即可。
例如：filename:apache content:apache

4）-条件1 条件2：必须不满足条件1，要满足条件2
例如：-filename:apache content:apache

第二种写法：
条件1 AND 条件2
条件1 OR 条件2
条件1 NOT 条件2
3.2 MultiFieldQueryParser
通过MultiFieldQueryParser对多个域查询。

@Test
public void testMultiFieldQueryParser() throws Exception {
```java
// 可以指定默认搜索的域是多个
String[] fields = { "name", "description" };
// 创建一个MulitFiledQueryParser对象
QueryParser parser = new MultiFieldQueryParser(fields, new IKAnalyzer());
// 指定查询语法 ，如果不指定域，就搜索默认的域
Query query = parser.parse("lucene");
//等同于name:lucene OR description:lucene
// Query query = parser.parse("name:lucene OR description:lucene");
// 2、 执行搜索
doSearch(query);
```
}
四、TopDocs
Lucene搜索结果可通过TopDocs遍历，TopDocs类提供了少量的属性，如下：


topdocs属性.PNG
注意：

Search方法需要指定匹配记录数量n：indexSearcher.search(query, n)
TopDocs.totalHits：是匹配索引库中所有记录的数量
TopDocs.scoreDocs：匹配相关度高的前边记录数组，scoreDocs的长度小于等于search方法指定的参数n
