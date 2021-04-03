---
title: Java对象复制Clone技术
tags: Java
category: Java
date: 2018-03-15 16:44:36
---
![image](http://ovi3ob9p4.bkt.clouddn.com/TIETU/CT0153.jpg)

在编码过程经常会碰到将一个对象传递给另一个对象，java中对于基本型变量采用的是值传递，而对于对象比如bean传递时采用的是引用传递也就是地址传递，而很多时候对于对象传递我们也希望能够象值传递一样，使得传递之前和之后有不同的内存地址，在这种情况下就可以clone一个新的对象来用.
<!--more-->
### 什么时候使用克隆对象技术

如：有一个对象A，在某一时刻A中已经包含了一些有效值，A是用来保存从数据库得到很多数据的一个对象。此时可能会需要一个和A完全相同新对象B，B用来修改A里面的某一个属性的值，仅仅是用来负责显示一下，不需要使得A.也受到影响。并且此函数的返回类型是A类型的。并且此后对B任何改动都不会影响到A中的值，也就是说，A与B是两个独立的对象，但B的初始值是由A对象确定的。在Java语言中，用简单的赋值语句是不能满足这种需求的。实现clone（）方法是不错的选择。

Java的所有类都默认继承java.lang.Object类，在java.lang.Object类中有一个方法clone()。该方法将返回Object对象的一个拷贝。要说明的有两点：

- 一是拷贝对象返回的是一个新对象，而不是一个引用。
- 二是拷贝对象与用  new 操作符返回的新对象的区别就是这个拷贝已经包含了一些原来对象的信息，而不是对象的初始信息。

怎样应用clone()方法？

#### 实现clone方法的步骤（）

1. 实现Cloneable接口

2. 重载Object类中的clone()方法，重载时需定义为public

3. 在重载方法中，调用`super.clone()`


```java
class CloneClass implements Cloneable{
    public int aInt;
    public Object clone(){
        CloneClass o = null;
        try{
            o = (CloneClass)super.clone();
        }catch(CloneNotSupportedException e){
            e.printStackTrace();
        }
        return o;
    }
｝
```

### 浅克隆与深克隆（影子克隆）

​       克隆(默认就是浅克隆)就是复制一个对象的复本.若只需要复制对象的字段值（对于基本数据类型,如:int,long,float等，则复制值；对于复合数据类型仅复制该字段值，如数组变量则复制地址，对于对象变量则复制对象的reference。

下面的例子包含三个类UnCloneA，CloneB，CloneMain。

CloneB类包含了一个UnCloneA的实例和一个int类型变量，并且重载clone()方法。

CloneMain类初始化CloneB 类的一个实例b1，然后调用clone()方法生成了一个b1的拷贝b2。最后考察一下b1和b2的输出：

```java
package clone;
class UnCloneA {
    private int i;
    public UnCloneA(int ii) { 
        i = ii; 
    }
    public void doubleValue() { 
        i *= 2; 
    }
    public String toString() {
        return Integer.toString(i);
    }
}
class CloneB implements Cloneable{
    public int aInt;
    public UnCloneA unCA = new UnCloneA(111);
    publicCloneB clone(){
        CloneB o = null;
        try{
            o = (CloneB)super.clone();
        }catch(CloneNotSupportedException e){
            e.printStackTrace();
        }
        return o;
    }
}
public class CloneMain {
    public static void main(String[] a){
        CloneB b1 = new CloneB();
        b1.aInt = 11;
        System.out.println("before clone,b1.aInt = "+ b1.aInt);
        System.out.println("before clone,b1.unCA = "+ b1.unCA);
        CloneB b2 = (CloneB)b1.clone();
        b2.aInt = 22;
        b2.unCA.doubleValue();
        System.out.println("=================================");
        System.out.println("after clone,b1.aInt = "+ b1.aInt);
        System.out.println("after clone,b1.unCA = "+ b1.unCA);
        System.out.println("=================================");
        System.out.println("after clone,b2.aInt = "+ b2.aInt);
        System.out.println("after clone,b2.unCA = "+ b2.unCA);
    }
}
```

```java
/ RUN RESULT:
before clone,b1.aInt = 11
before clone,b1.unCA = 111
after clone,b1.aInt = 11
after clone,b1.unCA = 222
after clone,b2.aInt = 22
after clone,b2.unCA = 222
*/
```

输出的结果说明int类型的变量aInt和UnCloneA的实例对象unCA的clone结果不一致，

int类型是真正的被clone了，因为改变了b2中的aInt变量，对b1的aInt没有产生影响，

也就是说，b2.aInt与b1.aInt已经占据了不同的内存空间，b2.aInt是b1.aInt的一个真正拷贝。

相反，对b2.unCA的改变同时改变了b1.unCA，很明显，b2.unCA和b1.unCA是仅仅指向同一个对象的不同引用！（也就是对对象中的对象克隆失败）

从中可以看出，调用Object类中clone()方法产生的效果是：先在内存中开辟一块和原始对象一样的空间，然后原样拷贝原始对象中的内容。

```java
public Channel copy(Integer cid,String solution, String mobileSolution, Integer siteId,
			Map<String, String> pathMap){
		Channel c=findById(cid);//旧
		Channel channel=new Channel();//新
		if(c!=null){
			ChannelExt ext=new ChannelExt();
			ChannelTxt channelTxt=new ChannelTxt();
			channel=(Channel) c.clone();
			ext=(ChannelExt) c.getChannelExt().clone();
			if(c.getChannelTxt()!=null){
				channelTxt=(ChannelTxt) c.getChannelTxt().clone();
			}
```

对基本数据类型，这样的操作是没有问题的，但对非基本类型变量，我们知道它们保存的仅仅是对象的引用，这也导致clone后的非基本类型变量和原始对象中相应的变量指向的是同一个对象。也就没有达到克隆的效果。

大多时候，这种clone的结果往往不是我们所希望的结果，这种clone也被称为"影子clone"。要想让b2.unCA指向与b2.unCA不同的对象，而且b2.unCA中还要包含b1.unCA中的信息作为初始信息，就要实现深度clone。

### 怎么进行深度clone？

深克隆与浅克隆的区别在于对复合数据类型的复制。若对象中的某个字段为复合类型，在克隆对象的时候，需要为该字段重新创建一个对象。

把上面的例子改成深度clone很简单，需要两个改变：一是让UnCloneA类也实现和CloneB类一样的clone功能（实现Cloneable接口，重载clone()方法）。二是在CloneB的clone()方法中加入一句`o.unCA = (UnCloneA)unCA.clone()`

程序如下：

```java
class UnCloneA implements Cloneable{
    private int i;
    public UnCloneA(int ii) { i = ii; }
    public void doubleValue() { i *= 2; }
    public String toString() {
        return Integer.toString(i);
    }
    public Object clone(){
        UnCloneA o = null;
        try{
            o = (UnCloneA)super.clone();
        }catch(CloneNotSupportedException e){
            e.printStackTrace();
        }
        return o;
    }
}

class CloneB implements Cloneable{
    public int aInt;
    public UnCloneA unCA = new UnCloneA(111);
    public Object clone(){
        CloneB o = null;
        try{
            o = (CloneB)super.clone();
        }catch(CloneNotSupportedException e){
            e.printStackTrace();
        }
        o.unCA = (UnCloneA)unCA.clone();
        return o;
    }
}
public class CloneMain {
    public static void main(String[] a){
        CloneB b1 = new CloneB();
        b1.aInt = 11;
        System.out.println("before clone,b1.aInt = "+ b1.aInt);
        System.out.println("before clone,b1.unCA = "+ b1.unCA);
        CloneB b2 = (CloneB)b1.clone();
        b2.aInt = 22;
        b2.unCA.doubleValue();
        System.out.println("=========");
        System.out.println("after clone,b1.aInt = "+ b1.aInt);
        System.out.println("after clone,b1.unCA = "+ b1.unCA);
        System.out.println("=========");
        System.out.println("after clone,b2.aInt = "+ b2.aInt);
        System.out.println("after clone,b2.unCA = "+ b2.unCA);
    }
}
```

```java
/ RUN RESULT:
before clone,b1.aInt = 11
before clone,b1.unCA = 111
after clone,b1.aInt = 11
after clone,b1.unCA = 111
after clone,b2.aInt = 22
after clone,b2.unCA = 222
*/
```

可以看出，现在b2.unCA的改变对b1.unCA没有产生影响。此时b1.unCA与b2.unCA指向了两个不同的UnCloneA实例，而且在CloneB b2 = (CloneB)b1.clone();调用的那一刻b1和b2拥有相同的值，在这里，b1.i = b2.i = 11。

### 注意

不是所有的类都能实现深度clone的。例如，如果把上面的CloneB类中的UnCloneA类型变量改成StringBuffer类型，看一下JDK API中关于StringBuffer的说明，StringBuffer没有重载clone()方法，更为严重的是StringBuffer还是一个final类，这也是说我们也不能用继承的办法间接实现StringBuffer的clone。如果一个类中包含有StringBuffer类型对象或和StringBuffer相似类的对象，我们有两种选择：要么只能实现影子clone，要么就在类的clone()方法中加一句（假设是SringBuffer对象，而且变量名仍是unCA）：

 `o.unCA = new StringBuffer(unCA.toString()); //原来的是：o.unCA = (UnCloneA)unCA.clone();`

还要知道的是除了基本数据类型能自动实现深度clone以外，String对象是一个例外，它clone后的表现好象也实现了深度clone，虽然这只是一个假象，但却大大方便了我们的编程。
通过以上我们可以看出在某些情况下，我们可以利用clone方法来实现对象只见的复制，但对于比较复杂的对象（比如对象中包含其他对象，其他对象又包含别的对象.....）这样我们必须进行层层深度clone，每个对象需要实现cloneable接口。

### Java集合的深克隆

下面例子有一个`Employee`集合，Employee是可变对象，成员变量`name`和`designation`。它们存储在`HashSet`中。使用`java.util.Collection`接口的`addAll()`方法创建集合拷贝。然后修改存储在原始集合每个`Employee`对象的`designation`值。理想情况下这个改变不会影响克隆集合，因为克隆集合和原始集合应该相互独立，但是克隆集合也被改变了。修正这个问题的方法是对存储在`Collection`类中的元素深克隆。

```java
import java.util.Collection; 
import java.util.HashSet;
import java.util.Iterator;
import org.slf4j.Logger; 
import org.slf4j.LoggerFactory;
 
/** 
* Java program to demonstrate copy constructor of Collection provides shallow 
* copy and techniques to deep clone Collection by iterating over them. 
* @author http://javarevisited.blogspot.com 
*/
public class CollectionCloningTest { 
    private static final Logger logger = LoggerFactory.getLogger(CollectionCloningclass); 
    public static void main(String args[]) { 
        // deep cloning Collection in Java 
        Collection<Employee> org = new HashSet<>(); 
        org.add(new Employee("Joe", "Manager")); 
        org.add(new Employee("Tim", "Developer")); 
        org.add(new Employee("Frank", "Developer")); 
 
        // creating copy of Collection using copy constructor 
        Collection<Employee> copy = new HashSet<>(org);
 
        logger.debug("Original Collection {}", org); 
        logger.debug("Copy of Collection {}", copy );
         
        Iterator<Employee> itr = org.iterator(); 
        while(itr.hasNext()){ 
            itr.next().setDesignation("staff"); 
        } 
 
        logger.debug("Original Collection after modification {}", org); 
        logger.debug("Copy of Collection without modification {}", copy ); 
 
        // deep Cloning List in Java 
 
    } 
}
 
class Employee { 
    private String name; 
    private String designation; 
     
    public Employee(String name, String designation) { 
        this.name = name; 
        this.designation = designation; 
    } 
     
    public String getDesignation() { 
        return designation; 
    } 
 
    public void setDesignation(String designation) { 
        this.designation = designation; 
    } 
 
    public String getName() { 
        return name; 
    } 
 
    public void setName(String name) { 
        this.name = name; 
    } 
 
    @Override
    public String toString() { 
        return String.format("%s: %s", name, designation ); 
    } 
}
```

输出：

```java
- Original Collection [Joe: Manager, Frank: Developer, Tim: Developer] 
- Copy of Collection [Joe: Manager, Frank: Developer, Tim: Developer] 
- Original Collection after modification [Joe: staff, Frank: staff, Tim: staff] 
- Copy of Collection without modification [Joe: staff, Frank: staff, Tim: staff]
```

可以看到改变原始`Collection`中`Employee`对象(改变designation为”`staff`“)在克隆集合中也有所反映，因为克隆是浅拷贝，指向堆中相同的`Employee`对象。为了修正这个问题，需要遍历集合，深克隆`Employee`对象，在这之前，要重写`Employee`对象的clone方法。

1. `Employee`实现`Cloneable`接口

2. 为`Employee`类增加下面的`clone()`方法

   ```java
   @Override
       protected Employee clone() { 
           Employee clone = null; 
           try{ 
               clone = (Employee) super.clone(); 
    
           }catch(CloneNotSupportedException e){ 
               throw new RuntimeException(e); // won't happen 
           }
             
           return clone; 
       }
   ```

3. 不使用拷贝构造函数，使用下面的代码来深拷贝集合

   ```java
   Collection<Employee> copy = new HashSet<Employee>(org.size()); 
    
   Iterator<Employee> iterator = org.iterator(); 
   while(iterator.hasNext()){ 
       copy.add(iterator.next().clone()); 
   }
   ```

4. 运行相同的代码更改原始集合，克隆集合不会也被更改。

   ```

   ```

可以看到克隆集合和原始集合相互独立，它们指向不同的对象。
[![Shallow vs Deep Clone Java](http://incdn1.b0.upaiyun.com/2014/04/8de7420211451f17a303eda3a5cb6710.png)](http://www.importnew.com/?attachment_id=10852)

这就是**Java中如何克隆集合**的内容。现在我们知道拷贝构造函数或者`List`或`Set`等各种集合类的`addAll()`方法仅仅创建了*集合的浅拷贝*，而且原始集合和克隆集合指向相同的对象。为避免这个问题，应该深克隆集合，遍历集合克隆每个元素。尽管这要求集合中的对象必须支持深克隆操作。

后面这些话是比较重要的：(易于理解)

各个集合类提供的拷贝构造函数作为克隆`List`，`Set`，`ArrayList`，`HashSet`或者其他集合实现的方法。需要记住的是，Java集合的拷贝构造函数只提供浅拷贝而不是深拷贝，这意味着存储在原始List和克隆List中的对象是相同的，指向Java堆内存中相同的位置。

### 简单注意

1.  在克隆java对象的时候不会调用构造器
2.  java提供一种叫浅拷贝（shallow copy）的默认方式实现clone，创建好对象的副本后然后通过赋值拷贝内容，意味着如果你的类包含引用类型，那么原始对象和克隆都将指向相同的引用内容，这是很危险的，因为发生在可变的字段上任何改变将反应到他们所引用的共同内容上。为了避免这种情况，需要对引用的内容进行深度克隆。
3.  克隆方法用于创建对象的拷贝，为了使用clone方法，类必须实现`java.lang.Cloneable`接口`重写protected方法clone`，如果没有实现Clonebale接口会抛出`CloneNotSupportedException.`

### Hibernate异常Found shared references的解决办法

- 在项目中的续签模块中，由于在谈判成功后要新建一份合同，并要将原合同中的站点信息设置到新合同中去，在合同的Entity中有一个list<站点>来关联的，一份合同可以有多少站点！ 

- 在新增合同的代码如下： 

```java
RenewalItem item = renewalItemDao.findById(id);  
PropertyContract pc = item.getPropertyContract();//旧合同  
PropertyContract newPc = new PropertyContract();//新合同  
List<BSProperty> bsList = pc.getBsProperty();           
if(null!=bsList && !bsList.isEmpty()){  
newPc.setBsProperty(bsList);//将旧合同中的关联站点设置到新合同中去。  
}  
....  
renewalItemDao.merge(item); 
```

此时会报：`org.hibernate.HibernateException: Found shared references to a collection`，这样的异常信息，意思是指发现共享引用集合，经过上网GOOGLE,有一帖子说： 

解决方法：  

1. **在拷贝后，新建一个集合，将原来的集合元素添加进去，并赋值给新拷贝的实体** 

```java
RenewalItem item = renewalItemDao.findById(id);  
PropertyContract pc = item.getPropertyContract();//旧合同  
PropertyContract newPc = new PropertyContract();//新合同  
List<BSProperty> bsList = pc.getBsProperty();           
if(null!=bsList && !bsList.isEmpty()){  
List<BSProperty> newBsList = new ArrayList<BSProperty>(bsList.size());  
for(BSProperty bs : bsList){  
  newBsList.add(bs);  
}  
newPc.setBsProperty(newBsList);//将旧合同中的关联站点设置到新合同中去。  
}  
....  
renewalItemDao.merge(item);  
```
