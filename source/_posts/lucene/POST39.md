---
title: Lucene实现文档进行全文检索功能
tags:
  - Lucene
category: Lucene
abbrlink: 10596
date: 2017-11-30 12:34:36
---
![image](http://ovi3ob9p4.bkt.clouddn.com/TIETU/CT0053.jpg)

Lucene是apache软件基金会4 jakarta项目组的一个子项目，是一个开放源代码的全文检索引擎工具包，但它不是一个完整的全文检索引擎，而是一个全文检索引擎的架构，提供了完整的查询引擎和索引引擎，部分文本分析引擎（英文与德文两种西方语言）。Lucene的目的是为软件开发人员提供一个简单易用的工具包，以方便的在目标系统中实现全文检索的功能，或者是以此为基础建立起完整的全文检索引擎。Lucene是一套用于全文检索和搜寻的开源程式库，由Apache软件基金会支持和提供。Lucene提供了一个简单却强大的应用程式接口，能够做全文索引和搜寻。在Java开发环境里Lucene是一个成熟的免费开源工具。就其本身而言，Lucene是当前以及最近几年最受欢迎的免费Java信息检索程序库。人们经常提到信息检索程序库，虽然与搜索引擎有关，但不应该将信息检索程序库与搜索引擎相混淆。
<!--more-->
这里讲一下使用Lucene对doc、docx、pdf、txt文档进行全文检索功能的实现。

涉及到的类一共有两个：

LuceneCreateIndex，创建索引：

```java
package com.yhd.test.poi;

import java.io.BufferedReader;
import java.io.File;
import java.io.FileInputStream;
import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.util.Date;

import org.apache.lucene.analysis.Analyzer;
import org.apache.lucene.analysis.standard.StandardAnalyzer;
import org.apache.lucene.document.DateTools;
import org.apache.lucene.document.Document;
import org.apache.lucene.document.Field;
import org.apache.lucene.index.IndexWriter;
import org.apache.lucene.store.Directory;
import org.apache.lucene.store.SimpleFSDirectory;
import org.apache.lucene.util.Version;
import org.apache.pdfbox.pdfparser.PDFParser;
import org.apache.pdfbox.pdmodel.PDDocument;
import org.apache.pdfbox.util.PDFTextStripper;
import org.apache.poi.hwpf.extractor.WordExtractor;
import org.apache.poi.xwpf.extractor.XWPFWordExtractor;
import org.apache.poi.xwpf.usermodel.XWPFDocument;

public class LuceneCreateIndex {

    /**
     * @param args
     * @throws IOException
     */
    public static void main(String[] args) throws IOException {
        // 保存word文件的路径
        String dataDirectory = "D:\\Studying\\poi\\test\\dataDirectory";
        // 保存Lucene索引文件的路径
        String indexDirectory = "D:\\Studying\\poi\\test\\indexDirectory";
        // 创建Directory对象 ，也就是分词器对象
        Directory directory = new SimpleFSDirectory(new File(indexDirectory));
        // 创建一个简单的分词器,可以对数据进行分词
        Analyzer analyzer = new StandardAnalyzer(Version.LUCENE_30);

        // 创建索引实例
        // 第1个参数是Directory,
        // 第2个是分词器,
        // 第3个表示是否是创建, true代表覆盖原先数据, 如果为false为在此基础上面修改,
        // 第4个MaxFieldLength表示对每个Field限制建立分词索引的最大数目，
        // 如果是MaxFieldLength.UNLIMITED，表示长度没有限制;
        // 如果是MaxFieldLength.LIMITED则表示有限制，可以通过IndexWriter对象的setMaxFieldLength（int
        // n）进行指定
        IndexWriter indexWriter = new IndexWriter(directory, analyzer, true,
                IndexWriter.MaxFieldLength.UNLIMITED);
        // 获取所有需要建立索引的文件
        File[] files = new File(dataDirectory).listFiles();

        for (int i = 0; i < files.length; i++) {
            // 文件是第几个
            System.out.println("这是第" + i + "个文件----------------");
            // 文件的完整路径
            System.out.println("完整路径：" + files[i].toString());
            // 获取文件名称
            String fileName = files[i].getName();
            // 获取文件后缀名，将其作为文件类型
            String fileType = fileName.substring(fileName.lastIndexOf(".") + 1,
                    fileName.length()).toLowerCase();
            // 文件名称
            System.out.println("文件名称：" + fileName);
            // 文件类型
            System.out.println("文件类型：" + fileType);

            Document doc = new Document();

            // String fileCode = FileType.getFileType(files[i].toString());
            // 查看各个文件的文件头标记的类型
            // System.out.println("fileCode=" + fileCode);

            InputStream in = new FileInputStream(files[i]);
            InputStreamReader reader = null;

            if (fileType != null && !fileType.equals("")) {

                if (fileType.equals("doc")) {
                    // 获取doc的word文档
                    WordExtractor wordExtractor = new WordExtractor(in);
                    // 创建Field对象，并放入doc对象中
                    // Field的各个字段含义如下：
                    // 第1个参数是设置field的name，
                    // 第2个参数是value，value值可以是文本（String类型，Reader类型或者是预分享的TokenStream）,
                    // 二进制（byet[]）, 或者是数字（一个 Number类型）
                    // 第3个参数是Field.Store，选择是否存储，如果存储的话在检索的时候可以返回值
                    // 第4个参数是Field.Index，用来设置索引方式
                    doc.add(new Field("contents", wordExtractor.getText(),
                            Field.Store.YES, Field.Index.ANALYZED));
                    // 关闭文档
                    wordExtractor.close();
                    System.out.println("注意：已为文件“" + fileName + "”创建了索引");

                } else if (fileType.equals("docx")) {
                    // 获取docx的word文档
                    XWPFWordExtractor xwpfWordExtractor = new XWPFWordExtractor(
                            new XWPFDocument(in));
                    // 创建Field对象，并放入doc对象中
                    doc.add(new Field("contents", xwpfWordExtractor.getText(),
                            Field.Store.YES, Field.Index.ANALYZED));
                    // 关闭文档
                    xwpfWordExtractor.close();
                    System.out.println("注意：已为文件“" + fileName + "”创建了索引");

                } else if (fileType.equals("pdf")) {
                    // 获取pdf文档
                    PDFParser parser = new PDFParser(in);
                    parser.parse();
                    PDDocument pdDocument = parser.getPDDocument();
                    PDFTextStripper stripper = new PDFTextStripper();
                    // 创建Field对象，并放入doc对象中
                    doc.add(new Field("contents", stripper.getText(pdDocument),
                            Field.Store.NO, Field.Index.ANALYZED));
                    // 关闭文档
                    pdDocument.close();
                    System.out.println("注意：已为文件“" + fileName + "”创建了索引");

                } else if (fileType.equals("txt")) {
                    // 建立一个输入流对象reader  
                    reader = new InputStreamReader(in); 
                    // 建立一个对象，它把文件内容转成计算机能读懂的语言
                    BufferedReader br = new BufferedReader(reader);   
                    String txtFile = "";
                    String line = null;

                    while ((line = br.readLine()) != null) {  
                        // 一次读入一行数据
                        txtFile += line;   
                    }  
                    // 创建Field对象，并放入doc对象中
                    doc.add(new Field("contents", txtFile, Field.Store.NO,
                            Field.Index.ANALYZED));
                    System.out.println("注意：已为文件“" + fileName + "”创建了索引");

                } else {

                    System.out.println();
                    continue;

                }

            }
            // 创建文件名的域，并放入doc对象中
            doc.add(new Field("filename", files[i].getName(), Field.Store.YES,
                    Field.Index.NOT_ANALYZED));
            // 创建时间的域，并放入doc对象中
            doc.add(new Field("indexDate", DateTools.dateToString(new Date(),
                    DateTools.Resolution.DAY), Field.Store.YES,
                    Field.Index.NOT_ANALYZED));
            // 写入IndexWriter
            indexWriter.addDocument(doc);
            // 换行
            System.out.println();
        }
        // 查看IndexWriter里面有多少个索引
        System.out.println("numDocs=" + indexWriter.numDocs());
        // 关闭索引
        indexWriter.close();

    }
}
```

LuceneSearch，进行搜索：

```java
package com.yhd.test.poi;

import java.io.File;
import java.io.IOException;

import org.apache.lucene.analysis.standard.StandardAnalyzer;
import org.apache.lucene.document.Document;
import org.apache.lucene.queryParser.ParseException;
import org.apache.lucene.queryParser.QueryParser;
import org.apache.lucene.search.IndexSearcher;
import org.apache.lucene.search.Query;
import org.apache.lucene.search.ScoreDoc;
import org.apache.lucene.search.TopDocs;
import org.apache.lucene.store.Directory;
import org.apache.lucene.store.SimpleFSDirectory;
import org.apache.lucene.util.Version;

public class LuceneSearch {
    public static void main(String[] args) throws IOException, ParseException {
        // 保存索引文件的地方
        String indexDirectory = "D:\\Studying\\poi\\test\\indexDirectory";
        // 创建Directory对象 ，也就是分词器对象
        Directory directory = new SimpleFSDirectory(new File(indexDirectory));
        // 创建 IndexSearcher对象，相比IndexWriter对象，这个参数就要提供一个索引的目录就行了
        IndexSearcher indexSearch = new IndexSearcher(directory);
        // 创建QueryParser对象,
        // 第1个参数表示Lucene的版本,
        // 第2个表示搜索Field的字段,
        // 第3个表示搜索使用分词器
        QueryParser queryParser = new QueryParser(Version.LUCENE_30,
                "contents", new StandardAnalyzer(Version.LUCENE_30));
        // 生成Query对象
        Query query = queryParser.parse("百度");
        // 搜索结果 TopDocs里面有scoreDocs[]数组，里面保存着索引值
        TopDocs hits = indexSearch.search(query, 10);
        // hits.totalHits表示一共搜到多少个
        System.out.println("找到了" + hits.totalHits + "个");
        // 循环hits.scoreDocs数据，并使用indexSearch.doc方法把Document还原，再拿出对应的字段的值
        for (int i = 0; i < hits.scoreDocs.length; i++) {
            ScoreDoc sdoc = hits.scoreDocs[i];
            Document doc = indexSearch.doc(sdoc.doc);
            System.out.println(doc.get("filename"));
        }
        indexSearch.close();
    }
}
```

详细的解释在代码注释里都有了，就不做过多解释了。需要的jar包如下：

![这里写图片描述](http://img.blog.csdn.net/20170728180442138?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZG9uZ2Rvbmc5MjIz/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

读取poi的类到[poi官网](https://poi.apache.org/)下载，读取pdf的类到[Apache PDFBox官网](https://pdfbox.apache.org/download.cgi)下载，这里用的1.8.13版本，2.0版本的调用方式与1.0版本已经不太一样了。

项目整体结构如下：

![这里写图片描述](http://img.blog.csdn.net/20170728180503680?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZG9uZ2Rvbmc5MjIz/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

先运行类：

LuceneCreateIndex

会读取目录dataDirectory，即：

> D:\Studying\poi\test\dataDirectory

下的文件，建立索引，索引会保存在目录indexDirectory，即：

> D:\Studying\poi\test\indexDirectory

下，然后运行：

LuceneSearch

使用索引进行查询，就能看到效果了。
