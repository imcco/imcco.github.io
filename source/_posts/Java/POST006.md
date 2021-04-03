---
title: Java成神之路-Java异常、集合、IO流、多线程和反射（六）
tags: Java
category: Java
date: 2018-02-23 14:44:36
---
![image](http://ovi3ob9p4.bkt.clouddn.com/TIETU/CT0131.jpg)

Java成神之路-Java异常、集合、IO流、多线程和反射
<!--more-->
# Java异常处理

## 一、异常概述

异常：Exception，是在运行发生的不正常情况。

原始异常处理：

代码阅读性差，臃肿不堪，与正常流程代码结合的很紧密，所以，在JAVA中进行一系列的改良，将一系列常见的问题，用面向对象的思考方式，对其进行了描述、封装。

在JAVA中，用类的形式对不正常情况进行了描述和封装对象。当程序出现问题时，调用相应的处理办法。

描述不正常情况的类，就称为异常类。将流程代码和异常代码进行分离。

异常就是JAVA通过面向对象的思想，将问题封装成了对象。用异常类对其进行描述。不同的问题，用不同的类进行描述。那么意味着，问题有多少，类就有多少。

## 二、异常体系

问题很多，意味着描述的类也很多，将其共性进行向上抽取，就形成了异常体系。最终异常分为两大类：

Throwable（父类）：问题发生，就应该抛出，让调用者处理。该体系的特点就在于Throwable及其子类都具有可抛性。

　　两个关键字实现可抛性：throws、throw

　　|--1.一般不可处理的。Error（错误）

　　　　特点：是由JVM（java虚拟机）抛出的严重性的问题。这种问题发生，一般不针对性处理，直接修改程序。

　　|--2.可以处理的。Exception（异常）

 　　　　特点：子类的后缀名都是用其父类名作为后缀，阅读性很强。

## 三、异常-原理&异常对象的抛出throw

可以看出，异常时，底层throw直接调用异常方法，抛出异常，只不过这些都在底层完成，我们看不到而已。

JAVA虚拟机它有一套异常处理机制，就是会把异常的各种信息，位置等报出来，以供解决异常。

真正开发的时候，这些异常信息是不会直接报出来的，会存成日志，我们定期查看。而且这个异常信息给用户也没用，只有给我们才有用。

## 四、异常-自定义异常&异常类的抛出throws

自定义异常：JAVA给出的一堆现有的异常没有我们需要的，这时候可以自定义了。但是这个类一定要继承Exception类。

## 五、异常-编译时检测异常和运行时异常的区别&throw和throws的区别

Exception体系分两种：
1.一种是编译时被检测异常（throws）。除runtimeException子类的所有子类。这样的问题可以针对性的处理。

2.运行时异常（throw）。Exception的子类中runtimeException和其子类。这种问题一般不处理，直接编译通过，在运行时让调用时的程序强制停止。

## 六、异常-异常捕捉try-catch

异常处理的捕捉形式：具体格式：

建立日志文件：第三方插件-log4j

## 七、异常-多catch情况

一个try对应多个catch的时候，小细节：

当多catch需要存在catch(Exception e)的时候，需要放到最后，不然会挂，因为Exception为父类，能接收所有的异常，放它之后，其他的就多余了，所以，它要放在最后的catch。

## 八、异常-异常处理原则

异常就是问题，JAVA对一些常见的问题已经弄好了，拿来用就好了。

如果，个别问题只在你自己的项目里出现，并且JAVA里没有这类问题，那就需要自己描述该问题。

1.方法内如果抛出需要检测的异常，那么方法上必须要声明，否则必须在方法内用try-catch捕捉，否则编译失败。

2.如果调用了声明异常的函数，要么try-catch要么throws，否则编译失败。

3.什么时候catch，什么时候throws？功能内容可以解决，用catch，解决不了，用throws告诉调用者，有调用者解决。

4.如果一个功能抛出了多个异常，那么调用时必须有对应多个catch进行针对性的处理。

## 九、异常-finally代码块

finally为一定会执行的代码，只有一种情况，finally不会执行。

try-catch-finally代码块组合特点：

1.try-catch-finally常见组合体

2.try-catch(可以多个catch)没有finally，没有资源需要释放（关闭），可以不用finally。

3.try-finally，没有catch时，方法旁边需要throws声明，因为没catch没处理。异常无法直接catch处理，但是资源需要关闭，这时用此组合。

## 十、异常的注意事项

1.子类在覆盖父类方法时，父类的方法如果抛出了异常，那么子类的方法只能抛出父类的异常或者该异常的子类。

 2.如果父类抛出多个异常，那么子类只能抛出父类异常的子集。----子类覆盖父类只能抛出父类异常或者子类或者子集。如果父类的方法没有抛出异常，那么子类覆盖时绝对不能抛，只能try。

 

常用异常方法：

Error类的常见子类：

![img](http://ovi3ob9p4.bkt.clouddn.com/Java/error.png)

 Exception类的常见子类：

![img](http://ovi3ob9p4.bkt.clouddn.com/Java/Exception.png)

RuntimeException类的常见的子类：

![img](http://ovi3ob9p4.bkt.clouddn.com/Java/RuntimeException.png)

# JAVA集合类汇总

## 一、集合与数组

数组（可以存储基本数据类型）是用来存现对象的一种容器，但是数组的长度固定，不适合在对象数量未知的情况下使用。

集合（只能存储对象，对象类型可以不一样）的长度可变，可在多数情况下使用。

## 二、层次关系

Collection接口是集合类的根接口，Java中没有提供这个接口的直接的实现类。但是却让其被继承产生了两个接口，就是Set和List。

1. Set中不能包含重复的元素。
2. List是一个有序的集合，可以包含重复的元素，提供了按索引访问的方式。

Map是Java.util包中的另一个接口，它和Collection接口没有关系，是相互独立的，但是都属于集合类的一部分。Map包含了key-value对。Map不能包含重复的key，但是可以包含相同的value。

3. Iterator，所有的集合类，都实现了Iterator接口，这是一个用于遍历集合中元素的接口，主要包含以下三种方法：

* hasNext()是否还有下一个元素。
* next()返回下一个元素。
* remove()删除当前元素。

## 三、几种重要的接口和类简介

1. List（有序、可重复）

List里存放的对象是有序的，同时也是可以重复的，List关注的是索引，拥有一系列和索引相关的方法，查询速度快。因为往list集合里插入或删除数据时，会伴随着后面数据的移动，所有插入删除数据速度慢。

2. Set（无序、不能重复）

Set里存放的对象是无序，不能重复的，集合中的对象不按特定的方式排序，只是简单地把对象加入集合中。

3. Map（键值对、键唯一、值不唯一）

Map集合中存储的是键值对，键不能重复，值可以重复。根据键得到值，对map集合遍历时先得到键的set集合，对set集合进行遍历，得到相应的值。

对比如下：

|            |             | 是否有序           | 是否允许元素重复                                          |
| ---------- | ----------- | ------------------ | --------------------------------------------------------- |
| Collection |             |                    |                                                           |
| List       | 是          | 是                 |                                                           |
| Set        | AbstractSet | 否                 | 否                                                        |
|            | HashSet     |                    |                                                           |
|            | TreeSet     | 是（用二叉排序树） |                                                           |
| Map        | AbstractMap | 否                 | 使用key-value来映射和存储数据，key必须唯一，value可以重复 |
|            | HashMap     |                    |                                                           |
|            | TreeMap     | 是（用二叉排序树） |                                                           |

 

## 四、遍历

 在类集中提供了以下四种的常见输出方式：

1）Iterator：迭代输出，是使用最多的输出方式。

2）ListIterator：是Iterator的子接口，专门用于输出List中的内容。

3）foreach输出：JDK1.5之后提供的新功能，可以输出数组或集合。

4）for循环

代码示例如下：

 for的形式：

```java
for（int i=0;i<arr.size();i++）{
	...
}
```

 foreach的形式： 

```java
for（int　i：arr）{
	...
}
```

 iterator的形式：

```java
Iterator it = arr.iterator();
while(it.hasNext()){ 
	object o =it.next(); 
...}
```



## 五、ArrayList和LinkedList

ArrayList和LinkedList在用法上没有区别，但是在功能上还是有区别的。LinkedList经常用在增删操作较多而查询操作很少的情况下，ArrayList则相反。

## 六、Map集合

实现类：HashMap、Hashtable、LinkedHashMap和TreeMap

* HashMap 

HashMap是最常用的Map，它根据键的HashCode值存储数据，根据键可以直接获取它的值，具有很快的访问速度，遍历时，取得数据的顺序是完全随机的。因为键对象不可以重复，所以HashMap最多只允许一条记录的键为Null，允许多条记录的值为Null，是非同步的

* Hashtable

Hashtable与HashMap类似，是HashMap的线程安全版，它支持线程的同步，即任一时刻只有一个线程能写Hashtable，因此也导致了Hashtale在写入时会比较慢，它继承自Dictionary类，不同的是它不允许记录的键或者值为null，同时效率较低。

* ConcurrentHashMap

线程安全，并且锁分离。ConcurrentHashMap内部使用段(Segment)来表示这些不同的部分，每个段其实就是一个小的hash table，它们有自己的锁。只要多个修改操作发生在不同的段上，它们就可以并发进行。

* LinkedHashMap

LinkedHashMap保存了记录的插入顺序，在用Iteraor遍历LinkedHashMap时，先得到的记录肯定是先插入的，在遍历的时候会比HashMap慢，有HashMap的全部特性。

* TreeMap

TreeMap实现SortMap接口，能够把它保存的记录根据键排序，默认是按键值的升序排序（自然顺序），也可以指定排序的比较器，当用Iterator遍历TreeMap时，得到的记录是排过序的。不允许key值为空，非同步的；

### map的遍历

#### 第一种：KeySet()

将Map中所有的键存入到set集合中。因为set具备迭代器。所有可以迭代方式取出所有的键，再根据get方法。获取每一个键对应的值。 keySet():迭代后只能通过get()取key 。
取到的结果会乱序，是因为取得数据行主键的时候，使用了HashMap.keySet()方法，而这个方法返回的Set结果，里面的数据是乱序排放的。
典型用法如下：

```java
Map map = new HashMap();
map.put("key1","lisi1");
map.put("key2","lisi2");
map.put("key3","lisi3");
map.put("key4","lisi4");  
```


//先获取map集合的所有键的set集合，keyset（）

```java
Iterator it = map.keySet().iterator();
 //获取迭代器
while(it.hasNext()){
	Object key = it.next();
System.out.println(map.get(key));
}
```

#### 第二种：entrySet（）

`Set<Map.Entry<K,V>>` entrySet() //返回此映射中包含的映射关系的 Set 视图。（一个关系就是一个键-值对），就是把(key-value)作为一个整体一对一对地存放到Set集合当中的。Map.Entry表示映射关系。entrySet()：迭代后可以e.getKey()，e.getValue()两种方法来取key和value。返回的是Entry接口。
典型用法如下：

```java
Map map = new HashMap();
map.put("key1","lisi1");
map.put("key2","lisi2");
map.put("key3","lisi3");
map.put("key4","lisi4");
```


//将map集合中的映射关系取出，存入到set集合

```java
Iterator it = map.entrySet().iterator();
	while(it.hasNext()){
	Entry e =(Entry) it.next();
	System.out.println("键"+e.getKey () + "的值为" + e.getValue());
}
```


推荐使用第二种方式，即entrySet()方法，效率较高。
对于keySet其实是遍历了2次，一次是转为iterator，一次就是从HashMap中取出key所对于的value。而entryset只是遍历了第一次，它把key和value都放到了entry中，所以快了。两种遍历的遍历时间相差还是很明显的。

## 七、 List集合

1. list中添加，获取，删除元素；

2. list中是否包含某个元素；
3. list中根据索引将元素数值改变(替换)；
4. list中查看（判断）元素的索引；
5. 根据元素索引位置进行的判断；
6. 利用list中索引位置重新生成一个新的list（截取集合）；
7. 对比两个list中的所有元素；
8. 判断list是否为空；
9. 返回Iterator集合对象；
10. 将集合转换为字符串；
11. 将集合转换为数组；
12. 集合类型转换；
13. 去重复；

```java
package MyTest01;
 
import java.util.ArrayList;
import java.util.List;
 
public class ListTest01 {
 
    public static void main(String[] args) {
         
            //list中添加，获取，删除元素
            List<String> person=new ArrayList<>();
            person.add("jackie");   //索引为0  //.add(e)
            person.add("peter");    //索引为1
            person.add("annie");    //索引为2
            person.add("martin");   //索引为3
            person.add("marry");    //索引为4
             
            person.remove(3);   //.remove(index)
            person.remove("marry");     //.remove(Object o)
             
            String per="";
            per=person.get(1);
            System.out.println(per);    ////.get(index)
             
            for (int i = 0; i < person.size(); i++) {
                System.out.println(person.get(i));  //.get(index)
            }
             
             
         
            //list总是否包含某个元素
            List<String> fruits=new ArrayList<>();
            fruits.add("苹果");
            fruits.add("香蕉");
            fruits.add("桃子");
            //for循环遍历list
            for (int i = 0; i < fruits.size(); i++) {
                System.out.println(fruits.get(i));
            }
            String appleString="苹果";
            //true or false
            System.out.println("fruits中是否包含苹果："+fruits.contains(appleString));
             
            if (fruits.contains(appleString)) {
                System.out.println("我喜欢吃苹果");
            }else {
                System.out.println("我不开心");
            }
             
            //list中根据索引将元素数值改变(替换)
            String a="白龙马", b="沙和尚", c="八戒", d="唐僧", e="悟空";
            List<String> people=new ArrayList<>();
            people.add(a);
            people.add(b);
            people.add(c);
            people.set(0, d);   //.set(index, element)      //将d唐僧放到list中索引为0的位置，替换a白龙马
            people.add(1, e);   //.add(index, element);     //将e悟空放到list中索引为1的位置,原来位置的b沙和尚后移一位
             
            //增强for循环遍历list
            for(String str:people){
                System.out.println(str);
            }
             
            //list中查看（判断）元素的索引
            List<String> names=new ArrayList<>();
            names.add("刘备");    //索引为0
            names.add("关羽");    //索引为1
            names.add("张飞");    //索引为2
            names.add("刘备");    //索引为3
            names.add("张飞");    //索引为4
            System.out.println(names.indexOf("刘备"));
            System.out.println(names.lastIndexOf("刘备"));
            System.out.println(names.indexOf("张飞"));
            System.out.println(names.lastIndexOf("张飞"));
             
            //根据元素索引位置进行的判断
            if (names.indexOf("刘备")==0) {
                System.out.println("刘备在这里");
            }else if (names.lastIndexOf("刘备")==3) {
                System.out.println("刘备在那里");
            }else {
                System.out.println("刘备到底在哪里？");
            }
             
            //利用list中索引位置重新生成一个新的list（截取集合）
            List<String> phone=new ArrayList<>();
            phone.add("三星");    //索引为0
            phone.add("苹果");    //索引为1
            phone.add("锤子");    //索引为2
            phone.add("华为");    //索引为3
            phone.add("小米");    //索引为4
            //原list进行遍历
            for(String pho:phone){
                System.out.println(pho);
            }
            //生成新list
            phone=phone.subList(1, 4);  //.subList(fromIndex, toIndex)      //利用索引1-4的对象重新生成一个list，但是不包含索引为4的元素，4-1=3
            for (int i = 0; i < phone.size(); i++) { // phone.size() 该方法得到list中的元素数的和
                System.out.println("新的list包含的元素是"+phone.get(i));
            }
             
            //对比两个list中的所有元素
            //两个相等对象的equals方法一定为true, 但两个hashcode相等的对象不一定是相等的对象
            if (person.equals(fruits)) {
                System.out.println("两个list中的所有元素相同");
            }else {
                System.out.println("两个list中的所有元素不一样");
            }
             
            if (person.hashCode()==fruits.hashCode()) {
                System.out.println("我们相同");
            }else {
                System.out.println("我们不一样");
            }
             
             
            //判断list是否为空
            //空则返回true，非空则返回false
            if (person.isEmpty()) {
                System.out.println("空的");
            }else {
                System.out.println("不是空的");
            }
             
            //返回Iterator集合对象
            System.out.println("返回Iterator集合对象:"+person.iterator());
             
            //将集合转换为字符串
            String liString="";
            liString=person.toString();
            System.out.println("将集合转换为字符串:"+liString);
             
            //将集合转换为数组，默认类型
            System.out.println("将集合转换为数组:"+person.toArray());
             
            ////将集合转换为指定类型（友好的处理）
            //1.默认类型
            List<Object> listsStrings=new ArrayList<>();
            for (int i = 0; i < person.size(); i++) {
                listsStrings.add(person.get(i));
            }
            //2.指定类型
            List<StringBuffer> lst=new ArrayList<>();
            for(String string:person){
                lst.add(StringBuffer(string));
            }
             
             
             
             
    }
 
    private static StringBuffer StringBuffer(String string) {
        return null;
    }
 
 
    }
```

## 八、Set集合

```java
//对 set 的遍历  
  
//1.迭代遍历：  
Set<String> set = new HashSet<String>();  
Iterator<String> it = set.iterator();  
while (it.hasNext()) {  
  String str = it.next();  
  System.out.println(str);  
}  
  
//2.for循环遍历：  
for (String str : set) {  
      System.out.println(str);  
}  
  
  
//优点还体现在泛型 假如 set中存放的是Object  
  
Set<Object> set = new HashSet<Object>();  
//for循环遍历：  
for (Object obj: set) {  
      if(obj instanceof Integer){  
                int aa= (Integer)obj;  
             }else if(obj instanceof String){  
               String aa = (String)obj  
             }  
              ........  
}
```



## 九、主要实现类区别小结

### Vector和ArrayList

1. vector是线程同步的，所以它也是线程安全的，而arraylist是线程异步的，是不安全的。如果不考虑到线程的安全因素，一般用arraylist效率比较高。
2. 如果集合中的元素的数目大于目前集合数组的长度时，vector增长率为目前数组长度的100%，而arraylist增长率为目前数组长度的50%。如果在集合中使用数据量比较大的数据，用vector有一定的优势。
3. 如果查找一个指定位置的数据，vector和arraylist使用的时间是相同的，如果频繁的访问数据，这个时候使用vector和arraylist都可以。而如果移动一个指定位置会导致后面的元素都发生移动，这个时候就应该考虑到使用linklist,因为它移动一个指定位置的数据时其它元素不移动。

ArrayList 和Vector是采用数组方式存储数据，此数组元素数大于实际存储的数据以便增加和插入元素，都允许直接序号索引元素，但是插入数据要涉及到数组元素移动等内存操作，所以索引数据快，插入数据慢，Vector由于使用了synchronized方法（线程安全）所以性能上比ArrayList要差，LinkedList使用双向链表实现存储，按序号索引数据需要进行向前或向后遍历，但是插入数据时只需要记录本项的前后项即可，所以插入数度较快。

### arraylist和linkedlist

1. ArrayList是实现了基于动态数组的数据结构，LinkedList基于链表的数据结构。
2. 对于随机访问get和set，ArrayList觉得优于LinkedList，因为LinkedList要移动指针。
3. 对于新增和删除操作add和remove，LinedList比较占优势，因为ArrayList要移动数据。 这一点要看实际情况的。若只对单条数据插入或删除，ArrayList的速度反而优于LinkedList。但若是批量随机的插入删除数据，LinkedList的速度大大优于ArrayList. 因为ArrayList每插入一条数据，要移动插入点及之后的所有数据。

### HashMap与TreeMap

1. HashMap通过hashcode对其内容进行快速查找，而TreeMap中所有的元素都保持着某种固定的顺序，如果你需要得到一个有序的结果你就应该使用TreeMap（HashMap中元素的排列顺序是不固定的）。
2. 在Map 中插入、删除和定位元素，HashMap是最好的选择。但如果您要按自然顺序或自定义顺序遍历键，那么TreeMap会更好。使用HashMap要求添加的键类明确定义了hashCode()和 equals()的实现。

两个map中的元素一样，但顺序不一样，导致hashCode()不一样。
同样做测试：
在HashMap中，同样的值的map,顺序不同，equals时，false;
而在treeMap中，同样的值的map,顺序不同,equals时，true，说明，treeMap在equals()时是整理了顺序了的。

### HashTable与HashMap

1. 同步性:Hashtable是线程安全的，也就是说是同步的，而HashMap是线程序不安全的，不是同步的。
2. HashMap允许存在一个为null的key，多个为null的value 。
3. hashtable的key和value都不允许为null。

# Java IO流
## 一、IO流概述

概述：

​         IO流简单来说就是Input和Output流，IO流主要是用来处理设备之间的数据传输，java对于数据的操作都是通过流实现，而java用于操作流的对象都在IO包中。

分类：

​        按操作数据分为：字节流和字符流。 如：Reader和InpurStream

​        按流向分：输入流和输出流。如：InputStream和OutputStream

IO流常用的基类：

​         * InputStream    ，    OutputStream

字符流的抽象基类：

​         * Reader       ，         Writer

由上面四个类派生的子类名称都是以其父类名作为子类的后缀：

​            如：FileReader和FileInputStream

## 二、字符流

### 1. 字符流简介：

\* 字符流中的对象融合了编码表，也就是系统默认的编码表。我们的系统一般都是GBK编码。

\* 字符流只用来处理文本数据，字节流用来处理媒体数据。

\* 数据最常见的表现方式是文件，字符流用于操作文件的子类一般是FileReader和FileWriter。

### 2.字符流读写：

注意事项：

* 写入文件后必须要用flush()刷新。

* 用完流后记得要关闭流

* 使用流对象要抛出IO异常

  ​


* 定义文件路径时，可以用“/”或者“\\”。

* 在创建一个文件时，如果目录下有同名文件将被覆盖。

* 在读取文件时，必须保证该文件已存在，否则出异常

  ​

示例1：在硬盘上创建一个文件,并写入一些文字数据

```java
class FireWriterDemo {
	public static void main(String[] args) throws IOException {             //需要对IO异常进行处理 

		//创建一个FileWriter对象，该对象一被初始化就必须要明确被操作的文件。
		//而且该文件会被创建到指定目录下。如果该目录有同名文件，那么该文件将被覆盖。

		FileWriter fw = new FileWriter("F:\\1.txt");//目的是明确数据要存放的目的地。

		//调用write的方法将字符串写到流中
		fw.write("hello world!");
	
		//刷新流对象缓冲中的数据，将数据刷到目的地中
		fw.flush();

		//关闭流资源，但是关闭之前会刷新一次内部缓冲中的数据。当我们结束输入时候，必须close();
		fw.write("first_test");
		fw.close();
		//flush和close的区别：flush刷新后可以继续输入，close刷新后不能继续输入。

	}
}
```

示例2：FileReader的reade()方法.

要求：用单个字符和字符数组进行分别读取

```java
class FileReaderDemo {
	public static void main(String[] args) {
		characters();
	}

/*****************字符数组进行读取*********************/
	private static void characters() {

		try {

			FileReader fr = new FileReader("Demo.txt");
			char []  buf = new char[6]; 
			//将Denmo中的文件读取到buf数组中。
			int num = 0;	
			while((num = fr.read(buf))!=-1) {

				//String(char[] value , int offest,int count) 分配一个新的String,包含从offest开始的count个字符
				sop(new String(buf,0,num));
			}
			sop('\n');
			fr.close();
		}
		catch (IOException e) {
			sop(e.toString());
		}
	}

/*****************单个字母读取*************************/
	private static void singleReader() {
		
		try {

			//创建一个文件读取流对象，和指定名称的文件关联。
			//要保证文件已经存在，否则会发生异常：FileNotFoundException
			FileReader fr = new FileReader("Demo.txt");

		
			//如何调用读取流对象的read方法？
			//read()方法，一次读取一个字符，并且自动往下读。如果到达末尾则返回-1
			int ch = 0;
			while ((ch=fr.read())!=-1) {
				sop((char)ch);
			}
			sop('\n');
			fr.close();

			/*int ch = fr.read();
			sop("ch=" + (char)ch);

			int ch2 = fr.read();
			sop("ch2=" + (char)ch2);

			//使用结束注意关闭流
			fr.close();	*/	
		}
		catch (IOException e) {
			sop(e.toString());
		}
	}

/**********************Println************************/
	private static void sop(Object obj) {
		System.out.print(obj);
	}

}
```



示例3：对已有文件的数据进行续写

```java
import java.io.*;

class  FileWriterDemo3 {
	public static void main(String[] args) {
		
		try {
			//传递一个参数,代表不覆盖已有的数据。并在已有数据的末尾进行数据续写
			FileWriter fw = new FileWriter("F:\\java_Demo\\day9_24\\demo.txt",true);
			fw.write(" is charactor table?");
			fw.close();
		}
		catch (IOException e) {
			sop(e.toString());
		}
		
	}

/**********************Println************************/
	private static void sop(Object obj)
	{
		System.out.println(obj);
	}
}
```



 练习：

  将F盘的一个文件复制到E盘。 

 思考：

  其实就是将F盘下的文件数据存储到D盘的一个文件中。

 步骤：

  1.在D盘创建一个文件，存储F盘中文件的数据。
  2.定义读取流和F：盘文件关联。
  3.通过不断读写完成数据存储。
  4.关闭资源。

 源码：

```java
import java.io.*;
import java.util.Scanner;

class CopyText {
	public static void main(String[] args) throws IOException {
		sop("请输入要拷贝的文件的路径:");
		Scanner in = new Scanner(System.in);
		String source = in.next();
		sop("请输入需要拷贝到那个位置的路径以及生成的文件名:");
		String destination = in.next();
		in.close();
		CopyTextDemo(source,destination);

	}

/*****************文件Copy*********************/
	private static void CopyTextDemo(String source,String destination) {

		try {
			FileWriter fw = new FileWriter(destination);
			FileReader fr = new FileReader(source);
			char []  buf = new char[1024]; 
			//将Denmo中的文件读取到buf数组中。
			int num = 0;	
			while((num = fr.read(buf))!=-1) {
                               //String(char[] value , int offest,int count) 分配一个新的String,包含从offest开始的count个字符
				fw.write(new String(buf,0,num));
			}
			fr.close();
			fw.close();
		}
		catch (IOException e) {
			sop(e.toString());
		}
	}

/**********************Println************************/
	private static void sop(Object obj) {
		System.out.println(obj);
	}
}
```

## 三、缓冲区

 ### 1. 字符流的缓冲区

BufferedReader和BufferedWreiter

* 缓冲区的出现时为了提高流的操作效率而出现的.
* 需要被提高效率的流作为参数传递给缓冲区的构造函数
* 在缓冲区中封装了一个数组，存入数据后一次取出

 BufferedReader示例：

  读取流缓冲区提供了一个一次读一行的方法readline，方便对文本数据的获取。
  readline()只返回回车符前面的字符，不返回回车符。如果是复制的话，必须加入newLine()，写入回车符

  newLine()是java提供的多平台换行符写入方法。

```java
import java.io.*;


class BufferedReaderDemo {
	public static void main(String[] args)  throws IOException {

		//创建一个字符读取流流对象，和文件关联
		FileReader rw = new FileReader("buf.txt");

		//只要将需要被提高效率的流作为参数传递给缓冲区的构造函数即可
		BufferedReader brw = new BufferedReader(rw);

		
		for(;;) {
			String s = brw.readLine();
			if(s==null) break;
			System.out.println(s);
		}
		
		brw.close();//关闭输入流对象

	}
}
```

 BufferedWriter示例：

```java
import java.io.*;


class BufferedWriterDemo {
	public static void main(String[] args)  throws IOException {

		//创建一个字符写入流对象
		FileWriter fw = new FileWriter("buf.txt");

		//为了提高字符写入效率，加入了缓冲技术。
		//只要将需要被提高效率的流作为参数传递给缓冲区的构造函数即可
		BufferedWriter bfw = new BufferedWriter(fw);

		//bfw.write("abc\r\nde");
		//bfw.newLine();               这行代码等价于bfw.write("\r\n"),相当于一个跨平台的换行符
		//用到缓冲区就必须要刷新
		for(int x = 1; x < 5; x++) {
			bfw.write("abc");
			bfw.newLine();					//java提供了一个跨平台的换行符newLine();
			bfw.flush();
		}



		bfw.flush();												//刷新缓冲区
		bfw.close();												//关闭缓冲区，但是必须要先刷新

		//注意，关闭缓冲区就是在关闭缓冲中的流对象
		fw.close();													//关闭输入流对象

	}
}
```



 ### 2.装饰设计模式

  装饰设计模式：：：：

  要求：自定义一些Reader类，读取不同的数据(装饰和继承的区别)

-   MyReader //专门用于读取数据的类

-   MyTextReader
- MyBufferTextReader
- MyMediaReader
- MyBufferMediaReader
- MyDataReader
- MyBufferDataReader

  如果将他们抽取出来，设计一个MyBufferReader，可以根据传入的类型进行增强

```java
  class MyBufferReader {
  MyBufferReader (MyTextReader text) {}
      MyBufferReader (MyMediaReader media) {}
      MyBufferReader (MyDataReader data) {}
  }
```


  但是上面的类拓展性很差。找到其参数的共同类型，通过多态的形式，可以提高拓展性

```java
  class MyBufferReader  extends MyReader{
      private MyReader r;                        //从继承变为了组成模式  装饰设计模式
      MyBufferReader(MyReader r) {}
  }
```

  优化后的体系：

- MyTextReader
- MyMediaReader
- MyDataReader
- MyBufferReader        //增强上面三个。装饰模式比继承灵活，

  ​                              避免继承体系的臃肿。降低类与类之间的耦合性
  装饰类只能增强已有的对象，具备的功能是相同的。所以装饰类和被装饰类属于同一个体系



  MyBuffereReader类：  自己写一个MyBuffereReader类，功能与BuffereReader相同

```java
class MyBufferedReader1  extends Reader{             
    private Reader r;
    MyBufferedReader1(Reader r){
        this.r  = r;
    }

    //一次读一行数据的方法
    public String myReaderline()  throws IOException {
        //定义一个临时容器，原BufferReader封装的是字符数组。
        //为了演示方便。定义一个StringBuilder容器。最终要将数据变成字符串
        StringBuilder sb = new StringBuilder();
        int ch = 0;
        while((ch = r.read()) != -1)
        {
            if(ch == '\r') 
                continue;
            if(ch == '\n')                    //遇到换行符\n,返回字符串
                return sb.toString();
            else
            sb.append((char)ch);
        }
        if(sb.length()!=0)                    //当最后一行不是以\n结束时候，这里需要判断
            return sb.toString();
        return null;
    }
    /*
    需要覆盖Reader中的抽象方法close()，read();
    */
    public void close()throws IOException {
        r.close();
    }

    public int read(char[] cbuf,int off, int len)throws IOException {   //覆盖read方法
        return r.read(cbuf,off,len);
    }

    public void myClose() throws IOException{
        r.close();
    }
}
```

## 四、字节流

参考： http://blog.csdn.net/qq_28261343/article/details/52678681

# Java 多线程
JAVA多线程实现的四种方式

[Java](http://lib.csdn.net/base/javaee)多线程实现方式主要有四种：继承Thread类、实现Runnable接口、实现Callable接口通过FutureTask包装器来创建Thread线程、使用ExecutorService、Callable、Future实现有返回结果的多线程。

其中前两种方式线程执行完后都没有返回值，后两种是带返回值的。


## 继承Thread类创建线程
Thread类本质上是实现了Runnable接口的一个实例，代表一个线程的实例。启动线程的唯一方法就是通过Thread类的start()实例方法。start()方法是一个native方法，它将启动一个新线程，并执行run()方法。这种方式实现多线程很简单，通过自己的类直接extend Thread，并复写run()方法，就可以启动新线程并执行自己定义的run()方法。例如：


```java
public class MyThread extends Thread {  
　　public void run() {  
　　 System.out.println("MyThread.run()");  
　　}  
}  
 
MyThread myThread1 = new MyThread();  
MyThread myThread2 = new MyThread();  
myThread1.start();  
myThread2.start();  
```


## 实现Runnable接口创建线程

如果自己的类已经extends另一个类，就无法直接extends Thread，此时，可以实现一个Runnable接口，如下：

```java
public class MyThread extends OtherClass implements Runnable {  
　　public void run() {  
　　 System.out.println("MyThread.run()");  
　　}  
}  
```

为了启动MyThread，需要首先实例化一个Thread，并传入自己的MyThread实例：

```java
MyThread myThread = new MyThread();  
Thread thread = new Thread(myThread);  
thread.start();  
```

事实上，当传入一个Runnable target参数给Thread后，Thread的run()方法就会调用target.run()，参考JDK源代码：

```java
public void run() {  
　　if (target != null) {  
　　 target.run();  
　　}  
}  
```

## 实现Callable接口通过FutureTask包装器来创建Thread线程

Callable接口（也只有一个方法）定义如下：   

```java
public interface Callable<V>   { 
  V call（） throws Exception;   
} 
public class SomeCallable<V> extends OtherClass implements Callable<V> {

    @Override
    public V call() throws Exception {
        // TODO Auto-generated method stub
        return null;
    }

}
Callable<V> oneCallable = new SomeCallable<V>();   
//由Callable<Integer>创建一个FutureTask<Integer>对象：   
FutureTask<V> oneTask = new FutureTask<V>(oneCallable);   
//注释：FutureTask<Integer>是一个包装器，它通过接受Callable<Integer>来创建，它同时实现了Future和Runnable接口。 
  //由FutureTask<Integer>创建一个Thread对象：   
Thread oneThread = new Thread(oneTask);   
oneThread.start();   
//至此，一个线程就创建完成了。
```


## 使用ExecutorService、Callable、Future实现有返回结果的线程

ExecutorService、Callable、Future三个接口实际上都是属于Executor框架。返回结果的线程是在JDK1.5中引入的新特征，有了这种特征就不需要再为了得到返回值而大费周折了。而且自己实现了也可能漏洞百出。

可返回值的任务必须实现Callable接口。类似的，无返回值的任务必须实现Runnable接口。

执行Callable任务后，可以获取一个Future的对象，在该对象上调用get就可以获取到Callable任务返回的Object了。

注意：get方法是阻塞的，即：线程无返回结果，get方法会一直等待。

再结合线程池接口ExecutorService就可以实现传说中有返回结果的多线程了。

下面提供了一个完整的有返回结果的多线程测试例子，在JDK1.5下验证过没问题可以直接使用。代码如下：



```java
import java.util.concurrent.*;  
import java.util.Date;  
import java.util.List;  
import java.util.ArrayList;  
  
/ 
* 有返回值的线程 
*/  
@SuppressWarnings("unchecked")  
public class Test {  
public static void main(String[] args) throws ExecutionException,  
    InterruptedException {  
   System.out.println("----程序开始运行----");  
   Date date1 = new Date();  
  
   int taskSize = 5;  
   // 创建一个线程池  
   ExecutorService pool = Executors.newFixedThreadPool(taskSize);  
   // 创建多个有返回值的任务  
   List<Future> list = new ArrayList<Future>();  
   for (int i = 0; i < taskSize; i++) {  
    Callable c = new MyCallable(i + " ");  
    // 执行任务并获取Future对象  
    Future f = pool.submit(c);  
    // System.out.println(">>>" + f.get().toString());  
    list.add(f);  
   }  
   // 关闭线程池  
   pool.shutdown();  
  
   // 获取所有并发任务的运行结果  
   for (Future f : list) {  
    // 从Future对象上获取任务的返回值，并输出到控制台  
    System.out.println(">>>" + f.get().toString());  
   }  
  
   Date date2 = new Date();  
   System.out.println("----程序结束运行----，程序运行时间【"  
     + (date2.getTime() - date1.getTime()) + "毫秒】");  
}  
}  
  
class MyCallable implements Callable<Object> {  
private String taskNum;  
  
MyCallable(String taskNum) {  
   this.taskNum = taskNum;  
}  
  
public Object call() throws Exception {  
   System.out.println(">>>" + taskNum + "任务启动");  
   Date dateTmp1 = new Date();  
   Thread.sleep(1000);  
   Date dateTmp2 = new Date();  
   long time = dateTmp2.getTime() - dateTmp1.getTime();  
   System.out.println(">>>" + taskNum + "任务终止");  
   return taskNum + "任务返回运行结果,当前任务时间【" + time + "毫秒】";  
}  
}  
```

代码说明：

- 上述代码中Executors类，提供了一系列工厂方法用于创建线程池，返回的线程池都实现了ExecutorService接口。

- public static ExecutorService newFixedThreadPool(int nThreads) 
- 创建固定数目线程的线程池。
- public static ExecutorService newCachedThreadPool() 
- 创建一个可缓存的线程池，调用execute 将重用以前构造的线程（如果线程可用）。如果现有线程没有可用的，则创建一个新线程并添加到池中。终止并从缓存中移除那些已有 60 秒钟未被使用的线程。
- public static ExecutorService newSingleThreadExecutor() 
- 创建一个单线程化的Executor。
- public static ScheduledExecutorService newScheduledThreadPool(int corePoolSize) 
- 创建一个支持定时及周期性的任务执行的线程池，多数情况下可用来替代Timer类。
- ExecutoreService提供了submit()方法，传递一个Callable，或Runnable，返回Future。如果Executor后台线程池还没有完成Callable的计算，这调用返回Future对象的get()方法，会阻塞直到计算完成。

软件-注重思想、逻

# Java反射机制详解

​     Java反射机制是在运行状态中，对于任意一个类，都能够知道这个类的所有属性和方法；对于任意一个对象，都能够调用它的任意一个方法和属性；这种动态获取的信息以及动态调用对象的方法的功能称为Java语言的反射机制。

## 1、关于Class

​    1、Class是一个类，一个描述类的类（也就是描述类本身），封装了描述方法的Method，描述字段的Filed，描述构造器的Constructor等属性
​    2、对象照镜子后（反射）可以得到的信息：某个类的数据成员名、方法和构造器、某个类到底实现了哪些接口。
​    3、对于每个类而言，JRE 都为其保留一个不变的 Class 类型的对象。
​        一个 Class 对象包含了特定某个类的有关信息。
​    4、Class 对象只能由系统建立对象
​    5、一个类在 JVM 中只会有一个Class实例

```java
package com.java.reflection;

public class Person {
    String name;
    private int age;

    public Person() {
        System.out.println("无参构造器");
    }

    public Person(String name, int age) {
        System.out.println("有参构造器");
        this.name = name;
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    @Override
    public String toString() {
        return "Person{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }
}
```



## 2、反射机制获取类有三种方法

```java

    /**
     * 反射机制获取类有三种方法
     */
    @Test
    public void testGetClass() throws ClassNotFoundException {
        Class clazz = null;

        //1 直接通过类名.Class的方式得到
        clazz = Person.class;
        System.out.println("通过类名: " + clazz);

        //2 通过对象的getClass()方法获取,这个使用的少（一般是传的是Object，不知道是什么类型的时候才用）
        Object obj = new Person();
        clazz = obj.getClass();
        System.out.println("通过getClass(): " + clazz);

        //3 通过全类名获取，用的比较多，但可能抛出ClassNotFoundException异常
        clazz = Class.forName("com.java.reflection.Person");
        System.out.println("通过全类名获取: " + clazz);
    }
```

- 通过类名: class com.java.reflection.Person
- 无参构造器
- 通过getClass(): class com.java.reflection.Person
- 通过全类名获取: class com.java.reflection.Person

## 3、利用newInstance创建对象：调用的类必须有无参的构造器

```java
/** 
 * Class类的newInstance()方法，创建类的一个对象。 
 */  
@Test  
public void testNewInstance()  
        throws ClassNotFoundException, IllegalAccessException, InstantiationException {  
  
    Class clazz = Class.forName("com.java.reflection.Person");  
  
    //使用Class类的newInstance()方法创建类的一个对象  
    //实际调用的类的那个 无参数的构造器（这就是为什么写的类的时候，要写一个无参数的构造器，就是给反射用的）  
    //一般的，一个类若声明了带参数的构造器，也要声明一个无参数的构造器  
    Object obj = clazz.newInstance();  
    System.out.println(obj);  
}  
```

- 无参构造器
  Person{name='null', age=0}

1. /** 
2.  * Class类的newInstance()方法，创建类的一个对象。 
3.  */  
4. @Test  
5. public void testNewInstance()  
6. ​        throws ClassNotFoundException, IllegalAccessException, InstantiationException {  
7.   ​
8. ​    Class clazz = Class.forName("com.java.reflection.Person");  
9.   ​
10. ​    //使用Class类的newInstance()方法创建类的一个对象  
11. ​    //实际调用的类的那个 无参数的构造器（这就是为什么写的类的时候，要写一个无参数的构造器，就是给反射用的）  
12. ​    //一般的，一个类若声明了带参数的构造器，也要声明一个无参数的构造器  
13. ​    Object obj = clazz.newInstance();  
14. ​    System.out.println(obj);  
15. }  

## 4、ClassLoader类加载器

类加载器详解：<http://blog.csdn.net/ochangwen/article/details/51473120>

```java
    /**
     * ClassLoader类装载器
     */
    @Test
    public void testClassLoader1() throws ClassNotFoundException, IOException {
        //1、获取一个系统的类加载器
        ClassLoader classLoader = ClassLoader.getSystemClassLoader();
        System.out.println("系统的类加载器-->" + classLoader);

        //2、获取系统类加载器的父类加载器(扩展类加载器（extensions classLoader）)
        classLoader = classLoader.getParent();
        System.out.println("扩展类加载器-->" + classLoader);

        //3、获取扩展类加载器的父类加载器
        //输出为Null,无法被Java程序直接引用
        classLoader = classLoader.getParent();
        System.out.println("启动类加载器-->" + classLoader);

        //

        //4、测试当前类由哪个类加载器进行加载 ,结果就是系统的类加载器
        classLoader = Class.forName("com.java.reflection.Person").getClassLoader();
        System.out.println("当前类由哪个类加载器进行加载-->"+classLoader);

        //5、测试JDK提供的Object类由哪个类加载器负责加载的
        classLoader = Class.forName("java.lang.Object").getClassLoader();
        System.out.println("JDK提供的Object类由哪个类加载器加载-->" + classLoader);
    }
```

- 系统的类加载器-->sun.misc.Launcher$AppClassLoader@43be2d65
- 扩展类加载器-->sun.misc.Launcher$ExtClassLoader@7a9664a1
- 启动类加载器-->null
- 当前类由哪个类加载器进行加载-->sun.misc.Launcher$AppClassLoader@43be2d65
- JDK提供的Object类由哪个类加载器加载-->null

#### getResourceAsStream方法

```java
 @Test  
    public void testGetResourceAsStream() throws ClassNotFoundException, IOException {  
//          这么写的话，文件需要放到src目录下  
        //       InputStream in = new FileInputStream("test.properties");  
  
        //5、关于类加载器的一个主要方法  
        //调用getResourceAsStream 获取类路径下的文件对应的输入流  
        InputStream in = this.getClass().getClassLoader()  
                .getResourceAsStream("com/java/reflection/test.properties");  
        System.out.println("in: " +in);  
  
        Properties properties = new Properties();  
        properties.load(in);  
  
        String driverClass = properties.getProperty("dirver");  
        String jdbcUrl = properties.getProperty("jdbcUrl");  
        //中文可能会出现乱码，需要转换一下  
        String user = new String(properties.getProperty("user").getBytes("ISO-8859-1"), "UTF-8");  
        String password = properties.getProperty("password");  
  
        System.out.println("diverClass: "+driverClass);  
        System.out.println("user: " + user);  
    }  
```



## 5、Method: 对应类中的方法

```java
public class Person {
    private String name;
    private int age;

    //新增一个私有方法
    private void privateMthod(){
    }
    
    public Person() {
        System.out.println("无参构造器");
    }

    public Person(String name, int age) {
        System.out.println("有参构造器");
        this.name = name;
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    /**
     * 
     * @param age  类型用Integer，不用int
     */
    public void setName(String name , int age){
        System.out.println("name: " + name);
        System.out.println("age:"+ age);

    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    @Override
    public String toString() {
        return "Person{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }
}
```

```java
    @Test
    public void testMethod() throws ClassNotFoundException, NoSuchMethodException, 
            IllegalAccessException, InstantiationException, InvocationTargetException {
        Class clazz = Class.forName("com.java.reflection.Person");

        //1、得到clazz 对应的类中有哪些方法,不能获取private方法
        Method[] methods =clazz.getMethods();
        System.out.print("        getMethods: ");
        for (Method method : methods){
            System.out.print(method.getName() + ", ");
        }

        //2、获取所有的方法(且只获取当着类声明的方法，包括private方法）
        Method[] methods2 = clazz.getDeclaredMethods();
        System.out.print("\ngetDeclaredMethods: ");
        for (Method method : methods2){
            System.out.print(method.getName() + ", ");
        }

        //3、获取指定的方法
        Method method = clazz.getDeclaredMethod("setName",String.class);//第一个参数是方法名，后面的是方法里的参数
        System.out.println("\nmethod : " + method);

        Method method2 = clazz.getDeclaredMethod("setName",String.class ,int.class);//第一个参数是方法名，后面的是方法里的参数
        System.out.println("method2: " + method2);

        //4、执行方法！
        Object obj = clazz.newInstance();
        method2.invoke(obj, "changwen", 22);
    }
```

>   getMethods: toString, getName, setName, setName, setAge, getAge, wait, wait, wait, equals, hashCode, getClass, notify, notifyAll,
> getDeclaredMethods: toString, getName, setName, setName, setAge, getAge, privateMthod,
> method : public void com.java.reflection.Person.setName(java.lang.String)
> method2: public void com.java.reflection.Person.setName(java.lang.String,int)
> 无参构造器
> name: changwen
> age:22

