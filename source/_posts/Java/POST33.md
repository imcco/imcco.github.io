---
title: Java技术重点回顾
tags: Java
category: Java
abbrlink: 44393
date: 2018-01-11 21:34:36
---
![image](http://ovi3ob9p4.bkt.clouddn.com/TIETU/CT0089.jpg)

Java技术重点回顾
<!--more-->
# JavaI/O
　　Java中和I/O操作相关的内容，I/O也是编程语言的一个基础特性，Java中的I/O分为两种类型，一种是顺序读取，一种是随机读取。

　　我们先来看顺序读取，有两种方式可以进行顺序读取，一种是InputStream/OutputStream，它是针对字节进行操作的输入输出流；另外一种是Reader/Writer，它是针对字符进行操作的输入输出流。

## InputStream的结构

- FileInputStream：操作文件，经常和BufferedInputStream一起使用
- PipedInputStream：可用于线程间通信
- ObjectInputStream：可用于对象序列化
- ByteArrayInputStream：用于处理字节数组的输入
- LineNumberInputStream：可输出当前行数，并且可以在程序中进行修改


## OutputStream的结构

PrintStream：提供了类似print和println的接口去输出数据
　　下面我们来看如何使用Stream的方式来操作输入输出

### 使用InputStream读取文件

```java
使用FileInputStream读取文件信息
public static byte[] readFileByFileInputStream(File file) throws IOException
{
    ByteArrayOutputStream output = new ByteArrayOutputStream();
    FileInputStream fis = null;
    try
    {
        fis = new FileInputStream(file);
        byte[] buffer = new byte[1024];
        int bytesRead = 0;
        while((bytesRead = fis.read(buffer, 0, buffer.length)) != -1)
        {
            output.write(buffer, 0, bytesRead);
        }
    }
    catch(Exception ex)
    {
        System.out.println("Error occurs during reading " + file.getAbsoluteFile());
    }
    finally
    {
        if (fis !=null) fis.close();
        if (output !=null) output.close();
    }
    return output.toByteArray();
}
```
### 使用BufferedInputStream读取文件

```java
public static byte[] readFileByBufferedInputStream(File file) throws Exception
{
    FileInputStream fis = null;
    BufferedInputStream bis = null;
    ByteArrayOutputStream output = new ByteArrayOutputStream();
    try
    {
        fis = new FileInputStream(file);
        bis = new BufferedInputStream(fis);
        byte[] buffer = new byte[1024];
        int bytesRead = 0;
        while((bytesRead = bis.read(buffer, 0, buffer.length)) != -1)
        {
            output.write(buffer, 0, bytesRead);
        }
    }
    catch(Exception ex)
    {
        System.out.println("Error occurs during reading " + file.getAbsoluteFile());
    }
    finally
    {
        if (fis != null) fis.close();
        if (bis != null) bis.close();
        if (output != null) output.close();
    }
    return output.toByteArray();
}
```
### 使用OutputStream复制文件

```java
public static void copyFileByFileOutputStream(File file) throws IOException
{
    FileInputStream fis = null;
    FileOutputStream fos = null;
    try
    {
        fis = new FileInputStream(file);
        fos = new FileOutputStream(file.getName() + ".bak");
        byte[] buffer = new byte[1024];
        int bytesRead = 0;
        while((bytesRead = fis.read(buffer,0,buffer.length)) != -1)
        {
            fos.write(buffer, 0, bytesRead);
        }
        fos.flush();
    }
    catch(Exception ex)
    {
        System.out.println("Error occurs during copying " + file.getAbsoluteFile());
    }
    finally
    {
        if (fis != null) fis.close();
        if (fos != null) fos.close();
    }
}
```
### 使用BufferedOutputStream复制文件

```java

public static void copyFilebyBufferedOutputStream(File file)throws IOException
{
    FileInputStream fis = null;
    BufferedInputStream bis = null;
    FileOutputStream fos = null;
    BufferedOutputStream bos = null;
    try
    {
        fis = new FileInputStream(file);
        bis = new BufferedInputStream(fis);
        fos = new FileOutputStream(file.getName() + ".bak");
        bos = new BufferedOutputStream(fos);
        byte[] buffer = new byte[1024];
        int bytesRead = 0;
        while((bytesRead = bis.read(buffer, 0, buffer.length)) != -1)
        {
            bos.write(buffer, 0, bytesRead);
        }
        bos.flush();
    }
    catch(Exception ex)
    {
        System.out.println("Error occurs during copying " + file.getAbsoluteFile());
    }
    finally
    {
        if (fis != null) fis.close();
        if (bis != null) bis.close();
        if (fos != null) fos.close();
        if (bos != null) bos.close();
    }
}
```
这里的代码对异常的处理非常不完整，稍后我们会给出完整严谨的代码。

## Reader的结构

这里的Reader基本上和InputStream能够对应上。　　

## Writer的结构

下面我们来看一些使用Reader或者Writer的例子

使用Reader读取文件内容

```java
public static String readFile(String file)throws IOException
{
    BufferedReader br = null;
    StringBuffer sb = new StringBuffer();
    try
    {
        br = new BufferedReader(new FileReader(file));
        String line = null;
        
        while((line = br.readLine()) != null)
        {
            sb.append(line);
        }
    }
    catch(Exception ex)
    {
        System.out.println("Error occurs during reading " + file);
    }
    finally
    {
        if (br != null) br.close();
    }
    return sb.toString();
}
```
### 使用Writer复制文件


```java
public static void copyFile(String file) throws IOException
{ 
    BufferedReader br = null;
    BufferedWriter bw = null;
    try
    {
        br = new BufferedReader(new FileReader(file));
        bw = new BufferedWriter(new FileWriter(file + ".bak"));
        String line = null;
        while((line = br.readLine())!= null)
        {
            bw.write(line);
        }
    }
    catch(Exception ex)
    {
        System.out.println("Error occurs during copying " + file);
    }
    finally
    {
        if (br != null) br.close();
        if (bw != null) bw.close();
    }
}
```
下面我们来看如何对文件进行随机访问，Java中主要使用RandomAccessFile来对文件进行随机操作。

### 创建一个大小固定的文件


```java
public static void createFile(String file, int size) throws IOException
{
    File temp = new File(file);
    RandomAccessFile raf = new RandomAccessFile(temp, "rw");
    raf.setLength(size);
    raf.close();
}
```
### 向文件中随机写入数据


```java
public static void writeFile(String file, byte[] content, int startPos, int contentLength) throws IOException
{
    RandomAccessFile raf = new RandomAccessFile(new File(file), "rw");
    raf.seek(startPos);
    raf.write(content, 0, contentLength);
    raf.close();
}
```
接下里，我们来看一些其他的常用操作

### 移动文件

```java
public static boolean moveFile(String sourceFile, String destFile)
{
    File source = new File(sourceFile);
    if (!source.exists()) throw new RuntimeException("source file does not exist.");
    File dest = new File(destFile);
    if (!(new File(dest.getPath()).exists())) new File(dest.getParent()).mkdirs();
    return source.renameTo(dest);
}
```
### 复制文件

```java
public static void copyFile(String sourceFile, String destFile) throws IOException
{
    File source = new File(sourceFile);
    if (!source.exists()) throw new RuntimeException("File does not exist.");
    if (!source.isFile()) throw new RuntimeException("It is not file.");
    if (!source.canRead()) throw new RuntimeException("File cound not be read.");
    File dest = new File(destFile);
    if (dest.exists())
    {
        if (dest.isDirectory()) throw new RuntimeException("Destination is a folder.");
        else
        {
            dest.delete();
        }
    }
    else
    {
        File parentFolder = new File(dest.getParent());
        if (!parentFolder.exists()) parentFolder.mkdirs();
        if (!parentFolder.canWrite()) throw new RuntimeException("Destination can not be written.");
    }
    FileInputStream fis = null;
    FileOutputStream fos = null;
    try
    {
        fis = new FileInputStream(source);
        fos = new FileOutputStream(dest);
        byte[] buffer = new byte[1024];
        int bytesRead = 0;
        while((bytesRead = fis.read(buffer, 0, buffer.length)) != -1)
        {
            fos.write(buffer, 0, bytesRead);
        }
        fos.flush();
    }
    catch(IOException ex)
    {
        System.out.println("Error occurs during copying " + sourceFile);
    }
    finally
    {
        if (fis != null) fis.close();
        if (fos != null) fos.close();
    }
}
```
### 复制文件夹

```java
public static void copyDir(String sourceDir, String destDir) throws IOException
{
    
    File source = new File(sourceDir);
    if (!source.exists()) throw new RuntimeException("Source does not exist.");
    if (!source.canRead()) throw new RuntimeException("Source could not be read.");
    File dest = new File(destDir);
    if (!dest.exists()) dest.mkdirs();
    
    File[] arrFiles = source.listFiles();
    for(int i = 0; i < arrFiles.length; i++)
    {
        if (arrFiles[i].isFile())
        {
            BufferedReader reader = new BufferedReader(new FileReader(arrFiles[i]));
            BufferedWriter writer = new BufferedWriter(new FileWriter(destDir + "/" + arrFiles[i].getName()));
            String line = null;
            while((line = reader.readLine()) != null) writer.write(line);
            writer.flush();
            reader.close();
            writer.close();
        }
        else
        {
            copyDir(sourceDir + "/" + arrFiles[i].getName(), destDir + "/" + arrFiles[i].getName());
        }
    }
}
```
### 删除文件夹

```java
public static void del(String filePath)
{
    File file = new File(filePath);
    if (file == null || !file.exists()) return;
    if (file.isFile())
    {
        file.delete();
    }
    else
    {
        File[] arrFiles = file.listFiles();
        if (arrFiles.length > 0)
        {
            for(int i = 0; i < arrFiles.length; i++)
            {
                del(arrFiles[i].getAbsolutePath());
            }
        }
        file.delete();
    }
}
```
### 获取文件夹大小

```java
public static long getFolderSize(String dir)
{
    long size = 0;
    File file = new File(dir);
    if (!file.exists()) throw new RuntimeException("dir does not exist.");
    if (file.isFile()) return file.length();
    else
    {
        String[] arrFileName = file.list();
        for (int i = 0; i < arrFileName.length; i++)
        {
            size += getFolderSize(dir + "/" + arrFileName[i]);
        }
    }
    
    return size;
}
```
### 将大文件切分成多个小文件

```java
public static void splitFile(String filePath, long unit) throws IOException
{
    File file = new File(filePath);
    if (!file.exists()) throw new RuntimeException("file does not exist.");
    long size = file.length();
    if (unit >= size) return;
    int count = size % unit == 0 ? (int)(size/unit) : (int)(size/unit) + 1;
    String newFile = null;
    FileOutputStream fos = null;
    FileInputStream fis =null;
    byte[] buffer = new byte[(int)unit];
    fis = new FileInputStream(file);
    long startPos = 0;
    String countFile = filePath + "_Count";
    PrintWriter writer = new PrintWriter(new FileWriter( new File(countFile)));
    writer.println(filePath + "\t" + size);
    for (int i = 1; i <= count; i++)
    {
        newFile = filePath + "_" + i;
        startPos = (i - 1) * unit;
        System.out.println("Creating " + newFile);
        fos = new FileOutputStream(new File(newFile));
        int bytesRead = fis.read(buffer, 0, buffer.length);
        if (bytesRead != -1)
        {
            fos.write(buffer, 0, bytesRead);
            writer.println(newFile + "\t" + startPos + "\t" + bytesRead);
        }
        fos.flush();
        fos.close();
        System.out.println("StartPos:" + i*unit + "; EndPos:" + (i*unit + bytesRead));
    }
    writer.flush();
    writer.close();
    fis.close();
}
```
### 将多个小文件合并成一个大文件

```java
public static void linkFiles(String countFile) throws IOException
{
    File file = new File(countFile);
    if (!file.exists()) throw new RuntimeException("Count file does not exist.");
    BufferedReader reader = new BufferedReader(new FileReader(file));
    String line = reader.readLine();
    String newFile = line.split("\t")[0];
    long size = Long.parseLong(line.split("\t")[1]);
    RandomAccessFile raf = new RandomAccessFile(newFile, "rw");
    raf.setLength(size);
    FileInputStream fis = null;
    byte[] buffer = null;
    
    while((line = reader.readLine()) != null)
    {
        String[] arrInfo = line.split("\t");
        fis = new FileInputStream(new File(arrInfo[0]));
        buffer = new byte[Integer.parseInt(arrInfo[2])];
        long startPos = Long.parseLong(arrInfo[1]);
        fis.read(buffer, 0, Integer.parseInt(arrInfo[2]));
        raf.seek(startPos);
        raf.write(buffer, 0, Integer.parseInt(arrInfo[2]));
        fis.close();
    }
    raf.close();
}
```
### 执行外部命令

```java
public static void execExternalCommand(String command, String argument)
{
    Process process = null;
    try
    {
        process = Runtime.getRuntime().exec(command + " " + argument);
        InputStream is = process.getInputStream();
        BufferedReader br = new BufferedReader(new InputStreamReader(is));
        String line = null;
        while((line = br.readLine()) != null)
        {
            System.out.println(line);
        }
    }
    catch(Exception ex)
    {
        System.err.println(ex.getMessage());
    }
    finally
    {
        if (process != null) process.destroy();
    }
}
```
# Java网络通信

Java实现网络通信，包括TCP通信、UDP通信、多播以及NIO。

## TCP连接
TCP的基础是Socket，在TCP连接中，我们会使用ServerSocket和Socket，当客户端和服务器建立连接以后，剩下的基本就是对I/O的控制了。

我们先来看一个简单的TCP通信，它分为客户端和服务器端。

客户端代码如下：
### 简单的TCP客户端

```java
import java.net.*;
import java.io.*;
public class SimpleTcpClient {

    public static void main(String[] args) throws IOException
    {
        Socket socket = null;
        BufferedReader br = null;
        PrintWriter pw = null;
        BufferedReader brTemp = null;
        try
        {
            socket = new Socket(InetAddress.getLocalHost(), 5678);
            br = new BufferedReader(new InputStreamReader(socket.getInputStream()));
            pw = new PrintWriter(socket.getOutputStream());
            brTemp = new BufferedReader(new InputStreamReader(System.in));
            while(true)
            {
                String line = brTemp.readLine();
                pw.println(line);
                pw.flush();
                if (line.equals("end")) break;
                System.out.println(br.readLine());
            }
        }
        catch(Exception ex)
        {
            System.err.println(ex.getMessage());
        }
        finally
        {
            if (socket != null) socket.close();
            if (br != null) br.close();
            if (brTemp != null) brTemp.close();
            if (pw != null) pw.close();
        }
    }
}
```

服务器端代码如下：
### 简单版本TCP服务器端

```java
import java.net.*;
import java.io.*;
public class SimpleTcpServer {

    public static void main(String[] args) throws IOException
    {
        ServerSocket server = null;
        Socket client = null;
        BufferedReader br = null;
        PrintWriter pw = null;
        try
        {
            server = new ServerSocket(5678);
            client = server.accept();
            br = new BufferedReader(new InputStreamReader(client.getInputStream()));
            pw = new PrintWriter(client.getOutputStream());
            while(true)
            {
                String line = br.readLine();
                pw.println("Response:" + line);
                pw.flush();
                if (line.equals("end")) break;
            }
        }
        catch(Exception ex)
        {
            System.err.println(ex.getMessage());
        }
        finally
        {
            if (server != null) server.close();
            if (client != null) client.close();
            if (br != null) br.close();
            if (pw != null) pw.close();
        }
    }
}
```

这里的服务器的功能非常简单，它接收客户端发来的消息，然后将消息“原封不动”的返回给客户端。当客户端发送“end”时，通信结束。

上面的代码基本上勾勒了TCP通信过程中，客户端和服务器端的主要框架，我们可以发现，上述的代码中，服务器端在任何时刻，都只能处理来自客户端的一个请求，它是串行处理的，不能并行，这和我们印象里的服务器处理方式不太相同，我们可以为服务器添加多线程，当一个客户端的请求进入后，我们就创建一个线程，来处理对应的请求。

改善后的服务器端代码如下：

### 多线程版本的TCP服务器端

```java
import java.net.*;
import java.io.*;
public class SmartTcpServer {
    public static void main(String[] args) throws IOException
    {
        ServerSocket server = new ServerSocket(5678);
        while(true)
        {
            Socket client = server.accept();
            Thread thread = new ServerThread(client);
            thread.start();
        }
    }
}

class ServerThread extends Thread
{
    private Socket socket = null;

    public ServerThread(Socket socket)
    {
        this.socket = socket;
    }
    
    public void run() {
        BufferedReader br = null;
        PrintWriter pw = null;
        try
        {
            br = new BufferedReader(new InputStreamReader(socket.getInputStream()));
            pw = new PrintWriter(socket.getOutputStream());
            while(true)
            {
                String line = br.readLine();
                pw.println("Response:" + line);
                pw.flush();
                if (line.equals("end")) break;
            }
        }
        catch(Exception ex)
        {
            System.err.println(ex.getMessage());
        }
        finally
        {
            if (socket != null)
                try {
                    socket.close();
                } catch (IOException e1) {
                    e1.printStackTrace();
                }
            if (br != null)
                try {
                    br.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            if (pw != null) pw.close();
        }
    }
}
```

修改后的服务器端，就可以同时处理来自客户端的多个请求了。

在编程的过程中，我们会有“资源”的概念，例如数据库连接就是一个典型的资源，为了提升性能，我们通常不会直接销毁数据库连接，而是使用数据库连接池的方式来对多个数据库连接进行管理，已实现重用的目的。对于Socket连接来说，它也是一种资源，当我们的程序需要大量的Socket连接时，如果每个连接都需要重新建立，那么将会是一件非常没有效率的做法。

　　和数据库连接池类似，我们也可以设计TCP连接池，这里的思路是我们用一个数组来维持多个Socket连接，另外一个状态数组来描述每个Socket连接是否正在使用，当程序需要Socket连接时，我们遍历状态数组，取出第一个没被使用的Socket连接，如果所有连接都在使用，抛出异常。这是一种很直观简单的“调度策略”，在很多开源或者商业的框架中（Apache/Tomcat），都会有类似的“资源池”。

TCP连接池的代码如下：

### 一个简单的TCP连接池

```java
import java.net.*;
import java.io.*;
public class TcpConnectionPool {

    private InetAddress address = null;
    private int port;
    private Socket[] arrSockets = null;
    private boolean[] arrStatus = null;
    private int count;
    
    public TcpConnectionPool(InetAddress address, int port, int count)
    {
        this.address = address;
        this.port = port;
        this .count = count;
        arrSockets = new Socket[count];
        arrStatus = new boolean[count];
        
        init();
    }
    
    private void init()
    {
        try
        {
            for (int i = 0; i < count; i++)
            {
                arrSockets[i] = new Socket(address.getHostAddress(), port);
                arrStatus[i] = false;
            }
        }
        catch(Exception ex)
        {
            System.err.println(ex.getMessage());
        }
    }
    
    public Socket getConnection()
    {
        if (arrSockets == null) init();
        int i = 0;
        for(i = 0; i < count; i++)
        {
            if (arrStatus[i] == false) 
            {
                arrStatus[i] = true;
                break;
            }
        }
        if (i == count) throw new RuntimeException("have no connection availiable for now.");
        
        return arrSockets[i];
    }
    
    public void releaseConnection(Socket socket)
    {
        if (arrSockets == null) init();
        for (int i = 0; i < count; i++)
        {
            if (arrSockets[i] == socket)
            {
                arrStatus[i] = false;
                break;
            }
        }
    }
    
    public void reBuild()
    {
        init();
    }
    
    public void destory()
    {
        if (arrSockets == null) return;
        
        for(int i = 0; i < count; i++)
        {
            try
            {
                arrSockets[i].close();
            }
            catch(Exception ex)
            {
                System.err.println(ex.getMessage());
                continue;
            }
        }
    }
}
```

## UDP连接

UDP是一种和TCP不同的连接方式，它通常应用在对实时性要求很高，对准确定要求不高的场合，例如在线视频。UDP会有“丢包”的情况发生，在TCP中，如果Server没有启动，Client发消息时，会报出异常，但对UDP来说，不会产生任何异常。

UDP通信使用的两个类时DatagramSocket和DatagramPacket，后者存放了通信的内容。

下面是一个简单的UDP通信例子，同TCP一样，也分为Client和Server两部分，Client端代码如下：

### UDP通信客户端
```java

import java.net.*;
import java.io.*;
public class UdpClient {

    public static void main(String[] args)
    {
        try
        {
            InetAddress host = InetAddress.getLocalHost();
            int port = 5678;
            BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
            while(true)
            {
                String line = br.readLine();
                byte[] message = line.getBytes();
                DatagramPacket packet = new DatagramPacket(message, message.length, host, port);
                DatagramSocket socket = new DatagramSocket();
                socket.send(packet);
                socket.close();
                if (line.equals("end")) break;
            }
            br.close();
        }
        catch(Exception ex)
        {
            System.err.println(ex.getMessage());
        }
    }
}
```

Server端代码如下：

### UDP通信服务器端

```java

import java.net.*;
import java.io.*;
public class UdpServer {

    public static void main(String[] args)
    {
        try
        {
            int port = 5678;
            DatagramSocket dsSocket = new DatagramSocket(port);
            byte[] buffer = new byte[1024];
            DatagramPacket packet = new DatagramPacket(buffer, buffer.length);
            while(true)
            {
                dsSocket.receive(packet);
                String message = new String(buffer, 0, packet.getLength());
                System.out.println(packet.getAddress().getHostName() + ":" + message);
                if (message.equals("end")) break;
                packet.setLength(buffer.length);
            }
            dsSocket.close();
        }
        catch(Exception ex)
        {
            System.err.println(ex.getMessage());
        }
    }
}
```

这里，我们也假设和TCP一样，当Client发出“end”消息时，认为通信结束，但其实这样的设计不是必要的，Client端可以随时断开，并不需要关心Server端状态。

## 多播（Multicast）

多播采用和UDP类似的方式，它会使用D类IP地址和标准的UDP端口号，D类IP地址是指224.0.0.0到239.255.255.255之间的地址，不包括224.0.0.0。

多播会使用到的类是MulticastSocket，它有两个方法需要关注：joinGroup和leaveGroup。

下面是一个多播的例子，Client端代码如下：

### 多播通信客户端

```java
import java.net.*;
import java.io.*;
public class MulticastClient {

    public static void main(String[] args)
    {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        try
        {
            InetAddress address = InetAddress.getByName("230.0.0.1");
            int port = 5678;
            while(true)
            {
                String line = br.readLine();
                byte[] message = line.getBytes();
                DatagramPacket packet = new DatagramPacket(message, message.length, address, port);
                MulticastSocket multicastSocket = new MulticastSocket();
                multicastSocket.send(packet);
                if (line.equals("end")) break;
            }
            br.close();
        }
        catch(Exception ex)
        {
            System.err.println(ex.getMessage());
        }
    }
}
```

服务器端代码如下：

### 多播通信服务器端

```java
import java.net.*;
import java.io.*;
public class MulticastServer {

    public static void main(String[] args)
    {
        int port = 5678;
        try
        {
            MulticastSocket multicastSocket = new MulticastSocket(port);
            InetAddress address = InetAddress.getByName("230.0.0.1");
            multicastSocket.joinGroup(address);
            byte[] buffer = new byte[1024];
            DatagramPacket packet = new DatagramPacket(buffer, buffer.length);
            while(true)
            {
                multicastSocket.receive(packet);
                String message = new String(buffer, packet.getLength());
                System.out.println(packet.getAddress().getHostName() + ":" + message);
                if (message.equals("end")) break;
                packet.setLength(buffer.length);
            }
            multicastSocket.close();
        }
        catch(Exception ex)
        {
            System.err.println(ex.getMessage());
        }
    }
}
```

## NIO（New IO）

NIO是JDK1.4引入的一套新的IO API，它在缓冲区管理、网络通信、文件存取以及字符集操作方面有了新的设计。对于网络通信来说，NIO使用了缓冲区和通道的概念。

下面是一个NIO的例子，和我们上面提到的代码风格有很大的不同。

### NIO例子

```java
import java.io.*;
import java.nio.*;
import java.nio.channels.*;
import java.nio.charset.*;
import java.net.*;
public class NewIOSample {

    public static void main(String[] args)
    {
        String host="127.0.0.1";
        int port = 5678;
        SocketChannel channel = null;
        try
        {
            InetSocketAddress address = new InetSocketAddress(host,port);
            Charset charset = Charset.forName("UTF-8");
            CharsetDecoder decoder = charset.newDecoder();
            CharsetEncoder encoder = charset.newEncoder();
            
            ByteBuffer buffer = ByteBuffer.allocate(1024);
            CharBuffer charBuffer = CharBuffer.allocate(1024);
            
            channel = SocketChannel.open();
            channel.connect(address);
            
            String request = "GET / \r\n\r\n";
            channel.write(encoder.encode(CharBuffer.wrap(request)));
            
            while((channel.read(buffer)) != -1)
            {
                buffer.flip();
                decoder.decode(buffer, charBuffer, false);
                charBuffer.flip();
                System.out.println(charBuffer);
                buffer.clear();
                charBuffer.clear();
            }
        }
        catch(Exception ex)
        {
            System.err.println(ex.getMessage());
        }
        finally
        {
            if (channel != null)
                try {
                    channel.close();
                } catch (IOException e) {
                    // TODO Auto-generated catch block
                    e.printStackTrace();
                }
        }
    }
}
```

上述代码会试图访问一个本地的网址，然后将其内容打印出来。

# Java多线程

多线程是一个复杂的话题，包含了很多内容，文章主要关注线程的基本属性、如何创建线程、线程的状态切换以及线程通信，我们把线程同步的话题留到下一篇文章中。

线程是操作系统运行的基本单位，它被封装在进程中，一个进程可以包含多个线程。即使我们不手动创造线程，进程也会有一个默认的线程在运行。

对于JVM来说，当我们编写一个单线程的程序去运行时，JVM中也是有至少两个线程在运行，一个是我们创建的程序，一个是垃圾回收。

## 线程基本信息

我们可以通过Thread.currentThread()方法获取当前线程的一些信息，并对其进行修改。

我们来看以下代码：

### 查看并修改当前线程的属性

```java

String name = Thread.currentThread().getName();
        int priority = Thread.currentThread().getPriority();
        String groupName = Thread.currentThread().getThreadGroup().getName();
        boolean isDaemon = Thread.currentThread().isDaemon();
        System.out.println("Thread Name:" + name);
        System.out.println("Priority:" + priority);
        System.out.println("Group Name:" + groupName);
        System.out.println("IsDaemon:" + isDaemon);
        
        Thread.currentThread().setName("Test");
        Thread.currentThread().setPriority(Thread.MAX_PRIORITY);
        name = Thread.currentThread().getName();
        priority = Thread.currentThread().getPriority();
        groupName = Thread.currentThread().getThreadGroup().getName();
        isDaemon = Thread.currentThread().isDaemon();
        System.out.println("Thread Name:" + name);
        System.out.println("Priority:" + priority);
```

其中列出的属性说明如下：

- GroupName，每个线程都会默认在一个线程组里，我们也可以显式的创建线程组，一个线程组中也可以包含子线程组，这样线程和线程组，就构成了一个树状结构。

- Name，每个线程都会有一个名字，如果不显式指定，那么名字的规则是“Thread-xxx”。

- Priority，每个线程都会有自己的优先级，JVM对优先级的处理方式是“抢占式”的。当JVM发现优先级高的线程时，马上运行该线程；对于多个优先级相等的线程，JVM对其进行轮询处理。Java的线程优先级从1到10，默认是5，Thread类定义了2个常量：MIN_PRIORITY和MAX_PRIORITY来表示最高和最低优先级。

我们可以看下面的代码，它定义了两个不同优先级的线程：

### 线程优先级示例

```java
public static void priorityTest()
{
    Thread thread1 = new Thread("low")
    {
        public void run()
        {
            for (int i = 0; i < 5; i++)
            {
                System.out.println("Thread 1 is running.");
            }
        }
    };
    
    Thread thread2 = new Thread("high")
    {
        public void run()
        {
            for (int i = 0; i < 5; i++)
            {
                System.out.println("Thread 2 is running.");
            }
        }
    };
    
    thread1.setPriority(Thread.MIN_PRIORITY);
    thread2.setPriority(Thread.MAX_PRIORITY);
    thread1.start();
    thread2.start();
}
```

从运行结果可以看出，是高优先级线程运行完成后，低优先级线程才运行。
isDaemon，这个属性用来控制父子线程的关系，如果设置为true，当父线程结束后，其下所有子线程也结束，反之，子线程的生命周期不受父线程影响。

我们来看下面的例子：

### IsDaemon 示例

```java
public static void daemonTest()
{
    Thread thread1 = new Thread("daemon")
    {
        public void run()
        {
            Thread subThread = new Thread("sub")
            {
                public void run()
                {
                    for(int i = 0; i < 100; i++)
                    {
                        System.out.println("Sub Thread Running " + i);
                    }
                }
            };
            subThread.setDaemon(true);
            subThread.start();
            System.out.println("Main Thread end.");
        }
    };
    
    thread1.start();
}
```

上面代码的运行结果，在和删除subThread.setDaemon(true);后对比，可以发现后者运行过程中子线程会完成执行后再结束，而前者中，子线程很快就结束了。

## 如何创建线程

上面的内容，都是演示默认线程中的一些信息，那么应该如何创建线程呢？在Java中，我们有3种方式可以用来创建线程。

Java中的线程要么继承Thread类，要么实现Runnable接口，我们一一道来。

### 使用内部类来创建线程
我们可以使用内部类的方式来创建线程，过程是声明一个Thread类型的变量，并重写run方法。示例代码如下：

```java
public static void createThreadByNestClass()
{
    Thread thread = new Thread()
    {
        public void run()
        {
            for (int i =0; i < 5; i++)
            {
                System.out.println("Thread " + Thread.currentThread().getName() + " is running.");
            }
            System.out.println("Thread " + Thread.currentThread().getName() + " is finished.");
        }
    };
    thread.start();
}
```

### 继承Thread以创建线程
我们可以从Thread中派生一个类，重写其run方法，这种方式和上面相似。示例代码如下：

```java
class MyThread extends Thread
{
    public void run()
    {
        for (int i =0; i < 5; i++)
        {
            System.out.println("Thread " + Thread.currentThread().getName() + " is running.");
        }
        System.out.println("Thread " + Thread.currentThread().getName() + " is finished.");
    }
}


public static void createThreadBySubClass()
{
    MyThread thread = new MyThread();
    thread.start();
}
```

### 实现Runnable接口以创建线程
我们可以定义一个类，使其实现Runnable接口，然后将该类的实例作为构建Thread变量构造函数的参数。示例代码如下：

```java
class MyRunnable implements Runnable
{
    public void run() 
    {
        for (int i =0; i < 5; i++)
        {
            System.out.println("Thread " + Thread.currentThread().getName() + " is running.");
        }
        System.out.println("Thread " + Thread.currentThread().getName() + " is finished.");
    }
}


public static void createThreadByRunnable()
{
    MyRunnable runnable = new MyRunnable();
    Thread thread = new Thread(runnable);
    thread.start();
}
```

上述3种方式都可以创建线程，而且从示例代码上看，线程执行的功能是一样的，那么这三种创建方式有什么不同呢？

这涉及到Java中多线程的运行模式，对于Java来说，多线程在运行时，有“多对象多线程”和“单对象多线程”的区别：

- 多对象多线程，程序在运行过程中创建多个线程对象，每个对象上运行一个线程。
- 单对象多线程，程序在运行过程中创建一个线程对象，在其上运行多个线程。

显然，从线程同步和调度的角度来看，多对象多线程要简单一些。上述3种线程创建方式，前两种都属于“多对象多线程”，第三种既可以使用“多对象多线程”，也可以使用“单对象单线程”。

我们来看下面的示例代码，里面会用到Object.notify方法，这个方法会唤醒对象上的一个线程；而Object.notifyAll方法，则会唤醒对象上的所有线程。

### notify示例

```java
public class NotifySample {
    
    public static void main(String[] args) throws InterruptedException
    {
        notifyTest();
        notifyTest2();
        notifyTest3();
    }
    
    private static void notifyTest() throws InterruptedException
    {
        MyThread[] arrThreads = new MyThread[3];
        for (int i = 0; i < arrThreads.length; i++)
        {
            arrThreads[i] = new MyThread();
            arrThreads[i].id = i;
            arrThreads[i].setDaemon(true);
            arrThreads[i].start();
        }
        Thread.sleep(500);
        for (int i = 0; i < arrThreads.length; i++)
        {
            synchronized(arrThreads[i])
            {
                arrThreads[i].notify();
            }
        }
    }
    
    private static void notifyTest2() throws InterruptedException
    {
        MyRunner[] arrMyRunners = new MyRunner[3];
        Thread[] arrThreads = new Thread[3];
        for (int i = 0; i < arrThreads.length; i++)
        {
            arrMyRunners[i] = new MyRunner();
            arrMyRunners[i].id = i;
            arrThreads[i] = new Thread(arrMyRunners[i]);
            arrThreads[i].setDaemon(true);
            arrThreads[i].start();
        }
        Thread.sleep(500);
        for (int i = 0; i < arrMyRunners.length; i++)
        {
            synchronized(arrMyRunners[i])
            {
                arrMyRunners[i].notify();
            }
        }
    }
    
    private static void notifyTest3() throws InterruptedException
    {
        MyRunner runner = new MyRunner();
        Thread[] arrThreads = new Thread[3];
        for (int i = 0; i < arrThreads.length; i++)
        {
            arrThreads[i] = new Thread(runner);
            arrThreads[i].setDaemon(true);
            arrThreads[i].start();
        }
        Thread.sleep(500);

        synchronized(runner)
        {
            runner.notifyAll();
        }
    }
}

class MyThread extends Thread
{
    public int id = 0;
    public void run()
    {
        System.out.println("第" + id + "个线程准备休眠5分钟。");
        try
        {
            synchronized(this)
            {
                this.wait(5*60*1000);
            }
        }
        catch(InterruptedException ex)
        {
            ex.printStackTrace();
        }
        System.out.println("第" + id + "个线程被唤醒。");
    }
}

class MyRunner implements Runnable
{
    public int id = 0;
    public void run() 
    {
        System.out.println("第" + id + "个线程准备休眠5分钟。");
        try
        {
            synchronized(this)
            {
                this.wait(5*60*1000);
            }
        }
        catch(InterruptedException ex)
        {
            ex.printStackTrace();
        }
        System.out.println("第" + id + "个线程被唤醒。");
    }
    
}
```

示例代码中，notifyTest()和notifyTest2()是“多对象多线程”，尽管notifyTest2()中的线程实现了Runnable接口，但是它里面定义Thread数组时，每个元素都使用了一个新的Runnable实例。notifyTest3()属于“单对象多线程”，因为我们只定义了一个Runnable实例，所有的线程都会使用这个实例。

notifyAll方法适用于“单对象多线程”的情景，因为notify方法只会随机唤醒对象上的一个线程。

## 线程的状态切换
对于线程来讲，从我们创建它一直到线程运行结束，在这个过程中，线程的状态可能是这样的：

- 创建：已经有Thread实例了， 但是CPU还有为其分配资源和时间片。
- 就绪：线程已经获得了运行所需的所有资源，只等CPU进行时间调度。
- 运行：线程位于当前CPU时间片中，正在执行相关逻辑。
- 休眠：一般是调用Thread.sleep后的状态，这时线程依然持有运行所需的各种资源，但是不会被CPU调度。
- 挂起：一般是调用Thread.suspend后的状态，和休眠类似，CPU不会调度该线程，不同的是，这种状态下，线程会释放所有资源。
- 死亡：线程运行结束或者调用了Thread.stop方法。
　　

下面我们来演示如何进行线程状态切换，首先我们会用到下面方法：

- Thread()或者Thread(Runnable)：构造线程。
- Thread.start：启动线程。
- Thread.sleep：将线程切换至休眠状态。
- Thread.interrupt：中断线程的执行。
- Thread.join：等待某线程结束。
- Thread.yield：剥夺线程在CPU上的执行时间片，等待下一次调度。
- Object.wait：将Object上所有线程锁定，直到notify方法才继续运行。
- Object.notify：随机唤醒Object上的1个线程。
- Object.notifyAll：唤醒Object上的所有线程。
　　下面，就是演示时间啦！！！

## 线程等待与唤醒

这里主要使用Object.wait和Object.notify方法，请参见上面的notify实例。需要注意的是，wait和notify都必须针对同一个对象，当我们使用实现Runnable接口的方式来创建线程时，应该是在Runnable对象而非Thread对象上使用这两个方法。

## 线程的休眠与唤醒
Thread.sleep实例
线程在休眠过程中，我们可以使用Thread.interrupt将其唤醒，这时线程会抛出InterruptedException。

### Thread.sleep实例
```java
public class SleepSample {
    
    public static void main(String[] args) throws InterruptedException
    {
        sleepTest();
    }
    
    private static void sleepTest() throws InterruptedException
    {
        Thread thread = new Thread()
        {
            public void run()
            {
                System.out.println("线程 " + Thread.currentThread().getName() + "将要休眠5分钟。");
                try
                {
                    Thread.sleep(5*60*1000);
                }
                catch(InterruptedException ex)
                {
                    System.out.println("线程 " + Thread.currentThread().getName() + "休眠被中断。");
                }
                System.out.println("线程 " + Thread.currentThread().getName() + "休眠结束。");
            }
        };
        thread.setDaemon(true);
        thread.start();
        Thread.sleep(500);
        thread.interrupt();
    }

}
```

## 线程的终止

虽然有Thread.stop方法，但该方法是不被推荐使用的，我们可以利用上面休眠与唤醒的机制，让线程在处理IterruptedException时，结束线程。

### Thread.interrupt示例

```java
public class StopThreadSample {

    public static void main(String[] args) throws InterruptedException
    {
        stopTest();
    }
    
    private static void stopTest() throws InterruptedException
    {
        Thread thread = new Thread()
        {
            public void run()
            {
                System.out.println("线程运行中。");
                try
                {
                    Thread.sleep(1*60*1000);
                }
                catch(InterruptedException ex)
                {
                    System.out.println("线程中断，结束线程");
                    return;
                }
                System.out.println("线程正常结束。");
            }
        };
        thread.start();
        Thread.sleep(500);
        thread.interrupt();
    }
}
```

## 线程的同步等待

当我们在主线程中创建了10个子线程，然后我们期望10个子线程全部结束后，主线程在执行接下来的逻辑，这时，就该Thread.join登场了。

### Thread.join示例
```java
public class JoinSample {

    public static void main(String[] args) throws InterruptedException
    {
        joinTest();
    }
    
    private static void joinTest() throws InterruptedException
    {
        Thread thread = new Thread()
        {
            public void run()
            {
                try
                {
                    for(int i = 0; i < 5; i++)
                    {
                        System.out.println("线程在运行。");
                        Thread.sleep(1000);
                    }
                }
                catch(InterruptedException ex)
                {
                    ex.printStackTrace();
                }
            }
        };
        thread.setDaemon(true);
        thread.start();
        Thread.sleep(1000);
        thread.join();
        System.out.println("主线程正常结束。");
    }
}
```

我们可以试着将thread.join();注释或者删除，再次运行程序，就可以发现不同了。

## 线程间通信

我们知道，一个进程下面的所有线程是共享内存空间的，那么我们如何在不同的线程之间传递消息呢？在回顾 Java I/O时，我们谈到了PipedStream和PipedReader，这里，就是它们发挥作用的地方了。

下面的两个示例，功能完全一样，不同的是一个使用Stream，一个使用Reader/Writer。

### PipeInputStream/PipedOutpueStream 示例
```java
public static void communicationTest() throws IOException, InterruptedException
{
    final PipedOutputStream pos = new PipedOutputStream();
    final PipedInputStream pis = new PipedInputStream(pos);
    
    Thread thread1 = new Thread()
    {
        public void run()
        {
            BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
            try
            {
                while(true)
                {
                    String message = br.readLine();
                    pos.write(message.getBytes());
                    if (message.equals("end")) break;
                }
                br.close();
                pos.close();
            }
            catch(Exception ex)
            {
                ex.printStackTrace();
            }
        }
    };
    
    Thread thread2 = new Thread()
    {
        public void run()
        {
            byte[] buffer = new byte[1024];
            int bytesRead = 0;
            try
            {
                while((bytesRead = pis.read(buffer, 0, buffer.length)) != -1)
                {
                    System.out.println(new String(buffer));
                    if (new String(buffer).equals("end")) break;
                    buffer = null;
                    buffer = new byte[1024];
                }
                pis.close();
                buffer = null;
            }
            catch(Exception ex)
            {
                ex.printStackTrace();
            }
        }
    };
    
    thread1.setDaemon(true);
    thread2.setDaemon(true);
    thread1.start();
    thread2.start();
    thread1.join();
    thread2.join();
}
```

### PipedReader/PipedWriter 示例
```java
private static void communicationTest2() throws InterruptedException, IOException
{
    final PipedWriter pw = new PipedWriter();
    final PipedReader pr = new PipedReader(pw);
    final BufferedWriter bw = new BufferedWriter(pw);
    final BufferedReader br = new BufferedReader(pr);
    
    Thread thread1 = new Thread()
    {
        public void run()
        {
            
            BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
            try
            {
                while(true)
                {
                    String message = br.readLine();
                    bw.write(message);
                    bw.newLine();
                    bw.flush();
                    if (message.equals("end")) break;
                }
                br.close();
                pw.close();
                bw.close();
            }
            catch(Exception ex)
            {
                ex.printStackTrace();
            }
        }
    };
    
    Thread thread2 = new Thread()
    {
        public void run()
        {
            
            String line = null;
            try
            {
                while((line = br.readLine()) != null)
                {
                    System.out.println(line);
                    if (line.equals("end")) break;
                }
                br.close();
                pr.close();
            }
            catch(Exception ex)
            {
                ex.printStackTrace();
            }
        }
    };
    
    thread1.setDaemon(true);
    thread2.setDaemon(true);
    thread1.start();
    thread2.start();
    thread1.join();
    thread2.join();
}
```
# Java多线程同步

线程同步话题。这是比多线程更复杂，稍不留意，我们就会“掉到坑里”，而且和单线程程序不同，多线程的错误是否每次都出现，也是不固定的，这给调试也带来了很大的挑战。

我们首先阐述什么是同步，不同步有什么问题，然后讨论可以采取哪些措施控制同步，接下来我们会仿照回顾网络通信时那样，构建一个服务器端的“线程池”，JDK为我们提供了一个很大的concurrent工具包，最后我们会对里面的内容进行探索。

## 为什么要线程同步？

说到线程同步，大部分情况下，我们是在针对“单对象多线程”的情况进行讨论，一般会将其分成两部分，一部分是关于“共享变量”，一部分关于“执行步骤”。

### 共享变量

当我们在线程对象（Runnable）中定义了全局变量，run方法会修改该变量时，如果有多个线程同时使用该线程对象，那么就会造成全局变量的值被同时修改，造成错误。我们来看下面的代码：

#### 共享变量造成同步问题

```java
class MyRunner implements Runnable
{
    public int sum = 0;
    
    public void run() 
    {
        System.out.println(Thread.currentThread().getName() + " Start.");
        for (int i = 1; i <= 100; i++)
        {
            sum += i;
        }
        try {
            Thread.sleep(500);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println(Thread.currentThread().getName() + " --- The value of sum is " + sum);
        System.out.println(Thread.currentThread().getName() + " End.");
    }
}


private static void sharedVaribleTest() throws InterruptedException
{
    MyRunner runner = new MyRunner();
    Thread thread1 = new Thread(runner);
    Thread thread2 = new Thread(runner);
    thread1.setDaemon(true);
    thread2.setDaemon(true);
    thread1.start();
    thread2.start();
    thread1.join();
    thread2.join();
}
```

这个示例中，线程用来计算1到100的和是多少，我们知道正确结果是5050（好像是高斯小时候玩过这个？），但是上述程序返回的结果是10100，原因是两个线程同时对sum进行操作。

### 执行步骤
我们在多个线程运行时，可能需要某些操作合在一起作为“原子操作”，即在这些操作可以看做是“单线程”的，例如我们可能希望输出结果的样子是这样的：

```
1 线程1：步骤1
2 线程1：步骤2
3 线程1：步骤3
4 线程2：步骤1
5 线程2：步骤2
6 线程2：步骤3
```


　　如果同步控制不好，出来的样子可能是这样的：

```
线程1：步骤1
线程2：步骤1
线程1：步骤2
线程2：步骤2
线程1：步骤3
线程2：步骤3
```

这里我们也给出一个示例代码：

### 执行步骤混乱带来的同步问题
```java
class MyNonSyncRunner implements Runnable
{
    public void run() {
        System.out.println(Thread.currentThread().getName() + " Start.");
        for(int i = 1; i <= 5; i++)
        {
            System.out.println(Thread.currentThread().getName() + " Running step " + i);
            try
            {
                Thread.sleep(50);
            }
            catch(InterruptedException ex)
            {
                ex.printStackTrace();
            }
        }
        System.out.println(Thread.currentThread().getName() + " End.");
    }
}


private static void syncTest() throws InterruptedException
{
    MyNonSyncRunner runner = new MyNonSyncRunner();
    Thread thread1 = new Thread(runner);
    Thread thread2 = new Thread(runner);
    thread1.setDaemon(true);
    thread2.setDaemon(true);
    thread1.start();
    thread2.start();
    thread1.join();
    thread2.join();
}
```

## 如何控制线程同步

既然线程同步有上述问题，那么我们应该如何去解决呢？针对不同原因造成的同步问题，我们可以采取不同的策略。

### 控制共享变量
我们可以采取3种方式来控制共享变量。

#### 将“单对象多线程”修改成“多对象多线程”　　
上文提及，同步问题一般发生在“单对象多线程”的场景中，那么最简单的处理方式就是将运行模型修改成“多对象多线程”的样子，针对上面示例中的同步问题，修改后的代码如下：

#### 解决共享变量问题方案一
```java
private static void sharedVaribleTest2() throws InterruptedException
{
    Thread thread1 = new Thread(new MyRunner());
    Thread thread2 = new Thread(new MyRunner());
    thread1.setDaemon(true);
    thread2.setDaemon(true);
    thread1.start();
    thread2.start();
    thread1.join();
    thread2.join();
}
```
我们可以看到，上述代码中两个线程使用了两个不同的Runnable实例，它们在运行过程中，就不会去访问同一个全局变量。

#### 将“全局变量”降级为“局部变量”
既然是共享变量造成的问题，那么我们可以将共享变量改为“不共享”，即将其修改为局部变量。这样也可以解决问题，同样针对上面的示例，这种解决方式的代码如下：

#### 解决共享变量问题方案二
```java
class MyRunner2 implements Runnable
{
    public void run() 
    {
        System.out.println(Thread.currentThread().getName() + " Start.");
        int sum = 0;
        for (int i = 1; i <= 100; i++)
        {
            sum += i;
        }
        try {
            Thread.sleep(500);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println(Thread.currentThread().getName() + " --- The value of sum is " + sum);
        System.out.println(Thread.currentThread().getName() + " End.");
    }
}


private static void sharedVaribleTest3() throws InterruptedException
{
    MyRunner2 runner = new MyRunner2();
    Thread thread1 = new Thread(runner);
    Thread thread2 = new Thread(runner);
    thread1.setDaemon(true);
    thread2.setDaemon(true);
    thread1.start();
    thread2.start();
    thread1.join();
    thread2.join();
}
```

我们可以看出，sum变量已经由全局变量变为run方法内部的局部变量了。

#### 使用ThreadLocal机制

ThreadLocal是JDK引入的一种机制，它用于解决线程间共享变量，使用ThreadLocal声明的变量，即使在线程中属于全局变量，针对每个线程来讲，这个变量也是独立的。

我们可以用这种方式来改造上面的代码，如下所示：

#### 解决共享变量问题方案三
```java
class MyRunner3 implements Runnable
{
    public ThreadLocal<Integer> tl = new ThreadLocal<Integer>();
    
    public void run() 
    {
        System.out.println(Thread.currentThread().getName() + " Start.");
        for (int i = 0; i <= 100; i++)
        {
            if (tl.get() == null)
            {
                tl.set(new Integer(0));
            }
            int sum = ((Integer)tl.get()).intValue();
            sum+= i;
            tl.set(new Integer(sum));
            try {
                Thread.sleep(10);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
        
        System.out.println(Thread.currentThread().getName() + " --- The value of sum is " + ((Integer)tl.get()).intValue());
        System.out.println(Thread.currentThread().getName() + " End.");
    }
}


private static void sharedVaribleTest4() throws InterruptedException
{
    MyRunner3 runner = new MyRunner3();
    Thread thread1 = new Thread(runner);
    Thread thread2 = new Thread(runner);
    thread1.setDaemon(true);
    thread2.setDaemon(true);
    thread1.start();
    thread2.start();
    thread1.join();
    thread2.join();
}
```

综上三种方案，第一种方案会降低多线程执行的效率，因此，我们推荐使用第二种或者第三种方案。

### 控制执行步骤

说到执行步骤，我们可以使用synchronized关键字来解决它。

#### 执行步骤问题解决方案

```java
class MySyncRunner implements Runnable
{
    public void run() {
        synchronized(this)
        {
            System.out.println(Thread.currentThread().getName() + " Start.");
            for(int i = 1; i <= 5; i++)
            {
                System.out.println(Thread.currentThread().getName() + " Running step " + i);
                try
                {
                    Thread.sleep(50);
                }
                catch(InterruptedException ex)
                {
                    ex.printStackTrace();
                }
            }
            System.out.println(Thread.currentThread().getName() + " End.");
        }
    }
}


private static void syncTest2() throws InterruptedException
{
    MySyncRunner runner = new MySyncRunner();
    Thread thread1 = new Thread(runner);
    Thread thread2 = new Thread(runner);
    thread1.setDaemon(true);
    thread2.setDaemon(true);
    thread1.start();
    thread2.start();
    thread1.join();
    thread2.join();
}
```

在线程同步的话题上，synchronized是一个非常重要的关键字。它的原理和数据库中事务锁的原理类似。我们在使用过程中，应该尽量缩减synchronized覆盖的范围,原因有二：

1. 被它覆盖的范围是串行的，效率低；
2. 容易产生死锁。

我们来看下面的示例：


#### synchronized示例
```java
private static void syncTest3() throws InterruptedException
{
    final List<Integer> list = new ArrayList<Integer>();
    
    Thread thread1 = new Thread()
    {
        public void run()
        {
            System.out.println(Thread.currentThread().getName() + " Start.");
            Random r = new Random(100);
            synchronized(list)
            {
                for (int i = 0; i < 5; i++)
                {
                    list.add(new Integer(r.nextInt()));
                }
                System.out.println("The size of list is " + list.size());
            }
            try
            {
                Thread.sleep(500);
            }
            catch(InterruptedException ex)
            {
                ex.printStackTrace();
            }
            System.out.println(Thread.currentThread().getName() + " End.");
        }
    };
    
    Thread thread2 = new Thread()
    {
        public void run()
        {
            System.out.println(Thread.currentThread().getName() + " Start.");
            Random r = new Random(100);
            synchronized(list)
            {
                for (int i = 0; i < 5; i++)
                {
                    list.add(new Integer(r.nextInt()));
                }
                System.out.println("The size of list is " + list.size());
            }
            try
            {
                Thread.sleep(500);
            }
            catch(InterruptedException ex)
            {
                ex.printStackTrace();
            }
            System.out.println(Thread.currentThread().getName() + " End.");
        }
    };
    
    thread1.start();
    thread2.start();
    thread1.join();
    thread2.join();
}
```

　　我们应该把需要同步的内容集中在一起，尽量不包含其他不相关的、消耗大量资源的操作，示例中线程休眠的操作显然不应该包括在里面。

## 构造线程池
我们在Java网络通信中，已经构建了一个Socket连接池，这里我们在此基础上，构建一个线程池，完成基本的启动、休眠、唤醒、停止操作。

基本思路还是以数组的形式保持一系列线程，通过Socket通信，客户端向服务器端发送命令，当服务器端接收到命令后，根据收到的命令对线程数组中的线程进行操作。

Socket客户端的代码保持不变，依然采用构建Socket连接池时的代码，我们主要针对服务器端进行改造。

首先，我们需要定义一个线程对象，它用来执行我们的业务操作，这里简化起见，只让线程进行休眠。


### 定义线程对象
```java
enum ThreadStatus
{
    Initial,
    Running,
    Sleeping,
    Stopped
}

enum ThreadTask
{
    Start,
    Stop,
    Sleep,
    Wakeup
}


class MyThread extends Thread
{
    public ThreadStatus status = ThreadStatus.Initial;
    public ThreadTask task;
    public void run()
    {
        status = ThreadStatus.Running;
        while(true)
        {
            try {
                Thread.sleep(3000);
                if (status == ThreadStatus.Sleeping)
                {
                    System.out.println(Thread.currentThread().getName() + " 进入休眠状态。");
                    this.wait();
                }
            } catch (InterruptedException e) {
                System.out.println(Thread.currentThread().getName() + " 运行过程中出现错误。");
                status = ThreadStatus.Stopped;
            }
        }
    }
}
```

然后，我们需要定义一个线程管理器，它用来对线程池中的线程进行管理，代码如下：

### 定义线程池管理对象

```java
class MyThreadManager
{
    public static void manageThread(MyThread[] threads, ThreadTask task)
    {
        for (int i = 0; i < threads.length; i++)
        {
            synchronized(threads[i])
            {
                manageThread(threads[i], task);
            }
        }
        System.out.println(getThreadStatus(threads));
    }
    
    public static void manageThread(MyThread thread, ThreadTask task)
    {
        if (task == ThreadTask.Start)
        {
            if (thread.status == ThreadStatus.Running)
            {
                return;
            }
            if (thread.status == ThreadStatus.Stopped)
            {
                thread = new MyThread();
            }
            thread.status = ThreadStatus.Running;
            thread.start();
            
        }
        else if (task == ThreadTask.Stop)
        {
            if (thread.status != ThreadStatus.Stopped)
            {
                thread.interrupt();
                thread.status = ThreadStatus.Stopped;
            }
        }
        else if (task == ThreadTask.Sleep)
        {
            thread.status = ThreadStatus.Sleeping;
        }
        else if (task == ThreadTask.Wakeup)
        {
            thread.notify();
            thread.status = ThreadStatus.Running;
        }
    }
    
    public static String getThreadStatus(MyThread[] threads)
    {
        StringBuffer sb = new StringBuffer();
        for (int i = 0; i < threads.length; i++)
        {
            sb.append(threads[i].getName() + "的状态：" + threads[i].status).append("\r\n");
        }
        return sb.toString();
    }
}
```

最后，是我们的服务器端，它不断接受客户端的请求，每收到一个连接请求，服务器端会新开一个线程，来处理后续客户端发来的各种操作指令。

### 定义服务器端线程池对象

```java
public class MyThreadPool {

    public static void main(String[] args) throws IOException
    {
        MyThreadPool pool = new MyThreadPool(5);
    }
    
    private int threadCount;
    private MyThread[] threads = null;
    
    
    public MyThreadPool(int count) throws IOException
    {
        this.threadCount = count;
        threads = new MyThread[count];
        for (int i = 0; i < threads.length; i++)
        {
            threads[i] = new MyThread();
            threads[i].start();
        }
        Init();
    }
    
    private void Init() throws IOException
    {
        ServerSocket serverSocket = new ServerSocket(5678);
        while(true)
        {
            final Socket socket = serverSocket.accept();
            Thread thread = new Thread()
            {
                public void run()
                {
                    try
                    {
                        System.out.println("检测到一个新的Socket连接。");
                        BufferedReader br = new BufferedReader(new InputStreamReader(socket.getInputStream()));
                        PrintStream ps = new PrintStream(socket.getOutputStream());
                        String line = null;
                        while((line = br.readLine()) != null)
                        {
                            System.out.println(line);
                            if (line.equals("Count"))
                            {
                                System.out.println("线程池中有5个线程");
                            }
                            else if (line.equals("Status"))
                            {
                                String status = MyThreadManager.getThreadStatus(threads);
                                System.out.println(status);
                            }
                            else if (line.equals("StartAll"))
                            {
                                MyThreadManager.manageThread(threads, ThreadTask.Start);
                            }
                            else if (line.equals("StopAll"))
                            {
                                MyThreadManager.manageThread(threads, ThreadTask.Stop);
                            }
                            else if (line.equals("SleepAll"))
                            {
                                MyThreadManager.manageThread(threads, ThreadTask.Sleep);
                            }
                            else if (line.equals("WakeupAll"))
                            {
                                MyThreadManager.manageThread(threads, ThreadTask.Wakeup);
                            }
                            else if (line.equals("End"))
                            {
                                break;
                            }
                            else
                            {
                                System.out.println("Command:" + line);
                            }
                            ps.println("OK");
                            ps.flush();
                        }
                    }
                    catch(Exception ex)
                    {
                        ex.printStackTrace();
                    }
                }
            };
            thread.start();
        }
    }
}
```

## 探索JDK中的concurrent工具包

为了简化开发人员在进行多线程开发时的工作量，并减少程序中的bug，JDK提供了一套concurrent工具包，我们可以用它来方便的开发多线程程序。

### 线程池　　
我们在上面实现了一个非常“简陋”的线程池，concurrent工具包中也提供了线程池，而且使用非常方便。

concurrent工具包中的线程池分为3类：ScheduledThreadPool、FixedThreadPool和CachedThreadPool。

首先我们来定义一个Runnable的对象

#### 定义Runnable对象

```java
class MyRunner implements Runnable
{
    public void run() {
        System.out.println(Thread.currentThread().getName() + "运行开始");
        for(int i = 0; i < 1; i++)
        {
            try
            {
                System.out.println(Thread.currentThread().getName() + "正在运行");
                Thread.sleep(200);
            }
            catch(Exception ex)
            {
                ex.printStackTrace();
            }
        }
        System.out.println(Thread.currentThread().getName() + "运行结束");
    }
}
```

可以看出，它的功能非常简单，只是输出了线程的执行过程。

#### ScheduledThreadPool
这和我们平时使用的ScheduledTask比较类似，或者说很像Timer，它可以使得一个线程在指定的一段时间内开始运行，并且在间隔另外一段时间后再次运行，直到线程池关闭。

##### ScheduledThreadPool示例

```java
ScheduledThreadPool示例
private static void scheduledThreadPoolTest()
{
    final ScheduledExecutorService scheduler = Executors.newScheduledThreadPool(3);
    
    MyRunner runner = new MyRunner();
    
    final ScheduledFuture<?> handler1 = scheduler.scheduleAtFixedRate(runner, 1, 10, TimeUnit.SECONDS);
    final ScheduledFuture<?> handler2 = scheduler.scheduleWithFixedDelay(runner, 2, 10, TimeUnit.SECONDS);
    
    scheduler.schedule(new Runnable()
    {
        public void run()
        {
            handler1.cancel(true);
            handler2.cancel(true);
            scheduler.shutdown();
        }
    }, 30, TimeUnit.SECONDS
    );
}
```

#### FixedThreadPool
这是一个指定容量的线程池，即我们可以指定在同一时间，线程池中最多有多个线程在运行，超出的线程，需要等线程池中有空闲线程时，才能有机会运行。

##### FixedThreadPool示例
```java
private static void fixedThreadPoolTest()
{
    ExecutorService exec = Executors.newFixedThreadPool(3);
    for(int i = 0; i < 5; i++)
    {
        MyRunner runner = new MyRunner();
        exec.execute(runner);
    }
    exec.shutdown();
}
```

注意它的输出结果：


```
pool-1-thread-1运行开始
pool-1-thread-1正在运行
pool-1-thread-2运行开始
pool-1-thread-2正在运行
pool-1-thread-3运行开始
pool-1-thread-3正在运行
pool-1-thread-1运行结束
pool-1-thread-1运行开始
pool-1-thread-1正在运行
pool-1-thread-2运行结束
pool-1-thread-2运行开始
pool-1-thread-2正在运行
pool-1-thread-3运行结束
pool-1-thread-1运行结束
pool-1-thread-2运行结束
```

可以看到从始至终，最多有3个线程在同时运行。

#### CachedThreadPool

这是另外一种线程池，它不需要指定容量，只要有需要，它就会创建新的线程。

它的使用方式和FixedThreadPool非常像，来看下面的代码：

##### CachedThreadPool示例

```java

private static void cachedThreadPoolTest()
{
    ExecutorService exec = Executors.newCachedThreadPool();
    for(int i = 0; i < 5; i++)
    {
        MyRunner runner = new MyRunner();
        exec.execute(runner);
    }
    exec.shutdown();
}
```

　　它的执行结果如下：


```
pool-1-thread-1运行开始
pool-1-thread-1正在运行
pool-1-thread-2运行开始
pool-1-thread-2正在运行
pool-1-thread-3运行开始
pool-1-thread-3正在运行
pool-1-thread-4运行开始
pool-1-thread-4正在运行
pool-1-thread-5运行开始
pool-1-thread-5正在运行
pool-1-thread-1运行结束
pool-1-thread-2运行结束
pool-1-thread-3运行结束
pool-1-thread-4运行结束
pool-1-thread-5运行结束
```

可以看到，它创建了5个线程。

#### 处理线程返回值

在有些情况下，我们需要使用线程的返回值，在上述的所有代码中，线程这是执行了某些操作，没有任何返回值。

如何做到这一点呢？我们可以使用JDK中的Callable<T>和CompletionService<T>，前者返回单个线程的结果，后者返回一组线程的结果。

#### 返回单个线程的结果
　　还是直接看代码吧：

##### Callable示例

```java
private static void callableTest() throws InterruptedException, ExecutionException
{
    ExecutorService exec = Executors.newFixedThreadPool(1);
    Callable<String> call = new Callable<String>()
    {
        public String call()
        {
            return "Hello World.";
        }
    };
    Future<String> result = exec.submit(call);
    System.out.println("线程的返回值是" + result.get());
    exec.shutdown();
}
```

线程的返回值是Hello World.

返回线程池中每个线程的结果

这里需要使用CompletionService<T>，代码如下：

##### CompletionService示例

```java
private static void completionServiceTest() throws InterruptedException, ExecutionException
{
    ExecutorService exec = Executors.newFixedThreadPool(10);
    CompletionService<String> service = new ExecutorCompletionService<String>(exec);
    for (int i = 0; i < 10; i++)
    {
        Callable<String> call = new Callable<String>()
        {
            public String call() throws InterruptedException
            {
                return Thread.currentThread().getName();
            }
        };
        service.submit(call);
    }
    
    Thread.sleep(1000);
    for(int i = 0; i < 10; i++)
    {
        Future<String> result = service.take();
        System.out.println("线程的返回值是" + result.get());
    }
    exec.shutdown();
}
```

　　执行结果如下：

```
线程的返回值是pool-2-thread-1
线程的返回值是pool-2-thread-2
线程的返回值是pool-2-thread-3
线程的返回值是pool-2-thread-5
线程的返回值是pool-2-thread-4
线程的返回值是pool-2-thread-6
线程的返回值是pool-2-thread-8
线程的返回值是pool-2-thread-7
线程的返回值是pool-2-thread-9
线程的返回值是pool-2-thread-10
```

#### 实现生产者-消费者模型

对于生产者-消费者模型来说，我们应该都不会陌生，通常我们都会使用某种数据结构来实现它。在concurrent工具包中，我们可以使用BlockingQueue来实现生产者-消费者模型，如下：

##### BlockingQueue示例
```
public class BlockingQueueSample {

    public static void main(String[] args)
    {
        blockingQueueTest();
    }
    
    private static void blockingQueueTest()
    {
        final BlockingQueue<Integer> queue = new LinkedBlockingQueue<Integer>();
        final int maxSleepTimeForSetter = 10;
        final int maxSleepTimerForGetter = 10;
        
        Runnable setter = new Runnable()
        {
            public void run()
            {
                Random r = new Random();
                while(true)
                {
                    int value = r.nextInt(100);
                    try
                    {
                        queue.put(new Integer(value));
                        System.out.println(Thread.currentThread().getName() + "---向队列中插入值" + value);
                        Thread.sleep(r.nextInt(maxSleepTimeForSetter) * 1000);
                    }
                    catch(Exception ex)
                    {
                        ex.printStackTrace();
                    }
                }
            }
        };
        
        Runnable getter = new Runnable()
        {
            public void run()
            {
                Random r = new Random();
                while(true)
                {
                    try
                    {
                        if (queue.size() == 0)
                        {
                            System.out.println(Thread.currentThread().getName() + "---队列为空");
                        }
                        else
                        {
                            int value = queue.take().intValue();
                            System.out.println(Thread.currentThread().getName() + "---从队列中获取值" + value);
                        }
                        Thread.sleep(r.nextInt(maxSleepTimerForGetter) * 1000);
                    }
                    catch(Exception ex)
                    {
                        ex.printStackTrace();
                    }
                }
            }
        };
        
        ExecutorService exec = Executors.newFixedThreadPool(2);
        exec.execute(setter);
        exec.execute(getter);
    }
}
```

我们定义了两个线程，一个线程向Queue中添加数据，一个线程从Queue中取数据。我们可以通过控制maxSleepTimeForSetter和maxSleepTimerForGetter的值，来使得程序得出不同的结果。

可能的执行结果如下：

```
pool-1-thread-1---向队列中插入值88
pool-1-thread-2---从队列中获取值88
pool-1-thread-1---向队列中插入值75
pool-1-thread-2---从队列中获取值75
pool-1-thread-2---队列为空
pool-1-thread-2---队列为空
pool-1-thread-2---队列为空
pool-1-thread-1---向队列中插入值50
pool-1-thread-2---从队列中获取值50
pool-1-thread-2---队列为空
pool-1-thread-2---队列为空
pool-1-thread-2---队列为空
pool-1-thread-2---队列为空
pool-1-thread-2---队列为空
pool-1-thread-1---向队列中插入值51
pool-1-thread-1---向队列中插入值92
pool-1-thread-2---从队列中获取值51
pool-1-thread-2---从队列中获取值92
```

因为Queue中的值和Thread的休眠时间都是随机的，所以执行结果也不是固定的。

#### 使用信号量来控制线程
JDK提供了Semaphore来实现“信号量”的功能，它提供了两个方法分别用于获取和释放信号量：acquire和release，示例代码如下：

##### SemaPhore示例
```java
private static void semaphoreTest()
{
    ExecutorService exec = Executors.newFixedThreadPool(10);
    final Semaphore semp = new Semaphore(2);
    
    for (int i = 0; i < 10; i++)
    {
        Runnable runner = new Runnable()
        {
            public void run()
            {
                try
                {
                    semp.acquire();
                    System.out.println(new Date() + " " + Thread.currentThread().getName() + "正在执行。");
                    Thread.sleep(5000);
                    semp.release();
                }
                catch(Exception ex)
                {
                    ex.printStackTrace();
                }
            }
        };
        exec.execute(runner);
    }
    
    exec.shutdown();
}
```
执行结果如下：

```
Tue May 07 11:22:11 CST 2013 pool-1-thread-1正在执行。
Tue May 07 11:22:11 CST 2013 pool-1-thread-2正在执行。
Tue May 07 11:22:17 CST 2013 pool-1-thread-3正在执行。
Tue May 07 11:22:17 CST 2013 pool-1-thread-4正在执行。
Tue May 07 11:22:22 CST 2013 pool-1-thread-5正在执行。
Tue May 07 11:22:22 CST 2013 pool-1-thread-6正在执行。
Tue May 07 11:22:27 CST 2013 pool-1-thread-7正在执行。
Tue May 07 11:22:27 CST 2013 pool-1-thread-8正在执行。
Tue May 07 11:22:32 CST 2013 pool-1-thread-10正在执行。
Tue May 07 11:22:32 CST 2013 pool-1-thread-9正在执行。
```

可以看出，尽管线程池中创建了10个线程，但是同时运行的，只有2个线程。

#### 控制线程池中所有线程的执行步骤

在前面，我们已经提到，可以用synchronized关键字来控制单个线程中的执行步骤，那么如果我们想要对线程池中的所有线程的执行步骤进行控制的话，应该如何实现呢？

我们有两种方式，一种是使用CyclicBarrier，一种是使用CountDownLatch。

CyclicBarrier使用了类似于Object.wait的机制，它的构造函数中需要接收一个整型数字，用来说明它需要控制的线程数目，当在线程的run方法中调用它的await方法时，它会保证所有的线程都执行到这一步，才会继续执行后面的步骤。

示例代码如下：

##### CyclicBarrier示例

```java
class MyRunner2 implements Runnable
{
    private CyclicBarrier barrier = null;
    public MyRunner2(CyclicBarrier barrier)
    {
        this.barrier = barrier;
    }
    
    public void run() {
        Random r = new Random();
        try
        {
            for (int i = 0; i < 3; i++)
            {
                Thread.sleep(r.nextInt(10) * 1000);
                System.out.println(new Date() + "--" + Thread.currentThread().getName() + "--第" + (i + 1) + "次等待。");
                barrier.await();
            }
        }
        catch(Exception ex)
        {
            ex.printStackTrace();
        }
    }
    
}

private static void cyclicBarrierTest()
{
    CyclicBarrier barrier = new CyclicBarrier(3);
    
    ExecutorService exec = Executors.newFixedThreadPool(3);
    for (int i = 0; i < 3; i++)
    {
        exec.execute(new MyRunner2(barrier));
    }
    exec.shutdown();
}
```

执行结果如下：

```
Tue May 07 11:31:20 CST 2013--pool-1-thread-2--第1次等待。
Tue May 07 11:31:21 CST 2013--pool-1-thread-3--第1次等待。
Tue May 07 11:31:24 CST 2013--pool-1-thread-1--第1次等待。
Tue May 07 11:31:24 CST 2013--pool-1-thread-1--第2次等待。
Tue May 07 11:31:26 CST 2013--pool-1-thread-3--第2次等待。
Tue May 07 11:31:30 CST 2013--pool-1-thread-2--第2次等待。
Tue May 07 11:31:32 CST 2013--pool-1-thread-1--第3次等待。
Tue May 07 11:31:33 CST 2013--pool-1-thread-3--第3次等待。
Tue May 07 11:31:33 CST 2013--pool-1-thread-2--第3次等待。
```

可以看出，thread-2到第1次等待点时，一直等到thread-1到达后才继续执行。

CountDownLatch则是采取类似”倒计时计数器”的机制来控制线程池中的线程，它有CountDown和Await两个方法。示例代码如下：

##### CountDownLatch示例

```java
private static void countdownLatchTest() throws InterruptedException
{
    final CountDownLatch begin = new CountDownLatch(1);
    final CountDownLatch end = new CountDownLatch(5);
    ExecutorService exec = Executors.newFixedThreadPool(5);
    for (int i = 0; i < 5; i++)
    {
        Runnable runner = new Runnable()
        {
            public void run()
            {
                Random r = new Random();
                try
                {
                    begin.await();
                    System.out.println(Thread.currentThread().getName() + "运行开始");
                    Thread.sleep(r.nextInt(10)*1000);
                    System.out.println(Thread.currentThread().getName() + "运行结束");
                }
                catch(Exception ex)
                {
                    ex.printStackTrace();
                }
                finally
                {
                    end.countDown();
                }
            }
        };
        exec.execute(runner);
    }
    begin.countDown();
    end.await();
    System.out.println(Thread.currentThread().getName() + "运行结束");
    exec.shutdown();
}
```

执行结果如下：

```
pool-1-thread-1运行开始
pool-1-thread-5运行开始
pool-1-thread-2运行开始
pool-1-thread-3运行开始
pool-1-thread-4运行开始
pool-1-thread-2运行结束
pool-1-thread-1运行结束
pool-1-thread-3运行结束
pool-1-thread-5运行结束
pool-1-thread-4运行结束
main运行结束
```
# Java集合

Java中的集合（Collection）。集合是编程语言中基础的一部分，Java自JDK早期，就引入了Java Collection Framework。设计JCF的那个人，后来还写了一本书，叫《Effective Java》。

Java中的集合主要集中在2部分，一部分是java.util包中，一部分是java.util.concurrent中，后者是在前者的基础上，定义了一些实现了同步功能的集合。

这篇文章主要关注java.util下的各种集合对象。Java中的集合对象可以粗略的分为3类：List、Set和Map。

完整清晰版图片请参见：http://files.cnblogs.com/wing011203/java_collection_structure.zip，解压缩后就可以看到未经缩放的版本。

## Collection概述
Java集合中的List和Set都从Collection出来，它是一个学习集合很不错的入口，它包含了集合中通常需要有的操作：

- 添加元素：add/addAll
- 清空集合：clear
- 删除元素：remove/removeAll
- 判断集合中是否包含某元素：contains/containsAll
- 判断集合是否为空：isEmpty
- 计算集合中元素的个数：size
- 将集合转换为数组：toArray
- 获取迭代器：iterator
　　

我们来看一个简单的例子，下面的代码会返回一个集合，集合中的元素是随机生成的整数：

```java
private static Collection initCollection()
{
    Collection<Integer> collection = new ArrayList<Integer>();
    Random r = new Random();
    for (int i = 0 ; i < 5; i++)
    {
        collection.add(new Integer(r.nextInt(100)));
    }
    
    return collection;
}
```
在对集合进行操作的过程中，遍历是一个经常使用的操作，我们可以使用两种方式对集合进行遍历：

1. 使用迭代器对集合进行遍历。正如上面描述Collection接口时所说，所有集合都会有一个迭代器，我们可以用它来遍历集合。


```java
private static void accessCollectionByIterator(Collection<Integer> collection)
{
    Iterator<Integer> iterator = collection.iterator();
    System.out.println("The value in the list:");
    while(iterator.hasNext())
    {
        System.out.println(iterator.next());
    }
}
```
2. 使用foreach遍历集合。

```java
private static void accessCollectionByFor(Collection<Integer> collection)
{
    System.out.println("The value in the list:");
    for(Integer value : collection)
    {
        System.out.println(value);
    }
}
```
## List
　　Java中的List是对数组的有效扩展，它是这样一种结构，如果不使用泛型，它可以容纳任何类型的元素，如果使用泛型，那么它只能容纳泛型指定的类型的元素。和数组相比，List的容量是可以动态扩展的。

　　List中的元素是可以重复的，里面的元素是“有序”的，这里的“有序”，并不是排序的意思，而是说我们可以对某个元素在集合中的位置进行指定。

　　List中常用的集合对象包括：ArrayList、Vector和LinkedList，其中前两者是基于数组来进行存储，后者是基于链表进行存储。其中Vector是线程安全的，其余两个不是线程安全的。

　　List中是可以包括null的，即使是使用了泛型。

　　ArrayList可能是我们平时用到的最多的集合对象了，在上述的示例代码中，我们也是使用它来实例化一个Collection对象，在此不再赘述。

## Vector
　　Vector的示例如下，首先我们看如何生成和输出Vector：

```java
private static void vectorTest1()
{
    List<Integer> list = new Vector<Integer>();
    for (int i = 0 ; i < 5; i++)
    {
        list.add(new Integer(100));
    }
    list.add(null);
    System.out.println("size of vector is " + list.size());
    System.out.println(list);
}
```
它的元素中，既包括了重复元素，也包括了null，输出结果如下：

```
size of vector is 6
[100, 100, 100, 100, 100, null]
```
下面的示例，演示了Vector中的一些常用方法：

```java
private static void vectorTest2()
{
    Vector<Integer> list = new Vector<Integer>();
    Random r = new Random();
    for (int i = 0 ; i < 10; i++)
    {
        list.add(new Integer(r.nextInt(100)));
    }
    System.out.println("size of vector is " + list.size());
    System.out.println(list);
    System.out.println(list.firstElement());
    System.out.println(list.lastElement());
    System.out.println(list.subList(3, 8));
    List<Integer> temp = new ArrayList<Integer>();
    for(int i = 4; i < 7; i++)
    {
        temp.add(list.get(i));
    }
    list.retainAll(temp);
    System.out.println("size of vector is " + list.size());
    System.out.println(list);
}
```
它的输出结果如下：

```
size of vector is 10
[39, 41, 20, 9, 29, 32, 54, 12, 94, 82]
39
82
[9, 29, 32, 54, 12]
size of vector is 3
[29, 32, 54]
```
## LinkedList
　　LinkedList使用链表来存储数据，它的示例代码如下：

```java
private static void linkedListTest1()
{
    LinkedList<Integer> list = new LinkedList<Integer>();
    Random r = new Random();
    for (int i = 0 ; i < 10; i++)
    {
        list.add(new Integer(r.nextInt(100)));
    }
    list.add(null);
    System.out.println("size of linked list is " + list.size());
    System.out.println(list);
    System.out.println(list.element());
    System.out.println(list.getFirst());
    System.out.println(list.getLast());
    System.out.println(list.peek());
    System.out.println(list.peekFirst());
    System.out.println(list.peekLast());
    System.out.println(list.poll());
    System.out.println(list.pollFirst());
    System.out.println(list.pollLast());
    System.out.println(list.pop());
    list.push(new Integer(100));
    System.out.println("size of linked list is " + list.size());
    System.out.println(list);
}

```
这里列出了LinkedList常用的各个方法，从方法名可以看出，LinkedList也可以用来实现栈和队列。

　　输出结果如下：

```
size of linked list is 11
[17, 21, 5, 84, 19, 57, 68, 26, 27, 47, null]
17
null
17
null
21
null
size of linked list is 8
[100, 84, 19, 57, 68, 26, 27, 47]
```
## Set
　　Set 和List类似，都是用来存储单个元素，单个元素的数量不确定。但Set不能包含重复元素，如果向Set中插入两个相同元素，那么后一个元素不会被插入。

　　Set可以大致分为两类：不排序Set和排序Set，不排序Set包括HashSet和LinkedHashSet，排序Set主要指TreeSet。其中HashSet和LinkedHashSet可以包含null。

### HashSet
　　**HashSet是由Hash表支持的一种集合，它不是线程安全的。**

　　我们来看下面的示例，它和Vector的第一个示例基本上是相同的：

```java
private static void hashSetTest1()
{
    Set<Integer> set = new HashSet<Integer>();
    
    for (int i = 0; i < 3; i++)
    {
        set.add(new Integer(100));
    }
    set.add(null);
    
    System.out.println("size of set is " + set.size());
    System.out.println(set);
}
```
　这里，**HashSet中没有包含重复元素，但包含了null，和Vector不同，**这里的输出结果如下：


```
size of set is 2
[null, 100]
```

　　对于HashSet是如何判断两个元素是否是重复的，我们可以深入考察一下。Object中也定义了equals方法，对于HashSet中的元素，它是根据equals方法来判断元素是否相等的，为了证明这一点，我们可以定义个“不正常”的类型：


```java
class MyInteger
{
    private Integer value;
    
    public MyInteger(Integer value)
    {
        this.value = value;
    }
    
    public String toString()
    {
        return String.valueOf(value);
    }
    
    public int hashCode()
    {
        return 1;
    }
    
    public boolean equals(Object obj)
    {
        return false;
    }
}
```
　可以看到，对于MyInteger来说，对于任意两个实例，我们都认为它是不相等的。

　　下面是对应的测试方法：
　　

```java
private static void hashSetTest2()
{
    Set<MyInteger> set = new HashSet<MyInteger>();
    
    for (int i = 0; i < 3; i++)
    {
        set.add(new MyInteger(100));
    }
    
    System.out.println("size of set is " + set.size());
    System.out.println(set);
}
```
它的输出结果如下：

```
size of set is 3
[100, 100, 100]
```

　　可以看到，现在HashSet里有“重复”元素了，但对于MyInteger来说，它们不是“相同”的。

### TreeSet

TreeSet是支持排序的一种Set，它的父接口是SortedSet。

　　我们首先来看一下TreeSet都有哪些基本操作：

```java
private static void treeSetTest1()
{
    TreeSet<Integer> set = new TreeSet<Integer>();
    
    Random r = new Random();
    for (int i = 0 ; i < 5; i++)
    {
        set.add(new Integer(r.nextInt(100)));
    }

    System.out.println(set);
    System.out.println(set.first());
    System.out.println(set.last());
    System.out.println(set.descendingSet());
    System.out.println(set.headSet(new Integer(50)));
    System.out.println(set.tailSet(new Integer(50)));
    System.out.println(set.subSet(30, 60));
    System.out.println(set.floor(50));
    System.out.println(set.ceiling(50));
}
```
它的输出结果如下：

```
[8, 42, 48, 49, 53]
8
53
[53, 49, 48, 42, 8]
[8, 42, 48, 49]
[53]
[42, 48, 49, 53]
49
53
```

　　TreeSet中的元素，一般都实现了Comparable接口，默认情况下，对于Integer来说，SortedList是采用升序来存储的，我们也可以自定义Compare方式，例如以降序的方式来存储。

　　下面，我们首先重新定义Integer：

#### 定义MyInteger2对象

```java
class MyInteger2 implements Comparable{
    public int value;
    
    public MyInteger2(int value)
    {
        this.value = value;
    }
    
    public int compareTo(Object arg0)  {
        MyInteger2 temp = (MyInteger2)arg0;
        if (temp == null) return -1;
        if (temp.value > this.value)
        {
            return 1;
        }
        else if (temp.value < this.value)
        {
            return -1;
        }
        return 0;
    }
    
    public boolean equals(Object obj){
        return compareTo(obj) == 0;
    }
    
    public String toString(){
        return String.valueOf(value);
    }
}
```
下面是测试代码：

```java
private static void treeSetTest2()
{
    TreeSet<Integer> set1 = new TreeSet<Integer>();
    TreeSet<MyInteger2> set2 = new TreeSet<MyInteger2>();
    Random r = new Random();
    for (int i = 0 ; i < 5; i++)
    {
        int value = r.nextInt(100);
        set1.add(new Integer(value));
        set2.add(new MyInteger2(value));
    }
    System.out.println("Set1 as below:");
    System.out.println(set1);
    System.out.println("Set2 as below:");
    System.out.println(set2);
}
```
代码的运行结果如我们所预期的那样，如下所示：

```
Set1 as below:
[13, 41, 42, 45, 61]
Set2 as below:
[61, 45, 42, 41, 13]
```
## Map
　　Map中存储的是“键值对”，和Set类似，Java中的Map也有两种：排序的和不排序的，不排序的包括HashMap、Hashtable和LinkedHashMap，排序的包括TreeMap。

### 非排序Map
　　HashMap和Hashtable都是采取Hash表的方式进行存储，HashMap不是线程安全的，Hashtable是线程安全的，我们可以把HashMap看做是“简化”版的Hashtable。

　　HashMap是可以存储null的，无论是对Key还是对Value。Hashtable是不可以存储null的。

　　无论HashMap还是Hashtable，我们观察它的构造函数，就会发现它可以有两个参数：initialCapacity和loadFactor，默认情况下，initialCapacity等于16，loadFactor等于0.75。这和Hash表中可以存放的元素数目有关系，当元素数目超过initialCapacity*loadFactor时，会触发rehash方法，对hash表进行扩容。如果我们需要向其中插入过多元素，需要适当调整这两个参数。

　　我们首先来看HashMap的示例：

```java
private static void hashMapTest1()
{
    Map<Integer,String> map = new HashMap<Integer, String>();
    
    map.put(new Integer(1), "a");
    map.put(new Integer(2), "b");
    map.put(new Integer(3), "c");
    
    System.out.println(map);
    System.out.println(map.entrySet());
    System.out.println(map.keySet());
    System.out.println(map.values());
}
```
　这会输出HashMap里的元素信息，如下所示。

```
{1=a, 2=b, 3=c}
[1=a, 2=b, 3=c]
[1, 2, 3]
[a, b, c]
```

　　下面的示例是对null的演示：
　　
```java
private static void hashMapTest2()
{
    Map<Integer,String> map = new HashMap<Integer, String>();
    
    map.put(null, null);
    map.put(null, null);
    map.put(new Integer(4), null);
    map.put(new Integer(5), null);
    
    System.out.println(map);
    System.out.println(map.entrySet());
    System.out.println(map.keySet());
    System.out.println(map.values());
}
```
执行结果如下：


```
{null=null, 4=null, 5=null}
[null=null, 4=null, 5=null]
[null, 4, 5]
[null, null, null]
```

接下来我们演示Hashtable，和上述两个示例基本上完全一样（代码不再展开）：

#### Hashtable示例

```java
private static void hashTableTest1()
{
    Map<Integer,String> table = new Hashtable<Integer, String>();
    
    table.put(new Integer(1), "a");
    table.put(new Integer(2), "b");
    table.put(new Integer(3), "c");
    
    System.out.println(table);
    System.out.println(table.entrySet());
    System.out.println(table.keySet());
    System.out.println(table.values());
}

private static void hashTableTest2()
{
    Map<Integer,String> table = new Hashtable<Integer, String>();
    
    table.put(null, null);
    table.put(null, null);
    table.put(new Integer(4), null);
    table.put(new Integer(5), null);
    
    System.out.println(table);
    System.out.println(table.entrySet());
    System.out.println(table.keySet());
    System.out.println(table.values());
}
```
执行结果如下：

```
{3=c, 2=b, 1=a}
[3=c, 2=b, 1=a]
[3, 2, 1]
[c, b, a]
Exception in thread "main" java.lang.NullPointerException
    at java.util.Hashtable.put(Unknown Source)
    at sample.collections.MapSample.hashTableTest2(MapSample.java:61)
    at sample.collections.MapSample.main(MapSample.java:11)
```

可以很清楚的看到，当我们试图将null插入到hashtable中时，报出了空指针异常。

### 排序Map

排序Map主要是指TreeMap，它对元素增、删、查操作时的时间复杂度都是O（log（n））。它不是线程安全的。

它的特点和TreeSet非常像，这里不再赘述。

# Java序列化

对象序列化。

　　首先，我们来讨论一下什么是序列化以及序列化的原理；然后给出一个简单的示例来演示序列化和反序列化；有时有些信息是不应该被序列化的，我们应该如何控制；我们如何去自定义序列化内容；最后我们讨论一下在继承结构的场景中，序列化需要注意哪些内容。

## 序列化概述
　　序列化，简单来讲，就是以“流”的方式来保存对象，至于保存的目标地址，可以是文件，可以是数据库，也可以是网络，即通过网络将对象从一个节点传递到另一个节点。

　　我们知道在Java的I/O结构中，有ObjectOutputStream和ObjectInputStream，它们可以实现将对象输出为二进制流，并从二进制流中获取对象，那为什么还需要序列化呢？这需要从Java变量的存储结构谈起，我们知道对Java来说，基础类型存储在栈上，复杂类型（引用类型）存储在堆中，对于基础类型来说，上述的操作时可行的，但对复杂类型来说，上述操作过程中，可能会产生重复的对象，造成错误。

而序列化的工作流程如下：

　　1. 输出流保存的对象都有一个唯一的序列号。

　　2. 个对象需要保存时，先对其序列号进行检查。

　　3. 当保存的对象中已包含该序列号时，不需要再次保存，否则，进入正常保存的流程。

　　正是通过序列号的机制，序列化才可以完整准确的保存对象的各个状态。

　　序列化保存的是对象中的各个属性的值，而不是方法或者方法签名之类的信息。对于方法或者方法签名，只要JVM能够找到正确的ClassLoader，那么就可以invoke方法。

　　序列化不会保存类的静态变量，因为静态变量是作用于类型，而序列化作用于对象。

## 简单的序列化示例
　　序列化的完整过程包括两部分：

　　1. 使用ObjectOutputStream将对象保存为二进制流，这一步叫做“序列化”。

　　2. 使用ObjectInputStream将二进制流转换成对象，这一步叫做“反序列化”。

　　下面我们来演示一个简单的示例，首先定义一个Person对象，它包含name和age两个信息。

定义Person对象
```java

class Person implements Serializable
{
    private String name;
    private int age;
    public void setName(String name) {
        this.name = name;
    }
    public String getName() {
        return name;
    }
    public void setAge(int age) {
        this.age = age;
    }
    public int getAge() {
        return age;
    }
    
    public String toString()
    {
        return "Name:" + name + "; Age:" + age;
    }
}
```
然后是两个公共方法，用来完成读、写对象的操作：


```java
private static void writeObject(Object obj, String filePath)
{
    try
    {
        FileOutputStream fos = new FileOutputStream(filePath);
        ObjectOutputStream os = new ObjectOutputStream(fos);
        os.writeObject(obj);
        os.flush();
        fos.flush();
        os.close();
        fos.close();
        System.out.println("序列化成功。");
    }
    catch(Exception ex)
    {
        ex.printStackTrace();
    }
}

private static Object readObject(String filePath)
{
    try
    {
        FileInputStream fis = new FileInputStream(filePath);
        ObjectInputStream is = new ObjectInputStream(fis);
        
        Object temp = is.readObject();
        
        fis.close();
        is.close();
        
        if (temp != null)
        {
            System.out.println("反序列化成功。");
            return temp;
        }
    }
    catch(Exception ex)
    {
        ex.printStackTrace();
    }
    
    return null;
}
```
这里，我们将对象保存的二进制流输出到磁盘文件中。

　　接下来，我们首先来看“序列化”的方法：
　　
```java
private static void serializeTest1()
{
    Person person = new Person();
    person.setName("Zhang San");
    person.setAge(30);
    System.out.println(person);
    writeObject(person, "d:\\temp\\test\\person.obj");
}
```
　我们定义了一个Person实例，然后将其保存到d:\temp\test\person.obj中。

　　最后，是“反序列化”的方法：

```java
private static void deserializeTest1()
{    
    Person temp = (Person)readObject("d:\\temp\\test\\person.obj");
    
    if (temp != null)
    {
        System.out.println(temp);
    }
}
```
　它从d:\temp\test\person.obj中读取对象，然后进行输出。

　　上述两个方法的执行结果如下：


```
Name:Zhang San; Age:30
序列化成功。
反序列化成功。
Name:Zhang San; Age:30
```

　　可以看出，读取的对象和保存的对象是完全一致的。

## 隐藏非序列化信息
　　有时，我们的业务对象中会包含很多属性，而有些属性是比较隐私的，例如年龄、银行卡号等，这些信息是不太适合进行序列化的，特别是在需要通过网络来传输对象信息时，这些敏感信息很容易被窃取。

　　Java使用transient关键字来处理这种情况，针对那些敏感的属性，我们只需使用该关键字进行修饰，那么在序列化时，对应的属性值就不会被保存。

　　我们还是看一个实例，这次我们定义一个新的Person2，其中age信息是我们不希望序列化的：

```java
class Person2 implements Serializable
{
    private String name;
    private transient int age;
    public void setName(String name) {
        this.name = name;
    }
    public String getName() {
        return name;
    }
    public void setAge(int age) {
        this.age = age;
    }
    public int getAge() {
        return age;
    }
    
    public String toString()
    {
        return "Name:" + name + "; Age:" + age;
    }
}
```
注意age的声明语句：


```
private transient int age;
```

　　下面是“序列化”和“反序列化”的方法：

```java
private static void serializeTest2()
{
    Person2 person = new Person2();
    person.setName("Zhang San");
    person.setAge(30);
    System.out.println(person);
    writeObject(person, "d:\\temp\\test\\person2.obj");
}

private static void deserializeTest2()
{    
    Person2 temp = (Person2)readObject("d:\\temp\\test\\person2.obj");
    
    if (temp != null)
    {
        System.out.println(temp);
    }
}
```
它的输出结果如下：


```
Name:Zhang San; Age:30
序列化成功。
反序列化成功。
Name:Zhang San; Age:0
```

　　可以看到经过反序列化的对象，age的信息变成了Integer的默认值0。

### 自定义序列化过程
　　我们可以对序列化的过程进行定制，进行更细粒度的控制。

　　思路是在业务模型中添加readObject和writeObject方法。下面看一个实例，我们新建一个类型，叫Person3：

```java
class Person3 implements Serializable
{
    private String name;
    private transient int age;
    public void setName(String name) {
        this.name = name;
    }
    public String getName() {
        return name;
    }
    public void setAge(int age) {
        this.age = age;
    }
    public int getAge() {
        return age;
    }
    
    public String toString()
    {
        return "Name:" + name + "; Age:" + age;
    }
    
    private void writeObject(ObjectOutputStream os)
    {
        try
        {
            os.defaultWriteObject();
            os.writeObject(this.age);
            System.out.println(this);
            System.out.println("序列化成功。");
        }
        catch(Exception ex)
        {
            ex.printStackTrace();
        }
    }
    
    private void readObject(ObjectInputStream is)
    {
        try
        {
            is.defaultReadObject();
            this.setAge(((Integer)is.readObject()).intValue() - 1);
            System.out.println("反序列化成功。");
            System.out.println(this);
        }
        catch(Exception ex)
        {
            ex.printStackTrace();
        }
    }
}
```
　请注意观察readObject和writeObject方法，它们都是private的，接受的参数是ObjectStream，然后在方法体内调用了defaultReadObject或者defaultWriteObject方法。

　　这里age同样是transient的，但是在保存对象的过程中，我们单独对其进行了保存，在读取时，我们将age信息读取出来，并进行了减1处理。

　　下面是测试方法：

```java
private static void serializeTest3()
{
    Person3 person = new Person3();
    person.setName("Zhang San");
    person.setAge(30);
    System.out.println(person);
    try
    {
        FileOutputStream fos = new FileOutputStream("d:\\temp\\test\\person3.obj");
        ObjectOutputStream os = new ObjectOutputStream(fos);
        os.writeObject(person);
        fos.close();
        os.close();
    }
    catch(Exception ex)
    {
        ex.printStackTrace();
    }
}

private static void deserializeTest3()
{    
    try
    {
        FileInputStream fis = new FileInputStream("d:\\temp\\test\\person3.obj");
        ObjectInputStream is = new ObjectInputStream(fis);
        is.readObject();
        fis.close();
        is.close();
    }
    catch(Exception ex)
    {
        ex.printStackTrace();
    }
}
```
输出结果如下：

```
Name:Zhang San; Age:30
序列化成功。
反序列化成功。
Name:Zhang San; Age:29
```

　　可以看到，经过反序列化得到的对象，其age属性已经减1。

### 探讨serialVersionUID
　　在上文中，我们描述序列化原理时，曾经提及每个对象都会有一个唯一的序列号，这个序列号，就是serialVersionUID。

　　当我们的对象实现Serializable接口时，该接口可以为我们生成serialVersionUID。

　　有两种方式来生成serialVersionUID，一种是固定值：1L，一种是经过JVM计算，不同的JVM采取的计算算法可能不同。

　　下面就是两个serialVersionUID的示例：

```
private static final long serialVersionUID = 1L;
private static final long serialVersionUID = -2380764581294638541L;
```

　　第一行是采用固定值生成的；第二行是JVM经过计算得出的。

　　那么serialVersionUID还有其他用途吗？

　　我们可以使用它来控制版本兼容。如果采用JVM生成的方式，我们可以看到，当我们业务对象的代码保持不变时，多次生成的serialVersionUID也是不变的，当我们对属性进行修改时，重新生成的serialVersionUID会发生变化，当我们对方法进行修改时，serialVersionUID不变。这也从另一个侧面说明，序列化是作用于对象属性上的。

　　当我们先定义了业务对象，然后对其示例进行了“序列化”，这时根据业务需求，我们修改了业务对象，那么之前“序列化”后的内容还能经过“反序列化”返回到系统中吗？这取决于业务对象是否定义了serialVersionUID，如果定义了，那么是可以返回的，如果没有定义，会抛出异常。

　　来看下面的示例，定义新的类型Person4：

```java
class Person4 implements Serializable
{
    private String name;
    private int age;
    public void setName(String name) {
        this.name = name;
    }
    public String getName() {
        return name;
    }
    public void setAge(int age) {
        this.age = age;
    }
    public int getAge() {
        return age;
    }
    private void xxx(){}
    
    public String toString()
    {
        return "Name:" + name + "; Age:" + age;
    }
}
```
然后运行下面的方法：

```java
private static void serializeTest4()
{
    Person4 person = new Person4();
    person.setName("Zhang San");
    person.setAge(30);
    
    writeObject(person, "d:\\temp\\test\\person4.obj");
}
```
接下来修改Person4，追加address属性：

```java
class Person4 implements Serializable
{
    private String name;
    private int age;
    private String address;
    public void setName(String name) {
        this.name = name;
    }
    public String getName() {
        return name;
    }
    public void setAge(int age) {
        this.age = age;
    }
    public int getAge() {
        return age;
    }
    private void xxx(){}
    
    public String toString()
    {
        return "Name:" + name + "; Age:" + age;
    }
    public void setAddress(String address) {
        this.address = address;
    }
    public String getAddress() {
        return address;
    }
}
```
　然后运行“反序列化”方法：
　
```java
private static void deserializeTest4()
{    
    Person4 temp = (Person4)readObject("d:\\temp\\test\\person4.obj");
    
    if (temp != null)
    {
        System.out.println(temp);
    }
}
```

```log
java.io.InvalidClassException: sample.serialization.Person4; local class incompatible: stream classdesc serialVersionUID = -2380764581294638541, local class serialVersionUID = -473458100724786987
    at java.io.ObjectStreamClass.initNonProxy(Unknown Source)
    at java.io.ObjectInputStream.readNonProxyDesc(Unknown Source)
    at java.io.ObjectInputStream.readClassDesc(Unknown Source)
    at java.io.ObjectInputStream.readOrdinaryObject(Unknown Source)
    at java.io.ObjectInputStream.readObject0(Unknown Source)
    at java.io.ObjectInputStream.readObject(Unknown Source)
    at sample.serialization.Sample.readObject(Sample.java:158)
    at sample.serialization.Sample.deserializeTest4(Sample.java:105)
    at sample.serialization.Sample.main(Sample.java:16)
```
但是当我们在Person4中添加serialVersionUID后，再次执行上述各步骤，得出的运行结果如下：


```
反序列化成功。
Name:Zhang San; Age:30
```
### 有继承结构的序列化
　　业务对象会产生继承，这在管理系统中是经常看到的，如果我们有下面的业务对象：

```java
class Person5
{
    private String name;
    private int age;
    public void setName(String name) {
        this.name = name;
    }
    public String getName() {
        return name;
    }
    public void setAge(int age) {
        this.age = age;
    }
    public int getAge() {
        return age;
    }
    
    public String toString()
    {
        return "Name:" + name + "; Age:" + age;
    }
    
    public Person5(String name, int age)
    {
        this.name = name;
        this.age = age;
    }
}

class Employee extends Person5 implements Serializable
{
    public Employee(String name, int age) {
        super(name, age);
    }

    private String companyName;

    public void setCompanyName(String companyName) {
        this.companyName = companyName;
    }

    public String getCompanyName() {
        return companyName;
    }
    
    public String toString()
    {
        return "Name:" + super.getName() + "; Age:" + super.getAge() + "; Company:" + this.companyName;
    }
}
```
Employee继承在Person5，Employee实现了Serializable接口，Person5没有实现，那么运行下面的方法：


```java
private static void serializeTest5()
{
    Employee emp = new Employee("Zhang San", 30);
    emp.setCompanyName("XXX");
    
    writeObject(emp, "d:\\temp\\test\\employee.obj");
}

private static void deserializeTest5()
{    
    Employee temp = (Employee)readObject("d:\\temp\\test\\employee.obj");
    
    if (temp != null)
    {
        System.out.println(temp);
    }
}
```
会正常运行吗？事实上不会，它会抛出如下异常：

```log
java.io.InvalidClassException: sample.serialization.Employee; no valid constructor
    at java.io.ObjectStreamClass$ExceptionInfo.newInvalidClassException(Unknown Source)
    at java.io.ObjectStreamClass.checkDeserialize(Unknown Source)
    at java.io.ObjectInputStream.readOrdinaryObject(Unknown Source)
    at java.io.ObjectInputStream.readObject0(Unknown Source)
    at java.io.ObjectInputStream.readObject(Unknown Source)
    at sample.serialization.Sample.readObject(Sample.java:158)
    at sample.serialization.Sample.deserializeTest5(Sample.java:123)
    at sample.serialization.Sample.main(Sample.java:18)
```
原因：**在有继承层次的业务对象，进行序列化时，如果父类没有实现Serializable接口，那么父类必须提供默认构造函数。**

　　我们为Person5添加如下默认构造函数：

```java
public Person5()
 {
    this.name = "Test";
     this.age = 1;
 }
```

　　再次运行上述代码，结果如下：

```
Name:Zhang San; Age:30; Company:XXX
序列化成功。
反序列化成功。
Name:Test; Age:1; Company:XXX
```
　　可以看到，反序列化后的结果，父类中的属性，已经被父类构造函数中的赋值代替了！

　　因此，我们推荐在有继承层次的业务对象进行序列化时，父类也应该实现Serializable接口。我们对Person5进行修改，使其实现Serializable接口，执行结果如下：

```
Name:Zhang San; Age:30; Company:XXX
序列化成功。
反序列化成功。
Name:Zhang San; Age:30; Company:XXX
```
这正是我们期望的结果。

# Java反射

关注反射及其相关话题。

　　反射可以帮助我们查看指定类型中的信息、创建类型的实例，调用类型的方法。我们平时使用框架，例如Spring、EJB、Hibernate等都大量的使用了反射技术。

## 反射简单示例
　　下面来演示反射相关的基本操作

　　首先是基础代码，我们定义一个接口及其实现，作为我们反射操作的目标：

```java
interface HelloWorldService
{
    void sayHello(String name);
}

class MyHelloWorld implements HelloWorldService
{
    public String name;
    
    
    public void sayHello(String name)
    {
        System.out.println("Hello " + name + ".");
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getName() {
        return name;
    }
}
```
### 获取方法及字段信息　　
　　下面的代码会输出给定类型中的方法和字段的声明信息：

```java
private static void printClassTypeInfo(String type) throws ClassNotFoundException
{
    Class classType = Class.forName(type);
    Method[] methods = classType.getDeclaredMethods();
    System.out.println("Methods info as below:");
    for(Method method : methods)
    {
        System.out.println(method.toGenericString());
    }
    Field[] fields = classType.getFields();
    System.out.println("Fields info as below:");
    for (Field field : fields)
    {
        System.out.println(field.toGenericString());
    }
}
```
在使用反射时，我们一般会使用java.lang.reflect包中的内容。

　　然后我们调用下面的代码：

```
printClassTypeInfo("sample.reflection.MyHelloWorld");
```

　　输出结果如下：

```log
Methods info as below:
public void sample.reflection.MyHelloWorld.sayHello(java.lang.String)
public java.lang.String sample.reflection.MyHelloWorld.getName()
public void sample.reflection.MyHelloWorld.setName(java.lang.String)
Fields info as below:
public java.lang.String sample.reflection.MyHelloWorld.name
```

### 实例化对象
　　我们可以使用class.netInstance的方式来创建一个对象，代码如下：

```java
private static void createInstanceTest() throws ClassNotFoundException, InstantiationException, IllegalAccessException
{
    Class classType = Class.forName("sample.reflection.MyHelloWorld");
    MyHelloWorld hello = (MyHelloWorld)classType.newInstance();
    hello.sayHello("Zhang San");
}
```
输出结果：

```
Hello Zhang San.
```

### 调用对象的方法
　　我们可以通过方法的名称以及参数类型构建一个Method实例，然后调用Method的invoke方法，来触发方法。

　　示例代码如下：

```java
private static void invokeMethodTest() throws InstantiationException, IllegalAccessException, ClassNotFoundException, NoSuchMethodException, SecurityException, IllegalArgumentException, InvocationTargetException
{
    Class classType = Class.forName("sample.reflection.MyHelloWorld");
    MyHelloWorld hello = (MyHelloWorld)classType.newInstance();
    Method method = classType.getMethod("sayHello", new Class[]{String.class});
    method.invoke(hello, new Object[]{"Zhang San"});
}
```
输出结果同上。

### 修改字段的值
　　和C#不同，Java中一般使用setxxx和getxxx显示为属性赋值，因此Java中并没有Property类型，而是有Field类型。

　　我们可以对Field的值进行修改，代码如下：

```java
private static void setFieldTest() throws ClassNotFoundException, NoSuchFieldException, SecurityException, InstantiationException, IllegalAccessException
{
    Class classType = Class.forName("sample.reflection.MyHelloWorld");
    MyHelloWorld hello = (MyHelloWorld)classType.newInstance();
    System.out.println("name is " + hello.name);
    Field field = classType.getField("name");
    field.set(hello, "Zhang San");
    System.out.println("name is " + hello.name);
}
```
执行结果如下：

```
name is null
name is Zhang San
```

　　可以看出，我们成功的修改了name的值。

## Annotation探索
　　一开始我们提到，反射是很多技术的基础，Annotation就是这样的，我们可以把Annotation看做是C#中的Attribute，它可以对类型、方法、属性、字段、方法参数等信息进行修饰。我们可以使用“@+Annotation名”的方式来使用Annotation。

### Annotation基本操作
　　来看下面的代码，我们定义了基于Type、Method、Parameter和Field上面的Annotation示例：

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@interface ClassAnnotation
{
    public String value();
}

@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@interface MethodAnnotation
{
    public String methodName();
    public String returnType();
}

@Target(ElementType.PARAMETER)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@interface ParameterAnnotation
{
    public String value();
}

@Target(ElementType.FIELD)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@interface FieldAnnotation
{
    public String value();
}
```
接着，我们定义了一个MyClass类型，使用了上述的Annotation：

```java
@ClassAnnotation("这是作用在类型上的Annotation")
class MyClass
{
    @MethodAnnotation(methodName="printInfo", returnType="void")
    public void printInfo(String info)
    {
        System.out.println(info);
    }
    
    @MethodAnnotation(methodName="printError", returnType="void")
    public void printError(@ParameterAnnotation("这是作用在参数上的Annotation")String error)
    {
        System.err.println(error);
    }
    
    @FieldAnnotation("这是作用在字段上的Annotation")
    public int count;
}
```
对于使用了Annotation，我们可以获取其中的信息，下面两种方式都可以获取Annotation，第一种方式是通过反射遍历类型及其方法、字段，一一读取Annotation信息；第二种方式是读取指定类型的Annotation：

读取Annotation方式一
```java
private static void annotationTest1()
{
    MyClass temp = new MyClass();
    
    Annotation[] annotations = temp.getClass().getAnnotations();
    for(Annotation a : annotations)
    {
        System.out.println(a.toString());
    }
    
    Method[] methods = temp.getClass().getDeclaredMethods();
    for(Method method : methods)
    {
        annotations = method.getAnnotations();
        for(Annotation a : annotations)
        {
            System.out.println(a.toString());
        }
        Annotation[][] paraAnnotations = method.getParameterAnnotations();
        for(int i = 0; i < paraAnnotations.length; i++)
        {
            for (Annotation a : paraAnnotations[i])
            {
                System.out.println(a.toString());
            }
        }
    }
    
    Field[] fields = temp.getClass().getFields();
    for (Field field : fields)
    {
        annotations = field.getAnnotations();
        for(Annotation a : annotations)
        {
            System.out.println(a.toString());
        }
    }
}
```

读取Annotation方式二
```java
private static void annotationTest2() throws ClassNotFoundException
{
    Class classType = Class.forName("sample.reflection.annotation.MyClass");
    boolean flag = classType.isAnnotationPresent(ClassAnnotation.class);
    if (flag)
    {
        ClassAnnotation annotation = (ClassAnnotation) classType.getAnnotation(ClassAnnotation.class);
        System.out.println(annotation.toString());
    }
    Method[] methods = classType.getMethods();
    for(Method method : methods)
    {
        if (method.isAnnotationPresent(MethodAnnotation.class))
        {
            System.out.println(((MethodAnnotation)method.getAnnotation(MethodAnnotation.class)).toString());
        }
        Annotation[][] paraAnnotations = method.getParameterAnnotations();
        for(int i = 0; i < paraAnnotations.length; i++)
        {
            for (Annotation a : paraAnnotations[i])
            {
                System.out.println(a.toString());
            }
        }
    }
    Field[] fields = classType.getFields();
    for (Field field:fields)
    {
        if (field.isAnnotationPresent(FieldAnnotation.class))
        {
            System.out.println(((FieldAnnotation)field.getAnnotation(FieldAnnotation.class)).toString());
        }
    }
}
```
上述两个方法的输出都是一样的，如下：


```
@sample.reflection.annotation.ClassAnnotation(value=这是作用在类型上的Annotation)
@sample.reflection.annotation.MethodAnnotation(methodName=printInfo, returnType=void)
@sample.reflection.annotation.MethodAnnotation(methodName=printError, returnType=void)
@sample.reflection.annotation.ParameterAnnotation(value=这是作用在参数上的Annotation)
@sample.reflection.annotation.FieldAnnotation(value=这是作用在字段上的Annotation)
```

### 在WebService中使用Annotation
　　上述代码看上去可能有些枯燥，不能显示出Annotation的威力，那么我们接下来看WebService，在WebService中，我们可以使用WebMethod、WebParam等Annotation来声明方法或者参数。

　　接下来，我们来实现一个非常简单的Web服务：

```java
@WebService(targetNamespace="http://test", serviceName="HelloService")
public class HelloServiceProvider
{
    @WebResult(name="HelloString")
    @WebMethod
    public String sayHello(@WebParam(name="userName") String name)
    {
        return "Hello " + name;
    }
    
    @Oneway
    @WebMethod(action="userLogin", operationName="userLogin")
    public void login()
    {
        System.out.println("User has logged on.");
    }
    
    public static void main(String[] args)
    {
        Thread thread = new Thread(new HelloServicePublisher());
        thread.start();
    }
}
```
然后定义一个Publisher：

```java
class HelloServicePublisher implements Runnable
{
    public void run()
    {
        Endpoint.publish("http://localhost:8888/test/HelloService", new HelloServiceProvider());
    }
}
```
在命令行中，我们定位到源代码路径，执行下面的命令：


```
wsgen -cp . HelloServiceProvider
```

　　wsgen位于JDK的bin目录中。

　　然后我们启动HelloServiceProvider，在浏览器中输入如下地址：http://localhost:8888/test/HelloService，可以看到如下信息：

点击WSDL链接，可以看到：

```xml
<!-- Published by JAX-WS RI at http://jax-ws.dev.java.net. RI's version is JAX-WS RI 2.2.4-b01. --><!-- Generated by JAX-WS RI at http://jax-ws.dev.java.net. RI's version is JAX-WS RI 2.2.4-b01. --><definitions targetNamespace="http://test" name="HelloService"><types><xsd:schema><xsd:import namespace="http://test" schemaLocation="http://localhost:8888/test/HelloService?xsd=1"/></xsd:schema></types><message name="sayHello"><part name="parameters" element="tns:sayHello"/></message><message name="sayHelloResponse"><part name="parameters" element="tns:sayHelloResponse"/></message><message name="userLogin"><part name="parameters" element="tns:userLogin"/></message><portType name="HelloServiceProvider"><operation name="sayHello"><input wsam:Action="http://test/HelloServiceProvider/sayHelloRequest" message="tns:sayHello"/><output wsam:Action="http://test/HelloServiceProvider/sayHelloResponse" message="tns:sayHelloResponse"/></operation><operation name="userLogin"><input wsam:Action="userLogin" message="tns:userLogin"/></operation></portType><binding name="HelloServiceProviderPortBinding" type="tns:HelloServiceProvider"><soap:binding transport="http://schemas.xmlsoap.org/soap/http" style="document"/><operation name="sayHello"><soap:operation soapAction=""/><input><soap:body use="literal"/></input><output><soap:body use="literal"/></output></operation><operation name="userLogin"><soap:operation soapAction="userLogin"/><input><soap:body use="literal"/></input></operation></binding><service name="HelloService"><port name="HelloServiceProviderPort" binding="tns:HelloServiceProviderPortBinding"><soap:address location="http://localhost:8888/test/HelloService"/></port></service></definitions>
```

JDK中自带了Web服务器，我们不需要把上述代码部署到其他服务器中。

### 动态代理机制
　　Spring中一大特色是AOP，面向方面编程也是框架设计一个趋势。对于业务中的共通操作，诸如记录日志、维护事务等，如果和业务逻辑纠缠在一起，会造成代码职责不清，后续维护困难等问题。利用AOP，我们可以很好的分离共通操作和业务操作。

　　下面我们来实现一个简单的AOP框架，要实现这样一个框架，需要3部分：
　　
1. InvocationHandler，来触发方法；
2. Interceptor，来定义拦截器；
3. DynamicProxy，来动态创建代理对象。

　　首先我们看Interptor的定义：

```java
interface AOPInterceptor
{
    public void before(Method method, Object[] args);
    public void after(Method method, Object[] args);
    public void afterThrowing(Method method, Object[] args);
    public void afterFinally(Method method, Object[] args);
}
```
接下来是InvocationHandler：

```java
class DynamicProxyInvocationHandler implements InvocationHandler
{
    private Object target;
    private AOPInterceptor interceptor;

    public DynamicProxyInvocationHandler(Object target, AOPInterceptor interceptor)
    {
        this.target = target;
        this.interceptor = interceptor;
    }
    
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable 
    {
        try
        {
            interceptor.before(method, args);
            Object returnValue = method.invoke(target, args);
            interceptor.after(method, args);
            return returnValue;
        }
        catch(Throwable t)
        {
            interceptor.afterThrowing(method, args);
            throw t;
        }
        finally
        {
            interceptor.afterFinally(method, args);
        }
    }
}
```
最后是DynamicProxy：

```java
class DynamicProxyFactoryImpl implements DynamicProxyFactory
{
    public <T> T createProxy(Class<T> clazz, T target, AOPInterceptor interceptor)
    {
        InvocationHandler handler = new DynamicProxyInvocationHandler(target, interceptor);
        return (T)Proxy.newProxyInstance(Thread.currentThread().getContextClassLoader(), new Class<?>[] {clazz}, handler);
    }
}
```
至此，我们构建了一个”简易“的AOP拦截器。下面我们来创建一些测试代码。

　　首先是实现AOPInterceptor接口：

```java
class MyInterceptor implements AOPInterceptor
{

    public void after(Method method, Object[] args) {
        System.out.println("方法执行结束。");
    }

    public void afterFinally(Method method, Object[] args) {
        System.out.println("方法体Finally执行结束。");
    }

    public void afterThrowing(Method method, Object[] args) {
        System.out.println("方法抛出异常。");
    }

    public void before(Method method, Object[] args) {
        System.out.println("方法开始执行");
    }
}
```
　然后利用本文一开始定义的HelloWorldService，来完成测试，需要在MyHello的sayHello方法最后，追加一行代码：


```
throw new RuntimeException();
```

接着是测试代码：

```java
private static void test()
{
    MyInterceptor interceptor = new MyInterceptor();
    HelloWorldService hello = new MyHelloWorld();
    DynamicProxyFactory factory = new DynamicProxyFactoryImpl();
    HelloWorldService proxy = factory.createProxy(HelloWorldService.class, hello, interceptor);
    proxy.sayHello("Zhang San");
}
```
最终，执行结果如下：

```log
方法开始执行
Hello Zhang San.
方法抛出异常。
方法体Finally执行结束。
Exception in thread "main" java.lang.reflect.UndeclaredThrowableException
    at sample.reflection.dynamicproxy.$Proxy0.sayHello(Unknown Source)
    at sample.reflection.dynamicproxy.Sample.test(Sample.java:18)
    at sample.reflection.dynamicproxy.Sample.main(Sample.java:9)
Caused by: java.lang.reflect.InvocationTargetException
    at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
    at sun.reflect.NativeMethodAccessorImpl.invoke(Unknown Source)
    at sun.reflect.DelegatingMethodAccessorImpl.invoke(Unknown Source)
    at java.lang.reflect.Method.invoke(Unknown Source)
    at sample.reflection.dynamicproxy.DynamicProxyInvocationHandler.invoke(Sample.java:60)
    ... 3 more
```

　　可以看出，我们已经在业务执行的前、后、异常抛出后以及finally执行后进行了拦截，达到了我们期望的效果。

# Java一些基础概念

地址下载：http://zangweiren.iteye.com/blog/241218

在看上述文章的时候，随手写了一些测试代码，以便加深理解。这也就是这篇文章的来源了。

## 类的初始化顺序
　　在Java中，类里面可能包含：静态变量，静态初始化块，成员变量，初始化块，构造函数。在类之间可能存在着继承关系，那么当我们实例化一个对象时，上述各部分的加载顺序是怎样的？

　　首先来看代码：

```java
class Parent
{
    public static StaticVarible staticVarible= new StaticVarible("父类-静态变量1");    
    public StaticVarible instVarible= new StaticVarible("父类-成员变量1");
    
    static
    {
        System.out.println("父类-静态块");
    }
    
    {
        System.out.println("父类-初始化块");
    }
    
    public static StaticVarible staticVarible2= new StaticVarible("父类-静态变量2");    
    public StaticVarible instVarible2= new StaticVarible("父类-成员变量2");
    
    public Parent()
    {
        System.out.println("父类-实例构造函数");
    }
}

class Child extends Parent
{
    public static StaticVarible staticVarible= new StaticVarible("子类-静态变量1");    
    public StaticVarible instVarible= new StaticVarible("子类-成员变量1");
    
    static
    {
        System.out.println("子类-静态块");
    }
    
    public Child()
    {
        System.out.println("子类-实例构造函数");
    }
    
    {
        System.out.println("子类-初始化块");
    }
    
    public static StaticVarible staticVarible2= new StaticVarible("子类-静态变量2");    
    public StaticVarible instVarible2= new StaticVarible("子类-成员变量2");
    
    
}

class StaticVarible
{
    public StaticVarible(String info)
    {
        System.out.println(info);
    }
}
```
　然后执行下面的语句：


```
Child child = new Child();
```

　　输出结果如下：

```
父类-静态变量1
父类-静态块
父类-静态变量2
子类-静态变量1
子类-静态块
子类-静态变量2
父类-成员变量1
父类-初始化块
父类-成员变量2
父类-实例构造函数
子类-成员变量1
子类-初始化块
子类-成员变量2
子类-实例构造函数
```
### 结论　　
　　从上述结果可以看出，在实例化一个对象时，各部分的加载顺序如下：

　**　父类静态成员/父类静态初始化块 -> 子类静态成员/子类静态初始化块 -> 父类成员变量/父类初始化块 -> 父类构造函数 -> 子类成员变量/子类初始化块 -> 子类构造函数**

## 和String相关的一些事儿
　　首先，我们聊一聊Java中堆和栈的事儿。

- 栈：存放基本类型，包括char/byte/short/int/long/float/double/boolean
- 堆：存放引用类型，同时一般会在栈中保留一个指向它的指针，垃圾回收判断一个对象是否可以回收，就是判断栈中是否有指针指向堆中的对象。

　　String作为一种特殊的数据类型，它不完全等同于基本类型，也不是全部的引用类型，许多面试题都有它的身影。

### String类型变量的存储结构
　　String的存储结构分为两部分，我们以String a = "abc";为例，描述String类型的存储方式：

　　1. 在栈中创建一个char数组，值分为是'a'，'b'，'c'。
　　2. 在堆中创建一个String对象。

### Java中的字符串池
　　为了节省空间和资源，JVM会维护一个字符串池，或者说会缓存一部分曾经出现过的字符串。

　　例如下面的代码：
```
String v1 = "ab";
String v2 = "ab";
```
　　实际上，v1==v2，因为JVM在v1声明后，已经对“ab”进行了缓存。

　　那么JVM对字符串进行缓存的依据是什么？我们来看下面的代码，非常有意思：

```java
public class StringTest {
    public static final String constValue = "ab";
    public static final String staticValue;
    
    static
    {
        staticValue="ab";
    }
    
    public static void main(String[] args)
    {
        String v1 = "ab";
        String v2 = "ab";
        System.out.println("v1 == v2 : " + (v1 == v2));
        String v3 = new String("ab");
        System.out.println("v1 == v3 : " + (v1 == v3));
        String v4 = "abcd";
        String v5 = "ab" + "cd";
        System.out.println("v4 == v5 : " + (v4 == v5));
        String v6 = v1 + "cd";
        System.out.println("v4 == v6 : " + (v4 == v6));
        String v7 = constValue + "cd";
        System.out.println("v4 == v7 : " + (v4 == v7));
        String v8 = staticValue + "cd";
        System.out.println("v4 == v8 : " + (v4 == v8));
        String v9 = v4.intern();
        System.out.println("v4 == v9 :" + (v4 == v9));
        String v10 = new String(new char[]{'a','b','c','d'});
        String v11 = v10.intern();
        System.out.println("v4 == v11 :" + (v4 == v11));
        System.out.println("v10 == v11 :" + (v10 == v11));
    }
}
```
请注意它的输出结果：


```
v1 == v2 : true
v1 == v3 : false
v4 == v5 : true
v4 == v6 : false
v4 == v7 : true
v4 == v8 : false
v4 == v9 :true
v4 == v11 :true
v10 == v11 :false
```

　　我们会发现，并不是所有的判断都返回true，这似乎和我们上面的说法有矛盾了。其实不然，因为

　　结论
　　1. JVM只能缓存那些在编译时可以确定的常量，而非运行时常量。

　　　　上述代码中的constValue属于编译时常量，而staticValue则属于运行时常量。

　　2. 通过使用 new方式创建出来的字符串，JVM缓存的方式是不一样的。

　　　　所以上述代码中，v1不等同于v3。

#### String的这种设计属于享元模式吗？
　　这个话题比较有意思，大部分讲设计模式的文章，在谈到享元时，一般就会拿String来做例子，但它属于享元模式吗？

　　字符串与享元的关系，大家可以参考下面的文章：http://www.cnblogs.com/winter-cn/archive/2012/01/21/2328388.html

#### 字符串的反转输出
　　这种情况下，一般会将字符串看做是字符数组，然后利用反转数组的方式来反转字符串。

### 眼花缭乱的方法调用
#### 有继承关系结构中的方法调用
　　继承是面向对象设计中的常见方式，它可以有效的实现”代码复用“，同时子类也有重写父类方法的自由，这就对到底是调用父类方法还是子类方法带来了麻烦。

　　来看下面的代码：

```java
public class PropertyTest {

    public static void main(String[] args)
    {
        ParentDef v1 = new ParentDef();
        ParentDef v2 = new ChildDef();
        ChildDef v3 = new ChildDef();
        System.out.println("=====v1=====");
        System.out.println("staticValue:" + v1.staticValue);
        System.out.println("value:" + v1.value);
        System.out.println("=====v2=====");
        System.out.println("staticValue:" + v2.staticValue);
        System.out.println("value:" + v2.value);
        System.out.println("=====v3=====");
        System.out.println("staticValue:" + v3.staticValue);
        System.out.println("value:" + v3.value);
    }
}

class ParentDef
{
    public static final String staticValue = "父类静态变量";
    public String value = "父类实例变量";
}

class ChildDef extends ParentDef
{
    public static final String staticValue = "子类静态变量";
    public String value = "子类实例变量";
}
```
　　
输出结果如下：

```
=====v1=====
staticValue:父类静态变量
value:父类实例变量
=====v2=====
staticValue:父类静态变量
value:父类实例变量
=====v3=====
staticValue:子类静态变量
value:子类实例变量
```

结论
**对于调用父类方法还是子类方法，只与变量的声明类型有关系，与实例化的类型没有关系。**

#### 到底是值传递还是引用传递
　　对于这个话题，我的观点是值传递，因为传递的都是存储在栈中的内容，无论是基本类型的值，还是指向堆中对象的指针，都是值而非引用。并且在值传递的过程中，JVM会将值复制一份，然后将复制后的值传递给调用方法。

按照这种方式，我们来看下面的代码：　　

```java
public class ParamTest {

    public void change(int value)
    {
        value = 10;
    }
    
    public void change(Value value)
    {
        Value temp = new Value();
        temp.value = 10;
        value = temp;
    }
    
    public void add(int value)
    {
        value += 10;
    }
    
    public void add(Value value)
    {
        value.value += 10;
    }
    
    public static void main(String[] args)
    {
        ParamTest test = new ParamTest();
        Value value = new Value();
        int v = 0;
        System.out.println("v:" + v);
        System.out.println("value.value:" + value.value);
        System.out.println("=====change=====");
        test.change(v);
        test.change(value);
        System.out.println("v:" + v);
        System.out.println("value.value:" + value.value);
        value = new Value();
        v = 0;
        System.out.println("=====add=====");
        test.add(v);
        test.add(value);
        System.out.println("v:" + v);
        System.out.println("value.value:" + value.value);
    }
}

class Value
{
    public int value;
}
```
它的输出结果：

```
v:0
value.value:0
=====change=====
v:0
value.value:0
=====add=====
v:0
value.value:10
```

　　我们看到，在调用change方法时，即使我们传递进去的是指向对象的指针，但最终对象的属性也没有变，这是因为在change方法体内，我们新建了一个对象，然后将”复制过的指向原对象的指针“指向了“新对象”，并且对新对象的属性进行了调整。但是“复制前的指向原对象的指针”依然是指向“原对象”，并且属性没有任何变化。

## final/finally/finalize的区别
　　final可以修饰类、成员变量、方法以及方法参数。使用final修饰的类是不可以被继承的，使用final修饰的方法是不可以被重写的，使用final修饰的变量，只能被赋值一次。

　　使用final声明变量的赋值时机：

　　1. 定义声明时赋值

　　2. 初始化块或静态初始化块中

　　3. 构造函数

　　来看下面的代码：


```java
class FinalTest
{
    public static final String staticValue1 = "静态变量1";
    public static final String staticValue2;
    
    static
    {
        staticValue2 = "静态变量2";
    }
    
    public final String value1 = "实例变量1";
    public final String value2;
    public final String value3;
    
    {
        value2 = "实例变量2";
    }
    
    public FinalTest()
    {
        value3 = "实例变量3";
    }
}
```
finally一般是和try...catch放在一起使用，主要用来释放一些资源。

　　我们来看下面的代码：

```java
public class FinallyTest {

    public static void main(String[] args)
    {
        finallyTest1();
        finallyTest2();
        finallyTest3();
    }
    
    private static String finallyTest1()
    {
        try
        {
            throw new RuntimeException();
        }
        catch(Exception ex)
        {
            ex.printStackTrace();
        }
        finally
        {
            System.out.println("Finally语句被执行");
        }
        try
        {
            System.out.println("Hello World");
            return "Hello World";
        }
        catch(Exception ex)
        {
            ex.printStackTrace();
        }
        finally
        {
            System.out.println("Finally语句被执行");
        }
        return null;
    }
    
    private static void finallyTest2()
    {
        int i = 0;
        for (i = 0; i < 3; i++)
        {
            try
            {
                if (i == 2) break;
                System.out.println(i);
            }
            finally
            {
                System.out.println("Finally语句被执行");
            }
        }
    }
    
    private static Test finallyTest3()
    {
        try
        {
            return new Test();
        }
        finally
        {
            System.out.println("Finally语句被执行");
        }
    }
}
```
执行结果如下：

```
java.lang.RuntimeException
    at sample.interview.FinallyTest.finallyTest1(FinallyTest.java:16)
    at sample.interview.FinallyTest.main(FinallyTest.java:7)
Finally语句被执行
Hello World
Finally语句被执行
0
Finally语句被执行
1
Finally语句被执行
Finally语句被执行
Test实例被创建
Finally语句被执行
```

　　注意在循环的过程中，对于某一次循环，即使调用了break或者continue，finally也会执行。

　　finalize则主要用于释放资源，在调用GC方法时，该方法就会被调用。

　　来看下面的示例：

```java
class FinalizeTest
{
    protected void finalize()
    {
        System.out.println("finalize方法被调用");
    }
    
    public static void main(String[] args)
    {
        FinalizeTest test = new FinalizeTest();
        test = null;
        Runtime.getRuntime().gc();
    }
}
```
　执行结果如下：

```
finalize方法被调用
```
## 关于基本类型的一些事儿
　　基本类型供分为9种，包括byte/short/int/long/float/double/boolean/void，每种基本类型都对应一个“包装类”，其他一些基本信息如下：

1. 基本类型：byte 二进制位数：8
2. 包装类：java.lang.Byte
3. 最小值：Byte.MIN_VALUE=-128
4. 最大值：Byte.MAX_VALUE=127
5. 基本类型：short 二进制位数：16
6. 包装类：java.lang.Short
7. 最小值：Short.MIN_VALUE=-32768
8. 最大值：Short.MAX_VALUE=32767
9. 基本类型：int 二进制位数：32
10. 包装类：java.lang.Integer
11. 最小值：Integer.MIN_VALUE=-2147483648
12. 最大值：Integer.MAX_VALUE=2147483647
13. 基本类型：long 二进制位数：64
14. 包装类：java.lang.Long
15. 最小值：Long.MIN_VALUE=-9223372036854775808
16. 最大值：Long.MAX_VALUE=9223372036854775807
17. 基本类型：float 二进制位数：32
18. 包装类：java.lang.Float
19. 最小值：Float.MIN_VALUE=1.4E-45
20. 最大值：Float.MAX_VALUE=3.4028235E38
21. 基本类型：double 二进制位数：64
22. 包装类：java.lang.Double
23. 最小值：Double.MIN_VALUE=4.9E-324
24. 最大值：Double.MAX_VALUE=1.7976931348623157E308
25. 基本类型：char 二进制位数：16
26. 包装类：java.lang.Character
27. 最小值：Character.MIN_VALUE=0
28. 最大值：Character.MAX_VALUE=65535

### 关于基本类型的一些结论（来自《Java面试解惑》）

未带有字符后缀标识的整数默认为int类型；未带有字符后缀标识的浮点数默认为double类型。
如果一个整数的值超出了int类型能够表示的范围，则必须增加后缀“L”（不区分大小写，建议用大写，因为小写的L与阿拉伯数字1很容易混淆），表示为long型。
带有“F”（不区分大小写）后缀的整数和浮点数都是float类型的；带有“D”（不区分大小写）后缀的整数和浮点数都是double类型的。
编译器会在编译期对byte、short、int、long、float、double、char型变量的值进行检查，如果超出了它们的取值范围就会报错。
int型值可以赋给所有数值类型的变量；long型值可以赋给long、float、double类型的变量；float型值可以赋给float、double类型的变量；double型值只能赋给double类型变量。
　　关于基本类型之间的转换
　　下面的转换是无损精度的转换：

byte->short
short->int
char->int
int->long
float->double
　　下面的转换是会损失精度的：

int->float
long->float
long->double
　　除此之外的转换，是非法的。

## 和日期相关的一些事儿
　　Java中，有两个类和日期相关，一个是Date，一个是Calendar。我们来看下面的示例：

```java
public class DateTest {

    public static void main(String[] args) throws ParseException
    {
        test1();
        test2();
        test3();
    }
    
    private static void test1() throws ParseException
    {
        Date date = new Date();
        System.out.println(date);
        DateFormat sf = new SimpleDateFormat("yyyy-MM-dd");
        System.out.println(sf.format(date));
        String formatString = "2013-05-12";
        System.out.println(sf.parse(formatString));
    }
    
    private static void test2()
    {
        Date date = new Date();
        System.out.println("Year:" + date.getYear());
        System.out.println("Month:" + date.getMonth());
        System.out.println("Day:" + date.getDate());
        System.out.println("Hour:" + date.getHours());
        System.out.println("Minute:" + date.getMinutes());
        System.out.println("Second:" + date.getSeconds());
        System.out.println("DayOfWeek:" + date.getDay());
    }
    
    private static void test3()
    {
        Calendar c = Calendar.getInstance();
        System.out.println(c.getTime());
        System.out.println(c.getTimeZone());
        System.out.println("Year:" + c.get(Calendar.YEAR));
        System.out.println("Month:" + c.get(Calendar.MONTH));
        System.out.println("Day:" + c.get(Calendar.DATE));
        System.out.println("Hour:" + c.get(Calendar.HOUR));
        System.out.println("HourOfDay:" + c.get(Calendar.HOUR_OF_DAY));
        System.out.println("Minute:" + c.get(Calendar.MINUTE));
        System.out.println("Second:" + c.get(Calendar.SECOND));
        System.out.println("DayOfWeek:" + c.get(Calendar.DAY_OF_WEEK));
        System.out.println("DayOfMonth:" + c.get(Calendar.DAY_OF_MONTH));
        System.out.println("DayOfYear:" + c.get(Calendar.DAY_OF_YEAR));
    }
}
```

输出结果如下：

```
Sat May 11 13:44:34 CST 2013
2013-05-11
Sun May 12 00:00:00 CST 2013
Year:113
Month:4
Day:11
Hour:13
Minute:44
Second:35
DayOfWeek:6
Sat May 11 13:44:35 CST 2013
sun.util.calendar.ZoneInfo[id="Asia/Shanghai",offset=28800000,dstSavings=0,useDaylight=false,transitions=19,lastRule=null]
Year:2013
Month:4
Day:11
Hour:1
HourOfDay:13
Minute:44
Second:35
DayOfWeek:7
DayOfMonth:11
DayOfYear:131
```
　需要注意的是，Date中的getxxx方法已经变成deprecated了，因此我们尽量使用calendar.get方法来获取日期的细节信息。

　　另外，注意DateFormat，它不仅可以对日期的输出进行格式化，而且可以逆向操作，将符合Format的字符串转换为日期类型。

# Java的JDBC

JDBC相关的话题。

## 概述
　　尽管在实际开发过程中，我们一般使用ORM框架来代替传统的JDBC，例如Hibernate或者iBatis，但JDBC是Java用来实现数据访问的基础，掌握它对于我们理解Java的数据操作流程很有帮助。

　　JDBC的全称是Java Database Connectivity。

　　JDBC对数据库进行操作的流程：

- 连接数据库
- 发送数据请求，即传统的CRUD指令
- 返回操作结果集

　
JDBC中常用的对象包括：

- ConnectionManager
- Connection
- Statement
- CallableStatement
- PreparedStatement
- ResultSet
- SavePoint


## 一个简单JDBC示例
　　我们来看下面一个简单的示例，它使用JDK自带的Derby数据库，创建一张表，插入一些记录，然后将记录返回：

```java

private static void test1() throws SQLException
{
    String driver = "org.apache.derby.jdbc.EmbeddedDriver";
    String dbURL = "jdbc:derby:EmbeddedDB;create=true";
    
    Connection con = null;
    Statement st = null;
    try
    {
        Class.forName(driver);
        con = DriverManager.getConnection(dbURL);
        st = con.createStatement();
        st.execute("create table foo(ID INT NOT NULL, NAME VARCHAR(30))");
        st.executeUpdate("insert into foo(ID,NAME) values(1, 'Zhang San')");
        
        ResultSet rs = st.executeQuery("select ID,NAME from foo");
        
        while(rs.next())
        {
            int id = rs.getInt("ID");
            String name = rs.getString("NAME");
            System.out.println("ID=" + id + "; NAME=" + name);
        }
    }
    catch(Exception ex)
    {
        ex.printStackTrace();
    }
    finally
    {
        if (st != null) st.close();
        if (con != null) con.close();
    }
}
```
## 如何建立数据库连接
　　上面的示例代码中，建立数据库连接的部分如下：


```
String driver = "org.apache.derby.jdbc.EmbeddedDriver";
String dbURL = "jdbc:derby:EmbeddedDB;create=true";

Class.forName(driver);
con = DriverManager.getConnection(dbURL);
```

建立数据库连接的过程，可以分为两步：

　　1. 加载数据库驱动，即上文中的driver以及Class.forName(dirver)

　　2. 定位数据库连接字符串， 即dbURL以及DriverManager.getConnection(dbURL)

　　不同的数据库，对应的dirver和dbURL不同，但加载驱动和建立连接的方式是相同的，即只需要修改上面driver和dbURL的值就可以了。

#### 自动加载数据库驱动
　　如果我们每次建立连接时，都要使用Class.forName(...)来手动加载数据库驱动，这样会很麻烦，我们可以通过配置文件的方式，来保存数据库驱动的信息。

　　我们可以在classpath中，即编译出来的.class的存放路径，添加如下文件：

```
META-INF\services\java.sql.Driver
```

　　对应的内容就是JDBC驱动的全路径，也就是上面driver变量的值：

```
org.apache.derby.jdbc.EmbeddedDriver
```

　　接下来，我们在程序中，就不需要再显示的用Class.forName(...)来加载驱动了，它会被自动加载进来，当我们的数据库发生变化时，只需要修改这个文件就可以了，例如当我们的数据库由Derby变为MySQL时，只需要将上述的配置修改为：

```
com.mysql.jdbc.Driver
```

　　但是，需要注意一点，这里只是配置了JDBC驱动的全路径，并没有包含jar文件的信息，因此，我们还是需要将包含该驱动的jar文件手动的放置到程序的classpath中。

## JDBC中的基本操作
　　对于数据库操作来说，CRUD操作应该是最常见的操作了， 即我们常说的增、删、查、改。

　　JDBC是使用Statement和ResultSet来完成这些操作的。

### 如何实现CRUD
　　下面是JDBC实现基本的CRUD示例:

```java

private static void insertTest() throws SQLException
{
    String dbURL = "jdbc:mysql://localhost/test";
    Connection con = DriverManager.getConnection(dbURL, "root", "123");
    Statement st = con.createStatement();
    st.execute("insert into user(ID,NAME) values(1, 'Zhang San')");
    st.execute("insert into user(ID,NAME) values(2, 'Li Si')");
    st.execute("insert into user(ID,NAME) values(3, 'Wang Wu')");
    System.out.println("=====insert test=====");
    showUser(st);
    st.close();
    con.close();
}

private static void deleteTest() throws SQLException
{
    String dbURL = "jdbc:mysql://localhost/test";
    Connection con = DriverManager.getConnection(dbURL, "root", "123");
    Statement st = con.createStatement();
    st.execute("delete from user where ID=3");
    System.out.println("=====delete test=====");
    showUser(st);
    st.close();
    con.close();
}

private static void updateTest() throws SQLException
{
    String dbURL = "jdbc:mysql://localhost/test";
    Connection con = DriverManager.getConnection(dbURL, "root", "123");
    Statement st = con.createStatement();
    st.executeUpdate("update user set NAME='TEST' where ID=2");
    System.out.println("=====update test=====");
    showUser(st);
    st.close();
    con.close();
}

private static void showUser(Statement st) throws SQLException
{
    ResultSet rs = st.executeQuery("select ID, NAME from user");
    while(rs.next())
    {
        int id = rs.getInt("ID");
        String name = rs.getString("NAME");
        System.out.println("ID:" + id + "; NAME=" + name);
    }
    rs.close();
}
```
　我们顺序调用上面的测试方法：

```
insertTest();
deleteTest();
updateTest();
```

　　执行结果如下：


```
=====insert test=====
ID:1; NAME=Zhang San
ID:2; NAME=Li Si
ID:3; NAME=Wang Wu
=====delete test=====
ID:1; NAME=Zhang San
ID:2; NAME=Li Si
=====update test=====
ID:1; NAME=Zhang San
ID:2; NAME=TEST
```

　　上面代码中的showUser方法会把user表中的所有记录打印出来。

### 如何调用存储过程
　　存储过程是做数据库开发时经常使用的技术，它可以通过节省编译时间的方式来提升系统性能，我们这里的示例使用MySQL数据库。

#### 如何调用不带参数的存储过程
　　假设我们现在有一个简单的存储过程，它只是返回user表中的所有记录，存储过程如下：

```sql
CREATE DEFINER=`root`@`localhost` PROCEDURE `GetUser`()
BEGIN
select ID,NAME from user;
END
```
我们可以使用CallableStatement来调用存储过程：

调用存储过程示例一

```java

private static void execStoredProcedureTest() throws SQLException
{
    String dbURL = "jdbc:mysql://localhost/test";
    Connection con = DriverManager.getConnection(dbURL, "root", "123");
    CallableStatement cst = con.prepareCall("call GetUser()");
    ResultSet rs = cst.executeQuery();
    while(rs.next())
    {
        int id = rs.getInt("ID");
        String name = rs.getString("NAME");
        System.out.println("ID:" + id + "; NAME=" + name);
    }
    rs.close();
    cst.close();
    con.close();
}
```
它的执行结果如下：

```
ID:1; NAME=Zhang San
ID:2; NAME=TEST
```
#### 如何调用带参数的存储过程
　　MySQL的存储过程中的参数分为三种：in/out/inout，我们可以把in看做入力参数，out看做出力参数，JDBC对这两种类型的参数设置方式不同：

1. in， JDBC使用类似于cst.set(1, 10)的方式来设置

2. out，JDBC使用类似于cst.registerOutParameter(2, Types.VARCHAR);的方式来设置

　　我们来看一个in参数的示例，假设我们希望返回ID为特定值的user信息，存储过程如下：

```sql
CREATE DEFINER=`root`@`localhost` PROCEDURE `GetUserByID`(in id int)
BEGIN
set @sqlstr=concat('select * from user where ID=', id);
prepare psmt from @sqlstr;
execute psmt;
END
```
　　Java的调用代码如下：
　　
JDBC调用存储过程示例二　
　
```java

private static void execStoredProcedureTest2(int id) throws SQLException
{
    String dbURL = "jdbc:mysql://localhost/test";
    Connection con = DriverManager.getConnection(dbURL, "root", "123");
    CallableStatement cst = con.prepareCall("call GetUserByID(?)");
    cst.setInt(1, id);
    ResultSet rs = cst.executeQuery();
    while(rs.next())
    {
        String name = rs.getString("NAME");
        System.out.println("ID:" + id + "; NAME=" + name);
    }
    rs.close();
    cst.close();
    con.close();
}
```
　　我们执行下面的语句：

```
execStoredProcedureTest2(1);
```

　　结果如下：

```
ID:1; NAME=Zhang San
```

　　对于out类型的参数，调用方式类似，不再赘述。

### 获取数据库以及结果集的metadata信息
　　在JDBC中，我们不仅能够对数据进行操作，我们还能获取数据库以及结果集的元数据信息，例如数据库的名称、驱动信息、表信息；结果集的列信息等。

#### 获取数据库的metadata信息
　　我们可以通过connection.getMetaData方法来获取数据库的元数据信息，它的类型是DatabaseMetaData。

获取数据库的元数据信息

```java

private static void test1() throws SQLException
{
    String dbURL = "jdbc:mysql://localhost/mysql";
    Connection con = DriverManager.getConnection(dbURL, "root", "123");
    
    DatabaseMetaData dbmd = con.getMetaData();
    
    System.out.println("数据库：" + dbmd.getDatabaseProductName() + " " + dbmd.getDatabaseProductVersion());
    System.out.println("驱动程序：" + dbmd.getDriverName() + " " + dbmd.getDriverVersion());

    ResultSet rs = dbmd.getTables(null, null, null, null);
    System.out.println(String.format("|%-26s|%-9s|%-9s|%-9s|", "表名称","表类别","表类型","表模式"));        
    while(rs.next())
    {
        System.out.println(String.format("|%-25s|%-10s|%-10s|%-10s|", 
                rs.getString("TABLE_NAME"),rs.getString("TABLE_CAT"),
                rs.getString("TABLE_TYPE"), rs.getString("TABLE_SCHEM")));
    }
}
```
这里我们使用的数据库是MySQL中自带的默认数据库：mysql，它会记录整个数据库服务器中的一些信息。上述代码执行结果如下：

```log
数据库：MySQL 5.5.28
驱动程序：MySQL-AB JDBC Driver mysql-connector-java-5.0.4 ( $Date: 2006-10-19 17:47:48 +0200 (Thu, 19 Oct 2006) $, $Revision: 5908 $ )
|表名称                       |表类别      |表类型      |表模式      |
|columns_priv             |mysql     |TABLE     |null      |
|db                       |mysql     |TABLE     |null      |
|event                    |mysql     |TABLE     |null      |
|func                     |mysql     |TABLE     |null      |
。。。
```

　　由于mysql中表比较多，上述结果只截取了一部分。

#### 获取结果集的元数据信息
　　我们可以通过使用resultset.getMetaData方法来获取结果集的元数据信息，它的类型是ResultSetMetaData。

获取结果集的元数据信息

```java

private static void test2() throws SQLException
{
    String dbURL = "jdbc:mysql://localhost/test";
    Connection con = DriverManager.getConnection(dbURL, "root", "123");
    Statement st = con.createStatement();
    ResultSet rs = st.executeQuery("select ID, NAME from user");
    ResultSetMetaData rsmd = rs.getMetaData();
    for (int i = 1; i <= rsmd.getColumnCount(); i++)
    {
        System.out.println("Column Name:" + rsmd.getColumnName(i) + "; Column Type:" + rsmd.getColumnTypeName(i));
    }
}
```
它的执行结果如下：

```
Column Name:ID; Column Type:INTEGER UNSIGNED
Column Name:NAME; Column Type:VARCHAR
```

　　可以看到，它返回类结果集中每一列的名称和类型。

## 基于ResultSet的操作
　　当我们需要对数据库进行修改时，除了上述通过Statement完成操作外，我们也可以借助ResultSet来完成。

　　需要注意的是，在这种情况下，我们定义Statement时，需要添加参数。

　　Statement构造函数可以包含3个参数：

1. resultSetType，它的取值包括：ResultSet.TYPE_FORWARD_ONLY、ResultSet.TYPE_SCROLL_INSENSITIVE 或 ResultSet.TYPE_SCROLL_SENSITIVE，默认情况下，该参数的值是ResultSet.TYPE_FORWARD_ONLY。

2. resultSetConcurrency，它的取值包括：ResultSet.CONCUR_READ_ONLY 或 ResultSet.CONCUR_UPDATABLE，默认情况下，该参数的值是ResultSet.CONCUR_READ_ONLY。

3. resultSetHoldability，它的取值包括：ResultSet.HOLD_CURSORS_OVER_COMMIT 或 ResultSet.CLOSE_CURSORS_AT_COMMIT。

为了使得ResultSet能够对数据进行操作我们需要：

- 将resultSetType设置为ResultSet.TYPE_SCROLL_SENSITIVE。
- 将resultSetConcurrency设置为ResultSet.CONCUR_UPDATABLE。

在通过ResultSet对数据进行调整的过程中，下面方法可能会被调用：

- resultset.last()
- resultset.first()
- resultset.moveToInsertRow()
- resultset.absolute()
- resultset.setxxx()
- resultset.updateRow()
- resultset.insertRow()

下面是一个通过ResultSet对数据进行增、删、改的示例：

通过ResultSet对数据进行增、删、改

```java

private static void getResultCount() throws SQLException
{
    System.out.println("=====Result Count=====");
    String dbURL = "jdbc:mysql://localhost/test";
    Connection con = DriverManager.getConnection(dbURL, "root", "123");
    Statement st = con.createStatement(ResultSet.TYPE_SCROLL_SENSITIVE, ResultSet.CONCUR_READ_ONLY, ResultSet.CLOSE_CURSORS_AT_COMMIT);
    ResultSet rs = st.executeQuery("select * from user");
    rs.last();
    System.out.println("返回结果的条数："+ rs.getRow());
    rs.first();
    
    rs.close();
    st.close();
    con.close();
}

private static void insertDataToResultSet() throws SQLException
{
    System.out.println("=====Insert=====");
    String dbURL = "jdbc:mysql://localhost/test";
    Connection con = DriverManager.getConnection(dbURL, "root", "123");
    Statement st = con.createStatement(ResultSet.TYPE_SCROLL_SENSITIVE, ResultSet.CONCUR_UPDATABLE);
    ResultSet rs = st.executeQuery("select ID,NAME from user");
    rs.moveToInsertRow();
    rs.updateInt(1, 4);
    rs.updateString(2, "Xiao Ming");
    rs.insertRow();
    showUser(st);
    
    rs.close();
    st.close();
    con.close();
}

private static void updateDataToResultSet() throws SQLException
{
    System.out.println("=====Update=====");
    String dbURL = "jdbc:mysql://localhost/test";
    Connection con = DriverManager.getConnection(dbURL, "root", "123");
    Statement st = con.createStatement(ResultSet.TYPE_SCROLL_SENSITIVE, ResultSet.CONCUR_UPDATABLE);
    ResultSet rs = st.executeQuery("select * from user");
    rs.last();
    int count = rs.getRow();
    rs.first();
    rs.absolute(count);
    rs.updateString(2, "Xiao Qiang");
    rs.updateRow();
    showUser(st);
    
    rs.close();
    st.close();
    con.close();
}

private static void delDataFromResultSet() throws SQLException
{
    System.out.println("=====Delete=====");
    String dbURL = "jdbc:mysql://localhost/test";
    Connection con = DriverManager.getConnection(dbURL, "root", "123");
    Statement st = con.createStatement(ResultSet.TYPE_SCROLL_SENSITIVE, ResultSet.CONCUR_UPDATABLE, ResultSet.CLOSE_CURSORS_AT_COMMIT);
    ResultSet rs = st.executeQuery("select * from user");
    rs.last();
    int count = rs.getRow();
    rs.first();
    rs.absolute(count);
    rs.deleteRow();
    showUser(st);
    
    rs.close();
    st.close();
    con.close();
}
```
分别调用上述方法：

```
1 getResultCount();
2 insertDataToResultSet();
3 updateDataToResultSet();
4 delDataFromResultSet();
```

　　执行结果如下：

```
=====Result Count=====
返回结果的条数：2
=====Insert=====
ID:1; NAME=Zhang San
ID:2; NAME=TEST
ID:4; NAME=Xiao Ming
=====Update=====
ID:1; NAME=Zhang San
ID:2; NAME=TEST
ID:4; NAME=Xiao Qiang
=====Delete=====
ID:1; NAME=Zhang San
ID:2; NAME=TEST
```

　　可以看到我们对ID为4的记录进行了插入、更新和删除操作。

## 预处理以及批处理
　　预处理和批处理都是用来提升系统性能的方式，一种是利用数据库的缓存机制，一种是利用数据库一次执行多条语句的方式。

### 预处理
　　数据库服务器接收到Statement后，一般会解析Statement、分析是否有语法错误、定制最优的执行计划，这个过程可能会降低系统的性能。一般的数据库服务器都这对这种情况，设计了缓存机制，当数据库接收到指令时，如果缓存中已经存在，那么就不再解析，而是直接运行。

　　这里相同的指令是指sql语句完全一样，包括大小写。

　　JDBC使用PreparedStatement来完成预处理：

```java
private static void test1() throws SQLException
{
    System.out.println("=====Insert a single record by PreparedStatement=====");
    String dbURL = "jdbc:mysql://localhost/test";
    Connection con = DriverManager.getConnection(dbURL, "root", "123");
    PreparedStatement pst = con.prepareStatement("insert into user(id,name) values(?,?)");
    pst.setInt(1, 5);
    pst.setString(2, "Lei Feng");
    pst.executeUpdate();
    showUser(pst);
    pst.close();
    con.close();
}
```
　执行结果如下：

```
=====Insert a single record by PreparedStatement=====
ID:1; NAME=Zhang San
ID:2; NAME=TEST
ID:5; NAME=Lei Feng
```

### 批处理
　　批处理是利用数据库一次执行多条语句的机制来提升性能，这样可以避免多次建立连接带来的性能损失。

　　批处理使用Statement的addBatch来添加指令，使用executeBatch方法来一次执行多条指令：

```java
private static void test2() throws SQLException
{
    System.out.println("=====Insert multiple records by Statement & Batch=====");
    String dbURL = "jdbc:mysql://localhost/test";
    Connection con = DriverManager.getConnection(dbURL, "root", "123");
    Statement st = con.createStatement();
    st.addBatch("insert into user(id,name) values(6,'Xiao Zhang')");
    st.addBatch("insert into user(id,name) values(7,'Xiao Liu')");
    st.addBatch("insert into user(id,name) values(8,'Xiao Zhao')");
    st.executeBatch();
    showUser(st);
    st.close();
    con.close();
}
```
执行结果如下：

```
=====Insert multiple records by Statement & Batch=====
ID:1; NAME=Zhang San
ID:2; NAME=TEST
ID:5; NAME=Lei Feng
ID:6; NAME=Xiao Zhang
ID:7; NAME=Xiao Liu
ID:8; NAME=Xiao Zhao
```

### 预处理和批处理相结合
　　我们可以把预处理和批处理结合起来，利用数据库的缓存机制，一次执行多条语句：

```java
private static void test3() throws SQLException
{
    System.out.println("=====Insert multiple records by PreparedStatement & Batch=====");
    String dbURL = "jdbc:mysql://localhost/test";
    Connection con = DriverManager.getConnection(dbURL, "root", "123");
    PreparedStatement pst = con.prepareStatement("insert into user(id,name) values(?,?)");
    pst.setInt(1, 9);
    pst.setString(2, "Xiao Zhang");
    pst.addBatch();
    pst.setInt(1, 10);
    pst.setString(2, "Xiao Liu");
    pst.addBatch();
    pst.setInt(1, 11);
    pst.setString(2, "Xiao Zhao");
    pst.addBatch();
    pst.executeBatch();
    showUser(pst);
    pst.close();
    con.close();
}
```
　执行结果如下：
　
```
=====Insert multiple records by PreparedStatement & Batch=====
ID:1; NAME=Zhang San
ID:2; NAME=TEST
ID:5; NAME=Lei Feng
ID:9; NAME=Xiao Zhang
ID:10; NAME=Xiao Liu
ID:11; NAME=Xiao Zhao
```

## 数据库事务
　　谈到数据库开发，事务是一个不可回避的话题，JDBC默认情况下，是每一步都自动提交的，我们可以通过设置connection.setAutoCommit(false)的方式来强制关闭自动提交，然后通过connection.commit()和connection.rollback()来实现事务提交和回滚。

### 简单的数据库事务
　　下面是一个简单的数据库事务的示例：

```java
private static void transactionTest1() throws SQLException
{
    System.out.println("=====Simple Transaction test=====");
    String dbURL = "jdbc:mysql://localhost/test";
    Connection con = DriverManager.getConnection(dbURL, "root", "123");
    Statement st = con.createStatement();
    try
    {
        con.setAutoCommit(false);
        st.executeUpdate("insert into user(id,name) values(12, 'Xiao Li')");
        con.commit();
    }
    catch(Exception ex)
    {
        ex.printStackTrace();
        con.rollback();
    }
    finally
    {
        con.setAutoCommit(true);
        showUser(st);
        if (st != null) st.close();
        if (con != null) con.close();
    }
}
```
　连续执行上述方法两次，我们可以得出下面的结果：


```log
=====Simple Transaction test=====
ID:1; NAME=Zhang San
ID:2; NAME=TEST
ID:5; NAME=Lei Feng
ID:12; NAME=Xiao Li
=====Simple Transaction test=====
ID:1; NAME=Zhang San
ID:2; NAME=TEST
ID:5; NAME=Lei Feng
ID:12; NAME=Xiao Li
com.mysql.jdbc.exceptions.MySQLIntegrityConstraintViolationException: Duplicate entry '12' for key 'PRIMARY'
    at com.mysql.jdbc.SQLError.createSQLException(SQLError.java:931)
    at com.mysql.jdbc.MysqlIO.checkErrorPacket(MysqlIO.java:2870)
    at com.mysql.jdbc.MysqlIO.sendCommand(MysqlIO.java:1573)
    at com.mysql.jdbc.MysqlIO.sqlQueryDirect(MysqlIO.java:1665)
    at com.mysql.jdbc.Connection.execSQL(Connection.java:3170)
    at com.mysql.jdbc.Statement.executeUpdate(Statement.java:1316)
    at com.mysql.jdbc.Statement.executeUpdate(Statement.java:1235)
    at sample.jdbc.mysql.ResultSetSample.transactionTest1(ResultSetSample.java:154)
    at sample.jdbc.mysql.ResultSetSample.main(ResultSetSample.java:17)

```

　　可以看到，第一次调用时，操作成功，事务提交，向user表中插入了一条记录；第二次调用时，发生主键冲突异常，事务回滚。

### 带有SavePoint的事务
　　当我们的事务操作中包含多个处理，但我们有时希望一些操作完成后可以先提交，这样可以避免整个事务的回滚。JDBC使用SavePoint来实现这一点。

```java
private static void transactionTest2() throws SQLException
{
    System.out.println("=====Simple Transaction test=====");
    String dbURL = "jdbc:mysql://localhost/test";
    Connection con = DriverManager.getConnection(dbURL, "root", "123");
    Statement st = con.createStatement();
    Savepoint svpt = null;
    try
    {
        con.setAutoCommit(false);
        st.executeUpdate("insert into user(id,name) values(13, 'Xiao Li')");
        st.executeUpdate("insert into user(id,name) values(14, 'Xiao Wang')");
        svpt = con.setSavepoint("roll back to here");
        st.executeUpdate("insert into user(id,name) values(15, 'Xiao Zhao')");
        st.executeUpdate("insert into user(id,name) values(13, 'Xiao Li')");
        con.commit();
    }
    catch(Exception ex)
    {
        ex.printStackTrace();
        con.rollback(svpt);
    }
    finally
    {
        con.setAutoCommit(true);
        showUser(st);
        if (st != null) st.close();
        if (con != null) con.close();
    }
}
```
执行结果如下：

```log
=====Simple Transaction test=====
com.mysql.jdbc.exceptions.MySQLIntegrityConstraintViolationException: Duplicate entry '13' for key 'PRIMARY'
    at com.mysql.jdbc.SQLError.createSQLException(SQLError.java:931)
    at com.mysql.jdbc.MysqlIO.checkErrorPacket(MysqlIO.java:2870)
    at com.mysql.jdbc.MysqlIO.sendCommand(MysqlIO.java:1573)
    at com.mysql.jdbc.MysqlIO.sqlQueryDirect(MysqlIO.java:1665)
    at com.mysql.jdbc.Connection.execSQL(Connection.java:3170)
    at com.mysql.jdbc.Statement.executeUpdate(Statement.java:1316)
    at com.mysql.jdbc.Statement.executeUpdate(Statement.java:1235)
    at sample.jdbc.mysql.ResultSetSample.transactionTest2(ResultSetSample.java:185)
    at sample.jdbc.mysql.ResultSetSample.main(ResultSetSample.java:18)
ID:1; NAME=Zhang San
ID:2; NAME=TEST
ID:5; NAME=Lei Feng
ID:13; NAME=Xiao Li
ID:14; NAME=Xiao Wang
```

可以看到最终事务报出了主键冲突异常，事务回滚，但是依然向数据库中插入了ID为13和14的记录。

另外，在确定SavePoint后，ID为15的记录并没有被插入，它是通过事务进行了回滚。

