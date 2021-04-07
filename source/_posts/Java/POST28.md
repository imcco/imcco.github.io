---
title: Java知识梳理（二）——算法与编程
category:
  - Java
copyright: true
tags:
  - Java
  - 单例模式
  - 排序算法
abbrlink: 44611
date: 2017-09-05 19:02:20
---
![image](http://ovi3ob9p4.bkt.clouddn.com/TIETU/CT0010.jpg)

Java算法编程。java排序算法，经典问题算法，单例模式

<!--more-->	

##### 编写一个程序，将a.txt文件中的单词与b.txt文件中的单词交替合并到c.txt文件中，a.txt文件中的单词用回车符分隔，b.txt文件中用回车或空格进行分隔。

答：

```java
import java.io.File;
import java.io.FileReader;
import java.io.FileWriter;

public class MainClass {
	public static void main(String[] args) throws Exception {
		FileManager a = new FileManager("a.txt", new char[] { '\n' });
		FileManager b = new FileManager("b.txt", new char[] { '\n', ' ' });
		FileWriter c = new FileWriter("c.txt");
		String aWord = null;
		String bWord = null;
		while ((aWord = a.nextWord()) != null) {
			c.write(aWord + "\n");
			bWord = b.nextWord();
			if (bWord != null)
				c.write(bWord + "\n");
		}
		while ((bWord = b.nextWord()) != null) {
			c.write(bWord + "\n");
		}
		c.close();
	}
}

class FileManager {
	String[] words = null;
	int pos = 0;

	public FileManager(String filename, char[] seperators) throws Exception {
		File f = new File(filename);
		FileReader reader = new FileReader(f);
		char[] buf = new char[(int) f.length()];
		int len = reader.read(buf);
		String results = new String(buf, 0, len);
		String regex = null;
		if (seperators.length > 1) {
			regex = "" + seperators[0] + "|" + seperators[1];
		} else {
			regex = "" + seperators[0];
		}
		words = results.split(regex);
	}

	public String nextWord() {
		if (pos == words.length)
			return null;
		return words[pos++];
	}
}
```

##### 编写一个程序，将d:\java目录下的所有.java文件复制到d:\jad目录下，并将原来文件的扩展名从.java改为.jad。

（大家正在做上面这道题，网上迟到的朋友也请做做这道题，找工作必须能编写这些简单问题的代码！）

答：listFiles方法接受一个FileFilter对象，这个FileFilter对象就是过虑的策略对象，不同的人提供不同的FileFilter实现，即提供了不同的过滤策略。

```java
import java.io.File;
import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.io.FilenameFilter;
import java.io.InputStream;
import java.io.OutputStream;

public class Jad2Java {
	public static void main(String[] args) throws Exception {
		File srcDir = new File("java");
		if (!(srcDir.exists() && srcDir.isDirectory()))
			throw new Exception("目录不存在");
		File[] files = srcDir.listFiles(new FilenameFilter() {
			public boolean accept(File dir, String name) {
				return name.endsWith(".java");
			}
		});
		System.out.println(files.length);
		File destDir = new File("jad");
		if (!destDir.exists())
			destDir.mkdir();
		for (File f : files) {
			FileInputStream fis = new FileInputStream(f);
			String destFileName = f.getName().replaceAll(".java", ".jad");
			FileOutputStream fos = new FileOutputStream(new File(destDir, destFileName));
			copy(fis, fos);
			fis.close();
			fos.close();
		}
	}

	private static void copy(InputStream ips, OutputStream ops) throws Exception {
		int len = 0;
		byte[] buf = new byte[1024];
		while ((len = ips.read(buf)) != -1) {
			ops.write(buf, 0, len);
		}
	}
}
```

由本题总结的思想及策略模式的解析：

```java
import java.io.File;
import java.io.FileFilter;
import java.util.Arrays;
class jad2java1{
//得到某个目录下的所有的java文件集合
//1.1得到目录 
	File srcDir =new File("d:java");
//1.2 得到目录下的所有java文件：
	File[] files =srcDir.listFiles(new MyFileFilter());
//1.3 只想得到.java的文件： 
	class MyFileFilter implements FileFilter{
		public boolean accept(File pathname){
		return pathname.getName().endsWith(".java");
	}
}
//2.将每个文件复制到另外一个目录，并改扩展名
//2.1 得到目标目录，如果目标目录不存在，则创建之
//2.2 根据源文件名得到目标文件名，注意要用正则表达式，注意.的转义。
//2.3 根据表示目录的File和目标文件名的字符串，得到表示目标文件的File。
	//要在硬盘中准确地创建出一个文件，需要知道文件名和文件的目录。
//2.4 将源文件的流拷贝成目标文件流，拷贝方法独立成为一个方法，方法的参数采用抽象流的形式。
	//方法接受的参数类型尽量面向父类，越抽象越好，这样适应面更宽广。
}
//分析listFiles方法内部的策略模式实现原理
File[] listFiles(FileFilter filter){
File[] files = listFiles(filter);
//Arraylist acceptedFilesList = newArrayList();
File[] acceptedFiles = new File[files.length];
int pos = 0;
for(File file: files){
boolean accepted = filter.accept(file);
if(accepted){
//acceptedFilesList.add(file);
acceptedFiles[pos++] = file;
	}
}
Arrays.copyOf(acceptedFiles,pos);
//return (File[])accpetedFilesList.toArray();
	}
}
```

##### 编写一个截取字符串的函数，输入为一个字符串和字节数，输出为按字节截取的字符串，但要保证汉字不被截取半个，如“我ABC”，4，应该截取“我AB”，输入“我ABC汉DEF”，6，应该输出“我ABC”，而不是“我ABC+汉的半个”。

答：

首先要了解中文字符有多种编码及各种编码的特征。

​    假设n为要截取的字节数。

```java
public class TrimGBK {
	public static void main(String[] args) throws Exception {
		String str = "我a爱中华abc我爱传智def";
		int num = trimGBK(str.getBytes("GBK"), 5);
		System.out.println(str.substring(0, num));
	}

	public static int trimGBK(byte[] buf, int n) {
		int num = 0;
		boolean bChineseFirstHalf = false;
		for (int i = 0; i < n; i++) {
			if (buf[i] < 0 && !bChineseFirstHalf) {
				bChineseFirstHalf = true;
			} else {
				num++;
				bChineseFirstHalf = false;
			}
		}
		return num;
	}
}
```

##### 有一个字符串，其中包含中文字符、英文字符和数字字符，请统计和打印出各个字符的个数。

答：如果一串字符如"aaaabbc中国1512"要分别统计英文字符的数量，中文字符的数量，和数字字符的数量，假设字符中没有中文字符、英文字符、数字字符之外的其他特殊字符。

```java
public class CountABC {
	public static void main(String[] args) {
		String str = "aaaabbc中国1512";
		int engishCount = 0;
		int chineseCount = 0;
		int digitCount = 0;
		for (int i = 0; i < str.length(); i++) {
			char ch = str.charAt(i);
			if (ch >= '0' && ch <= '9') {
				digitCount++;
			} else if ((ch >= 'a' && ch <= 'z') || (ch >= 'A' && ch <= 'Z')) {
				engishCount++;
			} else {
				chineseCount++;
			}
		}
		System.out.println("英文字符的数量" + engishCount + ",中文字符的数量" + chineseCount + "，和数字字符的数量"
				+ digitCount);
	}
}
```

##### 说明生活中遇到的二叉树，用java实现二叉树

这是组合设计模式。

我有很多个(假设10万个)数据要保存起来，以后还需要从保存的这些数据中检索是否存在某个数据，（我想说出二叉树的好处，该怎么说呢？那就是说别人的缺点），假如存在数组中，那么，碰巧要找的数字位于99999那个地方，那查找的速度将很慢，因为要从第1个依次往后取，取出来后进行比较。平衡二叉树（构建平衡二叉树需要先排序，我们这里就不作考虑了）可以很好地解决这个问题，但二叉树的遍历（前序，中序，后序）效率要比数组低很多。

代码如下：

```java
public class Node {
	public int value;
	public Node left;
	public Node right;

	public void store(int value) {
		if (value < this.value) {
			if (left == null) {
				left = new Node();
				left.value = value;
			} else {
				left.store(value);
			}
		} else if (value > this.value)

		{
			if (right == null) {
				right = new Node();
				right.value = value;
			} else {
				right.store(value);
			}
		}
	}

	public boolean find(int value) {
		System.out.println("happen" + this.value);
		if (value == this.value) {
			return true;
		} else if (value > this.value) {
			if (right == null)
				return false;
			return right.find(value);
		} else {
			if (left == null)
				return false;
			return left.find(value);
		}
	}

	public void preList() {
		System.out.print(this.value + ",");
		if (left != null)
			left.preList();
		if (right != null)
			right.preList();
	}

	public void middleList() {
		if (left != null)
			left.preList();
		System.out.print(this.value + ",");
		if (right != null)
			right.preList();
	}

	public void afterList() {
		if (left != null)
			left.preList();
		if (right != null)
			right.preList();
		System.out.print(this.value + ",");
	}

	public static void main(String[] args) {
		int[] data = new int[20];
		for (int i = 0; i < data.length; i++) {
			data[i] = (int) (Math.random() * 100) + 1;
			System.out.print(data[i] + ",");
		}
		System.out.println();
		Node root = new Node();
		root.value = data[0];
		for (int i = 1; i < data.length; i++) {
			root.store(data[i]);
		}
		root.find(data[19]);
		root.preList();
		System.out.println();
		root.middleList();
		System.out.println();
		root.afterList();
	}
}
```

##### 从类似如下的文本文件中读取出所有的姓名，并打印出重复的姓名和重复的次数，并按重复次数排序：

1,张三,28  2,李四,35  3,张三,28  4,王五,35  5,张三,28  6,李四,35  7,赵六,28  8,田七,35

程序代码如下:

```java
/*
 * 从类似如下的文本文件中读取出所有的姓名，并打印出重复的
 * 姓名和重复的次数，并按重复次数排序：
 */

import java.util.ArrayList;
import java.util.List;
import java.util.Map;
import java.util.TreeMap;

public class CopyOfGetNameTest {
	public static void main(String[] args) {
		Map<String, Integer> results = new TreeMap<String, Integer>();
		List<User> tm = new ArrayList<User>();
		tm.add(new User("张三", 28));
		tm.add(new User("李四", 35));
		tm.add(new User("张三", 28));
		tm.add(new User("王五", 35));
		tm.add(new User("张三", 28));
		tm.add(new User("李四", 35));
		tm.add(new User("赵六", 28));
		tm.add(new User("田七", 35));

		for (User x : tm) {
			dealLine(x, results);
		}
		for (Map.Entry<String, Integer> m : results.entrySet()) {
			System.out.println(m);
		}
	}

	private static void dealLine(User x, Map<String, Integer> map) {
		String name = x.getName();
		Integer value = (Integer) map.get(name);
		if (value == null) {
			value = 0;
		}
		map.put(name, value + 1);
	}

	public static void dealLine(String line, Map<String, Integer> map) {
		if (!"".equals(line.trim())) {
			String[] results = line.split(",");
			if (results.length == 3) {
				String name = results[1];
				Integer value = (Integer) map.get(name);
				if (value == null) {
					value = 0;
				}
				map.put(name, value + 1);
			}

		}
	}
}

class User implements Comparable<User> {
	public String name;
	public Integer value;

	public User(String name, Integer value) {
		this.name = name;
		this.value = value;
	}

	public String getName() {
		return name;
	}

	public Integer getAge() {
		return value;
	}

	@Override
	public String toString() {
		return "name = " + name + "   value = " + value;
	}

	@Override
	public boolean equals(Object obj) {
		if (this == obj) {
			return true;
		}
		if (obj instanceof User) {
			User user = (User) obj;
			if ((this.name.equals(user.name)) && (this.value == user.value)) {
				return true;
			}
		}
		return false;
	}

	public int compareTo(User user) {
		int num = 0;
		int i = this.name.compareTo(user.name);
		if (i > 0) {
			num = 1;
		} else if (i < 0) {
			num = -1;
		} else {
			num = this.value > user.value ? 1 : (this.value == user.value ? 0 : -1);
		}
		return num;
	}
}
```

##### 单实例Singleton设计模式

单实例Singleton设计模式可能是被讨论和使用的最广泛的一个设计模式了，这可能也是面试中问得最多的一个设计模式了。这个设计模式主要目的是想在整个系统中只能出现一个类的实例。这样做当然是有必然的，比如你的软件的全局配置信息，或者是一个Factory，或是一个主控类，等等。你希望这个类在整个系统中只能出现一个实例。当然，作为一个技术负责人的你，你当然有权利通过使用非技术的手段来达到你的目的。比如：你在团队内部明文规定，“XX类只能有一个全局实例，如果某人使用两次以上，那么该人将被处于2000元的罚款！”（呵呵），你当然有权这么做。但是如果你的设计的是东西是一个类库，或是一个需要提供给用户使用的API，恐怕你的这项规定将会失效。因为，你无权要求别人会那么做。所以，这就是为什么，我们希望通过使用技术的手段来达成这样一个目的的原因。

本文会带着你深入整个Singleton的世界，当然，我会放弃使用C++语言而改用Java语言，因为使用Java这个语言可能更容易让我说明一些事情。

#### **Singleton的教学版本**

这里，我将直接给出一个Singleton的简单实现，因为我相信你已经有这方面的一些基础了。我们姑且把这个版本叫做1.0版

```java
// version 1.0
public class Singleton {
    private static Singleton singleton = null;
    private Singleton() {  }
    public static Singleton getInstance() {
        if (singleton== null) {
            singleton= new Singleton();
        }
        return singleton;
    }
}
```

在上面的实例中，我想说明下面几个Singleton的特点：（下面这些东西可能是尽人皆知的，没有什么新鲜的）

1. 私有（private）的构造函数，表明这个类是不可能形成实例了。这主要是怕这个类会有多个实例。
2. 即然这个类是不可能形成实例，那么，我们需要一个静态的方式让其形成实例：getInstance()。注意这个方法是在new自己，因为其可以访问私有的构造函数，所以他是可以保证实例被创建出来的。
3. 在getInstance()中，先做判断是否已形成实例，如果已形成则直接返回，否则创建实例。
4. 所形成的实例保存在自己类中的私有成员中。
5. 我们取实例时，只需要使用Singleton.getInstance()就行了。

当然，如果你觉得知道了上面这些事情后就学成了，那得给你当头棒喝一下了，事情远远没有那么简单。

#### **Singleton的实际版本**

上面的这个程序存在比较严重的问题，因为是全局性的实例，所以，在多线程情况下，所有的全局共享的东西都会变得非常的危险，这个也一样，在多线程情况下，如果多个线程同时调用getInstance()的话，那么，可能会有多个进程同时通过 (singleton== null)的条件检查，于是，多个实例就创建出来，并且很可能造成内存泄露问题。嗯，熟悉多线程的你一定会说——“我们需要线程互斥或同步”，没错，我们需要这个事情，于是我们的Singleton升级成1.1版，如下所示：

```java
// version 1.1
public class Singleton
{
    private static Singleton singleton = null;
    private Singleton() {  }
    public static Singleton getInstance() {
        if (singleton== null) {
            synchronized (Singleton.class) {
                singleton= new Singleton();
            }
        }
        return singleton;
    }
}
```

嗯，使用了Java的synchronized方法，看起来不错哦。应该没有问题了吧？！错！这还是有问题！为什么呢？前面已经说过，如果有多个线程同时通过(singleton== null)的条件检查（因为他们并行运行），虽然我们的synchronized方法会帮助我们同步所有的线程，让我们并行线程变成串行的一个一个去new，那不还是一样的吗？同样会出现很多实例。嗯，确实如此！看来，还得把那个判断(singleton== null)条件也同步起来。于是，我们的Singleton再次升级成1.2版本，如下所示：

```java
// version 1.2
public class Singleton
{
    private static Singleton singleton = null;
    private Singleton()  {  }
    public static Singleton getInstance()  {
        synchronized (Singleton.class) {
            if (singleton== null) {
        singleton= new Singleton();
            }
         }
        return singleton;
    }
}
```

不错不错，看似很不错了。在多线程下应该没有什么问题了，不是吗？的确是这样的，1.2版的Singleton在多线程下的确没有问题了，因为我们同步了所有的线程。只不过嘛……，什么？！还不行？！是的，还是有点小问题，我们本来只是想让new这个操作并行就可以了，现在，只要是进入getInstance()的线程都得同步啊，注意，创建对象的动作只有一次，后面的动作全是读取那个成员变量，这些读取的动作不需要线程同步啊。这样的作法感觉非常极端啊，为了一个初始化的创建动作，居然让我们达上了所有的读操作，严重影响后续的性能啊！

还得改！嗯，看来，在线程同步前还得加一个(singleton== null)的条件判断，如果对象已经创建了，那么就不需要线程的同步了。OK，下面是1.3版的Singleton。

```java
// version 1.3
public class Singleton
{
    private static Singleton singleton = null;
    private Singleton()  {    }
    public static Singleton getInstance() {
        if (singleton== null)  {
            synchronized (Singleton.class) {
                if (singleton== null)  {
                    singleton= new Singleton();
                }
            }
        }
        return singleton;
    }
}
```

感觉代码开始变得有点罗嗦和复杂了，不过，这可能是最不错的一个版本了，这个版本又叫“双重检查”Double-Check。下面是说明：

1. 第一个条件是说，如果实例创建了，那就不需要同步了，直接返回就好了。
2. 不然，我们就开始同步线程。
3. 第二个条件是说，如果被同步的线程中，有一个线程创建了对象，那么别的线程就不用再创建了。

相当不错啊，干得非常漂亮！请大家为我们的1.3版起立鼓掌！

但是，如果你认为这个版本大攻告成，你就错了。

主要在于**singleton = new Singleton()**这句，这并非是一个原子操作，事实上在 JVM 中这句话大概做了下面 3 件事情。

1. 给 singleton 分配内存
2. 调用 Singleton 的构造函数来初始化成员变量，形成实例
3. 将singleton对象指向分配的内存空间（执行完这步 singleton才是非 null 了）

但是在 JVM 的即时编译器中存在指令重排序的优化。也就是说上面的第二步和第三步的顺序是不能保证的，最终的执行顺序可能是 1-2-3 也可能是 1-3-2。如果是后者，则在 3 执行完毕、2 未执行之前，被线程二抢占了，这时 instance 已经是非 null 了（但却没有初始化），所以线程二会直接返回 instance，然后使用，然后顺理成章地报错。

对此，我们只需要把singleton声明成 volatile 就可以了。下面是1.4版：

```java
// version 1.4
public class Singleton
{
    private volatile static Singleton singleton = null;
    private Singleton()  {    }
    public static Singleton getInstance()   {
        if (singleton== null)  {
            synchronized (Singleton.class) {
                if (singleton== null)  {
                    singleton= new Singleton();
                }
            }
        }
        return singleton;
    }
}
```

使用 volatile 有两个功用：

1）这个变量不会在多个线程中存在复本，直接从内存读取。

2）这个关键字会禁止指令重排序优化。也就是说，在 volatile 变量的赋值操作后面会有一个内存屏障（生成的汇编代码上），读操作不会被重排序到内存屏障之前。

但是，这个事情仅在Java 1.5版后有用，1.5版之前用这个变量也有问题，因为老版本的Java的内存模型是有缺陷的。

#### **Singleton 的简化版本**

上面的玩法实在是太复杂了，一点也不优雅，下面是一种更为优雅的方式：

这种方法非常简单，因为单例的实例被声明成 static 和 final 变量了，在第一次加载类到内存中时就会初始化，所以创建实例本身是线程安全的。

```java
// version 1.5
public class Singleton
{
    private volatile static Singleton singleton = new Singleton();
    private Singleton()  {    }
    public static Singleton getInstance()   {
        return singleton;
    }
}
```

但是，这种玩法的最大问题是——当这个类被加载的时候，new Singleton() 这句话就会被执行，就算是getInstance()没有被调用，类也被初始化了。

于是，这个可能会与我们想要的行为不一样，比如，我的类的构造函数中，有一些事可能需要依赖于别的类干的一些事（比如某个配置文件，或是某个被其它类创建的资源），我们希望他能在我第一次getInstance()时才被真正的创建。这样，我们可以控制真正的类创建的时刻，而不是把类的创建委托给了类装载器。

好吧，我们还得绕一下：

下面的这个1.6版是老版《Effective Java》中推荐的方式。

```java
// version 1.6
public class Singleton {
    private static class SingletonHolder {
        private static final Singleton INSTANCE = new Singleton();
    }
    private Singleton (){}
    public static final Singleton getInstance() {
        return SingletonHolder.INSTANCE;
    }
}
```

上面这种方式，仍然使用JVM本身机制保证了线程安全问题；由于 SingletonHolder 是私有的，除了 getInstance() 之外没有办法访问它，因此它只有在getInstance()被调用时才会真正创建；同时读取实例的时候不会进行同步，没有性能缺陷；也不依赖 JDK 版本。

#### **Singleton 优雅**版本

```java
public enum Singleton{
   INSTANCE;
}
```

居然用枚举！！看上去好牛逼，通过EasySingleton.INSTANCE来访问，这比调用getInstance()方法简单多了。

默认枚举实例的创建是线程安全的，所以不需要担心线程安全的问题。但是在枚举中的其他任何方法的线程安全由程序员自己负责。还有防止上面的通过反射机制调用私用构造器。

**这个版本基本上消除了绝大多数的问题。代码也非常简单，实在无法不用。这也是新版的《Effective Java》中推荐的模式。**

#### **Singleton的其它问题**

怎么？还有问题？！当然还有，请记住下面这条规则——“**无论你的代码写得有多好，其只能在特定的范围内工作，超出这个范围就要出Bug了**”，这是“陈式第一定理”，呵呵。你能想一想还有什么情况会让这个我们上面的代码出问题吗？

在C++下，我不是很好举例，但是在Java的环境下，嘿嘿，还是让我们来看看下面的一些反例和一些别的事情的讨论（**当然，有些反例可能属于钻牛角尖，可能有点学院派，不过也不排除其实际可能性，就算是提个醒吧**）：

* Class Loader。不知道你对Java的Class Loader熟悉吗？“类装载器”？！C++可没有这个东西啊。这是Java动态性的核心。顾名思义，类装载器是用来把类(class)装载进JVM的。JVM规范定义了两种类型的类装载器：启动内装载器(bootstrap)和用户自定义装载器(user-defined class loader)。 在一个JVM中可能存在多个ClassLoader，每个ClassLoader拥有自己的NameSpace。一个ClassLoader只能拥有一个class对象类型的实例，但是不同的ClassLoader可能拥有相同的class对象实例，这时可能产生致命的问题。如ClassLoaderA，装载了类A的类型实例A1，而ClassLoaderB，也装载了类A的对象实例A2。逻辑上讲A1=A2，但是由于A1和A2来自于不同的ClassLoader，它们实际上是完全不同的，如果A中定义了一个静态变量c，则c在不同的ClassLoader中的值是不同的。

于是，如果咱们的Singleton 1.3版本如果面对着多个Class Loader会怎么样？呵呵，多个实例同样会被多个Class Loader创建出来，当然，这个有点牵强，不过他确实存在。难道我们还要整出个1.4版吗？可是，我们怎么可能在我的Singleton类中操作Class Loader啊？是的，你根本不可能。在这种情况下，你能做的只有是——“保证多个Class Loader不会装载同一个Singleton”。

* 序例化。如果我们的这个Singleton类是一个关于我们程序配置信息的类。我们需要它有序列化的功能，那么，当反序列化的时候，我们将无法控制别人不多次反序列化。不过，我们可以利用一下Serializable接口的readResolve()方法，比如：

```java
public class Singleton implements Serializable
{
    ......
    ......
    protected Object readResolve()
    {
        return getInstance();
    }
}
```

* 多个Java虚拟机。如果我们的程序运行在多个Java的虚拟机中。什么？多个虚拟机？这是一种什么样的情况啊。嗯，这种情况是有点极端，不过还是可能出现，比如EJB或RMI之流的东西。要在这种环境下避免多实例，看来只能通过良好的设计或非技术来解决了。
* volatile变量。关于volatile这个关键字所声明的变量可以被看作是一种 “程度较轻的同步synchronized”；与 synchronized 块相比，volatile 变量所需的编码较少，并且运行时开销也较少，但是它所能实现的功能也仅是synchronized的一部分。当然，如前面所述，我们需要的Singleton只是在创建的时候线程同步，而后面的读取则不需要同步。所以，volatile变量并不能帮助我们即能解决问题，又有好的性能。而且，这种变量只能在JDK 1.5+版后才能使用。
* 关于继承。是的，继承于Singleton后的子类也有可能造成多实例的问题。不过，因为我们早把Singleton的构造函数声明成了私有的，所以也就杜绝了继承这种事情。
* 关于代码重用。也话我们的系统中有很多个类需要用到这个模式，如果我们在每一个类都中有这样的代码，那么就显得有点傻了。那么，我们是否可以使用一种方法，把这具模式抽象出去？在C++下这是很容易的，因为有模板和友元，还支持栈上分配内存，所以比较容易一些（程序如下所示），Java下可能比较复杂一些，聪明的你知道怎么做吗？

```c++
template class Singleton
{
    public:
        static T& Instance()
        {
            static T theSingleInstance; //假设T有一个protected默认构造函数
            return theSingleInstance;
        }
};
class OnlyOne : public Singleton
{
    friend class Singleton;
    int example_data;
    public:
        int GetExampleData() const {return example_data;}
    protected:
        OnlyOne(): example_data(42) {}   // 默认构造函数
        OnlyOne(OnlyOne&) {}
};
int main( )
{
    cout << OnlyOne::Instance().GetExampleData() << endl;
    return 0;
}
```

##### 递归算法题1

一个整数，大于0，不用循环和本地变量，按照n，2n，4n，8n的顺序递增，当值大于5000时，把值按照指定顺序输出来。
例：n=1237
则输出为：
1237，2474，4948，9896，9896，4948，2474，1237，

提示：写程序时，先致谢按递增方式的代码，写好递增的以后，再增加考虑递减部分。

```java
public static void doubleNum(int n){
		System.out.println(n);
	if(n<=5000)
	doubleNum(n*2);
		System.out.println(n);
}
```

##### 递归算法题2

第1个人10，第2个比第1个人大2岁，依次递推，请用递归方式计算出第8个人多大？

```java
public class Test {
    public static void main(String[] args) {
        System.out.println(computeAge(8));
    }
    public static int computeAge(int n) {
        if (n == 1) {
            return 10;
        } else {
            return computeAge(n - 1) + 2;
        }
    }
}
```

##### 排序算法 

```java
//插入排序:
 
package org.rut.util.algorithm.support;
import org.rut.util.algorithm.SortUtil;
public class InsertSort implements SortUtil.Sort{
    public void sort(int[] data) {
        int temp;
        for(int i=1;i<data.length;i++){
            for(int j=i;(j>0)&&(data[j]<data[j-1]);j--){
                SortUtil.swap(data,j,j-1);
            }
        }        
    }
}
//冒泡排序: 
 
for(var i=0; i<arr.length; i++) {    
    for(var j=i+1; j<=arr.length-1; j++) {
        if(eval(arr[i]) < eval(arr[j])) {        
            temp = arr[i];                      
            arr[i] = arr[j];                        
            arr[j] = temp;                                              
        }
    }
} 
 
package org.rut.util.algorithm.support; 
import org.rut.util.algorithm.SortUtil;
public class BubbleSort implements SortUtil.Sort
    public void sort(int[] data) {
        int temp;
        for(int i=0;i<data.length;i++){
            for(int j=data.length-1;j>i;j--){
                if(data[j]<data[j-1]){
                    SortUtil.swap(data,j,j-1);
                }
            }
        }
    }
 
}
 
//选择排序:
 
package org.rut.util.algorithm.support;
import org.rut.util.algorithm.SortUtil;
public class SelectionSort implements SortUtil.Sort {
    public void sort(int[] data) {
        int temp;
        for (int i = 0; i < data.length; i++) {
            int lowIndex = i;
            for (int j = data.length - 1; j > i; j--) {
                if (data[j] < data[lowIndex]) {
                    lowIndex = j;
                }
            }
            SortUtil.swap(data,i,lowIndex);
        }
    }
 
}
 
//Shell排序:
 
package org.rut.util.algorithm.support; 
import org.rut.util.algorithm.SortUtil;
public class ShellSort implements SortUtil.Sort{
    public void sort(int[] data) {
        for(int i=data.length/2;i>2;i/=2){
            for(int j=0;j<i;j++){
                insertSort(data,j,i);
            }
        }
        insertSort(data,0,1);
    }
    private void insertSort(int[] data, int start, int inc) {
        int temp;
        for(int i=start+inc;i<data.length;i+=inc){
            for(int j=i;(j>=inc)&&(data[j]<data[j-inc]);j-=inc){
                SortUtil.swap(data,j,j-inc);
            }
        }
    }
 
}
 
//快速排序:
 
package org.rut.util.algorithm.support; 
import org.rut.util.algorithm.SortUtil;
public class QuickSort implements SortUtil.Sort{
    public void sort(int[] data) {
        quickSort(data,0,data.length-1);        
    }
    private void quickSort(int[] data,int i,int j){
        int pivotIndex=(i+j)/2;
        //swap
        SortUtil.swap(data,pivotIndex,j);
         
        int k=partition(data,i-1,j,data[j]);
        SortUtil.swap(data,k,j);
        if((k-i)>1) quickSort(data,i,k-1);
        if((j-k)>1) quickSort(data,k+1,j);
         
    }
    private int partition(int[] data, int l, int r,int pivot) {
        do{
           while(data[++l]<pivot);
           while((r!=0)&&data[--r]>pivot);
           SortUtil.swap(data,l,r);
        }
        while(l<r);
        SortUtil.swap(data,l,r);        
        return l;
    }
 
}
//改进后的快速排序:
 
package org.rut.util.algorithm.support; 
import org.rut.util.algorithm.SortUtil;
public class ImprovedQuickSort implements SortUtil.Sort {
    private static int MAX_STACK_SIZE=4096;
    private static int THRESHOLD=10;
    public void sort(int[] data) {
        int[] stack=new int[MAX_STACK_SIZE];
         
        int top=-1;
        int pivot;
        int pivotIndex,l,r;
         
        stack[++top]=0;
        stack[++top]=data.length-1;
         
        while(top>0){
            int j=stack[top--];
            int i=stack[top--];
             
            pivotIndex=(i+j)/2;
            pivot=data[pivotIndex];
             
            SortUtil.swap(data,pivotIndex,j);
             
            //partition
            l=i-1;
            r=j;
            do{
                while(data[++l]<pivot);
                while((r!=0)&&(data[--r]>pivot));
                SortUtil.swap(data,l,r);
            }
            while(l<r);
            SortUtil.swap(data,l,r);
            SortUtil.swap(data,l,j);
             
            if((l-i)>THRESHOLD){
                stack[++top]=i;
                stack[++top]=l-1;
            }
            if((j-l)>THRESHOLD){
                stack[++top]=l+1;
                stack[++top]=j;
            }
             
        }
        //new InsertSort().sort(data);
        insertSort(data);
    }
   
    private void insertSort(int[] data) {
        int temp;
        for(int i=1;i<data.length;i++){
            for(int j=i;(j>0)&&(data[j]<data[j-1]);j--){
                SortUtil.swap(data,j,j-1);
            }
        }       
    }
 
}
 
//归并排序:
 
package org.rut.util.algorithm.support;
import org.rut.util.algorithm.SortUtil;
public class MergeSort implements SortUtil.Sort{
    public void sort(int[] data) {
        int[] temp=new int[data.length];
        mergeSort(data,temp,0,data.length-1);
    }
     
    private void mergeSort(int[] data,int[] temp,int l,int r){
        int mid=(l+r)/2;
        if(l==r) return ;
        mergeSort(data,temp,l,mid);
        mergeSort(data,temp,mid+1,r);
        for(int i=l;i<=r;i++){
            temp[i]=data[i];
        }
        int i1=l;
        int i2=mid+1;
        for(int cur=l;cur<=r;cur++){
            if(i1==mid+1)
                data[cur]=temp[i2++];
            else if(i2>r)
                data[cur]=temp[i1++];
            else if(temp[i1]<temp[i2])
                data[cur]=temp[i1++];
            else
                data[cur]=temp[i2++];            
        }
    }
 
}
 
//改进后的归并排序:
 
package org.rut.util.algorithm.support;
import org.rut.util.algorithm.SortUtil;
public class ImprovedMergeSort implements SortUtil.Sort {
    private static final int THRESHOLD = 10;
    public void sort(int[] data) {
        int[] temp=new int[data.length];
        mergeSort(data,temp,0,data.length-1);
    }
 
    private void mergeSort(int[] data, int[] temp, int l, int r) {
        int i, j, k;
        int mid = (l + r) / 2;
        if (l == r)
            return;
        if ((mid - l) >= THRESHOLD)
            mergeSort(data, temp, l, mid);
        else
            insertSort(data, l, mid - l + 1);
        if ((r - mid) > THRESHOLD)
            mergeSort(data, temp, mid + 1, r);
        else
            insertSort(data, mid + 1, r - mid);
 
        for (i = l; i <= mid; i++) {
            temp[i] = data[i];
        }
        for (j = 1; j <= r - mid; j++) {
            temp[r - j + 1] = data[j + mid];
        }
        int a = temp[l];
        int b = temp[r];
        for (i = l, j = r, k = l; k <= r; k++) {
            if (a < b) {
                data[k] = temp[i++];
                a = temp[i];
            } else {
                data[k] = temp[j--];
                b = temp[j];
            }
        }
    
    private void insertSort(int[] data, int start, int len) {
        for(int i=start+1;i<start+len;i++){
            for(int j=i;(j>start) && data[j]<data[j-1];j--){
                SortUtil.swap(data,j,j-1);
            }
        }
    }
 
}
//堆排序:
 
package org.rut.util.algorithm.support;
import org.rut.util.algorithm.SortUtil;
public class HeapSort implements SortUtil.Sort{
    public void sort(int[] data) {
        MaxHeap h=new MaxHeap();
        h.init(data);
        for(int i=0;i<data.length;i++)
            h.remove();
        System.arraycopy(h.queue,1,data,0,data.length);
    }
 
 
     private static class MaxHeap{
          
         
        void init(int[] data){
            this.queue=new int[data.length+1];
            for(int i=0;i<data.length;i++){
                queue[++size]=data[i];
                fixUp(size);
            }
        }
          
        private int size=0;
 
        private int[] queue;
                 
        public int get() {
            return queue[1];
        }
 
        public void remove() {
            SortUtil.swap(queue,1,size--);
            fixDown(1);
        }
        //fixdown
        private void fixDown(int k) {
            int j;
            while ((j = k << 1) <= size) {
                if (j < size && queue[j]<queue[j+1])
                    j++; 
                if (queue[k]>queue[j]) //不用交换
                    break;
                SortUtil.swap(queue,j,k);
                k = j;
            }
        }
        private void fixUp(int k) {
            while (k > 1) {
                int j = k >> 1;
                if (queue[j]>queue[k])
                    break;
                SortUtil.swap(queue,j,k);
                k = j;
            }
        }
 
    }
 
}
 
  
 
//SortUtil:
 
package org.rut.util.algorithm;
 
import org.rut.util.algorithm.support.BubbleSort;
import org.rut.util.algorithm.support.HeapSort;
import org.rut.util.algorithm.support.ImprovedMergeSort;
import org.rut.util.algorithm.support.ImprovedQuickSort;
import org.rut.util.algorithm.support.InsertSort;
import org.rut.util.algorithm.support.MergeSort;
import org.rut.util.algorithm.support.QuickSort;
import org.rut.util.algorithm.support.SelectionSort;
import org.rut.util.algorithm.support.ShellSort;
public class SortUtil {
    public final static int INSERT = 1;
    public final static int BUBBLE = 2;
    public final static int SELECTION = 3; 
    public final static int SHELL = 4; 
    public final static int QUICK = 5; 
    public final static int IMPROVED_QUICK = 6; 
    public final static int MERGE = 7; 
    public final static int IMPROVED_MERGE = 8; 
    public final static int HEAP = 9;
    public static void sort(int[] data) {
        sort(data, IMPROVED_QUICK);
    }
    private static String[] name={
            "insert","bubble","selection","shell","quick","improved_quick","merge","improved_merge","heap"
    };
     
    private static Sort[] impl=new Sort[]{
            new InsertSort(),
            new BubbleSort(),
            new SelectionSort(),
            new ShellSort(),
            new QuickSort(),
            new ImprovedQuickSort(),
            new MergeSort(),
            new ImprovedMergeSort(),
            new HeapSort()
    };
 
    public static String toString(int algorithm){
        return name[algorithm-1];
    }
     
    public static void sort(int[] data, int algorithm) {
        impl[algorithm-1].sort(data);
    }
 
    public static interface Sort {
        public void sort(int[] data);
    }
 
    public static void swap(int[] data, int i, int j) {
        int temp = data[i];
        data[i] = data[j];
        data[j] = temp;
    }
}
```

##### 有数组a[n]，用java代码将数组元素顺序颠倒

```java
public class Test {   
  public static void reverse(int[] a, int left, int right) {   
    if (left >= right)   
      return;   
    int temp;   
    temp = a[left];   
    a[left] = a[right];   
    a[right] = temp;   
    reverse(a, ++left, --right);   
  }     
  public static void main(String args[]) {   
    int[] a = { 1, 2, 3, 4, 5 };   
    reverse(a, 0, a.length - 1);   
    for (int i = 0; i < a.length; i++)   
      System.out.println(a[i]);   
  }   
}  
```

12．金额转换，阿拉伯数字的金额转换成中国传统的形式如：（￥1011）－>（一千零一拾一元整）输出。

```java
public class Test {   
    private static final char[] data = new char[]{'零','壹','贰','叁','肆','伍','陆','柒','捌','玖'}; 
    private static final char[] units = new char[]{'元','拾','佰','仟','万','拾','佰','仟','亿'}; 
    /*去零的代码
    return sb.reverse().toString().replaceAll("零[拾佰仟]","零").replaceAll("零+万","万").replaceAll("零+元","元").replaceAll("零+","零");*/
    /*去零倒序
    return sb.reverse().toString().replaceAll("零[拾佰仟]","零").replaceAll("零+万","万").replaceAll("零+元","元").replaceAll("零+","零");*/ 
    public static void main(String[] args) {
        System.out.println(convert(230009));
    }
    private static String convert(int money) {
        StringBuffer sbf = new StringBuffer();
        int unit = 0;
        while(money!=0)
        {
            sbf.insert(0,units[unit++]);
            int number = money%10;
            sbf.insert(0, data[number]);
            money /= 10;
        }
        return sbf.toString().replaceAll("零[拾佰仟]","零").replaceAll("零+万","万").replaceAll("零+元","元").replaceAll("零+","零");
    } 
}
```

