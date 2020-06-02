# 一、web网络基础

FTP( File Transfer Protocol,文本传输协议)

DNS(Domain Name System ,域名系统)  

TCP(Transmission Control Protocol,传输控制协议)

UDP(User Data Protocol,用户数据报协议)

IP(Internet Protocol,网际协议)

MAC(Media Access Control Address)

ARP(Address Resolution Protocol)

URL(Uniform Resource Locator,统一资源定位符)

URI(Uniform Resource Idenfier,统一资源标识符)



## TCP/IP分层管理

TCP/IP按层次分为

- 应用层
  - 决定了向用户提供应用服务时通信的活动
  - TCP/IP协议族内预存了各类通用的应用服务，FTP和DNS就是其中的两类，HTTP协议也属于该层
- 传输层
  - 对应上层应用层，提供出于网络连接中的两台计算机之间的数据传输
  - 在传输层有两个性质不同的协议:TCP和UDP
- 网络层(网络互连层)
  - 处理在网络上流动的数据包，数据包是网络传输的最小数据单位。该层规定了通过怎样的路径(传输路线)到达对方的计算机，并将数据包传给对方
- 链路层(数据链路层，网络接口层)
  - 用来处理连接网络的硬件部分。包括控制操作系统，驱动，光纤等物理可见部分，硬件上的范畴均在链路层的作用范围之内



## TCP/IP通信传输流

![image-20200527130844194](D:\notes\notes\typora\http\images\image-20200527130844194.png)

利用 TCP/IP 协议族进行网络通信时，会通过分层顺序与对方进行通信。发送端从应用层往下走，接收端则往应用层往上走



我们用 HTTP 举例来说明：

1. 首先作为发送端的客户端在应用层（HTTP 协议）发出一个想看某个 Web 页面的 HTTP 请求
2. 接着，为了传输方便，在传输层（TCP 协议）把从应用层处收到的数据（HTTP 请求报文）进行分割，并在各个报文上打上标记序号及端 口号后转发给网络层
3. 在网络层（IP 协议），增加作为通信目的地的 MAC 地址后转发给链 路层。这样一来，发往网络的通信请求就准备齐全了
4. 接收端的服务器在链路层接收到数据，按序往上层发送，一直到应用层。当传输到应用层，才能算真正接收到由客户端发送过来的 HTTP请求



![image-20200527131418758](D:\notes\notes\typora\http\images\image-20200527131418758.png)

发送端在层与层之间传输数据时，每经过一层时必定会被打上一个该层所属的首部信息。反之，接收端在层与层传输数据时，每经过一层时会把对应的首部消去。这种把数据信息包装起来的做法称为封装（encapsulate）。



## 与HTTP密切相关的协议TCP、IP和DNS

### 1、IP协议

- 按层次分，IP（Internet Protocol）网际协议位于网络层,几乎所有使用网络的系统都会用到 IP 协议。TCP/IP 协议族中的 IP 指的就是网际协议，协议名称中占据了一半位置，其重要性可见一斑。可能有人会把“IP”和“IP 地址”搞混，“IP”其实是一种协议的名称
- IP 协议的作用是把各种数据包传送给对方。而要保证确实传送到对方那里，则需要满足各类条件。其中两个重要的条件是 IP 地址和 MAC地址
  - IP 地址指明了节点被分配到的地址
  - MAC 地址是指网卡所属的固定地址
  - IP 地址可以和 MAC 地址进行配对。IP 地址可变换，但 MAC地址基本上不会更改。



使用 **ARP** 协议凭借 **MAC** 地址进行通信

IP 间的通信依赖 MAC 地址。在网络上，通信的双方在同一局域网（LAN）内的情况是很少的，通常是经过多台计算机和网络设备中转才能连接到对方。而在进行中转时，会利用下一站中转设备的 MAC地址来搜索下一个中转目标。这时，会采用 ARP 协议（Address Resolution Protocol）。ARP 是一种用以解析地址的协议，根据通信方的 IP 地址就可以反查出对应的 MAC 地址。

![image-20200527132926622](D:\notes\notes\typora\http\images\image-20200527132926622.png)



### 2、TCP协议

按层次分，TCP 位于传输层，提供可靠的字节流服务

所谓的字节流服务（Byte Stream Service）是指，为了方便传输，将大块数据分割成以报文段（segment）为单位的数据包进行管理。而可靠的传输服务是指，能够把数据准确可靠地传给对方。



为了准确无误地将数据送达目标处，TCP 协议采用了三次握手（three-way handshaking）策略。用 TCP 协议把数据包送出去后，它一定会向对方确认是否成功送达。

握手过程中使用了 TCP 的标志（flag） —— SYN（synchronize） 和 ACK（acknowledgement）。

- 发送端首先发送一个带 SYN 标志的数据包给对方。接收端收到后，回传一个带有 SYN/ACK 标志的数据包以示传达确认信息。最后，发送端再回传一个带 ACK 标志的数据包，代表“握手”结束。 
- 若在握手过程中某个阶段莫名中断，TCP 协议会再次以相同的顺序发送相同的数据包。

![image-20200527133504600](D:\notes\notes\typora\http\images\image-20200527133504600.png)

### 3、DNS服务

DNS（Domain Name System）服务是和 HTTP 协议一样位于应用层的协议。它提供域名到 IP 地址之间的解析服务。

DNS 协议提供通过域名查找 IP 地址，或逆向从 IP 地址反查域名的服务。

![image-20200527133949420](D:\notes\notes\typora\http\images\image-20200527133949420.png)

### 4、各种协议与HTTP协议之间的关系

![image-20200527134323049](D:\notes\notes\typora\http\images\image-20200527134323049.png)

### 5、URI与URL

URL:

- URL正是使用 Web 浏览器等访问 Web 页面时需要输入的网页地址。如 http://hackr.jp/ 

  就是 URL。



# 二、简单的http协议

![image-20200527135446543](D:\notes\notes\typora\http\images\image-20200527135446543.png)

下面则是从客户端发送给某个 HTTP 服务器端的请求报文中的内容。

```html
GET /index.htm HTTP/1.1 
Host: hackr.jp
```

起始行开头的GET表示请求访问服务器的类型，称为方法（method）。随后的字符串 /index.htm 指明了请求访问的资源对象，也叫做请求 URI（request-URI）。最后的 HTTP/1.1，即 HTTP 的版本号，用来提示客户端使用的 HTTP 协议功能

请求报文

1. 请求方法
2. 请求URI
3. 协议版本
4. 可选的请求首部字段(请求头)
5. 内容实体(请求体)

![image-20200527135731967](D:\notes\notes\typora\http\images\image-20200527135731967.png)

