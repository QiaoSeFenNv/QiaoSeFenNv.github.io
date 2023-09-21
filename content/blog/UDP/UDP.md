
---
title: "🚀 UDP  "
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



## UDP

UDP（User Datagram Protocol）是一种无连接的传输层协议，它不提供像TCP一样的可靠性和流控制，但它具有更低的开销和更快的传输速度，适合在要求实时性较高，但可靠性要求不高的场景下使用

### UDP协议原理：

1. 无连接：UDP协议是一种无连接的协议，它在发送数据之前不需要先建立连接，因此发送数据的过程非常快。UDP不像TCP那样需要先建立连接，维护连接状态，释放连接等，因此UDP可以快速发送数据，并且具有更低的开销。
2. 不可靠：UDP协议不提供可靠性保证，也就是说，当数据包发送出去后，UDP无法保证数据包一定会被接收方接收到。如果数据包在传输过程中丢失、重复、乱序，UDP无法检测到并进行重传，因此UDP不适合在对数据可靠性要求比较高的场景中使用。
3. 简单：UDP协议的头部非常简单，只有8个字节，包含源端口、目标端口、长度和校验和等信息。由于UDP头部非常简单，因此UDP的开销比较小，传输效率比较高。
4. 支持广播和多播：UDP协议支持向网络中的所有设备广播数据，也支持向一组设备发送数据，即多播。这使得UDP在实现多播和广播等应用时比TCP更具优势。





### UDP协议格式：

**UDP头部和UDP数据**

UDP头部格式如下：

| 源端口号 | 目标端口号 | 长度   | 校验和 |
| -------- | ---------- | ------ | ------ |
| 2 字节   | 2 字节     | 2 字节 | 2 字节 |

其中，各字段的含义如下：

- 源端口号和目标端口号：每个UDP数据包都包含源端口号和目标端口号。源端口号和目标端口号都是16位的整数，用于标识发送方和接收方的应用程序。
- 长度：该字段指定了UDP数据包（包括头部和数据）的长度，以字节为单位。因为UDP数据包的长度不能超过65535个字节，所以该字段只需要占用2个字节。
- 校验和：该字段用于校验UDP数据包是否在传输过程中发生了错误。UDP协议的校验和是可选的，如果不需要校验，则该字段的值为0。

UDP数据部分可以是任意长度的数据，最大长度为65535个字节（即2^16-1字节），没有规定数据的格式和内容。UDP数据包的总长度等于UDP头部长度加数据长度。



**UDP数据包的格式简单明了，头部长度只有8个字节，因此UDP协议的数据包大小比TCP协议的数据包要小。这使得UDP协议在网络传输中比TCP协议更加高效。**





### 端口：

1. 公认端口

公认端口是由IANA（Internet Assigned Numbers Authority）管理的端口号，它们的范围是0到1023。公认端口是被广泛使用的端口，常用于标准的网络服务，例如HTTP（80）、FTP（21）和Telnet（23）等。

2. 注册端口

注册端口是由IANA分配的端口号，范围从1024到49151。注册端口用于应用程序的特定服务，这些服务是由应用程序开发人员自己定义的。

3. 动态端口

动态端口的范围是从49152到65535。动态端口是由客户端操作系统自动分配的，用于发送数据包的源端口。当客户端应用程序发送UDP数据包时，它们不需要提前知道自己的源端口，操作系统会自动分配一个未被使用的动态端口。





### Java中的UDP编程：

1. DatagramSocket类：用于建立UDP连接，并进行数据的发送和接收操作。
2. DatagramPacket类：用于封装数据包，包括数据、目标地址和端口等信息。



```java
import java.net.*;

public class UDPSender {
    public static void main(String[] args) {
        try {
            DatagramSocket socket = new DatagramSocket(); // 创建DatagramSocket对象
            String message = "Hello, World!"; // 要发送的消息
            byte[] data = message.getBytes(); // 将消息转换为字节数组

            InetAddress address = InetAddress.getByName("127.0.0.1"); // 目标地址
            int port = 8888; // 目标端口号

            DatagramPacket packet = new DatagramPacket(data, data.length, address, port); // 创建DatagramPacket对象

            socket.send(packet); // 发送数据包

            byte[] buffer = new byte[1024]; // 创建缓冲区

            DatagramPacket receivePacket = new DatagramPacket(buffer, buffer.length); // 创建接收数据包

            socket.receive(receivePacket); // 接收响应数据包

            String response = new String(receivePacket.getData(), 0, receivePacket.getLength()); // 获取响应消息

            System.out.println("Response: " + response); // 输出响应消息

            socket.close(); // 关闭DatagramSocket对象

        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}

```

>创建了一个DatagramSocket对象，然后将需要发送的消息转换为字节数组，并使用DatagramPacket对象封装需要发送的数据、目标地址和端口等信息。然后使用DatagramSocket的send()方法将数据包发送到目标地址。
>
>创建了一个缓冲区和一个DatagramPacket对象用于接收来自目标地址的响应消息。最后，使用DatagramSocket的receive()方法接收来自目标地址的响应消息，并使用DatagramPacket的getData()方法获取响应消息，然后输出响应消息。最后，关闭了DatagramSocket对象



### HTTP 3

HTTP/3是一种基于UDP协议的新一代Web协议，它采用了QUIC协议作为传输层协议。QUIC（Quick UDP Internet Connections）是一种基于UDP协议的快速可靠传输协议。



1. 可靠传输：尽管UDP本身是一种不可靠的协议，但是QUIC协议在其上面增加了可靠性保证机制，确保了数据的可靠传输。因此，在HTTP/3中，UDP协议作为QUIC协议的底层传输协议，提供了可靠的数据传输保障。
2. 快速连接建立：QUIC协议使用了0-RTT技术，可以在客户端和服务器之间建立快速连接，避免了TCP连接建立时的握手延迟。这对于HTTP/3的性能提升非常重要，而UDP协议正是QUIC协议的底层传输协议，支持了这一特性。
3. 低延迟和高带宽利用率：UDP协议相比TCP协议具有更低的延迟和更好的带宽利用率。在HTTP/3中，采用了基于UDP的QUIC协议作为传输层协议，可以大幅度降低网络延迟，提升带宽利用率，进一步提升了HTTP/3的性能。
4. 兼容性：HTTP/3在设计上兼容了HTTP/2，并且在HTTP/2的基础上做出了一些改进。采用UDP协议作为QUIC协议的底层传输协议，可以保证HTTP/3的兼容性，同时具有更好的性能表现。





