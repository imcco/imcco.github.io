---
title: 关于word合并、分割、转换问题
tags:
  - Java
  - poi
copyright: true
category: Java
abbrlink: 51428
date: 2017-12-14 20:34:36
---
![image](http://ovi3ob9p4.bkt.clouddn.com/TIETU/CT0017.jpg)

最近项目中有个这样的需求：要把word 按章节(大纲)拆分多个word,然后再将这些word能合并成一个整体。看到这个需要，其实也是头疼，100个程序员中应该100位都没有做过类似的开发，最多的就是poi 读取、生成word,那么接下来，我会将最近研究的word切割、转换、合并以笔记的形式记录，也算做为积累吧！
  <!--more-->
word 分割 ：

   word 分割，使用的 poi，这个还真有点难，如果不是同事给国帮助，分割这块，还是很难搞定，不过合并用的第三方插件，很快就搞定了，上代码吧：
```java
package com.sysware.soft603.util.backUp;
 
import java.io.File;
import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.InputStream;
import java.io.OutputStream;
import java.io.OutputStreamWriter;
import java.util.ArrayList;
import java.util.List;
 
import org.apache.poi.xwpf.usermodel.BodyElementType;
import org.apache.poi.xwpf.usermodel.IBodyElement;
import org.apache.poi.xwpf.usermodel.XWPFDocument;
import org.apache.poi.xwpf.usermodel.XWPFParagraph;
import org.apache.poi.xwpf.usermodel.XWPFRun;
import org.apache.poi.xwpf.usermodel.XWPFStyles;
import org.dom4j.Element;
import org.dom4j.io.OutputFormat;
import org.dom4j.io.SAXReader;
import org.dom4j.io.XMLWriter;
 
import com.sysware.fai.entity.BookMarkInfo;
 
public class POItest1 {
    public static void main(String[] args) throws IOException {
 
        splitWord();
 
    }
 
    private static void splitWord() {
        String path = "d:\\GJB 1362A-2007 军工产品定型程序和要求.docx";
        InputStream is = null;
        List<BookMarkInfo> bookMarkInfos = new ArrayList<BookMarkInfo>();
        try {
            is = new FileInputStream(path);
        } catch (FileNotFoundException e) {
            // TODO 自动生成的 catch 块
            e.printStackTrace();
        }
 
        XWPFDocument doc = null;
        try {
            doc = new XWPFDocument(is);
        } catch (IOException e) {
            // TODO 自动生成的 catch 块
            e.printStackTrace();
        }
 
        // 获取段落
        List<XWPFParagraph> paras = doc.getParagraphs();
        // 获取bodyElements
        List<IBodyElement> bodyElements = doc.getBodyElements();
        // 获取doc样式
        XWPFStyles styles = doc.getStyles();
 
        int j = 0;
        // /切割成的word 文件存储位置
        String patha = "d:\\poi\\";
        // 根据大纲定义分割成的段落
        ArrayList<Integer> al_duanLuo = new ArrayList<Integer>();
        // 大纲名称
        ArrayList al2_name = new ArrayList<>();
        // 大纲级别
        ArrayList<Integer> al3_jiBie = new ArrayList<Integer>();
        ArrayList<Integer> al5 = new ArrayList<Integer>();
        // 存放生成wordId
        ArrayList<String> al6_wordId = new ArrayList<String>();
 
        for (int i = 0; i < bodyElements.size(); i++) {
            IBodyElement bodyElement = bodyElements.get(i);
            try {
                if (j == 0) {
                    al_duanLuo.add(i);
                    j++;
                    al2_name.add("首页");
                    al3_jiBie.add(1);
                    // al5.add(1);
                    al6_wordId.add(java.util.UUID.randomUUID().toString());
                }
                if (bodyElement.getElementType() == BodyElementType.PARAGRAPH) {
                    XWPFParagraph para = (XWPFParagraph) bodyElement;
 
                    if (styles.getStyle(para.getStyle()).getCTStyle().getName()
                            .getVal().contains("heading")) {
                        // 以标题创建第一个文件
                        al_duanLuo.add(i);
                        j++;
                        al2_name.add(para.getParagraphText());
                        // System.out.println(al2);
                        String temps = styles.getStyle(para.getStyle())
                                .getCTStyle().getName().getVal()
                                .split("heading")[1].trim();
                        al3_jiBie.add(Integer.parseInt(temps));
                        // al5.add(Integer.parseInt(para.getStyleID()));
                        al6_wordId.add(java.util.UUID.randomUUID().toString());
                        // System.out.println("a2======" + al2_name);
                        // System.out.println("a3======" + al3_jiBie);
                        // System.out.println("al======" + al_duanLuo);
                        // System.out.println("a5======" + al5);
                    }
                }
            } catch (Exception e) {
                // TODO Auto-generated catch block
                e.printStackTrace();
 
            }
 
        }
        // 定义存放父新id
        ArrayList al4_parentId = new ArrayList<>();
 
        for (int i = 0; i < al_duanLuo.size(); i++) {
            if (al3_jiBie.get(i) > 1) {
                int k;
                for (k = i; k >= 0; k--) {
                    if ((int) al3_jiBie.get(k) < (int) al3_jiBie.get(i)) {
                        break;
                    }
                }
                al4_parentId.add(al6_wordId.get(k));
            } else {
                al4_parentId.add(" ");
            }
        }
        // for (int i = 0; i < al_duanLuo.size(); i++) {
        // System.out.println("===================");
        // System.out.println("al2=" + al2_name.get(i));
        // System.out.println("al3=" + al3_jiBie.get(i));
        // System.out.println("all4=" + al4_parentId.get(i));
        // System.out.println("===================");
        // // 根据关系创建拼接组装xml文件
        // }
        System.out.println("al_duanLuo" + al_duanLuo);
        System.out.println("al2_name=" + al2_name);
        System.out.println("al3_jiBie=" + al3_jiBie);
        // System.out.println(al6_wordId);
        System.out.println("al4_parentId=" + al4_parentId);
 
        XWPFDocument newDoc = doc;
        int max = 0;
        max = bodyElements.size() - 1;
 
        al_duanLuo.add(max);
        try {
            for (int k = 0; k < al_duanLuo.size() - 1; k++) {
                path = "d:\\GJB 1362A-2007 军工产品定型程序和要求.docx";
                is = null;
                try {
                    is = new FileInputStream(path);
                } catch (FileNotFoundException e) {
                    // TODO 自动生成的 catch 块
                    e.printStackTrace();
                }
                doc = null;
                try {
                    doc = new XWPFDocument(is);
                    // 移除多级列表，移除前面的编号，这里分割后是有编号的，不过这里如果你不移除的话，直接把代码注释掉即可
                    if (k != 0) {
                        XWPFParagraph para1 = (XWPFParagraph) doc
                                .getBodyElements().get(al_duanLuo.get(k));
                        String str1 = para1.getStyleID();
                        doc.getStyles().getStyle(str1).getCTStyle().getPPr()
                                .unsetNumPr();
                    }
 
                } catch (IOException e) {
                    // TODO 自动生成的 catch 块
                    e.printStackTrace();
                }
 
                // 移除前0－－14，
                int temp = al_duanLuo.get(k);
                int tempCount = al_duanLuo.get(k + 1);
 
                for (int u = max; u > tempCount - 1; u--) {
                    doc.removeBodyElement(u);
                     
                }
 
                // 进行移除之前
                for (int l = temp - 1; l >= 0; l--) {
                    doc.removeBodyElement(l);
                     
                }
                // XWPFParagraph tempDuan = paras.get(temp);
                // paras.get(7).setStyle(paras.get(1).getStyle());
                // XWPFRun run = paras.get(7).insertNewRun(0);
                // run.setText("10086");
                OutputStream out = null;
 
                out = new FileOutputStream("d:\\poi\\" + al6_wordId.get(k)
                        + ".docx");
                BookMarkInfo bookMarkInfo = new BookMarkInfo();
                bookMarkInfo.setId(al6_wordId.get(k));
                bookMarkInfo.setPid(al4_parentId.get(k).toString());
                bookMarkInfo.setName(al2_name.get(k).toString());
                bookMarkInfo.setLevel(al3_jiBie.get(k));
                bookMarkInfos.add(bookMarkInfo);
                try {
                    doc.write(out);
                } catch (IOException e) {
                    // TODO 自动生成的 catch 块
                    e.printStackTrace();
                }
 
            }
        } catch (Exception e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
        System.out.println("over");
        CreateXml(bookMarkInfos);
        //遍历xml文件
         
    }
 
    public static void CreateXml(List<BookMarkInfo> bookMarkInfos) {
        String target = "d:/MyXml.xml";
        SAXReader reader = new SAXReader();
        try {
            org.dom4j.Document document = reader.read(new File(target));
            Element root = document.getRootElement();
            List<Element> list = root.elements();
            for (Element element : list) {
                root.remove(element);
            }
            //给xml加上标号,
            int j=0;
            for (int i=0;i< bookMarkInfos.size();i++) {
                BookMarkInfo bookMarkInfo =bookMarkInfos.get(i);
                //判断是否为第一级
                if(bookMarkInfo.getLevel()==1){
                    //为了是第一个不用加编号
                    if(i!=0){
                        j++ ;
                    }
                     
                }
                if (bookMarkInfo.getLevel() == 1) {
                    Element element = root.addElement("item");
                    element.addAttribute("id", bookMarkInfo.getId());
                    //加上编号
                    if(j==0){
                        element.addAttribute("name", bookMarkInfo.getName());
                    }else{
                         
                        element.addAttribute("name", j+bookMarkInfo.getName());
                    }
                    element.addAttribute("filename", bookMarkInfo.getId()
                            + ".docx");
                    GetXElement(element, bookMarkInfo, bookMarkInfos,j+"",0);
                }
            }
            saveXml(target, document);
            //遍历xml
        } catch (org.dom4j.DocumentException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
    }
 
    private static void saveXml(String target, org.dom4j.Document document) {
        try {
            OutputFormat outputFormat = OutputFormat.createPrettyPrint();
            outputFormat.setLineSeparator("\r\n");
            OutputStreamWriter outputStreamWriter = new OutputStreamWriter(
                    new FileOutputStream(target), "UTF-8");
            XMLWriter xmlWriter = new XMLWriter(outputStreamWriter,
                    outputFormat);
            xmlWriter.write(document);
            xmlWriter.close();
        } catch (IOException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
    }
 
    private static void GetXElement(Element parent, BookMarkInfo item,
            List<BookMarkInfo> bookMarkInfos,String  j,int k) {
        k=0;
        for (BookMarkInfo bookMarkInfo : bookMarkInfos) {
            if (bookMarkInfo.getLevel() == item.getLevel() + 1
                    && bookMarkInfo.getPid() == item.getId()) {
                k++ ;
                Element element = parent.addElement("item");
                element.addAttribute("id", bookMarkInfo.getId());
                element.addAttribute("name", j+"."+k+bookMarkInfo.getName());
                element.addAttribute("filename", bookMarkInfo.getId() + ".docx");
                GetXElement(element, bookMarkInfo, bookMarkInfos,j+"."+k,k);
            }
        }
    }
}
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<item id="1" name="1" filename="a.docx" author="杨稳" publishtime="2017-02-03" keywords="关键词" abstract="摘要" journal="" issn="" Implementation="2017-04-01"  groupname="国标" phase="研制阶段" content="内容分类">
  <!-- id=编号（标准规范）,name=名称,filename=文件名称,author=作者,publishtime="发表时间",keywords="关键词",abstract="摘要",journal="所属期刊",issn=ISSN,Implementation="实施日期",groupname="国标",phase="研制阶段",content="内容分类"-->
  <item id="1.1" name="1.1" filename="a.docx"/>
  <item id="1.2" name="1.2" filename="a.docx">
    <item id="1.2.1" name="1.2.1" filename="a.docx"/>
    <item id="1.2.2" name="1.2.2" filename="a.docx"/>
  </item>
  <item id="1.3" name="1.3" filename="a.docx">
    <item id="1.3.1" name="1.3.1" filename="a.docx"/>
  </item>
  <item id="1.4" name="1.4" filename="a.docx"/>
</item>

```

```xml
<?xml version="1.0" encoding="UTF-8"?>

<item id="1" name="1" filename="a.docx" author="杨稳" publishtime="2017-02-03" keywords="关键词" abstract="摘要" journal="" issn="" Implementation="2017-04-01" groupname="国标" phase="研制阶段" content="内容分类"> 
  <!-- id=编号（标准规范）,name=名称,filename=文件名称,author=作者,publishtime="发表时间",keywords="关键词",abstract="摘要",journal="所属期刊",issn=ISSN,Implementation="实施日期",groupname="国标",phase="研制阶段",content="内容分类"-->  
  <item id="cdc88f18-a024-4e88-9ba1-3d5c5a7a0c58" name="首页" filename="cdc88f18-a024-4e88-9ba1-3d5c5a7a0c58.docx"/>
  <item id="d6cd51f4-b16f-4d25-860f-d102d01f4d79" name="1范围" filename="d6cd51f4-b16f-4d25-860f-d102d01f4d79.docx"/>
  <item id="250b91c9-986e-4ad8-ad21-2008331d07ec" name="2引用文件" filename="250b91c9-986e-4ad8-ad21-2008331d07ec.docx"/>
  <item id="7e54f054-3880-4087-bde6-ec423bc0a3ad" name="3术语和定义" filename="7e54f054-3880-4087-bde6-ec423bc0a3ad.docx">
    <item id="0b9dfec5-a484-4092-a8a3-0f1c0c4caeae" name="3.1一级定委 first-class committee for finalization of military products" filename="0b9dfec5-a484-4092-a8a3-0f1c0c4caeae.docx"/>
    <item id="0192d8d5-fdc7-4f8d-8a6e-bb88a53f5395" name="3.2二级定委 second-class committee for finalization of military products" filename="0192d8d5-fdc7-4f8d-8a6e-bb88a53f5395.docx"/>
    <item id="1e172285-5b97-4da0-98be-60a90bd245b8" name="3.3军工产品 military products" filename="1e172285-5b97-4da0-98be-60a90bd245b8.docx"/>
    <item id="a255bca2-9380-485c-8c43-43bfb6c19a62" name="3.4军工产品定型 finalization of military products" filename="a255bca2-9380-485c-8c43-43bfb6c19a62.docx"/>
    <item id="d1df3277-4895-4282-b1b1-52cf9fe48a3c" name="3.5设计定型 design finalization of military products" filename="d1df3277-4895-4282-b1b1-52cf9fe48a3c.docx"/>
    <item id="70709984-ba90-40cc-b4ad-e29a025cb9ab" name="3.6生产定型 manufacture finalization of military products" filename="70709984-ba90-40cc-b4ad-e29a025cb9ab.docx"/>
    <item id="f27b646b-bdf6-4fa9-aa78-c9dab72450ce" name="3.7一级军工产品 first-class military products" filename="f27b646b-bdf6-4fa9-aa78-c9dab72450ce.docx"/>
    <item id="d80e9425-6502-46db-9251-43a06fa8a164" name="3.8二级军工产品 second-class military products" filename="d80e9425-6502-46db-9251-43a06fa8a164.docx"/>
    <item id="6d217bdf-a7c6-4751-8a64-0a16026d6e52" name="3.9军工产品鉴定 assessment of military products" filename="6d217bdf-a7c6-4751-8a64-0a16026d6e52.docx"/>
  </item>
  <item id="bd43a101-a948-4efc-ab33-cdb1bf324a6b" name="4总则" filename="bd43a101-a948-4efc-ab33-cdb1bf324a6b.docx">
    <item id="43463950-f7f8-459c-9a62-65ef8021027a" name="4.1定型工作的依据" filename="43463950-f7f8-459c-9a62-65ef8021027a.docx"/>
    <item id="fd4614c6-c00e-4244-ae25-0d9fdb3c5e51" name="4.2定型分类" filename="fd4614c6-c00e-4244-ae25-0d9fdb3c5e51.docx"/>
    <item id="2ccfe33a-c864-41ac-89af-91a05075eda1" name="4.3定型原则" filename="2ccfe33a-c864-41ac-89af-91a05075eda1.docx"/>
    <item id="b58bb7c0-cebf-45f6-8c86-30658ec89ca4" name="4.4定型分级" filename="b58bb7c0-cebf-45f6-8c86-30658ec89ca4.docx"/>
  </item>
  <item id="afc9af0e-9074-4dc6-be3b-6632f20135b8" name="5设计定型" filename="afc9af0e-9074-4dc6-be3b-6632f20135b8.docx">
    <item id="0ed6ad9a-34f1-427a-bf7e-70e1ec8717e0" name="5.1设计定型程序" filename="0ed6ad9a-34f1-427a-bf7e-70e1ec8717e0.docx"/>
    <item id="8634212f-0ec7-45b8-9596-69e7e6b3db78" name="5.2申请设计定型试验的条件" filename="8634212f-0ec7-45b8-9596-69e7e6b3db78.docx"/>
    <item id="f1ab75a0-def4-4058-ab36-df14ee4081d6" name="5.3申请设计定型试验" filename="f1ab75a0-def4-4058-ab36-df14ee4081d6.docx"/>
    <item id="d205e3ac-aa79-414f-8817-0bf693cb950c" name="5.4制定设计定型试验大纲" filename="d205e3ac-aa79-414f-8817-0bf693cb950c.docx">
      <item id="7659923a-d274-4ae2-b882-dbce090457cc" name="5.4.1试验大纲的制定" filename="7659923a-d274-4ae2-b882-dbce090457cc.docx"/>
      <item id="7d3ca588-771d-4b93-b3d9-fbaa7b60066a" name="5.4.2试验大纲内容和要求" filename="7d3ca588-771d-4b93-b3d9-fbaa7b60066a.docx"/>
      <item id="3fb7579f-23b0-47e5-95fb-02115726d4cb" name="5.4.3试验大纲编制说明" filename="3fb7579f-23b0-47e5-95fb-02115726d4cb.docx"/>
      <item id="a864a195-3fcc-40c8-8854-8521603144a3" name="5.4.4试验大纲变更" filename="a864a195-3fcc-40c8-8854-8521603144a3.docx"/>
    </item>
    <item id="67b5e4c2-16ac-4023-a215-d650f86d2d06" name="5.5组织设计定型试验" filename="67b5e4c2-16ac-4023-a215-d650f86d2d06.docx">
      <item id="fb17fdd6-def8-4219-b780-bdcb8fcf34f5" name="5.5.1试验要求" filename="fb17fdd6-def8-4219-b780-bdcb8fcf34f5.docx"/>
      <item id="fff01927-27c5-4e27-bfd9-8356846c64a9" name="5.5.2试验组织实施" filename="fff01927-27c5-4e27-bfd9-8356846c64a9.docx"/>
      <item id="e02e40c4-c1a9-42da-bf91-923e46f49ca3" name="5.5.3试验顺序" filename="e02e40c4-c1a9-42da-bf91-923e46f49ca3.docx"/>
      <item id="8428ba02-bda0-43f3-898a-cb61a0bffebb" name="5.5.4试验中断处理" filename="8428ba02-bda0-43f3-898a-cb61a0bffebb.docx"/>
      <item id="0f8ee823-1b3b-4357-9821-5b6c23bb1c9e" name="5.5.5试验恢复处理" filename="0f8ee823-1b3b-4357-9821-5b6c23bb1c9e.docx"/>
      <item id="008c1546-45d5-4bfd-96c5-d4150054d547" name="5.5.6承研承制单位的责任" filename="008c1546-45d5-4bfd-96c5-d4150054d547.docx"/>
      <item id="f19db0de-1e2f-47dd-b82f-173ae5a7acab" name="5.5.7试验记录" filename="f19db0de-1e2f-47dd-b82f-173ae5a7acab.docx"/>
      <item id="2c6df7ba-53ec-45c5-95f7-4be37e5b7406" name="5.5.8试验报告" filename="2c6df7ba-53ec-45c5-95f7-4be37e5b7406.docx"/>
    </item>
    <item id="8262e0da-b543-45da-b289-380cf43fab59" name="5.6申请设计定型" filename="8262e0da-b543-45da-b289-380cf43fab59.docx">
      <item id="ded722e3-4c04-4fe3-a8e4-bf30b73bcda2" name="5.6.1提出申请报告" filename="ded722e3-4c04-4fe3-a8e4-bf30b73bcda2.docx"/>
      <item id="45d1a9ed-01ed-46c5-a81a-1a0822627833" name="5.6.2申请报告内容" filename="45d1a9ed-01ed-46c5-a81a-1a0822627833.docx"/>
      <item id="3038774c-ddd7-440a-b172-ab758192dd8a" name="5.6.3申请报告附件" filename="3038774c-ddd7-440a-b172-ab758192dd8a.docx"/>
      <item id="ec05b70d-07c6-4f21-b5b0-f61fa08db313" name="5.6.4产品模型" filename="ec05b70d-07c6-4f21-b5b0-f61fa08db313.docx"/>
    </item>
    <item id="a9c7cf2a-48be-46c5-aef4-8c93e138db00" name="5.7设计定型审查" filename="a9c7cf2a-48be-46c5-aef4-8c93e138db00.docx">
      <item id="ea6c4556-a3fe-4177-a24b-d05ecc8b9054" name="5.7.1设计定型审查组和检查组" filename="ea6c4556-a3fe-4177-a24b-d05ecc8b9054.docx"/>
      <item id="b448feb3-4731-4344-bf27-a29309b4dded" name="5.7.2设计定型标准和要求" filename="b448feb3-4731-4344-bf27-a29309b4dded.docx"/>
      <item id="72e2b383-83ad-4699-ad07-23b6c29da944" name="5.7.3审查意见书" filename="72e2b383-83ad-4699-ad07-23b6c29da944.docx"/>
    </item>
    <item id="8f480f8a-cee6-4bb0-8a82-23c01b368f7c" name="5.8审批设计定型" filename="8f480f8a-cee6-4bb0-8a82-23c01b368f7c.docx">
      <item id="b5c7d912-9f6f-4660-beb3-57e19f5d4e95" name="5.8.1审批一级军工产品设计定型" filename="b5c7d912-9f6f-4660-beb3-57e19f5d4e95.docx">
        <item id="89fc14e1-09af-4537-915a-3fca40f7aa1d" name="5.8.1.1二级定委根据产品设计定型审查意见，审议一级军工产品设计定型有关事宜，符合设计定型标准和要求的，向一级定委呈报批准设计定型的请示；不符合设计定型标准和要求的，提出处理意见，连同原提交的军工产品设计定型申请文件一并退回申请单位。" filename="89fc14e1-09af-4537-915a-3fca40f7aa1d.docx"/>
        <item id="b47935da-32fd-4ff7-b1a2-8ffa74d69dba" name="5.8.1.2一级定委专家咨询委员会对二级定委报送的军工产品设计定型请示进行审核，审核后向一级定委提出定型咨询意见。专家咨询一委员会可参加二级定委组织的试验试用大纲评审、试验试用、定型审查等活动。" filename="b47935da-32fd-4ff7-b1a2-8ffa74d69dba.docx"/>
        <item id="1d7edc11-e6f2-4513-8268-ccf2de55e1ba" name="5.8.1.3一级定委对符合规定标准和要求的产品，按照规定的权限批准设计定型或报国务院、中央军委审批，下发批复；对不符合规定标准和要求的产品，提出处理意见，连同原报送的有关文件一并退回报送的二级定委。" filename="1d7edc11-e6f2-4513-8268-ccf2de55e1ba.docx"/>
        <item id="dd662740-de4a-4dee-9b05-afe92b3a020a" name="5.8.1.4产品批准设计定型后，由一级定委颁发产品设计定型证书，由二级定委对有关设计定型文件加盖设计定型专用章。" filename="dd662740-de4a-4dee-9b05-afe92b3a020a.docx"/>
      </item>
      <item id="e3da11b1-6637-4e06-adfd-191614132859" name="5.8.2审批二级军工产品设计定型" filename="e3da11b1-6637-4e06-adfd-191614132859.docx"/>
    </item>
  </item>
  <item id="248e1e89-f829-4e89-b4d7-9420dacc13b8" name="6生产定型" filename="248e1e89-f829-4e89-b4d7-9420dacc13b8.docx">
    <item id="90f82375-bf17-4d44-86b5-581bcce5ec2d" name="6.1生产定型条件和时间" filename="90f82375-bf17-4d44-86b5-581bcce5ec2d.docx"/>
    <item id="7a197dae-20e4-4ba7-abce-86d6ce6ced72" name="6.2生产定型程序" filename="7a197dae-20e4-4ba7-abce-86d6ce6ced72.docx"/>
    <item id="21da9766-d333-41cf-abc9-933006b59799" name="6.3部队试用产品与生产定型试验产品" filename="21da9766-d333-41cf-abc9-933006b59799.docx">
      <item id="2c96287b-3f41-4fb5-9ee5-71a4b3a763ee" name="6.3.1部队试用产品" filename="2c96287b-3f41-4fb5-9ee5-71a4b3a763ee.docx"/>
      <item id="f96c66e4-6279-4bd5-a52a-2948e7ec580e" name="6.3.2生产定型试验产品" filename="f96c66e4-6279-4bd5-a52a-2948e7ec580e.docx"/>
    </item>
    <item id="5f252b0e-7785-4d08-9d74-a85081c9dc5f" name="6.4组织工艺和生产条件考核" filename="5f252b0e-7785-4d08-9d74-a85081c9dc5f.docx"/>
    <item id="6ff6d59f-1711-42a1-beb9-cf8c96208049" name="6.5申请部队试用" filename="6ff6d59f-1711-42a1-beb9-cf8c96208049.docx"/>
    <item id="5fb9ca65-9ed9-426c-adb0-82c3c8a42d9f" name="6.6制定部队试用大纲" filename="5fb9ca65-9ed9-426c-adb0-82c3c8a42d9f.docx"/>
    <item id="7017ddc5-456f-470c-a73b-284704415106" name="6.7组织部队试用" filename="7017ddc5-456f-470c-a73b-284704415106.docx">
      <item id="d2b30828-a09a-4534-bdcd-4eda8aba8e90" name="6.7.1部队试用的实施" filename="d2b30828-a09a-4534-bdcd-4eda8aba8e90.docx"/>
      <item id="2d83ebef-af67-4ff7-8b2c-d61bcface34e" name="6.7.2部队试用报告" filename="2d83ebef-af67-4ff7-8b2c-d61bcface34e.docx"/>
    </item>
    <item id="eedd399f-0d47-42c0-b954-20b532a05340" name="6.8申请生产定型试验" filename="eedd399f-0d47-42c0-b954-20b532a05340.docx"/>
    <item id="a2f3b8ab-5fdf-44cd-8694-a020dbed6fe2" name="6.9制定生产定型试验大纲" filename="a2f3b8ab-5fdf-44cd-8694-a020dbed6fe2.docx"/>
    <item id="c35fa5ab-ba11-451a-bbf5-043add4496c1" name="6.10组织生产定型试验" filename="c35fa5ab-ba11-451a-bbf5-043add4496c1.docx"/>
    <item id="f5ad80a9-fdc2-4bab-a460-2da497dfca68" name="6.11申请生产定型" filename="f5ad80a9-fdc2-4bab-a460-2da497dfca68.docx">
      <item id="25783469-78ff-4400-b39c-795e72ed33b0" name="6.11.1提出申请报告" filename="25783469-78ff-4400-b39c-795e72ed33b0.docx"/>
      <item id="0344cbf0-1595-4124-b030-20a9ace0941c" name="6.11.2申请报告内容" filename="0344cbf0-1595-4124-b030-20a9ace0941c.docx"/>
      <item id="6862ca2d-b646-4eba-af5c-150de9055301" name="6.11.3申请报告附件" filename="6862ca2d-b646-4eba-af5c-150de9055301.docx"/>
    </item>
    <item id="a12aaeb2-2615-437a-aeb4-2ce7fc172657" name="6.12组织生产定型审查" filename="a12aaeb2-2615-437a-aeb4-2ce7fc172657.docx">
      <item id="e5987e7d-5a37-4906-961e-6ee839dfc743" name="6.12.1生产定型审查组 " filename="e5987e7d-5a37-4906-961e-6ee839dfc743.docx"/>
      <item id="b9c74941-650a-4f8f-a92c-8578e60c072b" name="6.12.2生产定型标准和要求" filename="b9c74941-650a-4f8f-a92c-8578e60c072b.docx"/>
      <item id="fa286bb9-27e0-4d50-ac57-78773dfbded3" name="6.12.3审查意见书" filename="fa286bb9-27e0-4d50-ac57-78773dfbded3.docx"/>
    </item>
    <item id="0346afa0-ca37-46e8-89c5-eaaec86a8898" name="6.13审批生产定型" filename="0346afa0-ca37-46e8-89c5-eaaec86a8898.docx"/>
  </item>
  <item id="6472b4ba-fbf8-4b8b-bf3a-7d0412edade2" name="7定型文件" filename="6472b4ba-fbf8-4b8b-bf3a-7d0412edade2.docx">
    <item id="f79e1d11-fb56-45cb-8529-fba7eac33185" name="7.1定型文件种类" filename="f79e1d11-fb56-45cb-8529-fba7eac33185.docx"/>
    <item id="07fce1f4-d186-42f1-ab89-1f9db5d65e0b" name="7.2定型文件清单" filename="07fce1f4-d186-42f1-ab89-1f9db5d65e0b.docx">
      <item id="cece5585-cf2f-4891-a4df-e1e5c2e57508" name="7.2.1设计定型文件" filename="cece5585-cf2f-4891-a4df-e1e5c2e57508.docx"/>
      <item id="45350173-17a8-4357-bb2c-540e5184c6b5" name="7.2.2生产定型文件" filename="45350173-17a8-4357-bb2c-540e5184c6b5.docx"/>
    </item>
    <item id="9d8ebba1-513a-4eea-ade1-8629e3d5ea60" name="7.3定型文件制作要求" filename="9d8ebba1-513a-4eea-ade1-8629e3d5ea60.docx">
      <item id="2ea2bd5a-fb02-4ffa-b219-4261f8232b42" name="7.3.1技术文件格式" filename="2ea2bd5a-fb02-4ffa-b219-4261f8232b42.docx"/>
      <item id="a6d486b7-c3ae-4109-adf7-54270879bdb8" name="7.3.2产品图样绘制" filename="a6d486b7-c3ae-4109-adf7-54270879bdb8.docx"/>
      <item id="1a9b9795-7469-4fd6-92be-ff43c9cf454e" name="7.3.3产品照片和录像片" filename="1a9b9795-7469-4fd6-92be-ff43c9cf454e.docx"/>
      <item id="ba1e384c-e16e-4b3a-a125-0575f38d3a1e" name="7.3.4文件整理" filename="ba1e384c-e16e-4b3a-a125-0575f38d3a1e.docx"/>
    </item>
    <item id="50c13504-32c3-4a6d-ae96-f9a35cfd6c9e" name="7.4定型文件汇总上报" filename="50c13504-32c3-4a6d-ae96-f9a35cfd6c9e.docx"/>
    <item id="3cca6d6b-13a2-495b-ba9d-bd21b8584917" name="7.5定型文件盖章" filename="3cca6d6b-13a2-495b-ba9d-bd21b8584917.docx"/>
    <item id="a7381eb0-fc6a-48f0-8fe1-236d63401a41" name="7.6定型文件的使用" filename="a7381eb0-fc6a-48f0-8fe1-236d63401a41.docx"/>
    <item id="a77fbb37-dc8d-489e-a29f-0fe414ee4c43" name="7.7定型文件的修改" filename="a77fbb37-dc8d-489e-a29f-0fe414ee4c43.docx"/>
  </item>
</item>

```
不过poi 分割.docx没问题，但是分割.doc 文件就会有问题

所以中间如果是.doc 文件的话，所以中间还得进行转换，这里我使用的技术是aspose for java,这个特别简单只要把jar 包引入即可两步，不过这个玩艺是收费的，所以有点扯，不过，最后在csdn花了10分，下载了一个license.xml(破解)：

代码如下：

```java
package com.sysware.soft603.util.backUp;

import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.InputStream;

import com.aspose.words.Document;
import com.aspose.words.License;

public class WordToPDF {

    public static boolean getLicense() {
        boolean result = false;
        try {
            // InputStream is =
            // Test.class.getClassLoader().getResourceAsStream("\\license.xml");
            InputStream is = Test.class.getClassLoader().getResourceAsStream(
                    "com\\sysware\\soft603\\util\\backUp\\license.xml");
            // InputStream is =
            // Test.class.getClassLoader().getResourceAsStream("testpackage/license2.xml");
            // InputStream is =
            // Test.class.getClassLoader().getResourceAsStream("subpackage/license3.xml");
            License aposeLic = new License();
            aposeLic.setLicense(is);
            result = true;
        } catch (Exception e) {
            e.printStackTrace();
        }
        return result;
    }

    public static void main(String[] args) {
        // 验证License
        if (!getLicense()) {
            return;
        }

        String path = "d:\\GJB 1362A-2007 军工产品定型程序和要求.doc";

        InputStream is = null;
        try {
            is = new FileInputStream(path);

            Document doc = new Document(is);
            doc.save("d:\\1028.pdf");//转pdf、docx都可以
        } catch (FileNotFoundException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        } catch (Exception e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
    }
}

```
以下是合并的代码：使用的是docx4j


```java
package com.sysware.soft603.util.backUp;

import java.io.File;
import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.InputStream;
import java.io.OutputStream;
import java.util.ArrayList;
import java.util.Iterator;
import java.util.List;

import org.apache.commons.io.IOUtils;
import org.apache.poi.xwpf.usermodel.BodyElementType;
import org.apache.poi.xwpf.usermodel.IBodyElement;
import org.apache.poi.xwpf.usermodel.XWPFDocument;
import org.apache.poi.xwpf.usermodel.XWPFParagraph;
import org.apache.poi.xwpf.usermodel.XWPFStyles;
import org.docx4j.jaxb.Context;
import org.docx4j.openpackaging.exceptions.Docx4JException;
import org.docx4j.openpackaging.packages.WordprocessingMLPackage;
import org.docx4j.openpackaging.parts.PartName;
import org.docx4j.openpackaging.parts.WordprocessingML.AlternativeFormatInputPart;
import org.docx4j.openpackaging.parts.WordprocessingML.MainDocumentPart;
import org.docx4j.relationships.Relationship;
import org.docx4j.wml.CTAltChunk;

public class MargeDoc {

    public void mergeDocx(List<String> list, String path) {
        List<InputStream> inList = new ArrayList<InputStream>();
        for (int i = 0; i < list.size(); i++)
            try {
                inList.add(new FileInputStream(list.get(i)));
            } catch (FileNotFoundException e) {
                e.printStackTrace();
            }
        try {
            InputStream inputStream = mergeDocx(inList);
            saveTemplate(inputStream, path);
        } catch (Docx4JException | IOException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
    }

    public InputStream mergeDocx(final List<InputStream> streams) throws Docx4JException, IOException {

        WordprocessingMLPackage target = null;
        final File generated = File.createTempFile("generated", ".docx");

        int chunkId = 0;
        Iterator<InputStream> it = streams.iterator();
        while (it.hasNext()) {
            InputStream is = it.next();
            if (is != null) {
                if (target == null) {
                    // Copy first (master) document
                    OutputStream os = new FileOutputStream(generated);
                    os.write(IOUtils.toByteArray(is));
                    os.close();

                    target = WordprocessingMLPackage.load(generated);
                } else {
                    // Attach the others (Alternative input parts)
                    insertDocx(target.getMainDocumentPart(), IOUtils.toByteArray(is), chunkId++);
                }
            }
        }

        if (target != null) {
            target.save(generated);
            return new FileInputStream(generated);
        } else {
            return null;
        }
    }

    // 鎻掑叆鏂囨。
    private void insertDocx(MainDocumentPart main, byte[] bytes, int chunkId) {
        try {
            AlternativeFormatInputPart afiPart = new AlternativeFormatInputPart(
                    new PartName("/part" + chunkId + ".docx"));
            // afiPart.setContentType(new ContentType(CONTENT_TYPE));
            afiPart.setBinaryData(bytes);
            Relationship altChunkRel = main.addTargetPart(afiPart);

            CTAltChunk chunk = Context.getWmlObjectFactory().createCTAltChunk();
            chunk.setId(altChunkRel.getId());

            main.addObject(chunk);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    public void saveTemplate(InputStream fis, String toDocPath) {
        FileOutputStream fos;
        int bytesum = 0;
        int byteread = 0;
        try {
            fos = new FileOutputStream(toDocPath);
            byte[] buffer = new byte[1444];
            while ((byteread = fis.read(buffer)) != -1) {
                bytesum += byteread; // 瀛楄妭鏁� 鏂囦欢澶у皬
                fos.write(buffer, 0, byteread);
            }
            fis.close();
            fos.close();
        } catch (FileNotFoundException e1) {
            e1.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    public static void main(String[] args) throws Docx4JException, IOException {
        MargeDoc wordUtil = new MargeDoc();
        String template = "D:/poinew";
        List<String> list = new ArrayList<String>();
        list.add(template + "/0.docx");
        list.add(template + "/1.docx");
        list.add(template + "/2.docx");
        list.add(template + "/3.docx");
        list.add(template + "/3.1.docx");
        list.add(template + "/3.2.docx");
        list.add(template + "/3.3.docx");
        list.add(template + "/3.4.docx");
        list.add(template + "/3.5.docx");
        list.add(template + "/3.6.docx");
        list.add(template + "/3.7.docx");
        list.add(template + "/3.8.docx");
        list.add(template + "/3.9.docx");
        list.add(template + "/4.docx");
        list.add(template + "/4.1.docx");
        list.add(template + "/4.2.docx");
        list.add(template + "/4.3.docx");
        list.add(template + "/4.4.docx");
        list.add(template + "/5.docx");
        list.add(template + "/5.1.docx");
        list.add(template + "/5.2.docx");
        list.add(template + "/5.3.docx");
        list.add(template + "/5.4.docx");
        list.add(template + "/5.4.1.docx");
        list.add(template + "/5.4.2.docx");
        list.add(template + "/5.4.3.docx");
        list.add(template + "/5.4.4.docx");
        list.add(template + "/5.5.docx");
        list.add(template + "/5.5.1.docx");
        list.add(template + "/5.5.2.docx");
        list.add(template + "/5.5.3.docx");
        list.add(template + "/5.5.4.docx");
        list.add(template + "/5.5.5.docx");
        list.add(template + "/5.5.6.docx");
        list.add(template + "/5.5.7.docx");
        list.add(template + "/5.5.8.docx");
        list.add(template + "/5.6.docx");
        list.add(template + "/5.6.1.docx");
        list.add(template + "/5.6.2.docx");
        list.add(template + "/5.6.3.docx");
        list.add(template + "/5.6.4.docx");
        list.add(template + "/5.7.docx");
        list.add(template + "/5.7.1.docx");
        list.add(template + "/5.7.2.docx");
        list.add(template + "/5.7.3.docx");
        list.add(template + "/5.8.docx");
        list.add(template + "/5.8.1.docx");
        list.add(template + "/5.8.1.1.docx");
        list.add(template + "/5.8.1.2.docx");
        list.add(template + "/5.8.1.3.docx");
        list.add(template + "/5.8.1.4.docx");
        list.add(template + "/5.8.2.docx");
        list.add(template + "/6.docx");
        list.add(template + "/6.1.docx");
        list.add(template + "/6.2.docx");
        list.add(template + "/6.3.docx");
        list.add(template + "/6.3.1.docx");
        list.add(template + "/6.3.2.docx");
        list.add(template + "/6.4.docx");
        list.add(template + "/6.5.docx");
        list.add(template + "/6.6.docx");
        list.add(template + "/6.7.docx");
        list.add(template + "/6.7.1.docx");
        list.add(template + "/6.7.2.docx");
        list.add(template + "/6.8.docx");
        list.add(template + "/6.9.docx");
        list.add(template + "/6.10.docx");
        list.add(template + "/6.11.docx");
        list.add(template + "/6.11.1.docx");
        list.add(template + "/6.11.2.docx");
        list.add(template + "/6.11.3.docx");
        list.add(template + "/6.12.docx");
        list.add(template + "/6.12.1.docx");
        list.add(template + "/6.12.2.docx");
        list.add(template + "/6.12.3.docx");
        list.add(template + "/6.13.docx");
        list.add(template + "/7.docx");
        list.add(template + "/7.1.docx");
        list.add(template + "/7.2.docx");
        list.add(template + "/7.2.1.docx");
        list.add(template + "/7.2.2.docx");
        list.add(template + "/7.3.docx");
        list.add(template + "/7.3.1.docx");
        list.add(template + "/7.3.2.docx");
        list.add(template + "/7.3.3.docx");
        list.add(template + "/7.3.4.docx");
        list.add(template + "/7.4.docx");
        list.add(template + "/7.5.docx");
        list.add(template + "/7.6.docx");
        list.add(template + "/7.7.docx");
        wordUtil.mergeDocx(list, template + "/out.docx");
        //splitWord();
    }

    private static void splitWord() {
        String path = "d:\\poinew\\out.docx";
        InputStream is = null;
        try {
            is = new FileInputStream(path);
        } catch (FileNotFoundException e) {
            // TODO 自动生成的 catch 块
            e.printStackTrace();
        }

        XWPFDocument doc = null;
        try {
            doc = new XWPFDocument(is);
        } catch (IOException e) {
            // TODO 自动生成的 catch 块
            e.printStackTrace();
        }

        // 获取bodyElements
        List<IBodyElement> bodyElements = doc.getBodyElements();
        // 获取doc样式
        XWPFStyles styles = doc.getStyles();
        System.out.println(doc.getParagraphs().size());
        for (int i = 0; i < bodyElements.size(); i++) {
            IBodyElement bodyElement = bodyElements.get(i);
            try {
                
                if (bodyElement.getElementType() == BodyElementType.PARAGRAPH) {
                    XWPFParagraph para = (XWPFParagraph) bodyElement;
                    System.out.println(styles.getStyle(para.getStyle()).getCTStyle().getName().getVal());
                    System.out.println(para.getText());

                    if (styles.getStyle(para.getStyle()).getCTStyle().getName().getVal().contains("heading")) {

                        XWPFParagraph para1 = (XWPFParagraph) doc.getBodyElements().get(i);
                        String str1 = para1.getStyleID();
                        doc.getStyles().getStyle(str1).getCTStyle().getPPr().unsetNumPr();
                        System.out.println("test");
                    }
                }
            } catch (Exception e) {
                // TODO Auto-generated catch block
                e.printStackTrace();

            }

        }
        XWPFDocument newDoc = doc;
        int max = 0;
        path = "d:\\poinew\\out.docx";
        OutputStream out = null;
        try {
            out = new FileOutputStream("d:\\poinew\\newOut.docx");
            try {
                doc.write(out);
            } catch (IOException e) {
                // TODO Auto-generated catch block
                e.printStackTrace();
            }
        } catch (FileNotFoundException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
        System.out.println("over");

    }
}

```
使用jacob合并多个word为单个word
1导入jar  包,我使用的是1.7,dil文件一定要和jar包匹配

 

使用前操作
    1、把dll文件放在%JAVA_HOME%\bin下（注意系统是32位还是64位），
    也可以放在C:\Windows\System32下，如果是64位应该放在C:\Windows\SysWOW64 下。建议放在jdk的bin目录下
    2、如果是在eclipse下开发，需要重新引入jdk（Preference/Java/Installed JREs）
    3、开发时将jacab.jar包放在项目lib下并add到liabraries中即可。

测试用例: 我在我两台电脑上都部署了,但不知为啥,一台运行出现异常

```java
package com.dyz.test;
 
import java.util.ArrayList;
import java.util.List;
 
import com.jacob.activeX.ActiveXComponent;
import com.jacob.com.Dispatch;
import com.jacob.com.Variant;
 
public class TestWord {
 
    public static void main(String[] args) {
        List list = new ArrayList();
        String file1 = "D:\\file1.doc";
        String file2 = "D:\\file2.doc";
        //String file3 = "D:\\file3.doc";
        list.add(file1);
        list.add(file2);
        //list.add(file3);
    //  System.out.println(System.getProperty("java.library.path"));
        uniteDoc(list, "d:\\file.doc");
    }
 
    public static void uniteDoc(List fileList, String savepaths) {
        if (fileList.size() == 0 || fileList == null) {
            return;
        }
        // 打开word
        ActiveXComponent app = new ActiveXComponent("Word.Application");// 启动word
        try {
            // 设置word不可见
            app.setProperty("Visible", new Variant(false));
            // 获得documents对象
            Object docs = app.getProperty("Documents").toDispatch();
            // 打开第一个文件
            Object doc = Dispatch
                    .invoke((Dispatch) docs,
                            "Open",
                            Dispatch.Method,
                            new Object[] { (String) fileList.get(0),
                                    new Variant(false), new Variant(true) },
                            new int[3]).toDispatch();
            // 追加文件
            for (int i = 1; i < fileList.size(); i++) {
                Dispatch.invoke(app.getProperty("Selection").toDispatch(),
                        "insertFile", Dispatch.Method, new Object[] {
                                (String) fileList.get(i), "",
                                new Variant(false), new Variant(false),
                                new Variant(false) }, new int[3]);
            }
            // 保存新的word文件
            Dispatch.invoke((Dispatch) doc, "SaveAs", Dispatch.Method,
                    new Object[] { savepaths, new Variant(1) }, new int[3]);
            Variant f = new Variant(false);
            Dispatch.call((Dispatch) doc, "Close", f);
        } catch (Exception e) {
            throw new RuntimeException("合并word文件出错.原因:" + e);
        } finally {
            app.invoke("Quit", new Variant[] {});
        }
    }
 
}
```
