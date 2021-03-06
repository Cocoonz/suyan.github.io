---
layout: post
title: 《计算机网络：自顶向下方法》学习笔记（更新中）
category: 学习
tags: "computer science"
description: 
---



## Chapter 2 Application Layer

### 网络应用架构

*Network application architectures*

由应用开发者设计，决定了应用在不同终端上的结构

1. C/S 架构（client-server architecture）

   - 特点：
     - 一台常开主机（always-on host）（服务器）响应来自其他主机（客户机）的请求
     - 客户机之间互相不能直接通信
     - server 具有固定的、为人熟知的 IP 地址

   - 数据中心（data center）：多台主机构成的虚拟服务器（virtual server），解决单台 server 不能满足应用需求的问题
   - 应用：Web、FTP、Telnet、e-mail

2. P2P 架构（peer-to-peer architecture）
   - 特点：
     - 没有专门 server，主机之间直接通信
     - 良好的自扩展性（self-scalability）
     - 高效（efficient），因为不需要服务器架构，也没有带宽限制
   - 面临的挑战：
     - ISP friendly，目前大多数 ISP 提供的服务都是上下行速度不对称的，P2P 的方式给 ISP 带来了很大压力
     - 安全性
     - 动机（incentive）设计，鼓励用户贡献带宽等资源
   - 应用：BitTorrent、迅雷、PPTV



### 进程通信

*Processes Communicating*

1. 同一终端上的进程通信：进程间通信（interprocess communication），由操作系统管理

2. 不同终端上的进程通信：通过网络交换信息

   - client process：发起通信的一方，server precess：等待通信的一方

   - **Socket**：进程与网络的软件交界（software interface），是同一台主机上**应用层和网络层的交界**。（如果把 process 比作房子，那么 socket 就是房子的大门）

     > a socket is the interface between the application layer and the transport
     > layer within a host.

   - socket 也被称为应用层和网络层之间的应用编程接口（Application Programming Interface），应用开发者对于应用层的部分有控制权，但对网络层部分没有什么权限

   ![](https://s2.ax1x.com/2019/11/11/MlJ80I.png)

3. 进程寻址（Process Addressing）

   进程地址由两部分信息组成：

   - 主机地址（the address of host）：即 32 位 IP 地址

   - 目标进程标识符（an identifier that specifies the receiving process in the destination host），或者说是目标进程的 socket，即端口号（port number）

   - 一些主流应用被分配了固定的端口号：web server 80，mail server 25

     > 常用端口号	http://www.iana.org



### 传输层协议为应用提供的服务（Transport Services Available to Applications）  

1. 可靠数据传输（reliable data transfer）

   - 一些应用需要完整无误的数据传输，如电子邮件、文件传输等

   - loss-tolerant application：可以接受传输过程中数据丢失，如多媒体应用

2. 吞吐量（throughput）

   - 可用带宽（available throughput）：发送进程传输数据给接收进程的速度
   - 带宽保证（throughput guarantees）：保证带宽达到某一水平
   - 带宽敏感性应用（bandwidth-sensitive applications）：有最小带宽要求，如当前很多多媒体应用
   - 可伸缩应用（elastic application）：自适应带宽，如电子邮件、文件传输等

3. 时间（timing）

   实时应用如电话、电子会议、多人游戏等对延时要求较高

4. 安全性（security）

   发送端和接收端的加解密、数据完整性（data integrity）、末尾校验（end-point authentication）



### 因特网提供的传输服务

*Transport Services Provided by the Internet*

1. TCP

   - 在应用层可以通过 SSL 加密

   - 面向连接（connection-oriented）的服务：开始通信前需要通过握手建立两个应用 socket 之间的 TCP 连接（TCP connection），完成通信后需要断开连接
   - 可靠数据传输
   - 拥塞控制机制（congestion control mechanism）：当网络拥塞时对发送端节流

2. UDP

   - 无需建立连接
   - 数据传输不可靠
   - 没有拥塞控制

3. 今天的因特网虽然可以为时间敏感的应用提供满意的服务，但是它仍不能提供对吞吐量和延时限制的保证

   ![page0](https://s2.ax1x.com/2019/11/12/M18wfU.png) 



### 应用层协议

*Application-Layer Protocols*

应用层协议决定了不同主机上运行的应用程序如何互相通信。

> An application-layer protocol defines how an application’s processes, running on different end systems, pass messages to each other.  

- 应用层协议定义了：
  - 消息的类型（  The types of messages exchanged  ），如请求消息（request messages）、回复消息（response messages）
  - 不同消息类型的语法字段及其定义（  The syntax of the various message types, such as the fields in the message and how the fields are delineated  ）
  - 每个字段的语义（  The semantics of the fields  ）
  - 发送和接受消息的时间和方式（  Rules for determining when and how a process sends messages and responds to messages  ）

- 应用层协议仅仅是应用的一部分

  一个 web 应用的组成：文档格式标准（HTML）、客户端应用（浏览器，如 Firefox 或 Chrome）、服务器应用（web server，如 Apache）、应用层协议（HTTP）

  一个 e-mail 应用的组成：邮件格式标准、mail servers、mail clients（e.g. Outlook）、应用层协议（SMTP）



### Web 应用

1. 一些术语：

   - 网页（web pages）：由对象（objects）组成的文件，对象也是一个文件，可以是 HTML 文件、文本文件，也可以是图片、视频、应用等。绝大多数网页由一个基础 HTML 文件（base HTML file）和若干引用对象（referenced objects）组成

   -  统一资源定位系统（uniform resource locator，URL）：定位对象的标识符，由主机名（hostname）和路径名（pathname）组成，用于寻址某一特定对象

     ![image-20191109235137589](https://s2.ax1x.com/2019/11/11/MlJM1e.png)

2. Web 应用在应用层使用 HTTP 协议

   

### HTTP 协议

1. 

2. 无状态（stateless）：不记录主机信息，比如同一个主机连续两次请求同一个对象，服务器会返回两次，而不是察觉它已经请求过了

3. 持续连接（persistent connection）：一次 TCP 连接处理多个请求（request），默认模式

   非持续连接（non-persistent connection）：一次 TCP 连接处理一个请求（request），但是可以配置多个请求并行（parallel）
   
   🌰：client 向服务器请求一个网页，它包含一个 HTML 文件和 10 张图片，在非持续连接方式下，需要请求 11 次（建立 11 次 TCP 连接），持续方式下只需要 1 次。
   
   - round-trip time（RTT）：数据包（packet）从服务器到客户端**往返**一次的时间（包括传播延迟（packet-propagation delay）、排队延迟（packet-queuing delay）、处理时间（packet-processing delay）<font color="gray">1.4 节</font>）
   
   - 响应时间（response time）：从客户端发起请求到最终收到数据的时间
   
     - non-persistent connection：2 RTT + transmisson time
   
       <img src="https://s2.ax1x.com/2019/11/11/MlJ3nA.md.png" style="zoom:50%;" />
   
4. 两种消息格式：

   - HTTP request message

     - 由 ASCII 码书写

     - 第一行：Request line

       - method：GET / POST / PUT（上传） / DELETE / HEAD（调试）
       - URL
       - Version：HTTP version

     - 接下来若干行：Header lines

       - Host：请求对象所在的主机名（代理服务器需要）
       - User-agent：浏览器类型及版本
       - Connection：指明是否是持续连接
       - ……

     - 空行：额外的回车+换行（carriage return + line feed）

     - entity body：POST 方法需要，用户提交的内容

       ![image-20191110123920991](https://s2.ax1x.com/2019/11/11/MlJQ6H.md.png)

     - 使用 GET 方法提交表单时，不需要填写 Entity body 字段，而是将用户填写的信息放在 request line 的 URL 字段，如：<font style="font-family:courier; font-weight=800">www.somesite.com/animalsearch?monkeys&bananas</font>

   - HTTP response message

     - 第 1 行：status line

       - HTTP version
       - 状态码（status code）

       <img src="https://s2.ax1x.com/2019/11/11/MlJYAP.md.png" style="zoom:50%;" />

       - 状态描述（status phrase）

     - 若干行：header line

       - Connection：指明是否是持续连接
       - Date：服务器发送回执消息的日期时间
       - Server：web server 的类型
       - Last-Modified：对象上次被修改的时间
       - Content-Length：字节长度
       - Content-Type：指明 entity body 字段的对象的文件类型（文件类型由本字段指明，并非文件拓展名）

     - 第 8 行：空行，回车 + 换行

     - entity body：返回的对象文件

     ![image-20191110131328647](https://s2.ax1x.com/2019/11/11/MlJG7t.png)

   - header line 可选非常多，不胜枚举

5. Cookies：用户与服务器的交互

   - 四个组成部分：

     - a cookie header line in HTTP response message
     - a cookie header line in HTTP request message
     - 用户主机上的 cookie 文件，由浏览器管理
     - 后端数据库（back-end database）

   - 🌰：Susan 第一次访问 Amazon

     - 服务器创建一个独一无二的**标识符（identification number）**，并在后端数据库添加了一个以标识符作为索引的表项（entry）
     - 服务器在返回给 Susan 的 response message 中添加了这样一个头部：<font style="font-family:courier;font-weight:bold">Set-cookie: 1678（identification number）</font>
     - Susan 的浏览器收到服务器返回的消息后，向 cookie 文件中追加了服务器的主机名（hostname）以及标识符
     - 之后每次 Susan 向 Amazon 服务器发起请求时，request message 中都带有头部 <font style="font-family:courier;font-weight:bold">Cookie: 1678</font>

     一周后，Susan 再次访问 Amazon，request message 中带有 cookie 头部，服务器根据标识符从数据库中取出有关 Susan 的信息，用于收货地址、商品推荐、购物车合并下单等

   - 隐私问题引起争议

6. Web 缓存 / 代理服务器（Web Cache / proxy server）

   - 代理原服务器（original server）处理用户请求，拥有自己独立的存储空间，存储最近被请求的对象文件
   - 用户浏览器可以被配置为所有请求优先被定位到代理服务器
   - 既是 server 又是 client
   - 由当地 ISP 购置和安装
   - 优点：
     - 大大降低了响应时间（尤其当 $bandwidth_{client-server} \ll bandwidth_{client-cache}$ 时，通常 client 与 cache 之间的通信都是迅速的）
     - 整体上减小了全网络的交通量，提高了所有应用的性能

   - conditional GET：cache 给服务器发送请求时带上头部  <font style="font-family:courier;font-weight:bold">If-Modified-Since:</font>，如果在这个时间之后对象更新过，服务器返回最新的对象文件；否则不返回任何 entity body 字段，但需要在 status code 字段指明 <font style="font-family:courier;font-weight:bold">304 Not Modified</font>

     条件 GET 用于解决 cache 中的数据和原始服务器数据不同步的问题



### FTP 协议

1. 两条并行的 TCP 连接：

   - 控制连接（control connection）：由 client 发起
   - 数据连接（data connection）：由 server 发起，一次连接只能传输一个文件

   所以 FTP 中控制信息的传输是 out-of-band 的（同理，HTTP 和 SMTP 中控制信息的传输是 in-band 的）

   ![image-20191110151458733](https://s2.ax1x.com/2019/11/11/MlJNh8.png)

2. 建立过程
   - client 发起 TCP 连接（控制连接），端口号 21
   - client 发送用户名和密码，并发送改变目录的命令
   - server 发起 TCP 连接（数据连接），端口号 20
3. FTP 服务器是有状态（state）的，用户账户和控制连接是绑定的，服务器必须可以追踪用户当前访问的目录（这也限制了 FTP 服务器可以同时服务的会话（session）数量）
4. 报文格式：
   - command（client→server）：4 位大写 ASCII 字母
   - reply（server→client）：3 位数字，类似于 status code
5. 与 HTTP 比较：
   - 底层都是 TCP 协议
   - 最大的区别是 FTP 的数据连接和控制连接是分开的



### SMTP 协议

1. 电子邮件系统的组成：
   -  User Agent：允许用户查看、回复、转发、保存、编辑新邮件，如 OutLook 等
   - mail server：核心，保存用户收到的邮件 / 发送邮件
   - Simple Mail Transfer Protocol（SMTP）：决定了发件人发送的邮件如何被送给收件人

2. 电子邮件发送和收取的过程：

   🌰：Alice 想要发一封 email 给 Bob

   - Alice 用她的 user agent 编辑好了一封邮件，点击发送
   - Alice 的 user agent 把这封邮件发送到她的 mail server 上，在那里，这封邮件进入发件队列（outgoing message queue）
   - 这封邮件被发送到 Bob 的 mail server 上（发送可能失败，Alice 的 mail server 将这封邮件放入消息队列（message queue）中，隔一段时间再尝试发送；如果发送还是多次失败，这封邮件将从消息队列中移除，并且邮件服务器将会告知 Alice）
   - Bob 的 mail server 收到邮件并保存在 mailbox 中
   - 当 Bob 查看他的邮件时，这封邮件将被从 mailbox 中取出

   ![image-20191110212136691](https://s2.ax1x.com/2019/11/11/MlJa9S.png)

3. SMTP 协议概述

   - 过程
     - SMTP 的客户端（运行在发件服务器主机上）向 SMTP 的服务器端（运行在收件服务器主机上）发起 TCP 连接，端口号 25
     - 连接建立，SMTP 握手（应用层），握手过程中 client 需要确认发件和收件的地址
     - 客户端把邮件发送给服务器端
     - 
   - 不需要中间服务器，发件和收件服务器直接建立 TCP 连接通信
   - persistent TCP connection

4. HTTP 与 SMTP 比较

   |                         SMTP                         |                HTTP                 |
   | :--------------------------------------------------: | :---------------------------------: |
   |         push protocol，TCP 连接由发件方发起          | pull protocol，TCP 连接由收件方发起 |
   | 报文必须是 7 位 ASCII 码格式，图片等必须编码后再传输 |              没有限制               |
   |               每个报文可以传输多个对象               | 每个对象都需要由独立的 TCP 连接传输 |

5. mail access protocols（收件协议）

   一般来说，user agent 运行在用户主机上，但是 mailbox 存储在常开的服务器上，这个服务器一般由当地 ISP 架设。mail access protocol 决定了邮件如何从收件人的 mail server 传输到收件人的主机上。

   - Post Office Protocol version 3，POP3
   - International Mail Access Protocol，IMAP



### DNS 协议

1. 标识主机的两种方法：
   - 主机名（hostname）：方便记忆，数字和字母的组合，对主机地址信息没有说明，并且对路由不友好
   - IP 地址（IP address）：指明主机在因特网上的地址，4 字节，长度固定，层次化

2. DNS 协议

   用户偏好主机名，路由偏好 IP 地址，所以需要一种目录服务将主机名解析成 IP 地址，DNS 就是为此而生的

   - DNS（Domain Name System）是：
     - DNS 是一个分布式数据库（distributed database）运行在一系列 DNS 服务器上
     - 允许不同主机查询数据库的应用层协议

   - 域名解析过程：

     🌰：用户的主机请求 URL <font style="font-family:courier;font-weight:bold">www.someschool.edu/index.html</font>

     - DNS 的客户端运行在用户主机上
     - 浏览器提取 URL 中的 hostname 部分 <font style="font-family:courier;font-weight:bold">www.someschool.edu</font> 并将其发送给 DNS 客户端
     - DNS 客户端向服务器发送请求，询问该主机名对应的 IP 地址
     - DNS 客户端收到服务器应答，得到 IP 地址并告知浏览器
     - 浏览器发起 TCP 连接
     - ……

   - DNS 应用提供的其他服务：
     
     - host aliasing
   - 所有 DNS 请求和应答都通过 UDP 数据包传输，端口号 53



## Chapter 3 Transport Layer

### 概览

1. 传输层协议为不同主机上的应用**进程**提供逻辑通信（logical communication），网络层协议为不同**主机**提供逻辑通信

   逻辑通信：在应用进程的角度来看，两个主机是直接相连的（但事实上，两个主机在地理上可能相距甚远，并且通过很多路由和不同的链接方式才连接到一起）

2. 在一个终端系统内，传输层协议将应用进程的消息传递到网络的边缘（网络层），但是并不关注消息在网络核心中是如何传输的

   > Within an end system, a transport protocol moves messages from application processes to the network edge (that is, the network layer) and vice versa, but it doesn’t have any say about how the messages are moved within the network core.

3. 传输层协议所能提供的服务受制于低层的网络层协议（延时和带宽）

   >the services that a transport protocol can provide are often constrained by the service model of the underlying network-layer protocol.

   但是，当底层的网络协议是不可靠的时候，传输层协议仍可以提供可靠的数据传输（加密保证报文不在传输过程中被读取读取）

   >a transport protocol can offer reliable data transfer service to an application even when the underlying network protocol is unreliable 

4. 传输层的数据包（packet）叫做 **segment**。TCP 的数据包一般也叫做 segment，但是 UDP 的数据包一般叫做 datagram（网络层数据包也叫 datagram）。
5. 传输层协议的最主要任务：**将端到端的传输服务扩展为进程到进程的传输服务**（Extending host-to-host delivery to process-to-process delivery），即 multiplexing 和 demultiplexing



### 复用 & 分用（multiplexing & demultiplexing）

1. 复用（multiplexing）

   - 发生于源主机，从源主机上不同的 socket 处收集数据块，并为它们加上不同的头部，封装成 segments，发送到网络层

   - 要求：

     - socket 有唯一标识符

     - 每个 segment 包含特殊字段表示目的 socket，即源端口号（source port number）字段和目的端口号（destination port number）字段

       端口号是 16 位数字（0\~65535），0\~1023 是周知端口（well known port numbers）

   ![image-20191111102719060](https://s2.ax1x.com/2019/11/11/MlJwcQ.png)

2. 分用（demultiplexing）

   发生于目的主机，从目的主机网处收集 segments，解析其中的字段，并将它们转发到对应的 socket 处

3. TCP 与 UDP 的区别
   - TCP 的 socket 标识符是 4 个元组：源 IP 地址、源端口号、目的 IP 地址、目的端口号
   - UDP 的 socket 标识符是 2 个元组：目的 IP 地址、目的端口号



### 可靠数据传输

*reliable data transfer*

1. idt 1.0

2. idt 2.0

   - ARQ（automatic repeat request） 协议

   - 追加内容：
     - 错误检测（error detection）
     - 收件人回执（receiver feedback）：positive acknowledgement（ACK） / negative acknowledgement（NAK）
     - 重新传输（retransmission）：收件方收到错误数据时可以发送回执 NAK，要求发送方重新发送
   - 由于在这类协议中，仅当 sender 确认 receiver 已经正确收到当前数据包才会发送下一个，所以这类协议也叫做 **stop-and-wait** 协议

   有限状态机（FSM）表示：![image 20191111205826706](https://s2.ax1x.com/2019/11/11/MlJrBn.png) 

3. idt 2.1 （improved version of idt 2.0）

   - 解决问题：idt 2.0 中 ACK / NAK 数据包出错时无法校验
   - 改进方法：在数据包和回执中带上序列号

   FSM 表示：

    ![image 20191111212512209](https://s2.ax1x.com/2019/11/11/MlJ6A0.png)  

   ![](https://s2.ax1x.com/2019/11/11/MlJcNV.md.png)

4. idt 2.2

   - 改进：移除了 NAK 数据包，ACK 包发送上一次检测正确的序列号，即如果两次连续 ACK 序列号一致（duplicate ACKs）表明发生错误

   FSM 表示：

   ![image-20191111213731945](https://s2.ax1x.com/2019/11/11/MlJghT.png)

   ![image-20191111213903035](https://s2.ax1x.com/2019/11/11/MlJR9U.png)

5. idt 3.0

   - 基于假设：底层通道会丢包（lose packet）
   - 解决问题：如何检测丢包？如何解决？

6. 管道（Pipelining）：

   ![image-20191111214926283](https://s2.ax1x.com/2019/11/11/MlJW3F.png)

   ![](https://s2.ax1x.com/2019/11/11/MlU8xS.md.png)

   - 基于问题：传统的 idt 方式使用 stop-and-wait 方式，导致 sender 的带宽利用率非常低（大部分时间都用于 propagate 了）

   - 改进方法：sender 允许一次发送多个包，不用等待回执
   
   - 带来的改变：
   
     - 序列号范围增加
   
     - 协议双方都需要增加缓冲区，sender 需要缓存已经送出但没收到回执的数据包
     
     - 新的错误检测和恢复（error detect and restore）方法
     - **Go-Back-N**（GBN）	——	fill the pipeline
       
         - 允许 sender 一次发送多个数据包，但是管道中最多同时存在 N 个未收到回执的数据包（包括已发出的和未发出的）
         
         - N 指窗口大小（window size），GBN 协议也叫滑动窗口协议（sliding window protocol）
         
         - 收到的数据包顺序错误也会被拒绝，返回的 ACK 包含最后收到的顺序正确的序列号
         
         - sender 需要缓存：window 的上下界、<font style="font-family:courier;font-weight:bold">nextseqnum</font> 在窗口中的位置
         
         - receiver 需要缓存：下一个顺序数据包的队列号
         
         - 为什么施加窗口大小 N 的限制？流量控制和拥塞控制
         
             ![page0](https://s2.ax1x.com/2019/11/11/MlUcqJ.png) 
         
         - FSM 表示：
         
            ![page0](https://s2.ax1x.com/2019/11/11/MlrEFJ.png)
         
            GBN 发送端：
         
            - 接收到上层协议发过来的数据包时，首先判断窗口是否已经充满，如果窗口已满，则把数据包退回给上层协议（实际操作中使用信号量实现同步，仅当窗口未满时通知上层传送数据）；如果窗口未满，则对数据包进行封装并放入队列；
            - 累积确认（cumulative acknowledgement），即确认了第 $n$ 个，表明已经确认了 $n$ 以下的所有数据包
            - 超时重传（重传所有当前**已经发出但是没有收到确认**的数据包）
         
          ![page0](https://s2.ax1x.com/2019/11/11/Mlr8FH.png)  
       
          GBN 接收端：
       
          - 丢弃所有非按序（out-of-order）的数据包（因为接收端将数据包传给上层协议的时候是按序传的）
         
            这个特性决定了 GBN 的接收端只需要缓存户按照顺序下一个应当接受的数据包的序列号
         
            
         
       - **selective repeat**（SR） 
       
         - 针对问题：GBN 的性能问题，当单个数据包出现错误时，可能导致重传多次
         - 对传输正确的数据包**分别**进行确认
         - 不在乎数据包是否按顺序到达，只要正确都给予确认回执（ACK），乱序的数据包将先被缓存
       
         - receiver 将数据发给上层协议时按批（batch）进行
       
         - sender 和 receiver 的窗口状态未必一致
       
         - 窗口大小必须 $\leq$ 序列号范围长度的一半
       
            ![page0](https://s2.ax1x.com/2019/11/11/MlcIDx.png) 
       
     - 总结
     
     <img src="C:%5CUsers%5Caqyjz%5CAppData%5CRoaming%5CTypora%5Ctypora-user-images%5Cimage-20191112000035950.png" alt="image-20191112000035950" style="zoom:120%;" />



### UDP 协议

*User Datagram Protocol*

1. 提供的服务
   - process-to-process delivery
   - error checking
2. 特点：
   - 无连接，无握手过程
   - 
3. UDP 数据报（datagram）格式
   - 头部（header）：8 bytes



### TCP 协议

*Transmission Control Protocol*

1. TCP 的特点：

   - 依赖很多底层的

   - 双向（full-duplex / bidirectional）通信：连接建立后，从 client 到 server 的数据和从 server 到 client 的数据可以同时传送
   - 点到点（point-to-point）：连接建立在两个独立主机之间
   - TCP 存在于终端系统内，与底层的网络无关
   - 连接建立之初需要建立并保存一系列 TCP 状态变量

2. TCP 连接建立过程：

   - client 进程告知 client 主机的传输层它希望与 server 主机的 server 进程建立连接

   - client 主机的传输层向 server 主机的传输层发起 TCP 连接

   - **三次握手（three-way handshake）**

     - 三次握手过程中分别为发送方和收取方预留出 send buffer 和 receive buffer

   - 数据传输

     - maximum segment size（MSS）：segment 数据段的最大长度，即最大**应用层数据**长度（不包含头部）

       超过 MSS 的数据需要分包发送

     - maximum transmission unit（MTU）

3. TCP 报文段（segment）格式

   <img src="https://s2.ax1x.com/2019/11/11/MlJDns.png" alt="image-20191111114807225" style="zoom:67%;" />

   - 头部（header）：20 bytes

     - 源端口号和目标端口号（16 bits * 2）：用于复用、分用

     - 序列号（32 bits）、确认号（32 bits）：用于可靠数据传输

       - **序列号（sequence number）**是报文段中第一个字节的字节流序号（TCP 将报文看作是非结构化的字节流）

         > The sequence number for a segment is therefore the byte-stream number of the first byte in the segment.

         🌰：数据流包含 5000 个字节，MSS 长度为 100，那么这条数据需要分为 50 个 TCP 报文段传输，第一个 segment 的序列号为 0，第二个为 1，……，以此类推

       - **确认号（acknowledgement number）**是指双向通信中一个主机期待收到的另一个主机发送过来下一条报文的序列号

         🌰：主机 A 与主机 B 通信，现在主机 A 已经收到了序列号为 0~53 的 segment，并期待主机 B 从 54 开始继续发送，那么主机 A 可以给主机 B 发送 segment，其中确认号为 54

     - 接收窗口（16 bits）：用于流量控制（flow control）

     - 头部长度（4 bits）

     - 标志字段（6 bits）

       - ACK：用于连接确认
       - RST / SYN / FIN：用于连接建立和断开
       - PSH：接收方需要立即把数据传送给高层
       - URG：指明存在紧急（urgent）信息，地址由 urgent data pointer 字段（16 bits）说明

     - 选项字段（可选，变长）：发送方和接收方需要商讨 MSS 等



## Chapter 4 Network Layer

### 总览

- （4.1）网络层的数据包叫做 **datagram**

- （4.1.1）网络层的任务：将数据包从发送主机送到接收主机

    > The role of the network layer is thus deceptively simple—to move packets from a
    > sending host to a receiving host.

    为了完成这个任务，需要 2 个主要功能：

    - **转发（forwarding）**：当一个数据包到达路由器的输入链路（input link）时，路由器需要将它送到合适的输出链路（output link）
        - 每个路由器都保存了一个**转发表（forwarding table）**，通过对比数据包的头部与转发表决定数据包的去向
    - **路由（routing）**：路由器需要根据路由算法（routing algorithm）计算出数据包从发送主机到接收主机需要经过的路径
        - 路由算法决定了向转发表中插入什么值
        - 路由算法可以是中心化的（centralized）也可以是去中心化的（decentralized）

    注意以上两者的区别，转发是路由器本地（router-local）的操作，路由是整个网络层面（network-wide）的操作

    - 一些网络还需要连接建立（connection setup）功能，路径中的路由器之间相互握手

- 交换机（packet switch）：依据头部字段，将数据包从输入链路接口转发到相应输出链路接口的设备

    > a general packet-switching device that transfers a packet from input link interface to output link interface, according to the value in a field in the header of the packet.  

    - 链路层交换机（link-layer switches）：依据的是链路层结构（link-layer frame）字段的值
    - 路由器（router）：网络层交换机，依据的是网络层字段的值




### 虚拟电路网络 & 数据包网络

- （4.2）虽然网络层与传输层的服务都分为有连接和无连接两种，但是它们有一些本质区别：

    1. 网络层的服务是由网络层为传输层提供的**主机到主机（host-to-host）**的服务，传输层的服务是由传输层为应用层提供的**进程到进程（process-to-process）**的服务

        > In the network layer, these services are host-to-host services provided by the network layer for the transport layer. In the transport layer these services are processto-process services provided by the transport layer for the application layer.

    2. 只提供有连接服务的网络称为**虚拟电路网络（virtual-circuit networks, VC）**，只提供无连接服务的网络称为**数据报网络（datagram networks）**
    3. 传输层由终端（end systems）建立连接，网络层中由路由（routers）建立连接

- （4.2.1）虚拟电路网络

    - 其中的连接（connections）称为虚拟电路（virtual circuits，VCs），组成部分：

        1. 路径（path）：包括若干路由器和路由器间的链路（links），连接源主机和目标主机
        2. **VC 号（VC numbers）**：用于标识路径中的每条链路，在 VC 建立阶段由网络层分配
        3. 转发表中的表项（entries）：用于标识路由器

    - 每个数据包在头部包含 VC 号字段，当这个数据包途径路径中的一个路由器时，路由器查找转发表，更新 VC 号的数据

    - 路由器必须保存正在进行的连接的连接状态信息（connection state information）：VC 建立时，路由器在自己的转发表中添加相应表项，VC 断开时移除这些表项

    - VC 建立的阶段：

        - VC 建立（VC setup）：源主机通知网络层希望建立连接并指明目标主机地址，网络层规划路径、为每条链路分配 VC 号、为途中路由器的转发表添加相应表项并为连接预留资源
        - 数据传递（data transfer）
        - VC 拆除（VC teardown）：删除先前在转发表中添加的表项

    - **网络层的连接是两个终端系统之间的连接**，路径中的路由器对此并不知情

        > Connection setup at the transport layer involves only the two end systems.
        >
        > ... the routers within the network are completely oblivious to it.

    - 发起（initiate）和终止（terminate）连接的消息称为信号消息（signaling messages），约束它们的协议称为信号协议（signaling protocols）

- （4.2.2）数据报网络

    - 不需要预先建立连接，也不需要路由器保存连接状态信息
    - 当数据包到达一个路由器时，该路由器依据转发表决定将数据包发往哪个输出链路（outgoing link）
        - 前缀匹配（prefix match）：表项格式（地址的前 N 位，输出链路）
        - 最长前缀匹配规则（longest prefix matching rule）：本规则适用于前缀匹配方法中一个地址可能匹配多个前缀的情况

    - 路由器中的转发表由路由算法（routing algorithm）修改，在 VC 网络中每条新连接建立都需要修改，在数据报网络中可能定期修改



### 路由器的结构



### IP 协议

*How addressing and forwarding are done in the Internet*

- 负责将数据包从源点交付到终点，所有的 TCP、UDP、ICMP 及 IGMP 数据报都以 IP 数据报格式传输
- 提供**不可靠**、**无连接**的数据报传送服务，对数据进行“尽力传输”，只负责将数据包发送到目的主机，不管传输正确与否，不做验证、不发确认、不保证顺序、不纠错

- （4.4）网络层结构概览

    三个主要部分

    1. IP 协议：寻址（*addressing*）
    2. 路由协议：路径规划和转发
    3. ICMP 协议：错误检测和通知

    <img src="https://s2.ax1x.com/2019/11/26/MzcjRe.png" style="zoom: 50%;" />

- IPv4 数据报格式

     <img src="https://s2.ax1x.com/2019/11/26/Mzg8WF.png" style="zoom: 50%;" /> 

    - 版本号（*version*）：4 bits
    
    - 头部长度（*header length*）：4 bits，用于指明报文内容从何处开始。大多数 IP 报文不包含可选项，典型的头部长度为 20 bits
    
    - 服务类型（type of service，TOS）
    
    - 数据报长度（*datagram length*），以 Bytes 为单位：头部 + 数据的总长度，16 bits（∴IP 报文的理论上的最大长度为 65535 字节，尽管事实上很少有报文超过 1500 字节）
    
    - 标识符（identification number） / 信号（*flags*） / 分片偏移（*fragmentation offset*）：用于 IP 分片，IPv6 没有这些字段
    
    - 存活时间（*time-to-live*，TTL）：每当数据报被路由器处理一次，TTL 减一，当 TTL = 0 时，数据报被丢弃
    
    - 上层协议（*Upper-layer protocol*）：仅在终点使用
    
    - 首部校验和（*header checksum*）：将 2 Bytes 看成一个数字，求和的反码（1s compliment）。校验和出错的数据报将直接被路由器丢弃。
    
      >The header checksum is computed by treating each 2 bytes in the header as a number and summing these numbers using 1s complement arithmetic.  
    
    - 源 IP 地址（source IP address）、目标 IP 地址（destination IP address）：各 32 bits
    
    - 可选部分（options）
    
    以上字段为头部。
    
    - 数据（data / payload）

- （4.4.1）**IP 数据报分片（IP datagram fragmentation）**
  - 最大传输单元（maximun transmission unit，**MTU**）：链路层帧的最大数据长度。不同的链路（link）可以拥有不同的网络层协议，因此对应不同的 MTU，如 $\rm MTU_{Ethernet} = 1500 bytes$
  - IP 数据包的最大负载长度为 65535 字节
  - 数据包可以被源主机或在其路径上的其他路由器分片（分片意味着需要重新计算校验和），但是只能由目的主机重组（reassemble）
  - （🌰 Figure 4.14）IP 数据报中与分片有关的头部字段：标识（*identification number*）、标志（*flags*）、分片偏移（*fragmentation offset*）
    - 标识：标识字段与源 IP 地址唯一确定了一个数据包；相同数据包的分片具有相同的标识号（也是重组的依据）
    - 标志：3 bit，第一位保留，第二位不分片位（1 表示不分片，0 表示分片），第三位还有分片位（1 表示还有分片，0 表示当前是最后一片）
    - 偏移量：用于目标主机重组分片时确定分片在原数据包中的相对位置，**以 8 字节为单位（所以分片时需要保证每片长度为 8 字节的倍数）**

- IP 数据包校验

     - ⭐IP 的校验和**只校验IP 头部**而不校验数据部分

          原因：

          1. 所有封装在 IP 数据包中的高层协议都有校验整个数据包的校验和
          2. 每经过一个路由器首部都要改变一次，但数据部分不改变，检验数据部分开销太大

     - 发送端计算校验和：将数据包按 16 位分段，将所有段相加取反码得到校验和

     - 接收端计算校验和：将数据包（包括校验和）按 16 位分段，将所有段相加取反码，结果为 0 接受，否则丢弃

- （4.4.2）**IPv4 地址（IPv4 addressing）**
  
  - 一个 IP 地址唯一标识了因特网上的一台主机
  
  - 路由器或者主机与链路的交界称为**接口（interface）**，IP 地址用于寻址接口而非主机或者路由器
  
  - 地址空间：协议使用的地址总数，IP 地址的地址空间为 $2^{32}$
  
  - 地址分类
  
        <img src="https://s2.ax1x.com/2020/01/05/lDQbDJ.png" alt="1" style="zoom: 80%;" /> <img src="https://s2.ax1x.com/2020/01/05/lDQHu4.png" alt="2" style="zoom:80%;" />  
       - A、B、C：正常用户使用
  
            - 其中地址分为网络地址（net-id）和主机地址（host-id）两部分
  
                  <img src="https://s2.ax1x.com/2020/01/05/lDl3an.png" alt="1" style="zoom:80%;" /> 
  
       - D：广播地址
  
       - E：保留地址
  
  - 特殊 IP 地址
  
       - 网络地址：主机号全 “0” 的 IP 地址作为网络本身的标识，不分配给任何主机
       - 直接广播地址：主机号为全 “1” 的 IP 地址作为广播地址，不分配给任何主机
       - 有限广播地址：32 位全为 “1” 的 IP地址（255.255.255.255）
       - 主机本身地址：32 为全为 “0” 的 IP 地址（0.0.0.0）
       - 回环地址：127.0.0.1
  
  - 全局 IP 地址和专用 IP 地址
  
       - 全局 IP 地址：用于 Internet 上的公共主机
       - 专用 IP 地址：仅用于专用网络内部的主机
       - 本地主机必须经过**网络地址转换**服务器（NAT 或代理服务器）才能访问 Internet
  
  - 子网划分
  
       - 主机号（host-id）划分为子网号（sub-id）和主机号（host-id）
       - 子网掩码（subnet mask）：用来查询子网号的位数，为 1 的位留给网络号和子网号，为 0 的位留给主机号
            - 点分表示法
            - CIDR（Classless Inter-Domain Routing） 表示法，斜线（/）后标注**前缀（prefix）**长度
                 - 为什么叫 classless？因为在 CIDR 前网络号的位数被限定为 8 的倍数
  
- DHCP 协议（动态主机配置协议）

     - 简介

          - 动态分配 IP 地址
          - 一般用于大型网络环境配置
          - 分配的 IP 地址的租期在 1 分钟 ~ 100 年之间，到期后可分配给别的主机
          - 使用 **UDP** 的熟知端口 67（服务器）和 68（客户端）

     - 地址分配

          DHCP 有两个数据库，分别存放静态地址（与物理地址捆绑）和动态地址（可用 IP 地址池）

- NAT 协议（网络地址转换协议）

     - **将专用地址（如企业内部网 Intranet）转换为公用地址（如互联网 Internet）**
     - 优点
          - 减少了 IP 地址注册的费用，节省了日益匮乏的 IP 地址
          - 隐藏了内部 IP 地址和拓扑结构，降低受攻击的风险
     - NAT 功能常被集成到路由器、防火墙、单独的 NAT 设备中
     - NAT 设备维护一个状态表，记录专用地址到公用地址的映射
     - 直接对数据包的报头进行修改

### ICMP 协议

*Internet Control Message Protocol 因特网控制报文协议* 

- ICMP 查询报文（成对出现）
  - 回显请求和应答：判断两个主机之间的 IP 通信是否可行
  - 时间戳请求和应答
  - 地址掩码求情和应答
  - 路由器查询和通告：查询目的路由器地址以及它是否正常工作
    - 优先级：0 最高优先级，默认路由器；0x8000 0000 最低优先级
    - 生存时间（TTL）

- ICMP 差错报文：只报错给源，不纠正，纠错由上层协议完成
     - 目的端不可达、源点抑制、超时、参数问题、重定向

- ⭐应用
  - **`ping`：测试两个主机之间的连通性，使用了 ICMP 回显请求和应答**
  - **`traceroute`：跟踪一个分组从源点到终点的路径，使用了 ICMP 超时差错报文**

### Routing in the Internet

- 内部网关协议（Intra-AS routing protocols / Interior gateway protocols）

  - RIP（routing information protocol）

    - **应用层协议，端口号 520，传输层使用 UDP**

    - 分布式、基于**距离向量**

    - **要求网络中每一个路由器都维护从她自己到其他每一个目的网络的唯一最佳距离记录**

      - **距离**：通常为“跳数（hop）”，即从源端口到目的端口所经过的路由器个数，每经过一个路由器跳数 +1。特别地，从以路由器到直接连接的网络距离为 1。
      - 一条路由最多只能包含 15 个路由器，距离为 16 表示网络不可达。

    - 适用于小型互联网

    - 路由器刚开始工作时，只知道自己直连的网络的距离（距离为 1）。经过若干次更新后，所有路由器最终都会知道到达本自治系统（AS）任何一个网络的嘴短距离和下一跳路由器的地址，即“**收敛**”。

    - **每 30s 交换一次路由信息**，交换的信息是自己的路由表，然后根据新信息更新路由表，**仅和相邻路由器交换信息**

    - 如果 **180s** 没收到邻居路由器的通告，则判定邻居没了，修改自己的路由表，将邻居表示为不可达，并发通告（advertisement）告知自己的其他邻居

    - 交换路由信息使用的是 **RIP 响应报文**（也叫 RIP 通告，RIP advertisement），包含至多 25 个目标子网以及到达该目标子网的最短距离

    - RIP 表有 3 列，🌰

       <img src="https://s2.ax1x.com/2020/01/04/l0qOLq.png" alt="image 20200104094814445" style="zoom:50%;" /> 

    - 特点：**好消息传得快，坏消息传得慢**，当网络出现故障时，要经过比较长的时间（数分钟）才能将此消息传送到所有的路由器，“**慢收敛**”

  - 开放最短路径优先协议，OSPF（open shortest path protocol first）

    - “开放”标明 OSPF 不是受某一家厂商控制，而是公开发表的；“对端路径”是因为使用了 Dijkstra 提出的最短路径算法 SPF
    - OSPF 是链路层协议，不使用传输层协议，直接使用 IP 协议
    - 最主要特征：使用分布式的**链路状态协议**
    - 首先，每个路由器都要掌握完整的网络拓扑结构和链路费用信息；然后在本地运行 Dijkstra 最短路径算法来决定以自己为根通往每个子网的最短路径树；最后计算链路费用
    - **每隔 30min 或者当链路状态发生变化**时，路由器向 AS 内**所有**其他路由器**广播**（broadcast）路由信息，

  - 一般 OSPF 用在高层 ISPs，RIP 用在低层 ISPs 和企业网络

- 外部网关协议（inter-AS routing protocols / EGP）

  - BGP（Border Gateway Protocols）
    
    - **使用 TCP 协议**，端口号 179
    
    - router 对之间通过半永久（semi-permanent）TCP 连接交换路由信息，这个 TCP 连接叫做 **BGP 会话（session）**
    
      BGP 会话不等同于物理链路
    
    - 



## Chapter 5 Link Layer

### Introduction

- 术语
  - 节点（node）：运行链路层协议的设备
  - 链路（link）：连接相邻两个节点的通讯频道
  - 网络适配器（network adapter / network interface card, NIC）：特殊用途的芯片，用于执行链路层的种种服务，是链路层协议执行的场所
- 链路层提供的服务：
  - 装帧（framing）：将网络层的数据报（datagram）封装为帧（frame）
  - 链路访问（link access）：MAC 协议规定帧如何被传输到链路上
  - 可靠传输（reliable delivery）：保证网络层数据报无误传输
  - 错误检测和校正（error detection and correction）：链路层的错误检测技术比较成熟，通常由硬件完成
    - 奇偶校验位（parity bit）
      - 1-D 奇偶校验
      - 2-D 奇偶校验
    - 校验和（checksum）
    - 循环冗余检测（cyclic redundant check, CRC）



