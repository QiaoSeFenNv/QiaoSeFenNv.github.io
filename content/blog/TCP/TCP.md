---
title: "🚀 TCP  "
description: ""
lead: ""
summary: ""
date: 2023-09-16T13:45:39+08:00
lastmod: 2023-09-16T13:45:39+08:00
draft: false
weight: 100
images: []
categories: []
tags: []
contributors: ["QiaoSe FenNv"]
pinned: false
homepage: false
---





## TCP

*传输控制协议*（英語：Transmission Control Protocol，縮寫：*TCP*）*是一种面向连接*的、*可靠*的、基于字节流的*传输*层通信*协议*

TCP协议是在互联网协议中的传输层协议，它负责在不可靠的网络中提供安全、可靠的数据通信。并通过一系列的措施来保证数据包的可保数据包的可靠传输，包括序列号、确认应答、重传机制等。





### 为什么网络不可靠呢？

#### 宽带限制

**互联网上的宽带往往受到限制，因此可能发生拥塞和延迟，导致数据包丢失和传输失败**

##### 宽带

通常指在单位时间内可传输数据的最大速率，通常以 **位/秒 （`bps`）** 或者 **兆位/秒（`Mbps`）**

>网络带宽通常使用“比特每秒”（bps）作为单位来表示。而在计算机领域中，通常使用“兆比特每秒”（Mbps）或“千兆比特每秒”（Gbps）来表示网络带宽。其中，1Mbps等于1,000,000比特每秒，1Gbps等于1,000,000,000比特每秒。





**假设你想从你的电脑上传一个大小为1 GB的文件到云存储服务中。假设你的网络带宽是100 Mbps（百兆比特每秒），那么根据带宽的定义，在理想情况下你可以在10秒内完成文件的上传。但实际情况可能会因为网络拥堵等因素而变得更慢。**



**文件大小为 1 GB**

$$
1GB = 1 * 1024 * 1024 * 1024 * 8 bit
$$

**网络带宽为 100 Mbps**
$$
100 Mbps = 100 * 1000 * 1000 bps
$$

**速率公式**
$$
时间 = 文件大小 ÷ 网络带宽
$$

**计算结果：**
$$
( 1 * 1024 * 1024 * 1024 * 8 bit ) ÷ [ 100 * 1000 * 1000 (bps/s) ] ≈ 10.74秒
$$





#### 网络安全

⽹络中存在的恶意软件和攻击可能会导致数据包的损坏或丢失，这可能会影响数据的可靠性。

##### 针对 TCP 常见的攻击



**SYN攻击**：SYN攻击是一种常见的DoS（拒绝服务）攻击方式。攻击者发送大量伪造的TCP SYN连接请求，占用目标主机的连接队列资源，导致合法的连接请求无法被处理。

**攻击方式：**

攻击者伪造大量的SYN请求并向目标主机发送，这些请求看起来像是从合法的客户端发送的，但实际上这些请求并不会回复ACK，从而使目标主机的连接队列堆积满，无法再接受新的连接请求。这样，合法用户将无法访问受攻击的网络服务。

**攻击工具：**

攻击者可以使用一些网络工具来发动SYN攻击，例如hping、nmap和zmap等工具。这些工具允许攻击者以高速和大量地向目标主机发送SYN请求，从而使其连接队列堆积满。

**防御措施：**

防火墙可以过滤掉来自恶意IP地址的SYN请求，而IDS可以监测网络流量和异常连接，从而及时发现和防御SYN攻击。

使用负载均衡技术。负载均衡技术可以将连接请求分散到多台服务器上，从而分摊连接队列的负载，减少单台服务器受到攻击的风险。







**窗口扫描攻击：**窗口扫描攻击是一种利用TCP连接的方式进行端口扫描的攻击方法。攻击者发送大量的TCP连接请求，利用TCP连接的窗口大小信息探测目标主机的开放端口信息。

**攻击方式：**

窗口扫描攻击利用了 TCP 协议中的一个特性，即当客户端发送一个 SYN 包到服务器端时，如果服务器的端口是关闭的，那么服务器会返回一个 RST 包（复位包），表示拒绝连接。但如果服务器端的端口是打开的，服务器会发送 SYN/ACK 包，表示准备建立连接。攻击者可以通过观察服务器的 SYN/ACK 包的窗口大小来判断端口是否打开。

**攻击工具：**

攻击者可以通过编写程序或使用现成的扫描工具来进行窗口扫描攻击，如 Nmap 等扫描工具。攻击者使用窗口扫描工具发送一些特定的 TCP 报文给目标主机，通过观察返回的 TCP 报文，判断端口是否打开或关闭

**防御措施：**

关闭不必要的端口和服务，只打开必须的端口和服务，可以减少攻击者进行窗口扫描的机会

在防火墙上禁止 ICMP 和 UDP 报文的返回，只允许合法的 TCP 报文通过，可以有效地减少窗口扫描的攻击。







**TCP Reset攻击**：TCP Reset攻击是一种利用TCP连接复位（RST）报文强制中断TCP连接的攻击方式。攻击者发送伪造的TCP RST报文，欺骗目标主机终止正常的TCP连接。

**攻击方式：**

攻击者会发送虚假的TCP重置（RST）包给通信双方的一方，以终止这方的TCP连接。攻击者可以伪造TCP RST包的源地址和端口号，让另一端的主机误以为连接已经被重置，从而终止连接。

**攻击工具：**

攻击者可以使用一些网络工具来进行TCP Reset攻击，例如Hping、Scapy、Nmap等。

**防御措施：**

使用防火墙和入侵检测/防御系统（IDS/IPS）来监控和过滤网络流量，防止恶意的TCP RST包进入网络。

使用加密协议（如TLS/SSL）来保护TCP连接，使攻击者无法窃取或篡改TCP数据流





**TCP序列号攻击**：TCP序列号攻击是一种利用TCP序列号的漏洞进行的攻击方法。攻击者通过分析TCP数据包的序列号，伪造合法的TCP数据包，欺骗目标主机接受恶意数据。

**攻击方式：**

攻击者可以利用被攻击主机上的TCP/IP栈的缺陷，推测出下一个TCP报文的序列号，并通过发送伪造的TCP报文来欺骗被攻击主机，从而实现窃取或篡改TCP数据流的目的

**攻击工具：**

攻击者可以使用一些工具来进行TCP序列号攻击，例如Scapy、Nmap、hping等。

**防御措施：**

使用加密协议（如TLS/SSL）来保护TCP连接，使攻击者无法窃取或篡改TCP数据流。

配置TCP/IP栈的参数，例如通过启用TCP Timestamps或减少TCP Time-Wait状态的持续时间来增加随机性，减少被攻击的可能性





**TCP欺骗攻击**：TCP欺骗攻击是一种利用TCP协议的特性进行的攻击方式。攻击者伪造TCP连接请求，欺骗目标主机建立TCP连接，然后发送恶意数据进行攻击。

**攻击方式：**

攻击者通常需要先扫描目标主机上开放的端口和服务，然后发送伪造的TCP报文来欺骗目标主机。欺骗攻击可以分为两类：

1. 短连接欺骗攻击：攻击者发送一个欺骗性的SYN报文，被攻击主机会向欺骗者返回SYN/ACK报文。攻击者可以选择忽略ACK报文，使得TCP连接无法完成建立，也可以发送一个RST报文，中断已经建立的连接。
2. 长连接欺骗攻击：攻击者在成功建立一个TCP连接后，可以发送伪造的TCP数据流来窃取或篡改数据。

**攻击工具：**

攻击者可以使用一些工具来进行TCP欺骗攻击，例如Scapy、Hping、Nmap等。

**防御措施：**

使用加密协议（如TLS/SSL）来保护TCP连接，使攻击者无法窃取或篡改TCP数据流。

配置网络设备（如路由器、防火墙等）以过滤非法的TCP报文，例如使用基于规则的访问控制列表（ACL）等技术。





#### 网络拓补

##### 星型拓扑（Star Topology）

星型拓扑是⼀种常⻅的物理拓扑结构，其中所有计算机节点都通过集线器（Hub）或交换机（Switch）与⼀个中⼼节点连接。中⼼节点是⽹络的核⼼，⽤于控制和管理数据流量，因此这种拓扑结构通常被⽤于较⼩的局域⽹中。



##### 总线拓扑（Bus Topology）

总线拓扑是⼀种线性拓扑结构，其中所有计算机节点都通过⼀条共享的通信线（Bus）连接。这种拓扑结构的优点是简单和低成本，但是当⼀台计算机节点故障时，整个⽹络的运⾏都会受到影响。



##### 环型拓扑（Ring Topology）

环型拓扑是⼀种闭合的拓扑结构，其中每个计算机节点都与相邻的两个节点连接。这种拓扑结构的优点是可以避免数据碰撞和冲突，但是如果其中⼀个计算机节点出现故障，整个⽹络就会瘫痪。



##### 树型拓扑（Tree Topology）

树型拓扑是⼀种层次结构的拓扑结构，其中多个星型拓扑通过根节点相连。这种拓扑结构的优点是可以提供更⾼的容错性和可靠性，但是需要更多的⽹络设备和更复杂的配置。



##### ⽹状拓扑（Mesh Topology）

⽹状拓扑是⼀种复杂的拓扑结构，其中每个计算机节点都与多个其他节点相连，形成⼀个⼤型的⽹状结构。这种拓扑结构的优点是具有更⾼的容错性和可靠性，但是需要更多的⽹络设备和更复杂的配置。这些拓扑结构都有各⾃的优缺点和适⽤场景，⽹络设计⼈员需要根据实际需求和资源情况选择最合适的拓扑结构。







### TCP如何保证可靠传输？

它通过⼀系列的措施来确保数据包的可靠传输，包括序列号、确认应答、重传机制等



#### 确认应答机制

TCP传输数据时，每发送⼀个数据包都会要求接收⽅发送⼀个确认应答，以确认数据包已被接收。如果发送⽅没有收到确认应答，就会重新发送数据包，直到接收到确认应答为⽌。

假设有两台计算机A和B通过TCP协议进⾏数据传输。A发送⼀个数据包给B，B收到数据包后会发送⼀个ACK确认应答给A，表示已经成功接收到数据包。如果A在规定时间内没有收到B的确认应答，就会认为数据包丢失，会重新发送数据包，直到B发送确认应答为⽌。



#### 序列号机制

TCP在传输数据时，给每个数据包赋予⼀个序列号。接收⽅通过检查序列号，可以判断是否有数据包丢失或重复，从⽽实现数据包的可靠传输。



假设有两台计算机A和B通过TCP协议进⾏数据传输。A发送⼀个数据包给B，这个数据包的序列号为100，B收到数据包后会检查其序列号是否为100，如果是，则将其存储在缓存区中，并向A发送⼀个ACK确认应答，表示已经成功接收到数据包。如果B收到的是序列号为99的数据包，则会将其丢弃，因为数据包的序列号不正确。

>序列号是⽤来标识TCP连接中每⼀个传输的字节的，序列号按照字节的顺序依次递增，保证了字节的唯⼀性和有序性。序列号的初始值是随机的，每发送⼀个字节，序列号就会增加⼀个字节。滑动窗⼝⼤⼩是⽤来控制发送⽅发送数据的数量的，接收⽅通告给发送⽅的滑动窗⼝⼤⼩代表了接收⽅还能接收多少字节的数据，如果接收⽅的滑动窗⼝⼤⼩为0，则发送⽅必须等待接收⽅通告新的窗⼝⼤⼩才能继续发送数据。
>
>
>
>如果发送⽅的序列号是1000，窗⼝⼤⼩为2000，那么发送⽅可以发送序列号在1000到2999之间的数据。接收⽅接收到数据后，会确认已经成功接收的字节序列的最⼤序号，⽐如确认号为2000，这意味着接收⽅已经成功接收了序列号在1000到1999之间的数据，因此发送⽅可以发送序列号在2000到3999之间的数据。



#### 滑动窗⼝机制

TCP在发送数据包时，采⽤滑动窗⼝机制来控制发送速度。发送⽅和接收⽅各有⼀个窗⼝，通过动态调整窗⼝⼤⼩，控制发送和接收数据包的速度，从⽽保证数据包的可靠传输。



滑动窗⼝是 TCP 实现流量控制和拥塞控制的重要机制。发送⽅和接收⽅都有⾃⼰的滑动窗⼝，发送⽅的滑动窗⼝⽤于控制⾃⼰的发送速率，接收⽅的滑动窗⼝⽤于告诉发送⽅⾃⼰可以接收的数据量

>TCP的滑动窗⼝⼤⼩是由接收⽅来决定的，接收⽅通过通告接收窗⼝⼤⼩来告诉发送⽅可以发送多少数据。接收⽅的接收窗⼝⼤⼩取决于两个因素：其⼀是接收⽅缓存区的剩余⼤⼩、其⼆是接收⽅的处理能⼒
>
>
>
>接收⽅在接收到数据后，将已经成功接收的字节序列的最⼤序号告诉发送⽅。根据这个序号，发送⽅就可以计算出已经被成功接收的字节数。如果接收⽅的接收缓存区已经满了，那么接收⽅就会通告⼀个零窗⼝，表示当前⽆法接收数据。发送⽅会收到这个通告，就会停⽌发送数据，等待接收⽅通告⼀个⾮零窗⼝。当接收⽅接收缓存区有⾜够的空间时，就会通告⼀个新的窗⼝⼤⼩，从⽽允许发送⽅继续发送数据。



#### 超时重传机制

TCP在发送数据包时，会设置⼀个超时时间。如果在超时时间内没有收到确认应答，就会重新发送数据包，直到收到确认应答或达到最⼤重传次数为⽌。



超时重传是指在TCP协议中，当发送⽅发出⼀个数据包后，在规定的时间内没有收到确认应答（ACK），就会重新发送该数据包，直到收到确认应答为⽌。这样可以保证数据的可靠传输。



>超时时间是动态调整的。发送端首先会将超时时间设为一个较小的值，然后根据网络的拥塞情况和丢包率等动态调整超时时间。如果在超时时间内没有收到对应的 ACK，则会重新发送该数据包，并将超时时间加倍。如果重传次数达到一定的限制，则会认为连接出现了问题，关闭连接
>
>默认的最大重传次数在不同的实现中可能会有所不同，通常为 3 次到 7 次不等。这个值可以通过修改操作系统的 TCP 参数来调整。但是过高的重传次数会导致网络拥塞，因此应该谨慎调整





#### 三次握手和四次挥手

TCP三次握手和四次挥手是TCP协议在建立连接和关闭连接时的基本过程。它们是为了确保数据的可靠性和完整性。



##### 三次握手的意义

1. 防止已经失效的连接请求报文段到达服务器，造成错误。
2. 保证客户端和服务器的初始序列号是同步的，避免后续的数据传输出现混乱。
3. 确保双方的接收和发送能力正常，可以正常通信。

###### 过程：

第一步，客户端发送SYN报文段：

客户端发送SYN报文段，告诉服务器它想要建立连接。SYN=1表示这是一个连接请求报文段，客户端生成一个随机的序列号seq=x，表示从x开始传输数据。

>客户端生成的随机序列号seq=x可以是任意一个32位的整数。一般情况下，操作系统会根据特定的算法生成随机序列号，比如基于当前时间、进程ID等信息生成一个随机数，然后用这个随机数来作为初始的seq值。这样做是为了使每次连接请求的seq值都是随机的，从而增加攻击者猜测seq值的难度，提高了连接的安全性。

```bash
   客户端                 		服务器
     |                      	|
     |  SYN = 1, seq=x          |
     |  --------------------->  |
     |                      	|

```



第二步，服务器回复一个SYN+ACK报文段：

服务器收到连接请求后，回复一个SYN+ACK报文段。其中SYN=1表示这是一个连接请求报文段，ACK=1表示确认客户端的请求报文已经收到，服务器生成一个随机的序列号seq=y，表示从y开始传输数据，同时也确认了客户端的序列号seq=x+1（因为第1步中客户端的序列号为x，所以服务器的确认序列号为x+1）。

>在接收到连接请求报文段之后，服务器会生成自己的随机序列号，并把这个序列号放入SYN+ACK报文段中返回给客户端，以表示服务器已经接受了客户端的请求，可以进行下一步的确认操作。（**客户端和服务器生成的随机序列号seq=x和seq=y，主要是为了标识数据传输中的字节流，并且保证每个报文段的序列号都是唯一的、随机的、不可预测的，从而提高连接的安全性和可靠性**）
>
>

```bash
  客户端                		 服务器
     |                     		 |
     |  SYN = 1, seq=x         	 |
     |  -----------------------> |
     |                     		 |
     |  SYN+ACK, seq=y,     	 |
     |  ack=x+1             	 |
     |  <----------------------- |
     |                      	 |
```



第三步，客户端回复一个ACK报文段：

客户端收到服务器的SYN+ACK报文段后，再回复一个ACK报文段，表示客户端确认服务器的确认报文已经收到，客户端生成一个随机的序列号seq=x+1，表示从x+1开始传输数据。

```bash
  客户端                		 服务器
     |                     		 |
     |  SYN = 1, seq=x         	 |
     |  -----------------------> |
     |                     		 |
     |  SYN+ACK, seq=y,     	 |
     |  ack=x+1             	 |
     |  <----------------------- |
     |                      	 |
     |  ACK, seq=x+1,       	 |
     |  ack=y+1             	 |
     |  -----------------------> |
     |                     		 |
```





##### 四次挥手的意义

1. 客户端向服务器发送FIN请求后，如果服务器还有未发送的数据，可以在回复ACK报文后继续发送数据，保证数据的完整性。
2. 服务器可以在发送FIN请求后等待一段时间，等待已经发送的数据被客户端确认接收，保证数据的可靠性。
3. 客户端和服务器都需要等待一段时间（一般为2MSL），以确保对方已经接收到FIN请求和ACK确认，避免数据丢失和重传。

###### 过程：

第一步：客户端发送关闭连接请求

客户端发送一个FIN（终止）消息给服务端，表示它不再需要连接。这个消息的序列号是客户端最后一个发送的消息的序列号，因为客户端不会再发送数据了。

```bash

客户端               服务端
  |                    |
  |-----FIN----->      |
  |    (FIN_WAIT_1)    |
  |                    |

```



第二步：服务端回复确认消息

服务端接收到FIN消息后，会发回一个ACK（确认）消息，告诉客户端已经收到了关闭请求。ACK消息的序列号是服务端最后一个接收的消息的序列号，同时确认客户端的FIN消息，ACK消息的确认号为客户端的序列号+1。

```bash
客户端               服务端
  |                    |
  |-----FIN----->      |
  |    (FIN_WAIT_1)    |
  |                    |
  |<----ACK------------|
  |    (CLOSE_WAIT)    |
  |                    |

```

客户端接收到服务端的ACK数据包后，进入FIN_WAIT_2状态，等待服务端发送关闭请求

```
客户端               服务端
  |                    |
  |-----FIN----->      |
  |    (FIN_WAIT_1)    |
  |                    |
  |<----ACK------------|
  |    (CLOSE_WAIT)    |
  |                    |
  |                    |
  |    (FIN_WAIT_2)    |
  |                    |

```

第三步：服务端发送关闭连接请求

服务端也需要关闭连接，所以它会发送一个FIN消息给客户端，序列号是服务端最后一个发送的消息的序列号，确认号为客户端的序列号+1。

```bash
客户端               服务端
  |                    |
  |-----FIN----->      |
  |    (FIN_WAIT_1)    |
  |                    |
  |<----ACK------------|
  |    (CLOSE_WAIT)    |
  |                    |
  |                    |
  |<----FIN-------------|
  |    (LAST_ACK)      |
  |                    |
```



第四步：客户端回复确认消息

客户端接收到服务端的FIN数据包后，发送ACK数据包，表示已经接收到服务端的关闭请求，并进入TIME_WAIT状态，等待2MSL时间后进入CLOSED状态。

```bash
客户端               服务端
  |                    |
  |-----FIN----->      |
  |    (FIN_WAIT_1)    |
  |                    |
  |<----ACK------------|
  |    (CLOSE_WAIT)    |
  |                    |
  |                    |
  |<----FIN-------------|
  |    (LAST_ACK)      |
  |                    |
  |-----ACK----------> |
  |    (TIME_WAIT)     |
  |                    |
```

服务端接收到客户端的ACK数据包后，进入CLOSED状态，连接关闭

```bash
客户端               服务端
  |                    |
  |-----FIN----->      |
  |    (FIN_WAIT_1)    |
  |                    |
  |<----ACK------------|
  |    (CLOSE_WAIT)    |
  |                    |
  |                    |
  |<----FIN-------------|
  |    (LAST_ACK)      |
  |                    |
  |-----ACK----------> |
  |    (TIME_WAIT)     |
  |                    |
  |                    |
  |<----ACK-------------|
  |     (CLOSED)       |
  |                    |

```

客户端等待2MSL时间后，进入CLOSED状态，连接关闭

```bash
客户端               服务端
  |                    |
  |-----FIN

```





>这些设置可以保证TCP协议的可靠性和完整性。TCP协议是面向连接的协议，每个连接都需要经过三次握手建立，保证连接的正常建立和通信。在关闭连接时，四次挥手可以保证数据的完整性和可靠性。这些设置可以避免数据传输出现错误和丢失，保证TCP协议的可靠性和稳定性。









#### 其他重要知识补充：



**时间戳选项**：TCP在头部中添加⼀个选项字段，⽤于记录当前数据包发送的时间戳。时间戳选项可以让发送⽅根据时间戳来计算序列号，从⽽增加序列号的范围。



**时间窗⼝机制**：TCP还引⼊了时间窗⼝机制，⽤于限制发送⽅在⼀个时间段内发送的数据包数量。发送⽅通过计算接收⽅的接收窗⼝⼤⼩，来确定当前可以发送的数据包数量。如果接收⽅的接收窗⼝很⼩，说明缓存区已经满了，此时发送⽅就需要等待接收⽅确认应答之后再继续发送数据包。这样可以避免发送⽅⼀次性发送过多的数据包，从⽽减少序列号的重复。



**拥塞控制机制：**来避免⽹络拥塞，保证⽹络的稳定性和可靠性。拥塞控制的实现主要分为两个⽅⾯：慢启动和拥塞避免。

>慢启动机制是指在 TCP 连接刚建⽴时，发送⽅先发送少量数据，然后根据对⽅接收的情况逐渐增加发送量，以此来适应⽹络的传输速度
>
>步骤：
>
>1、发送⽅每经过⼀个往返时间（Round-Trip Time，RTT），就将拥塞窗⼝的⼤⼩增加 1，即窗⼝⼤⼩变为原来的 1 + 1/mss（mss 表示TCP 报⽂段的最⼤⻓度）
>
>2、如果⽹络出现拥塞，接收⽅会发送⼀个重复确认（Duplicate Acknowledgement），表示某个报⽂段已经被接收⽅接收了多次。当发送⽅连续收到 3 个重复确认时，就意味着⽹络可能已经发⽣拥塞，于是发送⽅将慢启动阈值设置为当前拥塞窗⼝的⼀半，并将拥塞窗⼝的⼤⼩重新设置为慢启动阈值，重新开始慢启动阶段。









### TCP 协议需要解决什么问题？



1、如何将数据包从源主机传输到⽬标主机

TCP通过IP协议实现⽹络互联，它将数据划分成多个数据包，每个数据 包都有⼀个TCP头和⼀个IP头。TCP使⽤IP地址来确定源和⽬的主机， 使⽤端⼝号来确定⽬标进程



2、如何确定数据包传输时的路由路径

在路由选择⽅⾯，TCP使⽤IP协议提供的 路由选择功能来确定数据包的路由路径。为了保证数据包的可靠传 输



3、确保数据包可靠传输

TCP通过分段将数据分成更⼩的数TCP通过分段将数据分成更⼩的数据 包进⾏传输，以确保可靠性和有序性。在数据传输过程中，TCP还能够检测并纠正数据传输中出现的错误据包进⾏传输，以确保可靠性和 有序性。在数据传输过程中，TCP还能够检测并纠正数据传输中出现的错误



TCP通过使⽤校验和机制来纠正数据包。每个TCP报⽂段都有⼀个16位 校验和字段，⽤于检查报⽂段中的错误。在发送端，TCP计算数据段 的校验和并将其添加到数据包头部。在接收端，TCP重新计算接收到 的数据段的校验和，如果检测到错误，则丢弃该数据段并请求发送端 重新发送该数据段

>假设TCP报⽂段中的数据为：0x4500 003c 1c46 4000 4006 b2a3 ac10  0a63 ac10 0a0c，
>
>TCP头部信息为：0x4d2f 71b0 0000 0000 8002 ffff 29cc  0000 0204 05b4 0000。
>
>则TCP计算出的校验和为：0xbce8。该校验和与 数据包⼀起发送到接收⽅，接收⽅同样按照相同的算法计算数据包的 校验和，并将其与发送⽅传来的校验和进⾏⽐较。如果两个校验和相等，则表明数据传输过程中没有发⽣错误
>
>计算方式：
>
>1. 首先将TCP头部和TCP数据字段看作一个连续的字节序列。
>2. 将该字节序列划分为16位的块，如果最后剩下的不足16位，则在末尾添加0，使得该字节序列的长度为偶数个字节。
>3. 将所有16位块进行二进制反码求和，将得到一个16位的结果。
>4. 将上一步得到的16位结果再次取反得到最终的TCP校验和值。





### TCP 结构



**TCP报⽂段头部**：TCP 协议将需要传输的数据分成多个 报⽂段，每个报⽂段都包含⼀个 TCP报⽂段头部，TCP报⽂段头部 包含了TCP协议所需的各种控制信 息，如源端⼝号、⽬标端⼝号、 序列号、确认号、窗⼝⼤⼩等。



1 源端⼝号（Source Port）和⽬标端⼝号（Destination Port）： 分别指发送端和接收端的端⼝号。TCP协议使⽤端⼝号来区分不同的 应⽤程序。



2 序列号（Sequence Number）：指本次传输数据的第⼀个字 节在数据流中的序列号。⽤来保证接收⽅可以正确地重组数据流。



3 确认号（Acknowledgment Number）：指接收端期望下⼀个 收到的字节在数据流中的序列号。⽤来实现可靠传输，接收⽅发送⼀ 个ACK报⽂确认已经成功接收到数据。



4 数据偏移（Data Offset）：指TCP头部的⻓度，以4字节为单 位。TCP头部⻓度最⼩为20字节，最⼤为60字节。



5 保留（Reserved）：暂时未使⽤，保留字段。



6 控制位（Flags）：包含了TCP协议的⼀些控制信息，如 URG、ACK、PSH、RST、SYN、FIN等标志位。这些标志位⽤来控制 TCP连接的建⽴、维护和释放。



7 窗⼝⼤⼩（Window Size）：表示接收⽅可以接收的字节数 量。⽤来实现流量控制。



8 校验和（Checksum）：⽤于检测TCP报⽂段是否出现了错 误。TCP报⽂段头部和数据部分的校验和都要计算，如果出现了错 误，接收⽅会丢弃这个报⽂段。



9 紧急指针（Urgent Pointer）：⽤于实现紧急数据传输。紧急 指针指出了紧急数据在数据流中的位置。



10 选项（Options）：可选字段，包含了⼀些TCP协议的可选参 数，如时间戳、最⼤报⽂段⻓度等



**数据部分**：TCP协议的 数据部分是需要传输的数据，其 ⼤⼩不⼀定是报⽂段的整数倍， 因此需要进⾏分段操作。



**TCP报⽂段尾部**：TCP 报⽂段尾部是TCP协议为了保证传 输数据的可靠性⽽添加的冗余校 验信息，主要⽤于校验数据是否 在传输过程中发⽣了损坏或丢失





