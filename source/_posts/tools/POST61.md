---
title: Wireshark抓包工具使用以及常用抓包规则
tags: Wireshark
category: Wireshark
abbrlink: 64619
date: 2017-12-19 22:34:36
---
![image](http://ovi3ob9p4.bkt.clouddn.com/TIETU/CT0057.jpg)
 
Wireshark是一个非常好用的抓包工具，当我们遇到一些和网络相关的问题时，可以通过这个工具进行分析，不过要说明的是，这只是一个工具，用法是非常灵活的，所以今天讲述的内容可能无法直接帮你解决问题，但是只要你有解决问题的思路，学习用这个软件就非常有用了。

<!--more-->

Wireshark官方下载地址：http://www.wireshark.org/download.html

### 简单介绍

这个软件的一些常用按钮，因为本人也是接触这个软件不久，所以就简单的说下最常用的按钮好了，打开软件后，下面红框中的按钮从左到右依次是：

- 列表显示所有网卡的网络包情况，一般用的很少；
- 显示抓包选项，一般都是点这个按钮开始抓包；
- 开始新的抓包，一般用的也很少；
- 停止抓包，当你抓完包之后，就是点这个停止了；
- 清空当前已经抓到的数据包，可以防止抓包时间过长机器变卡；

而实际上，一般我们只要知道上面加粗部分的按钮功能，就可以完成抓包了，剩下的就是如何抓你想要的数据包，如何分析的问题了。

![image](http://ovi3ob9p4.bkt.clouddn.com/Wireshark/Wireshark001.png)

接下来说下抓包选项界面，也就是点第二个按钮出来的界面，同样，这里也只介绍最常用的几个功能，首先下图中最上面的红框是选择需要抓的网卡，选择好网卡后会在下面显示这个网卡的IP地址。

然后Capture Filter中就是要写抓包规则的地方，也叫做“过滤规则”，我们下面要说的很多规则都是要写到这个框里的，规则写好后，点下面的Start就开始抓包了。

![image](http://ovi3ob9p4.bkt.clouddn.com/Wireshark/Wireshark002.png)

当抓包结束之后，如果你需要把抓到的数据包找其他人分析，那么可以点菜单上的file，然后点Save As保存抓到的数据包，如下图：

![image](http://ovi3ob9p4.bkt.clouddn.com/Wireshark/Wireshark003.png)

ok，到这里，基础的使用方法说完了，接下来步入很关键的内容。

### 使用Wireshark时最常见的问题

当您使用默认设置时，会得到大量冗余信息，以至于很难找到自己需要的部分。这就是为什么过滤器会如此重要。它们可以帮助我们在庞杂的结果中迅速找到我们需要的信息。

#### 过滤器的区别

- 捕捉过滤器（CaptureFilters）：用于决定将什么样的信息记录在捕捉结果中。需要在开始捕捉前设置。
- 显示过滤器（DisplayFilters）：在捕捉结果中进行详细查找。他们可以在得到捕捉结果后随意修改。
那么我应该使用哪一种过滤器呢？

两种过滤器的目的是不同的。
- 捕捉过滤器是数据经过的第一层过滤器，它用于控制捕捉数据的数量，以避免产生过大的日志文件。
- 显示过滤器是一种更为强大（复杂）的过滤器。它允许您在日志文件中迅速准确地找到所需要的记录。

两种过滤器使用的语法是完全不同的。

##### 捕捉过滤器

![image](http://ovi3ob9p4.bkt.clouddn.com/Wireshark/Wireshark004.png)

Protocol（协议）:
可能的值: ether, fddi, ip, arp, rarp, decnet, lat, sca, moprc, mopdl, tcp and udp.
如果没有特别指明是什么协议，则默认使用所有支持的协议。

Direction（方向）:
可能的值: src, dst, src and dst, src or dst
如果没有特别指明来源或目的地，则默认使用 “src or dst” 作为关键字。
例如，”host 10.2.2.2″与”src or dst host 10.2.2.2″是一样的。

Host(s):
可能的值： net, port, host, portrange.
如果没有指定此值，则默认使用”host”关键字。
例如，”src 10.1.1.1″与”src host 10.1.1.1″相同。

Logical Operations（逻辑运算）:
可能的值：not, and, or.
否(“not”)具有最高的优先级。或(“or”)和与(“and”)具有相同的优先级，运算时从左至右进行。
例如，
“not tcp port 3128 and tcp port 23″与”(not tcp port 3128) and tcp port 23″相同。
“not tcp port 3128 and tcp port 23″与”not (tcp port 3128 and tcp port 23)”不同。

###### 例子：

tcp dst port 3128  //捕捉目的TCP端口为3128的封包。

ip src host 10.1.1.1  //捕捉来源IP地址为10.1.1.1的封包

host 10.1.2.3  //捕捉目的或来源IP地址为10.1.2.3的封包。

ether host e0-05-c5-44-b1-3c //捕捉目的或来源MAC地址为e0-05-c5-44-b1-3c的封包。如果你想抓本机与所有外网通讯的数据包时，可以将这里的mac地址换成路由的mac地址即可。

src portrange 2000-2500  //捕捉来源为UDP或TCP，并且端口号在2000至2500范围内的封包。

not imcp  //显示除了icmp以外的所有封包。（icmp通常被ping工具使用）

src host 10.7.2.12 and not dst net 10.200.0.0/16 //显示来源IP地址为10.7.2.12，但目的地不是10.200.0.0/16的封包。

(src host 10.4.1.12 or src net 10.6.0.0/16) and tcp dst portrange 200-10000 and dst net 10.0.0.0/8  //捕捉来源IP为10.4.1.12或者来源网络为10.6.0.0/16，目的地TCP端口号在200至10000之间，并且目的位于网络 10.0.0.0/8内的所有封包。

src net 192.168.0.0/24
src net 192.168.0.0 mask 255.255.255.0  //捕捉源地址为192.168.0.0网络内的所有封包。

#### 注意事项：

当使用关键字作为值时，需使用反斜杠“/”。
“ether proto /ip” (与关键字”ip”相同).
这样写将会以IP协议作为目标。

“ip proto /icmp” (与关键字”icmp”相同).
这样写将会以ping工具常用的icmp作为目标。

可以在”ip”或”ether”后面使用”multicast”及”broadcast”关键字。

当您想排除广播请求时，”no broadcast”就会非常有用。

Protocol（协议）:
您可以使用大量位于OSI模型第2至7层的协议。点击”Expression…”按钮后，您可以看到它们。
比如：IP，TCP，DNS，SSH

String1, String2 (可选项):

协议的子类。
点击相关父类旁的”+”号，然后选择其子类。

![image](http://ovi3ob9p4.bkt.clouddn.com/Wireshark/Wireshark005.png)

##### 例子：
snmp || dns || icmp //显示SNMP或DNS或ICMP封包。 

ip.addr == 10.1.1.1 
//显示来源或目的IP地址为10.1.1.1的封包。

ip.src != 10.1.2.3 or ip.dst != 10.4.5.6  //显示来源不为10.1.2.3或者目的不为10.4.5.6的封包。

换句话说，显示的封包将会为：

来源IP：除了10.1.2.3以外任意；目的IP：任意
以及
来源IP：任意；目的IP：除了10.4.5.6以外任意
ip.src != 10.1.2.3 and ip.dst != 10.4.5.6  //显示来源不为10.1.2.3并且目的IP不为10.4.5.6的封包。

换句话说，显示的封包将会为：

来源IP：除了10.1.2.3以外任意；同时须满足，目的IP：除了10.4.5.6以外任意
tcp.port == 25  //显示来源或目的TCP端口号为25的封包。 
tcp.dstport == 25  //显示目的TCP端口号为25的封包。 
tcp.flags  //显示包含TCP标志的封包。 
tcp.flags.syn == 0×02  //显示包含TCP SYN标志的封包。

如果过滤器的语法是正确的，表达式的背景呈绿色。如果呈红色，说明表达式有误。

更为详细的说明请见：http://openmaniak.com/cn/wireshark_filters.php

以上只是抓包和简单的过滤，那么其实如果你要想达到能够分析这些网络包的要求时，还需要了解下一些数据包的标记，比如我们常说的TCP三次握手是怎么回事？

#### 三次握手Three-way Handshake
一个虚拟连接的建立是通过三次握手来实现的
1. (Client) –> [SYN] –> (Server)
假如Client和Server通讯. 当Client要和Server通信时，Client首先向Server发一个SYN (Synchronize) 标记的包，告诉Server请求建立连接.
注意: 一个 SYN包就是仅SYN标记设为1的TCP包(参见TCP包头Resources). 认识到这点很重要，只有当Server收到Client发来的SYN包，才可建立连接，除此之外别无他法。因此，如果你的防火墙丢弃所有的发往外网接口的SYN包，那么你将不 能让外部任何主机主动建立连接。

2. (Client) <– [SYN/ACK] <–(Server)
接着，Server收到来自Client发来的SYN包后，会发一个对SYN包的确认包(SYN/ACK)给Client，表示对第一个SYN包的确认，并继续握手操作.
注意: SYN/ACK包是仅SYN 和 ACK 标记为1的包.

3. (Client) –> [ACK] –> (Server)
Client收到来自Server的SYN/ACK 包,Client会再向Server发一个确认包(ACK)，通知Server连接已建立。至此，三次握手完成，一个TCP连接完成。
Note: ACK包就是仅ACK 标记设为1的TCP包. 需要注意的是当三此握手完成、连接建立以后，TCP连接的每个包都会设置ACK位。

这就是为何连接跟踪很重要的原因了. 没有连接跟踪,防火墙将无法判断收到的ACK包是否属于一个已经建立的连接.一般的包过滤(Ipchains)收到ACK包时,会让它通过(这绝对不是个 好主意).

而当状态型防火墙收到此种包时，它会先在连接表中查找是否属于哪个已建连接，否则丢弃该包。

#### 四次握手Four-way Handshake
四次握手用来关闭已建立的TCP连接

1. (Client) –> ACK/FIN –> (Server)
2. (Client) <– ACK <– (Server)
3. (Client) <– ACK/FIN <– (Server)
4. (Client) –> ACK –> (Server)

##### 注意: 
由于TCP连接是双向连接, 因此关闭连接需要在两个方向上做。ACK/FIN 包(ACK 和FIN 标记设为1)通常被认为是FIN(终结)包.然而, 由于连接还没有关闭, FIN包总是打上ACK标记. 没有ACK标记而仅有FIN标记的包不是合法的包，并且通常被认为是恶意的。

连接复位Resetting a connection

四次握手不是关闭TCP连接的唯一方法. 有时,如果主机需要尽快关闭连接(或连接超时,端口或主机不可达),RST (Reset)包将被发送. 注意在，由于RST包不是TCP连接中的必须部分, 可以只发送RST包(即不带ACK标记). 但在正常的TCP连接中RST包可以带ACK确认标记

请注意RST包是可以不要收到方确认的?

无效的TCP标记Invalid TCP Flags

到目前为止，你已经看到了 SYN, ACK, FIN, 和RST 标记. 另外，还有PSH (Push) 和URG (Urgent)标记.

最常见的非法组合是SYN/FIN 包. 注意:由于 SYN包是用来初始化连接的, 它不可能和 FIN和RST标记一起出现. 这也是一个恶意攻击.

由于现在大多数防火墙已知 SYN/FIN 包, 别的一些组合,例如SYN/FIN/PSH, SYN/FIN/RST, SYN/FIN/RST/PSH。很明显，当网络中出现这种包时，很你的网络肯定受到攻击了。

别的已知的非法包有FIN (无ACK标记)和”NULL”包。如同早先讨论的，由于ACK/FIN包的出现是为了关闭一个TCP连接，那么正常的FIN包总是带有 ACK 标记。”NULL”包就是没有任何TCP标记的包(URG,ACK,PSH,RST,SYN,FIN都为0)。

到目前为止，正常的网络活动下，TCP协议栈不可能产生带有上面提到的任何一种标记组合的TCP包。当你发现这些不正常的包时，肯定有人对你的网络不怀好意。

UDP (用户数据包协议User Datagram Protocol)

TCP是面向连接的，而UDP是非连接的协议。UDP没有对接受进行确认的标记和确认机制。对丢包的处理是在应用层来完成的。(or accidental arrival).

此处需要重点注意的事情是：在正常情况下，当UDP包到达一个关闭的端口时，会返回一个UDP复位包。由于UDP是非面向连接的, 因此没有任何确认信息来确认包是否正确到达目的地。因此如果你的防火墙丢弃UDP包，它会开放所有的UDP端口(?)。

由于Internet上正常情况下一些包将被丢弃，甚至某些发往已关闭端口(非防火墙的)的UDP包将不会到达目的，它们将返回一个复位UDP包。

因为这个原因，UDP端口扫描总是不精确、不可靠的。

看起来大UDP包的碎片是常见的DOS (Denial of Service)攻击的常见形式 (这里有个DOS攻击的例子，http://grc.com/dos/grcdos.htm ).

ICMP (网间控制消息协议Internet Control Message Protocol)

如同名字一样， ICMP用来在主机/路由器之间传递控制信息的协议。 ICMP包可以包含诊断信息(ping, traceroute - 注意目前unix系统中的traceroute用UDP包而不是ICMP)，错误信息(网络/主机/端口 不可达 network/host/port unreachable), 信息(时间戳timestamp, 地址掩码address mask request, etc.)，或控制信息 (source quench, redirect, etc.) 。

你可以在http://www.iana.org/assignments/icmp-parameters 中找到ICMP包的类型。

尽管ICMP通常是无害的，还是有些类型的ICMP信息需要丢弃。

Redirect (5), Alternate Host Address (6), Router Advertisement (9) 能用来转发通讯。

Echo (8), Timestamp (13) and Address Mask Request (17) 能用来分别判断主机是否起来，本地时间 和地址掩码。注意它们是和返回的信息类别有关的。 它们自己本身是不能被利用的，但它们泄露出的信息对攻击者是有用的。

ICMP消息有时也被用来作为DOS攻击的一部分(例如：洪水ping flood ping,死 ping ?呵呵，有趣 ping of death)?/p>

包碎片注意A Note About Packet Fragmentation

如果一个包的大小超过了TCP的最大段长度MSS (Maximum Segment Size) 或MTU (Maximum Transmission Unit)，能够把此包发往目的的唯一方法是把此包分片。由于包分片是正常的，它可以被利用来做恶意的攻击。

因为分片的包的第一个分片包含一个包头，若没有包分片的重组功能，包过滤器不可能检测附加的包分片。典型的攻击Typical attacks involve in overlapping the packet data in which packet header is 典型的攻击Typical attacks involve in overlapping the packet data in which packet header isnormal until is it overwritten with different destination IP (or port) thereby bypassing firewall rules。包分片能作为 DOS 攻击的一部分，它可以crash older IP stacks 或涨死CPU连接能力。

Netfilter/Iptables中的连接跟踪代码能自动做分片重组。它仍有弱点，可能受到饱和连接攻击，可以把CPU资源耗光。

OK，到此为止，关于Wireshark抓包工具的一些小教程已经写完了，而导致我想写这么一个纠结的教程的原因是，前几天通过这个抓包解决了梦幻西游在网维大师无盘上容易掉线的问题，当时捕捉到梦幻西游掉线时的数据包是这样的。
注意下图中的红色数据，123.58.184.241是梦幻西游的服务器，而192.168.1.41是玩梦幻西游的客户机，在掉线时，发现是先有梦幻西游的服务器向客户机发送一个[FIN,ACK]数据包，根据上面的解释，FIN标记的数据包是代表要断开连接的意思，而接着客户机又回给服务器一个确认断开链接包。当看到这个抓包数据时，就意识到，大家说的在网维大师系统虚拟盘上梦幻爱掉线的问题，并非普通的网络问题，因为通过数据包的信息来看，是梦幻服务器主动要求断开链接，产生这个情况无非是以下几个原因：
1、服务器发现客户端非法，比如有外挂什么的，踢掉了客户机；
2、服务器压力大，踢掉了客户机；
3、总之不是客户端问题导致的掉线；

![image](http://ovi3ob9p4.bkt.clouddn.com/Wireshark/Wireshark006.png)

那么既然结论是如此，为什么会有在网维大师系统虚拟盘上容易出现梦幻掉线问题呢？原因是由于网维大师系统虚拟盘是模拟真实硬盘方式来实现的，而在模拟过程中，将硬盘的序列号设置为固定过的OSDIY888了，而梦幻西游刚好后识别客户机硬盘信息，发现大量客户端的硬盘序列号都是一样的，就认为是作弊或者使用挂机外挂了，结果就导致随机被服务器踢下线的情况发生，后来我们将硬盘序列号设置为空，则没再出现该问题。这个问题在未来的新版本中会解决掉。

说这个案例的目的并不是为了说明抓包多有用，而是想说明一些解决问题的思路和方法，有些人是有思路，但是缺方法，比如不会用工具，而有些人收集了很多工具却不会用，而我其实就属于后者，几年前就收集了n多工具，但是用到的没几个。慢慢的学会用这些工具后，发现思维+工具，解决问题是效率暴增，接下来的几天里，会陆续介绍写小工具给大家，也希望大家有空学习下，有问题先百度，再自己摸索，而不是一味的求助，毕竟求人不如求己！自己能直接搞定，是皆大欢喜的事情~
注意：由于某些系统为了防止ARP攻击，都免疫掉了一个Npptools.dll文件，这会导致该软件无法正常安装，打下这个补丁就可以了。
