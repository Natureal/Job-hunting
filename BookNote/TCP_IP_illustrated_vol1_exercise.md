## TCP/IP 详解 卷一 习题自解

电子书Link: [TCP/IP Illustrated, Volume 1 The Protocols W. Richard Stevens](https://www.isi.edu/~hussain/TEACH/Spring2014/notes/Steven00a.pdf)

---

#### 第一章：概述

**1.1 请计算最多有多少个A类、B类和C类网络号。**

![](imgs/IP_class.png)

网络号数量：

A类：2^7 - 2 = 126（0 和 127 的网络号在网络上不可用）

B类：2^14 = 16384（注意，这里不用再减了）

C类：2^21 = 2097152

前 8 位二进制的范围：

A类：[0, 127]

B类：[128, 191]

C类：[192, 223]

D类：[224, 239]

E类：[240, 247]

**Extension 1**

各类 IP 地址占总共 2^32 个 IPv4 地址的比例：

A类：50%

B类：25%

C类：12.5%

**Extension 2**

拓展一下，关于子网号全0/全1带来的二义性问题，讨论如下：

（1）在RFC950中，全0全1的子网号不建议被使用。陈述如下：

**Reference:** RFC950 States “In certain contexts, it is useful to have fixed addresses with functional significance rather than as identifiers of specific hosts.  When such usage is called for, the address zero is to be interpreted as meaning “this”, as in “this network”.  The all-ones address is to be interpreted as meaning “all”, as in “all hosts”.  For example, the address 128.9.255.255 could be interpreted as meaning all hosts on the network 128.9.  Or, the address 0.0.0.37 could be interpreted as meaning host 37 on this network.”

It is useful to preserve and extend the interpretation of these special addresses in subnetted networks.  This means the values of all zeros and all ones in the subnet field should not be assigned to actual (physical) subnets.

我们看到RFC950建议将全1的子网号+主机号的地址解释为所有主机，全0的子网号的地址解释为该网络上的某个主机。因此这两种子网号不建议被使用为标识号，指向某个子网内的某个主机。

举个例子，考虑一个B类IP地址172.16.1.10，设其子网掩码为255.255.224.0，如果计算其**子网号**，将得到172.16.0.0。我们发现这个地址同时可以是一个IP地址，这将造成混淆。

（2）但是，在RFC1878中已经说明，在现代的网络环境中已经可以使用全0/全1的子网号，参考如下：

**Reference1:** On the issue of using subnet zero and the all-ones subnet, RFC 1878 states, "This practice (of excluding all-zeros and all-ones subnets) is obsolete. Modern software will be able to utilize all definable networks." Today, the use of subnet zero and the all-ones subnet is generally accepted and most vendors support their use. However, on certain networks, particularly the ones using legacy software, the use of subnet zero and the all-ones subnet can lead to problems.

**Reference2:** Prior to Cisco IOS® Software Release 12.0, Cisco routers, by default, did not allow an IP address belonging to subnet zero to be configured on an interface. However, if a network engineer working with a Cisco IOS software release older than 12.0 finds it safe to use subnet zero, the **ip subnet-zero command** in the global configuration mode can be used to overcome this restriction.

我们看到现代的设备供应商已经可以解决这个歧义问题，方法是通过 ip subnet-zero command 来允许使用全0子网号。（解决全1子网号的方法并没有找到，猜测应该类似）

**1.2 用匿名FTP从主机nic.merit.edu上获取文件nsfnet/statistics/history.netcount。该文件包含在NSFNET网络上登记的国内和国外的网络数。画出坐标系，横坐标代表年，纵坐标代表网络总数的对数值。纵坐标的最大值是习题1.1的结果。如果数据显示一个明显的趋势，请估计按照当前的编址体制推算，何时会用完所有的网络地址**

无法访问。但是理论上IPv4的地址应该早就使用完了，如今采用了NAT（Network Address Tranlation）技术来防止其过快消耗完。

**1.3 获取一份主机需求RFC拷贝[Braden 1989a]，阅读有关应用于TCP/IP协议每一层的稳健性原则。这个原则的参考对象是什么？**

在 [RFC索引站](https://www.rfc-editor.org/search/rfc_search.php) 中搜索 RFC1122，在 Introduction 中有相关描述如下：

```
1.2.2  Robustness Principle

         At every layer of the protocols, there is a general rule whose
         application can lead to enormous benefits in robustness and
         interoperability [IP:1]:

                "Be liberal in what you accept, and
                 conservative in what you send"

         Software should be written to deal with every conceivable
         error, no matter how unlikely; sooner or later a packet will
         come in with that particular combination of errors and
         attributes, and unless the software is prepared, chaos can
         ensue.  In general, it is best to assume that the network is
         filled with malevolent entities that will send in packets
         designed to have the worst possible effect.  This assumption
         will lead to suitable protective design, although the most
         serious problems in the Internet have been caused by
         unenvisaged mechanisms triggered by low-probability events;
```

该原则为：自由地接受，保守(谨慎)地发送。

这个建议会有益于健壮性和互通性。在这个建议下，软件应该被设计为可以处理任何可能的错误，否则迟早会产生混乱（bug）。通常，我们应该设想网络上充斥着恶毒（混乱）的信息，数据包以最坏的情况被发送。这些设想能帮助我们构建出合适并且具有保护性的设计，即使这些最坏情况是由那些低概率事件造成的。

**1.4 获取一份最新的赋值RFC拷贝。“quote of the day” 协议的有名端口号是什么？哪个RFC对该协议进行了定义**

有名端口号：17

The Quote of the Day（QOTD）是 [RFC 865](https://tools.ietf.org/html/rfc865) 中定义的协议。其用处很简单，用于向客户广播每日报价（事实上可以是任何简单的小于 512 字符的报文）。客户可以通过 TCP/17 或者 UDP/17 端口对支持 QOTD 协议的服务器发起连接/发送数据报，当然服务器会丢弃任何发来的数据，并回复每日报价（或任何简单的报文）。

**Extension**

现在很少能看到 QOTD 协议的身影，因为其经常被防火墙墙掉来避免一类 denial-of-service attack 攻击（如 ping flood）。

**1.5 如果你有一个接入TCP/IP互联网的主机账号，它的主IP地址是多少？这台主机是否接入了Internet？它是多接口主机吗**

通过 Google 查到了自己的广域网 IP（即：公网 IP），为 133.3.201.56，说明已经接入了 Intenret。

在 Ubuntu 命令行中可用 ifconfig 命令查询网卡信息。看到该主机有多个网络设备（包括 docker 创建的网桥），但只有一个 wlp2s0 是实际作为 Internet 接口的（连接到互联网），所以不是多接口主机（non-multihomed）。

```
docker0: flags=4099<UP,BROADCAST,MULTICAST>  mtu 1500
        inet 172.17.0.1  netmask 255.255.0.0  broadcast 172.17.255.255
        ether 02:42:e6:75:ea:5f  txqueuelen 0  (Ethernet)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

enp0s31f6: flags=4099<UP,BROADCAST,MULTICAST>  mtu 1500
        ether e8:6a:64:c5:2b:7c  txqueuelen 1000  (Ethernet)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
        device interrupt 16  memory 0xee300000-ee320000

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 25922  bytes 2420536 (2.4 MB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 25922  bytes 2420536 (2.4 MB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

wlp2s0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 10.238.26.252  netmask 255.255.254.0  broadcast 10.238.27.255
        inet6 fe80::5fb7:8933:bde9:e0d4  prefixlen 64  scopeid 0x20<link>
        ether 58:a0:23:41:4d:35  txqueuelen 1000  (Ethernet)
        RX packets 5433006  bytes 7022091037 (7.0 GB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 1120983  bytes 222076917 (222.0 MB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```

**1.6 获取一份RFC1000的拷贝，了解RFC这个术语从何而来**

[RFC 1000](https://www.rfc-editor.org/rfc/rfc1000.txt)

**1.7 与Internet协会联系，isoc@isoc.org或者+1 7036489888，了解有关加入的情况**

pass

**1.8 用匿名FTP从主机is.internic.net处获取文件about-internic/information-about-the-internic。**

无法访问。pass

---

#### 第二章：链路层

**2.1 如果你的系统支持netstat命令，那么请用它确定系统上的接口及其MTU**

netstat -i 查看接口：

```
Kernel Interface table
Iface      MTU    RX-OK RX-ERR RX-DRP RX-OVR    TX-OK TX-ERR TX-DRP TX-OVR Flg
docker0   1500        0      0      0 0             0      0      0      0 BMU
enp0s31f  1500        0      0      0 0             0      0      0      0 BMU
lo       65536    26864      0      0 0         26864      0      0      0 LRU
wlp2s0    1500  5452669      0      0 0       1138393      0      0      0 BMRU
```

一般来说环回接口的 MTU 会比较大。

---

#### 第三章：IP：网际协议

**3.1 环回地址必须是127.0.0.1吗？**

不是必须，IPv4 的环回地址之一是 127.0.0.1，但是 127.0.0.0 - 127.255.255.255 均保留。此范围内的任何地址都将环回到本地主机中，而不会出现在任何网络中。

**3.2 在图3-6中指出有两个网络接口的路由器**

![](imgs/TCP_IP_3-6.png)

R2-R8, gemini, R12, R192, R82, R52-R55, R58, R60, R57, bsdi(也看做路由器的话)

**3.3 子网号为16bit的A类地址与子网号为8bit的B类地址的子网掩码有什么不同？**

没什么不同，都为 255.255.255.0

**Extension**

这同时也是一个没有进行子网划分的C类地址

**3.4 阅读RFC1219[Tsuchiya 1991]，学习分配子网号和主机号的有关推荐技术**

[RFC1219](https://www.rfc-editor.org/rfc/rfc1219.txt)

[中文翻译](https://www.xuebuyuan.com/1749604.html)

**3.5 子网掩码255.255.0.255是否对A类地址有效**

有效，这被成为非连续子网掩码。但是RFC不建议使用这种掩码。

**3.6 你认为为什么3.9小节中打印出来的环回接口的MTU要设置为1536？**

虽然不知道具体原因，不过查看RFC894（Ethernet II frames），我们看到如下：

```
  +----+----+------+------+-----+
   | DA | SA | Type | Data | FCS |
   +----+----+------+------+-----+
             ^^^^^^^^

   DA      Destination MAC Address (6 bytes)
   SA      Source MAC Address      (6 bytes)
   Type    Protocol Type           (2 bytes: >= 0x0600 or 1536 decimal)  <---
   Data    Protocol Data           (46 - 1500 bytes)
   FCS     Frame Checksum          (4 bytes)
```

以太网将帧长限制为0x600（即：1536）。

当然现代的计算机 loopback 的 MTU 都很大，比如 65536。

**3.7 TCP/IP协议族是基于一种数据报的网络技术，即IP层，其他的协议族则基于面向连接的网络技术。阅读文献[Clark 1988]，找出数据报网络层提供的三个优点。**

参考: 1988 Clark

First, they eliminate the need for connection state within the intermediate switching nodes, which means that the Internet can be reconstituted after a failure without concern about state.

首先，它不需要维护网络链路中的中间交换节点的状态，这意味着网络可以在不考虑中间故障状态的情况下直接重组。

Secondly, the datagram provides a basic building block out of which a variety of types of service can be implemented. In contrast to the virtual circuit, which usually implies a fixed type of service, the datagram provides a more elemental service which the endpoints can combine as appropriate to build the type of service needed.

第二，数据报作为一个基础组件和基础服务，使得许多其他服务可以利用其建立起来。如 TCP， UDP。但面向连接的虚拟电路就不行。

Third, the datagram represents the minimum network service assumption, which has permitted a wide variety of networks to be incorporated into various Internet realizations. The decision to use the datagram was an extremely successful one, which allowed the Internet to meet its most important goals very successfully.

第三，数据报代表了最基础和底层的网络服务，保证了一系列网络可以联通在一起组成更大的网络结构。数据报的使用是十分成功的案例。

---

#### 第四章：ARP：地址解析协议

**4.1 当输入命令以生成类似图4-4那样的输出时，发现本地ARP快速缓存为空以后，输入命令 bsdi % rsh svr4 arp -a，如果发现目的主机上的ARP快速缓存也是空的，那将发生什么情况？（该命令将在svr4主机上运行arp -a 命令）**

![](imgs/TCP_IP_4-4.png)

rsh 命令会建立 TCP 连接，在执行指令后，目的主机上的 arp 缓存必定有本地主机。

**Extension**

rsh（remote shell）能让指令在远程主机上运行。

**4.2 请描述如何判断一个给定主机是否能正确处理接收到的非必要的ARP请求的方法**

回答参考了书后标准答案。

```
保证你的主机上的 ARP 缓存中没有登记以太网上的某个叫作 foo 的主机。保证 foo 引导时
发送一个免费 ARP 请求, 同时在 foo 引导时,在那台主机上运行 tcpdump。然后关闭
主机 foo,此时因为使用加了 temp 选项的 arp 命令, 在你的主机的 ARP 缓存中有名为 foo 的
不正确的登记项。重新引导 foo 并在它启动好之后,察看主机的 ARP 缓存,看看不正确的登记
项是不是已经被更正了。
```

**4.3 由于发送一个数据包后ARP将等待响应，因此4.2节所描述的步骤7可能会持续一段时间。你认为ARP将如何处理在这期间收到的相同目的IP地址发来的多个数据包**

等11.9节

**4.4 在4.5节的最后，我们支出Host Requirements RFC和伯克利派生系统在处理活动ARP表目的超时时存在差异。那么如果我们在一个由伯克利派生系统的客户端上，试图与一个正在更换以太网卡而处于关机状态的服务器主机联系，这时会发生什么情况？如果服务器在引导过程中广播一份免费ARP，这种情况是否会发生变化？**

由于客户机保存了服务器的一份完整的 ARP 登记项，那么在服务器关机时，客户机将无法和服务器联系，20 分钟后 ARP 登记项超时。

如果服务器在一个新的硬件地址重启了，并且在引导时免费广播了 ARP，那么客户机就能更新 ARP 登记项并联系上服务器，如果没有广播，那么情况和上述一样。

---

#### 第五章：RARP：逆地址解析协议

**5.1 RARP需要不同的帧类型字段吗？ARP和RARP都使用相同的值0x0806吗？**

RARP 帧类型字段：0x8035，（ARP 的帧类型为：0x0806）。事实上一个有区别ARP帧类型字段并不是必须的，因为op字段对于ARP请求，ARP应答，RARP请求,RARP应答都有一个不同的值。但是不同的帧类型使得 RARP 服务器处理请求/应答起来更加方便。（区别于内核的 ARP 服务器）

**5.2 在一个有多个RARP服务器的网络上，如何防止它们的响应发生冲突？**

方法 1. 在每个 RARP 服务器响应前延迟一个随机值。（可以指定一个为主服务器，无需延迟，其他次服务器延迟）

方法 2. 指定一个主服务器来处理响应，而其他次服务器只处理短时间内重复的请求（重复的情况可能发生在主服务器宕机时）

---

#### 第六章：ICMP：Internet 控制报文协议

**6.1 在6.2节的末尾，我们列出了5种不发送ICMP差错报文的特殊条件。如果这些条件不满足，而我们又在局域网上向一个似乎不存在的端口号发送一份广播UDP数据报，这时会发生什么样的情况？**

参考答案：如果在局域网线上有一百个主机,每个都可能在同一时刻发送一个 ICMP 端口不可达的报文。很多报文的传输都可能发生冲突(如果使用的是以太网),这将导致 1秒或2秒的时间里网络不可用。

**6.2 阅读RFC[Braden 1989a]，注意生成一个ICMP端口不可达差错是否为“必须”，“应该”，或者“可能”。这些信息所在的页码和章节是多少？**

参考答案：should，应该

**6.3 阅读RFC1349[Almquist 1992]，看看IP的服务类型字段是如何被ICMP设置的？**

参考答案：如我们在图 3-2 所指出的,发送一个 ICMP 差错总是将 TOS 置为0。发送一个 ICMP 查询请求可以将 TOS 置为任何值,但是发送相应的应答必须将 TOS 置为相同的值。

**6.4 如果你的系统提供netstat命令，请用它来查看接收和发送的ICMP报文类型**

netstat -s 命令显示如下：

```
Icmp:
    73 ICMP messages received
    17 input ICMP message failed
    ICMP input histogram:
        destination unreachable: 70
        echo requests: 3
    364 ICMP messages sent
    0 ICMP messages failed
    ICMP output histogram:
        destination unreachable: 361
        echo replies: 3
IcmpMsg:
        InType3: 70
        InType8: 3
        OutType0: 3
        OutType3: 361
```

---

#### 第七章：Ping 程序

**7.1 请画出7.2节中ping输出的时间线**

pass

**7.2 若把bsdi和slip主机之间的SLIP链路设置为9600b/s，请计算这时的RTT。假定默认的数据是56字节。**

IP 数据报大小 = 56B 数据 + 20B IP首部 + 8B ICMP首部 = 84 字节

SLIP 链路速率 = 9600b/s = 960B/s （因为每个字节要加上1bit起始位和1bit结束位）

RTT = 84 / 960 * 2 =  175 ms。

**7.3 当前BSD版中的ping程序允许我们为ICMP报文的数据部分指定一种模式（数据部分的前8个字节不用来存放模式，因为它要存放发送报文的时间）。如果我们指定的模式为0xc0，请重新计算上一题中的答案。（提示：阅读2.4节）**

未理解。

参考答案：(86 + 48) bytes divided by 960 bytes/sec, times 2 gives 279.2 ms. The additional 48
bytes are because the final 48 bytes of the 56 bytes in the data portion must be escaped:
0xc0 is the SLIP END character.

**7.4 使用压缩SLIP（CSLIP，见2.5节）是否会影响我们在7.2节中看到的ping输出中的时间值？**

CSLIP 只压缩 TCP 首部和 IP 首部，对 ICMP 报文没有影响。

**7.5 在图2-4中，ping环回地址与ping主机以太网地址会出现什么不同？**

![](imgs/TCP_IP_2-4.png)

在图中我们可以看到，ping主机以太网地址会比ping环回地址多进行一些判断，在图中为多出两步判断:(1）目的IP地址是否与广播地址或多播地址相同？（2）目的IP地址是否与接口IP地址相同？。

参考答案：在一个 SPARC 工作站 ELC 上,对回环地址的 ping 操作产生一个1.310 ms的 RTT,而对一个主机的以太网地址的 ping 操作产生一个 1.460 ms的 RTT。这个差值是由于以太网驱动程序需要时间来判定这个数据报的目的地址是一个本地的主机。需要一个产生微秒级输出的 ping 来验证这一点。

---

#### 第八章：Traceroute 程序

**8.1 当IP将接收到的TTL字段减一，发现它为0时，将会发生什么结果？**

如果输入的数据报TTL为0，减一后TTL会变成255（溢出），并让数据报继续传输。尽管正常情况下，一个路由器永远不会收到一个TTL为0的数据报，但这种情况确实有时会发生。

**8.2 traceroute程序是如何计算RTT的？将这种计算RTT的方法与ping相比较。**

traceroute会保存其发送分组的时间，之后在接收到ICMP应答（超时/端口不可达）时，取出当前时间相减即可计算出RTT。

那么如何对应发送的报文和接收到的ICMP应答呢？ICMP应答都属于差错报文，因此必须包含生成该差错报文的数据报的IP首部（包含任何选项），还必须至少包含紧跟在首部后的前8个字节（这里包含了UDP的首部）。在UDP首部中包含了源端口号（比如33435），我们知道traceroute客户会将这个源端口号设置成它的进程ID和32768的逻辑或。这样就客户就能收集对应的应答报文了。

**8.3（本习题与下一道习题是基于开发traceroute程序过程中遇到的实际问题，它们来自于traceroute程序源代码注释）。假设源主机和目的主机之间有三个路由器（R1，R2，R3），而中间的路由器（R2）在进入TTL字段为1时，将TTL字段减一，但却错误地将该IP数据报发往下一个路由器。请描述会发生什么结果。在运行traceroute程序时会看到什么样的现象？**

第一行TTL=1，显示R1，正确。

第二行TTL=2，显示R3，这是错误的。这里是因为R2错误地将报文传给R3，R3看到TTL=0就回送ICMP超时报文了。

第三行TTL=3，显示R3，正确。

这个错误所表现出的线索即是：两个连续的输出行标识了同一个路由器。

**8.4 同样，假设源主机和目的主机之间有三个路由器。由于目的主机上存在错误，因此，它总是将进入TTL值作为外出ICMP报文的TTL值。请描述这将发生什么结果，你会看到什么现象。**

参考答案：在这种情况下, TTL为1标识了R1, TTL为2标识了R2, TTL为3标识了R3,但是当TTL为4时, UDP数据报到达了目的地, 其输入的TTL为1。ICMP端口不可达报文生成了,但它的TTL是1(错误地从进入的TTL复制而来)。这个ICMP报文到了R3, 在那儿TTL被减1, 报文被丢弃。没有生成一个ICMP超时报文, 因为被丢弃的数据报是一个ICMP的差错报文(端口不可达)。类似的现象也出现在TTL为5的探测分组, 但这次输出的端口不可达
报文以TTL为2开始(进入的TTL), 这个报文被传给R2, 在那儿被丢弃。对应于TTL为6探测分组的端口不可达报文被传递给R1, 在那儿被丢弃。最后, 对应于TTL为7探测分组的端口不可达报文被送回了原地, 到达时它的TTL为1(traceroute认为一个TTL为0或1的到达ICMP报文是有问题的,因此它在RTT后面打印了一个惊叹号)。总之, TTL为1、2和3的行正确地标识了R1、R2和R3, 接下来的三行每个都包含三个超时, 再接下来的TTL为7的行标识了目的地。

**8.5 在图8-8运行例子中，我们可以在sun和netb之间的SLIP链路上运行tcpdump程序。如果指定-v选项，就可以看到返回ICMP报文的TTL值。这样，我们可以看到进入netb、butch、Gabby和enss142.UT.westnet.net的TTL值分别为255、253、252和249。这是否为我们判断是否存在丢失路由器提供了额外的信息？**

从被tcpdump捕捉的ICMP报文的TTL可以看出，这些路由器都将一个ICMP报文的输出TTL设置为255。而从butch来的TTL=253值表明在butch和netb之间可能有一个未被察觉的路由器，否则TTL应该等于254。249也是类似的问题。这些未察觉的路由器可能没有正确地处理送达到他们并TTL=1的UDP数据报，但是对那些经过他们的ICMP数据报正确地进行了TTL减一的操作。还有一个可能的理由是，返回的ICMP报文采用的路径不同于UDP数据报的路径，因此也造成了TTL不连续的情况。

**8.6 SunOS和SVR4都提供了带-l选项的ping版本，以提供宽松源选路。手册上说明，该选项可以与-R选项（指定记录路由选项）一起使用。如果已经进入到这些系统中，请尝试同时用这两个选项。其结果是什么？如果采用tcpdump来观测数据报，请描述其过程。**

待做

**8.7 比较ping和traceroute程序在处理同一台主机上客户的多个实例的不同点。**

（1）ping的客户把ICMP回显**请求**报文的标识符字段设置为它的进程ID。ICMP回显应答报文包含同样值的标识符字段。每个客户都要查看这个返回的标识符字段，并且只处理那些它发送过的报文。

（2）traceroute客户将它的UDP源端口号设置为它的进程ID和32768的逻辑或。而返回的ICMP报文总是包含产生错误的IP数据报首部后面的前8个字节（包括了完整的UDP首部），所以这个源端口号可以在其首部中被找到。

**8.8 比较ping和traceroute程序在计算往返时间上的不同点。**

（1）ping客户将ICMP回显请求报文的选项数据部分设置成发送时间。这个选项数据必须在ICMP回显应答报文中返回。这样使得即使分组返回时失序，客户也能计算出RTT。

（2）traceroute客户必须记录下发送报文的时间，在获得应答后计算时间。

Summary: 因为这里的不同，ping每秒发送一个分组，而不管是否收到应答。traceroute发送一个请求并在收到应答（或超时）后，再发送下一个请求。

**8.9 我们已经说过，traceroute程序选取开始UDP目的主机端口号为33453，每发送一个数据报将此数加1。在1.9节中，我们说过暂时端口号通常是1024~5000之间的值，因此traceroute程序的目的主机端口号不可能是目的主机上所使用的端口号。在Solaris2.2系统中的情况也是如此吗？（提示：查看E.4节）**

参考答案：默认情况下Solaris2.2从32768开始使用临时的UDP端口，所以目的主机上目的端口已经被使用的可能性更大。

**8.10 RFC1393[Malkin 1993b]提出了另一种判断到目的主机路径的方法。请问其优缺点是什么？**

[RFC1393]([https://www.rfc-editor.org/rfc/pdfrfc/rfc1393.txt.pdf](https://www.rfc-editor.org/rfc/pdfrfc/rfc1393.txt.pdf))

Malkin的想法是定义新的IP选项，名为 IP traceroute option。在发送带有这种选项的ICMP回显请求报文后，会使得每个收到其的路由器都回送一个新定义的 ICMP 回应报文（Return Packet），当然这个新定义的回应报文会提供回显请求报文的目的地和新定义的IP选项。

优点：设路由器为n个，那么为了记录下路由路径，只用发送n+1个报文（1个回显请求+n个回应）。而且不会遇到路径变更的问题。相比而言，traceroute需要发送2n个报文，且有路劲变更的风险。

缺点：这种功能需要被安装在路由器中。解决方法是让新版的IP协议支持这种功能。

---

#### 第九章：IP 选路

**9.1 Why do you think both types of ICMP redirects network and host exist?**

（在RFC792第一次定义ICMP标准时，子网划分并没有被使用。）从空间利用角度上讲，使用网络重定向比使用N个主机重定向更节约路由表的空间。

**9.2 In the routing table for svr4 shown at the beginnig of Section 9.2, is a specific route to the host slip(140.252.13.65) necessary? What would change if this entry were removed from the routing table?**

这一项并不是必须的，但是如果把它删除了，那么之后svr4发送给slip的报文将被发送给默认路由sun，sun又转发给bsdi。因为sun把数据报从与接收到数据报相同的接口转发出去，它会给svr4回复一个ICMP重定向报文，在svr4的路由表中又重新添加回删掉的这一项。

**9.3 Consider a cable with both 4.2BSD hosts and 4.3BSD hosts. Assume the network ID is 140.1. The 4.2BSD hosts only recognize a host ID of all zero bits as the broadcast address (140.1.0.0), while the 4.3BSD hosts normally send a boradcast using a host ID of all one bits(140.1.255.255). Also, the 4.2BSD hosts by default will try to forward incoming datagrams, even if they have only a single interface.**

当4.2BSD主机收到目标为140.1.255.255的数据报时，它发现目的地与自己同处一个子网，于是广播ARP请求，当然没有回应。当多个4.2BSD主机在几乎同一时刻收到这种报文并广播ARP请求会造成网络阻塞。

**9.4 Continue the previous exercise, assuming someone corrects this problem by adding an entry to the ARP cache on one system on the 140.1 subnet (using the arp command) saying that the IP address 140.1.255.255 has a correspongindg Ethernet address of all one bits(the Ethernet boradcast). Describe what happens now.**

这会导致每台4.2BSD主机持续不断地发送以太网广播报文（链式效应），造成网络崩溃。

**9.5 Examine your system's routing table and describe each entry.**

```
naturain@naturain:/etc$ route
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
default         _gateway        0.0.0.0         UG    600    0        0 wlp2s0
link-local      0.0.0.0         255.255.0.0     U     1000   0        0 docker0
172.17.0.0      0.0.0.0         255.255.0.0     U     0      0        0 docker0
172.20.52.0     0.0.0.0         255.255.252.0   U     600    0        0 wlp2s0
naturain@naturain:/etc$ route -n
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
0.0.0.0         172.20.55.254   0.0.0.0         UG    600    0        0 wlp2s0
169.254.0.0     0.0.0.0         255.255.0.0     U     1000   0        0 docker0
172.17.0.0      0.0.0.0         255.255.0.0     U     0      0        0 docker0
172.20.52.0     0.0.0.0         255.255.252.0   U     600    0        0 wlp2s0
```

第二条和第三条为docker创建的路由。

重点关注wlp2s0网卡上的两条路由：

第一条路由带有G标识，为间接路由，指向网关的IP(172.20.55.254)。

第四条路由实质上指的是该主机所在子网，172.20.52.0表示了该子网，Gateway字段为0.0.0.0，表示发往该网络的数据报不需要路由，这是显然的。（补充：在发送到同个子网其他主机前需要利用ARP确定其物理地址。）

---

#### 第十章：动态选路协议


**10.1 In Figure 10.9 which of the routes came to gateway from the router kpno?**

![](imgs/TCP_IP_10-9.png)

下图是路由连接情况的局部图：

![](imgs/TCP_IP_10-7.png)

可以看到gateway直接和140.252.101.0和140.252.104.0网路连接。所以除了这两个路由，其他13个路由都是从kpno到gateway的。

**10.2 Assume a router has 30 routes to advertise using RIP, requiring one datagram with 25 routes and another with the remaining 5. What happens if once an hour the first datagram with 25 routes is lost?**

根据RIP协议，路由器会定期（30秒）广播自己的路由表进行更新，如果这次的丢失了，下个周期仍然会进行广播，因此问题不大。（一般来说，路由如果3分钟都没有更新才会失效。）

**10.3 The OSPF packet format has a checksum field, but the RIP packet does not. Why?**

RIP运行在UDP上，而UDP本身就提供了包含首部和数据部分的校验和。OSPF运行在IP上，IP的校验和只包含其首部，因此OSPF必须拥有自己校验和。

**10.4 What effect does load balancing, as done by OSPF, have on a transport layer?**

负载均衡增加了分组失序的可能性，并可能会使得传输层计算RTT出错。

**10.5 Read RFC 1058 for additional details on the implementation of RIP. In Figure 10.8 each router advertises only the routes that it provides, and none of the other routes that it learned about through the other router's broadcasts on the 140.252.1 network. What is this technique called?**

[RFC 1058](https://www.rfc-editor.org/rfc/pdfrfc/rfc1058.txt.pdf)

Simple Split Horizon: The "simple split horizon" scheme omits routes learned from one neighbor in updates sent to that neighbor.

**10.6 In Section 3.4 we said there are more than 100 hosts on the 140.252.1 subnet in addition to the eight routers we show in Figure 10.7. What do these 100 hosts do with the eight broadcasts that arrive every 30 seconds (Figure 10.8)?**

100个主机的设备驱动会处理这些广播报文，经过IP层和UDP层，并且最终被丢弃，因为主机不具有路由功能，UDP的520端口并不在使用。

---

#### 第十一章：UDP：用户数据报协议

**11.1 在11.5节中，向UDP数据报中写入1473字节用户数据时会导致以太网分片。在采用IEEE 802封装格式时，导致分片的最小用户数据长度是多少？**

IEEE 802 的用户数据容量比默认的RFC894定义的少8字节。

答案：1465B

**11.2 阅读RFC 791[Postel 1981a]，理解为什么除最后一片外，其他片的数据长度均要求为8字节的整数倍？**

[RFC791](https://www.rfc-editor.org/rfc/rfc791.txt)

”The fragment offset is measured in units of 8 octets (64 bits).  The first fragment has offset zero.“

因为IP数据报的占13位的片偏移字段是以8个字节为单位的。

**11.3 假定有一个以太网和一份8192字节的UDP数据报，那么需要分成多少个数据报片，每个数据报片的偏移和长度为多少？**

加上8字节的UDP首部，IP层需要发送8200字节。因为每一个IP packet都要加上20字节的IP首部，所以在MTU=1500的以太网上：

第一个数据片：1480@0+

第二个数据片：1480@1480+

第三个数据片：1480@2960+

第四个数据片：1480@4440+

第五个数据片：1480@5920+

第六个数据片：800@7400

**11.4 继续前一习题，假定这些数据报片要经过一条MTU为552的SLIP链路。必须记住每一个数据报片中的数据（除IP首部外）为8字节的整数倍。那么又将分成多少个数据报片？每个数据报片的偏移和长度为多少？**

每个1480字节的数据片将被再次分片，而且每片需要再次加上20字节的IP首部，所以在SLIP上最大可能传输的数据片长度为552-20=532字节，又因为长度需要为8的倍数，所以最大长度为528字节。

因此每个1480字节的数据片被分为528 + 528 + 424字节。800字节的数据片被分为528+272字节。

**11.5 一个用UDP发送数据报的应用程序，它把数据报分成4个数据报片。假定第一片和第2片到达目的端，而第3片和第4片丢失了。应用程序在10秒钟后超时重发该数据报，并且被分成相同的4片（相同的偏移和长度）。假定这一次接收主机重新组装的时间为60秒，那么当重发的第3片和第4片到达目的端时，原先收到的第1片和第2片还没有被丢弃。接收端能否把这4片数据重新组装成一份IP数据报？**

不能。重新组装只能将那些拥有相同标识符的数据片进行重组。

**11.6 你是如何知道图11-15中的片实际上与图11-14中第5行和第6行相对应？**

IP首部中的标识符字段相同，都是47942。

**11.7 主机gemini开机33天后，netstat程序显示48000000份IP数据报中由于首部检验和差错被丢弃129份，在30000000个TCP段中由于TCP检验和差错被丢弃20个。但是，在大约18000000份UDP数据报中，因为UDP检验和差错而被丢弃的数据报一份也没有。请说明两个方面的原因（提示：参见图11-4）**

（1）从图11-4中，我们能发现gemini没有设置发出的UDP报文中的检验和，那么很可能其对收到的UDP报文也不会检查检验和。

（2）绝大多数的UDP通信发生在本地网络LAN上，而不是WAN上，这规避了WANs上许多变幻莫测的情况。

**11.8 在讨论分片时没有提及任何关于IP首部中的选项--它们是否也要被复制到每个数据报片中，或者只留在第一个数据报片中？我们已经讨论过下面这些IP选项：记录路由（7.3节）、时间戳（7.4节）、严格和宽松的源站选路（8.5节）。你希望分片如何处理这些选项？对照RFC791检查你的答案。**

（1）需要被复制进每片的选项：严格和宽松的源站选路

（2）不会被复制，只出现在第一片中的选项：时间戳、记录路由

**11.9 在图1-8中，我们说UDP数据报是根据目的UDP端口号进行分配的。这正确吗？**

不正确。

根据11.12节，我们知道是根据目的UDP端口、源UDP端口、目的IP地址、源IP地址。

---

#### 第十二章：广播与多播

**12.1 广播是否增加了网络通信量？**

单单一个广播并不足以增加通信量，但是其增加了额外主机的处理负担。

当主机错误地回应广播时（比如ICMP端口不可达报文），会增加网络通信量。

Plus：路由器不会转发广播，但是网桥会，因此在桥接的网络中，广播能传播地更远。

**考虑一个拥有50台主机的以太网：20台运行TCP/IP，其他30台运行其他的协议族。主机如何处理来自运行另一个协议族主机的广播？**

每台主机的网卡都会收到广播，并把广播报文交给设备驱动程序，如果根据其协议字段，发现主机上没有运行相应的协议程序，则丢弃报文。

**登录到一个过去从来没有用过的Unix系统，并且打算找出所有支持广播的接口的指向子网的广播地址。如何做到这点？**

运行 ifconfig 命令：

```
enp0s31f6: flags=4099<UP,BROADCAST,MULTICAST>  mtu 1500
        ether e8:6a:64:c5:2b:7c  txqueuelen 1000  (Ethernet)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
        device interrupt 16  memory 0xee300000-ee320000

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 15005  bytes 9396694 (9.3 MB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 15005  bytes 9396694 (9.3 MB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

wlp2s0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 10.238.26.252  netmask 255.255.254.0  broadcast 10.238.27.255
        inet6 fe80::5fb7:8933:bde9:e0d4  prefixlen 64  scopeid 0x20<link>
        ether 58:a0:23:41:4d:35  txqueuelen 1000  (Ethernet)
        RX packets 1633541  bytes 2094096535 (2.0 GB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 397938  bytes 99929727 (99.9 MB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

```

根据得到的interfaces的信息，我们通过其tags可以知道enp0s31f6和wlp2s0支持广播（BROADCAST），并且wlp2s0直接给出了指向子网的广播地址：10.238.27.555。

**如果我们用ping程序向一个广播地址发送一个长的分组，如 sun % ping 140.252.13.63 1472，它正常工作，但将分组的长度再增加一个字节后出现如下差错，究竟出了什么问题？**

```
sun % 140.252.13.63 1473
PING 140.252.13.63: 1473 data bytes
sendto: Message too long
```

伯克利派的实现并不允许广播报文被分片，这是一个规定，并没有特别的技术原因（当然想要减少广播报文分组数是一个原因啦）。当数据长度为1472时，IP数据报刚好达到了以太网的MTU=1500。


**重做习题10.6，假定8个RIP报文是通过多播而不是广播（使用RIP版本2）。有什么变化？**

取决于这100台主机的以太网接口卡对多播的支持，则多播可能被接口卡忽视，可能被设备驱动程序丢弃，也可能经过IP和UDP传上来，主机如果不支持路由功能则丢弃报文。

---

#### 第十三章：IGMP：Internet组管理协议

**13.1 我们知道主机通过设置随机时延来调度IGMP的发送。一个局域网中的主机采取什么措施才能避免两台主机产生相同的随机时延？**

可以用一些host-unique的值来生成随机数（比如作为随机数种子），比如IP地址/链路层地址。

**13.2 在[Casner and Deering 1992]中，他们提到UDP缺少两个通过MBONE传送音频采样数据的条件：分组失序检测和分组重复检测。你怎样在UDP上增加这些功能？**

可以增加带有序列号和时间戳信息的头部字段。

---

#### 第十四章：DNS域名系统

**14.1 讨论一个DNS名字解析器和一个DNS名字服务器作为客户程序、服务器或同时作为客户和服务器的情况。**

DNS名字解析器只可能是客户，但是DNS名字服务器可以作为客户或者服务器。

**14.2 说明图14-12中构成响应的75个字节的含义。**

![](imgs/TCP_IP_14-12.png)

占有44字节的查询会被返回。

剩下31字节：2字节的指针（指向域名），10字节的固定字段（类型、类、TTL、资源长度），19字节的资源数据（包含域名）。

**14.3 在12.3节中我们指出，一个即可接收点分十进制形式的IP地址、也可接受主机名的应用程序，应先假定输入的是IP地址，如果失败，再假定是主机名。如果改变这个测试顺序会出现什么情况？**

如果先假定其是主机名，则当输入的是IP地址，也要调用一次DNS，会浪费资源和时间。

**14.4 每个UDP数据报有一个相应的长度。一个接收UDP数据报的进程将被告知这个长度。当名字解析器使用TCP而不是UDP来处理查询请求时，由于TCP是没有任何记录标记的字节流，那么应用程序是如何知道有多少数据返回？注意在DNS的报文首部（图14-3）中没有任何长度字段（提示：查阅RFC1035）**

[RFC1035](https://www.rfc-editor.org/rfc/rfc1035.txt)

Messages sent over TCP connections use server port 53 (decimal).  The message is prefixed with a two byte length field which gives the message.

**14.5 我们说一个名字服务器必须知道根名字服务器的IP地址，这一信息可通过匿名FTP获得。不幸的是当根名字服务器表发生变化时，并不是所有的系统管理员都会更新他们的DNS配置文件（根名字服务器表的确会发生变化，尽管不是经常的）你认为DNS如何处理这个问题？**

当一个名字服务器启动时，会从磁盘读取根名字服务器列表，并尝试向其中一个根名字服务器发送查询（NS类型），索要最新的根名字服务器列表。因此，这要求至少一个根名字服务器是没有改变过的。

**14.6 利用习题1.8指明的文件来确定谁应负责维护根名字服务器。名字服务器更新的频度是怎样的？**

The registration services of the InterNIC updates the root servers three times a week.

**14.7 维护一个名字服务器和一个无状态的名字解析器高速缓存的问题分别是什么？**

解析器会随着应用程序启动和退出，并且因为是无状态的，所以其无法分辨不同的名字服务器。

**14.8 在图14-10的讨论中，我们指出名字服务器将对A类型记录进行排序以便在公共网中的地址先出现。谁对A类型记录进行这种排序，是名字服务器还是名字解析器？**

由名字解析器完成排序，显然，解析器对客户所在网络的拓扑结构更加熟悉。（有疑问，名字服务器这么菜的吗？）


---

#### 第十五章：TFTP：简单文本传送协议

---

#### 第十六章：BOOTP：引导程序协议

---

#### 第十七章：TCP：传输控制协议

---

#### 第十八章：TCP连接的建立与终止

---

#### 第十九章：TCP的交互数据流

---

#### 第二十章：TCP的成块数据流

---

#### 第二十一章：TCP的超时与重传

---

#### 第二十二章：TCP的坚持定时器

---

#### 第二十三章：TCP的保活定时器

#### 第二十四章：TCP的未来和性能

#### 第二十五章：SNMP：简单网络管理协议

#### 第二十六章：Telnet和Rlogin：远程登录

#### 第二十七章：FTP：文件传送协议

#### 第二十八章：SMTP：简单邮件传送协议

#### 第二十九章：网络文件系统

#### 第三十章：其他的TCP/IP应用程序
