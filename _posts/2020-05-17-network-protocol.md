---
layout: article
title: 计算机网络 — 常见网络协议
tags: 计算机网络

lang: zh-Hans
key: Network_Protocol
pageview: true
toc: true
show_subscribe: false
---

## 为什么要学习网络协议？

只有通过网络协议，才能使一大片机器互相协作、共同完成一件事。

网络协议满足 **语法、语义、顺序** 三要素，以下面的 HTTP 协议为例：

```
HTTP/1.1 200 OK
Date: Tue, 27 Mar 2018 16:50:26 GMT Content-Type: text/html;charset=UTF-8 Content-Language: zh-CN
<!DOCTYPE html>
<html>
<head>
<base href="https://pages.kaola.com/" />
<meta charset="utf-8"/> <title> 网易考拉 3 周年主会场 </title>
```

- 符合语法，只有按照指定的格式，浏览器才认，先是状态、然后是首部、最后是内容。
- 符合语义，按照约定的意思来。例如，状态 200 表示网页返回成功。
- 符合顺序，先发出一个 HTTP 请求，才有上面的一段 HTTP 返回的数据。

## 浏览器输入 URL 回车后发生了什么？

1. **DNS 解析**

将域名解析成对应的服务器 IP 地址，客户端收到域名地址后，首先去找本地的 hosts 文件，检查在该文件中是否有相应的域名、IP 对应关系，如果有，则向其 IP 地址发送请求，如果没有，再去找 DNS 服务器。

DNS、HTTP 都在 **应用层**。

2. **建立 TCP 连接**

通过三次握手，建立客户端与服务器的 TCP 网络连接。

UDP、TCP 都在 **传输层**，传输层封装完毕后，会将包交给 **网络层**，网络层的协议是 IP 协议。

3. **发送 HTTP 请求**
4. **服务器处理请求**
5. **返回响应结果**
6. **关闭 TCP 连接**

通过四次挥手，断开客户端与服务器的 TCP 网络连接。

7. **浏览器解析 HTML**
8. **浏览器布局渲染**  

## 网络为什么要分层？

复杂的程序都要分层，每一层专注做本层的事情。

只要是在网络上跑的包，都是完整的。可以有下层没上层，绝对不可能有上层没有下层。

## 无类型域间路由（CIDR）

由于 C 类地址能包含的最大主机数量太少了，只有 254 个，而 B 类地址能包含的最大足迹数量又太多了，有 65534 个，为了解决这一尴尬的问题，就诞生了 CIDR，这种方式打破了原来设计的几类地址的做法，而是将 32 位的 IP 地址一分为二，前面是 **网络号**，后面是 **主机号**，例如 `10.100.122.2/24`，前 24 位表示网络号，后 8 位表示主机号。网络位全取 1 为**子网掩码**，例如 `255.255.255.0`，子网掩码与 IP 地址按位与运算可以得到网络号。

## MAC 地址

MAC 地址是全局唯一的，网卡生产数来的时候就已经有了，不会有两张网卡的 MAC 地址是相同的。

#### 为什么不用 MAC 地址进行通信呢？

MAC 地址更像是身份证号，是唯一的标识，比如你不能说我要找身份证号是 xxx 的人在哪，只能说是我要去北京市海淀徐xx小区xx门找到身份证号是 xxx 的人，MAC 地址的通信范围只局限在一个子网中。

## 动态主机配置协议（DHCP）

如果想要和其他机器通讯，就需要一个通讯地址，那么如果配置这个 IP 地址呢？这就用到 DHCP 协议了，它是一个局域网的网络协议，指的是由服务器控制一段 IP 地址范围，客户机登录服务器时就可以自动获得服务器分配的IP地址和子网掩码。

## 地址解析协议（ARP）

在网络通信中，主机和主机通信的数据包需要依据 OSI 模型从上到下进行数据封装，当数据封装完整后，再向外发出。所以在局域网的通信中，不仅需要源/目 IP 地址的封装，也需要源/目 MAC 地址的封装，上层应用程序更多关心 IP 地址，而不去管 MAC 地址，这时就需要 ARP 协议去找到 IP 地址和 MAC 地址的映射，即询问 IP 对应的 MAC 地址，询问方式靠「吼」。

## 生成树协议（STP）

在一个负载的网络环境中，难免会出现环路，环路会产生广播风暴，最终导致整个网络资源被耗尽，网络瘫痪不可用。为了破除环路，可以采用数据链路层协议 STP，运行该协议的设备通过彼此交互信息发现网络中的环路，并有选择的对某个端口进行阻塞，最终将环形网络结果修剪成无环路的树形网状结构。

## 虚拟局域网（VLAN）

一组逻辑上的设备和用户，不受物理位置的限制，可以根据功能、部门及应用等因素将他们组织起来，相互之间的通信就好像它们在同一个网段中一样，可以控制广播活动，提高网路的安全性。

## 互联网控制报文协议（ICMP）

网络包在异常复杂的网络环境中传输时，常常会遇到各种各样的问题。当遇到问题时，总不能死的不明不白，要传出消息来，报告情况，这样才可以调整传输策略。而 ICMP 就是用来确认 IP 包是否成功到达目的地址并通知在发送过程中 IP 包被丢弃的原因的，是一种网络层协议。

## 网关（Gateway）

网关是一个网络连接到另一个网络的「关口」，实质上是一个通过通向其他网络的 IP 地址。很多情况下，人们把网关就叫做路由器。其实不完全准确，路由器是一台设备，它有五个网口或者网卡，相当于有五只手，分别连着五个局域网。每只手的 IP 地址都和局域网的 IP 地址有相同的网段，每只手都是它握住的那个局域网的网关。

## 动态路由协议

### 1. OSPF

- 内部路由协议，向本自治系统中所有路由器发送信息
- 发送的信息就是与路由器相邻的所有路由器的链路状态，但这只是路由器所知道的部分信息
- 只有在链路状态发生变化时，路由器才向所有路由器用洪泛法发送此信息

### 2. RIP

- 内部路由协议，仅和相邻路由交换信息
- 路由器交换的信息是当前本路由器所知道的全部信息
- 按固定时间间隔交换路由信息，例如每隔 30 秒

### 3. BGP

外部路由协议，在一个国家内部，有路当前选近的走，但是国家质检，不光有远近的问题，还有政策的问题，这就好比从我家里到目的地最近，但是不是谁都能从我家走啊。

## TCP 和 UDP

[计算机网络 — 传输层 TCP 与 UDP 详解](https://str818.github.io/2019/07/18/network-transport_layer.html)

## 套接字 Socket

Socket 是在应用层和传输层之间的一个抽象层，它把 TCP/IP 层复杂的操作抽象为几个简单的接口供应用层调用，来实现进行在网络中的通信。Socket 的出现只是为了更方便的使用 TCP/IP 协议栈，形成了几个最基本的函数接口，例如 `carete`、`listen`、`accept`、`connect`、`read`、`write` 等。

## 流媒体协议（RTMP）

网络协议将编码好的视频流，从主播端推到服务器，在服务器上有个运行了同样协议的服务端来接收这些网络包，从而得到里面的视频流，这个过程称为 **接流**。

服务端接到视频流之后，可以对视频流进行一定的处理，例如转码，也即从一个编码格式，转成另一种 格式。因为观众使用的客户端千差万别，要保证他们都能看到直播。

流处理完毕之后，就可以等待观众的客户端来请求这些视频流。观众的客户端请求的过程称为 **拉流**。

如果有非常多的观众，同时看一个视频直播，那都从一个服务器上拉流，压力太大了，因而需要一个视 频的分发网络，将视频预先加载到就近的边缘节点，这样大部分观众看的视频，是从边缘节点拉取的， 就能降低服务器的压力。

当观众的客户端将视频流拉下来之后，就需要进行解码，也即通过上述过程的逆过程，将一串串看不懂 的二进制，再转变成一帧帧生动的图片，在客户端播放出来，这样就可以看到直播了。

## 下载文件

想象一下，下载一个电影需要通过什么方式？

### 1. HTTP

最简单的方式就是通过 HTTP 下载，但是通过浏览器下载的时候，只要文件稍微大一点，下载速度就很慢。

### 2. FTP

FTP 采用两个 TCP 连接来传输一个文件

- **控制连接：**服务器以被动的方式，打开众所周知用于 FTP 的端口 21，客户端则主动发起连接。该连接将命令从客户端传给服务器，并传回服务器的应答。常用的命令有：list—获取文件目录；reter —取一个文件；store—存一个文件。
- **数据连接：**每当一个文件在客户端与服务器之间传输时，就创建一个数据连接。

### 3. P2P

无论是 HTTP 的方式，还是 FTP 的方式，都有一个比较大的缺点，就是难以解决单一服务器的带宽压力， 因为它们使用的都是传统的客户端服务器的方式。

而 P2P，资源开始并不集中地存储在某些设备上，而是分散地存储在多台设备上。想要下载一个文件的时候，你只要得到那些已经存在了文件的 peer，并和这些 peer 之间，建立点对点的连接，而不需要到中心服务器上，就可以就近下载文件。一旦下载了文件，你也就成为 peer 中的一员，这种方式，参与的人越多，下载速度越快

#### 种子（.torrent）文件

当你想下载一个文件的时候，怎么知道哪些 peer 有这个文件呢?

这就用到种子啦，也即咱们比较熟悉的 `.torrent` 文件。`.torrent` 文件由两部分组成，分别是：announce (tracker URL)和文件信息。

下载时，BT 客户端首先解析 `.torrent` 文件，得到 tracker 地址，然后连接 tracker 服务器。tracker 服务器回应下载者的请求，将其他下载者(包括发布者)的 IP 提供给下载者。下载者再连接其他下载者，根据 `.torrent` 文件，两者分别对方告知自己已经有的块，然后交换对方没有的数据。此时不需要其他服务器参与，并分散了单个线路上的数据流量，因此减轻了服务器的负担。

## 域名系统（DNS）

域名解析是互联网上一项十分重要的服务，DNS 就是作为域名和 IP 地址相互映射的一个分布式数据库。DNS 能够使用户更方便的访问互联网，而不用去记住能够被机器直接读取的IP数串。通过域名，最终得到该域名对应的IP地址的过程叫做域名解析。

### 域名解析过程

<div align="center"> <img src="https://s1.ax1x.com/2020/05/18/YWwWbF.jpg" width="100%"/> </div>

1. 电脑客户端会发出一个 DNS 请求，问 `www.163.com` 的 IP 是啥啊，并发给本地域名服务器 (本地 DNS)。那本地域名服务器 (本地 DNS) 是什么呢？如果是通过 DHCP 配置，本地 DNS 由你的网络 服务商(ISP)，如电信、移动等自动分配，它通常就在你网络服务商的某个机房。
2. 本地 DNS 收到来自客户端的请求。你可以想象这台服务器上缓存了一张域名与之对应 IP 地址的大 表格。如果能找到 `www.163.com`，它直接就返回 IP 地址。如果没有，本地 DNS 会去问它的根域 名服务器：“老大，能告诉我 `www.163.com `的 IP 地址吗？” 根域名服务器是最高层次的，全球共有 13 套。它不直接用于域名解析，但能指明一条道路。
3. 根 DNS 收到来自本地 DNS 的请求，发现后缀是 `.com`，说：“哦，`www.163.com` 啊，这个域名是由 `.com` 区域管理，我给你它的顶级域名服务器的地址，你去问问它吧。”
4. 地 DNS 转向问顶级域名服务器：“老二，你能告诉我 `www.163.com` 的 IP 地址吗？” 顶级域名服务器就是大名鼎鼎的比如 `.com`、`.net`、 `.org` 这些一级域名，它负责管理二级域名，比如 `163.com`，所以它能提供一条更清晰的方向。
5. 顶级域名服务器说：“我给你负责 `www.163.com` 区域的权威 DNS 服务器的地址，你去问它应该能问到。”
6. 本地 DNS 转向问权威 DNS 服务器：“您好，`www.163.com` 对应的 IP 是啥呀? ”`163.com` 的权威 DNS 服务器，它是域名解析结果的原出处。为啥叫权威呢？就是我的域名我做主。
7. 权限 DNS 服务器查询后将对应的 IP 地址 X.X.X.X 告诉本地 DNS。
8. 本地 DNS 再将 IP 地址返回客户端，客户端和目标建立连接。

### 负载均衡

站在客户端角度，域名解析是一次 **DNS 递归查询过程**。DNS 除了可以通过名称映射为 IP 地址，它还可以做另外一件事，就是负载均衡。比如想去吃北京的肯德基，北京有很多家肯德基，如果一个人想去吃肯德基，就可以就近找一下，而不用大家都去同一家，这就是负载均衡。

最简单的，可以通过轮询实现负载均衡，这次返回这个 IP，下次返回另一个 IP。此外还可以做 **全局负载均衡**，比如北京的用户访问北京的数据中心，上海的用户访问上海的数据中心。

可以配置多个 **全局负载均衡器（GSLB）**，例如可以根据请求本地 DNS 服务器所在的运营商，获取到用户所在的运营商，之后根据本地 DNS 服务器所在地获取到用户的地址位置，最后返回临近的数据中心 IP。

## 内容分发网络（CDN）

CDN 本质上是将媒体资源、动静态图片、HTML、CSS、JS等内容缓存到离用户更近的 IDC 中，从而让用户进行共享资源，实现缩减站点间的响应时间等需求。

## 虚拟专用网络（VPN）

VPN 被定义为通过一个公用互联网络建立一个临时的、安全的连接，是一条穿过混乱的公用网络的安全、稳定隧道，使用这条隧道可以对数据进行几倍加密达到安全使用互联网的目的，广泛使用企业办公当中，虚拟专用网也可以是针对企业内部网的扩展，虚拟专用网可以帮助远程用户、公司分支机构、商业伙伴及供应商同公司的内部网建立可信的安全连接，用于经济有效地连接到商业伙伴和用户的安全外联网虚拟专用网，因此很多办公一族在自己电脑中也需要建立 VPN 连接，方便远程办公等等。VPN 可以将一个机构的多个数据中心通过隧道的方式连接起来，让机构感觉在一个数据中心里面。