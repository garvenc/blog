本文更新于2022-12-09。

[TOC]

# 电话交换技术

**VoIP**（Voice Over IP）即承载于IP网上的语音电话。

**PSTN**（Public Switched Telephone Network）即公共交换电话网。

**交换机**（Switch或Exchange）位于电话网的中心，用于连接每个用户。

交换机间通过**中继线**（Trunk）相连。

现行电话网采用**E.164**号码格式。

**E1**，**T1**。

移动网是在固网的基础上增加了许多基站（Base Station），并增加了归属位置寄存器（Home Location Register，HLR）和拜访位置寄存器（Visitor Location Register，VLR），以记录用户的位置（在哪个天线的覆盖范围内）、支持异地漫游等。

1G是模拟系统，2G是以GSM为代表的数字系统，3G主要支持高速IP数据传输，4G是3G之后的延伸。

LTE的定义是长期演进而来的，其为现代的手机等移动设备提供了高速的数据通信手段，逐步实现全IP交换。运营商可选择以下几种方案解决LTE网络中的语音传输问题：

* VoLTE（Voice Over LTE，LET网络直传）：该方案基于IMS网络，使用该方案意味着语音以数据流形式在LTE网络中传输，所以无需调用传统电路交换网络，旧网络无需保留。
* CSFB（Circuit Switched Fallback，电路交换网络支援）：该方案中的LTE网络只用于数据传输，当有语音拨叫或呼入时，终端将使用原有电路交换网络。
* SVLTE（Simultaneous Voice and LTE，LTE与语音网同步支持）：该方案使用可以同时支持LTE网络和电路交换网络的终端。
* OTT（Ove-The-Top）：一种将服务建立于网络之上的方式。但语音业务是移动运营商的最主要的收入来源，把LTE的语音业务完全交给OTT是一种非常激进的观点，在电信领域得到的支持并不多。

**信令**（Signaling）。

* 对于普通的话机，用户线上传送的是模拟信号。话机通过电压变化来传递摘、挂机信号；通过**DTMF**（Dual Tone Multi Frequency，双音多频）在通话过程中传送要拨叫的电话号码；通过**FSK**（Frenquency-shift keying，即频移键控）来支持来电显示，主叫号码在第一声振铃和第二声振铃的时间间隔内传送。

* **ISDN**（Integrated Service Digital Network，综合业务数字网）在用户线上传送的是数字信号。它的基本速率接口（Base Rate Interface，BRI）使用144kbit/s的2B+D信道——两个64kbit/s的B信道用来传输话音、数据和图像，一个16kbit/s的D信道用来传输信令或分组信息。在我国并没有发挥出它应有的作用，很快被ADSL取代。

* **ISDN PRI**（Primary Rate Interface，基群速率接口）信令。信令和话路在同一个E1上传送，通常使用第16时隙，而0时隙传送同步信号，其它30个时隙传输通话信息，因此又称为30B+D。运营商与用户设备一般使用PRI信令对接。

* **七号信令**（Signaling System No. 7，SS7）是我国目前使用的主要信令方式，用于局间通信。

* **H.323**属于VoIP领域的通信信令，适用于用户线信令和局间信令。

* **SIP**（Session Initiation Protocol）即会话发起协议。属于VoIP领域的通信信令，适用于用户线信令和局间信令。

**IMS**（IP Multimedia Subsystem）即IP多媒体子系统。它旨在在过度阶段兼容原有的PSTN TDM网络（基于电路交换的网络，在IMS中称为CS域），并最终将所有的业务都转到基于包交换的网络（PS域）中。

**POTS**（Plain Old Telephone Service）即普通老式电话业务。典型的业务有：

* 缩位拨号（Abbreviated dialing）。
* 呼前转移（Call Forwarding），又称呼叫转移，包括：无条件转移、遇忙转移、无应答转移。
* 新转移方式。
* 立即热线（Hotline）。
* 延迟热线（Delayed Hotline）。
* 呼叫等待（Call Waiting）。
* 三方通话（Three Way，Conference Call）。
* 来电显示（Caller ID Presentation）。
* 呼出限制（Call Barring），又称密码限呼。
* 免打扰服务（Do Not Disturb）。
* 叫醒服务（Alarm Call），又称闹钟服务。
* 遇忙回叫（Completion of Call to Busy Subscriber，CCBS，Auto Callback）。

**PBX**（Private Branch eXchange）即专用小交换机。设备一般安装在企业内部，上端通过运营商提供的模拟或数字中继线连接到PSTN，下端则直接接企业内部的话机。

**IP-PBX**首先是一个PBX，具有传统PBX的绝大部分功能，另外由于使用了IP通信，能通过IP网提供语音、视频以及即时消息通信。

呼叫转移有两种：**盲转**（Blind Transfer）、**协商转**（Attended Transfer）。

**代接**。

组内代接，也就是**同组代答**。

**呼叫中心**（Call Center）。

**CTI**（Computer Telephony Integration）即计算机电话集成。

**CTI-Link**为交换机的一个可以受计算机系统控制的接口，由计算机系统通过某种协议获取交换机用户话机的状态信息以及对呼叫的控制命令。

**MOH**（Music on Hold）即保持音乐。

**DID**（Direct Inbound Dial）即对内直接呼入。外线号码就称为DID号码，简称DID。

**回铃声**（Ring Back Tone）。

**彩铃**（CRBT，Color Ring Back Tone）。

**Early Media**（早期媒体）。

**语音信箱**（Voicemail）。

**SBC**（Session Border Controller）即边界会话控制器，主要位于一堆SIP服务器的边界，用于隐藏内部服务器的拓扑结构、抵御外来攻击等。SBC可能是一个代理服务器，也可能是一个B2BUA。一般来说，SBC具有两个或多个网卡，一边连接互联网，一边连接内部的网络。

**3PCC**（Third Party Call Control，第三方电话呼叫控制）指的是由第三方控制者（Controller）在另外两者之间建立的一个会话，由控制者负责会话双方的媒体协商。

**来话**（Inbound Call），即**入局通话**。

**去话**（Outbound Call），即**出局通话**。

只有单向的语音流，称为**单通**。

**同振**即多个话机都会振铃，哪个先接听则接通哪个，其他自动挂断。**顺振**即前面的号码呼叫失败则呼叫下一个。

**IVR**（Interactive Voice Response），即交互式语音响应，实际上就是电话语音菜单。

**舒适噪声**（Comfortable NOice，CN）。

**TTS**（Text To Speech）是将文本转换成语音的一项技术，又称为**语音合成**（Synthesis）。

**ASR**（Automatic Speech Recognition）即语音识别。

**呼叫前转**通过拨打一个特定的功能码（一般使用\*57\*登记，#57#取消）登记欲转移到的号码，之后所有呼叫都会转移到该号码上。

**ACD**（Automatic Call Distribution），自动电话分配，通过一定策略为来话分配空闲**坐席**（Agent）的功能。

**onhook坐席**，挂机坐席，特点是当队列来了电话后再去呼叫坐席。**offhook坐席**，摘机坐席，特点是坐席会事先呼入队列并等待，当有来电时就可以立即接听，当服务完成后又变成等待状态，继续等待下一个呼叫到来。

**融屏**，即将多个会议成员的图像缩小，再合并到一个图像上。

**话单**（Call Detail Record，CDR）。

一般拨打外地用户需要加一个特殊的号码，称为**出局字冠**。

**端局**只有本地用户，**汇接局**为各端局之间的通话做转接服务。

既带本地用户，又作为汇接局，称为混合模式的汇接局。不带本地用户，专门做汇接，称为纯粹的汇接局。

每个端局都同时连接到两个汇接局上，一旦其中一个出现故障，则另一个可以接替工作，这种拓扑方式称为**双归属**。

**长途局**。

**IAD**，因特网接入设备。

电话机通过一根电话线连接到某个设备上，该设备提供一个模拟电话线的接口，称为**FXS**。电话机上也有一个模拟电话线的接口，称为**FXO**（在物理上就是一个RJ11接口）。FXS口是带电的，FXO口是不带电的。

**共享线路呈现**（Shared Lines Appearence，SLA），可以在自己的话机上监视其它话机的状态，知道其它话机是忙或闲。该功能在模拟话机是没有的。

从若干个网关中自动选择一个进行呼出，这种选择的过程称为**选线**，也称为**号码连选**。

# FreeSWITCH

总体架构：FreeSWITCH由一个稳定的**核心**（Core）及一些外围模块组成。核心包含关键的数据结构和复杂的代码、状态机、数据库等。绝大部分应用层的功能都在外围的模块中实现。外围模块是可以动态加载及卸载的。外围模块通过核心提供的Public API与核心进行通信，核心通过回调（或称钩子）机制执行外围模块中的代码。

**呼叫字符串**（Dial String，有时也叫Call URL）。

**挂机原因**（Hangup Cause）。

**单腿通话**（one-legged connection）。

每次呼叫，FreeSWITCH都会启动一个**Session**（会话，它包括SIP会话，SIP会在每对UAC-UAS之间生成一个SIP Session），用于控制整个呼叫，它会一直持续到通话结束。

每个Session都一一对应着一个**Channel**（通道，又称信道），Channel是一个UA间通信的实体，相当于FreeSWITCH的一条**腿**（leg），每个Channel都用一个唯一的UUID来标识，称为**Channel UUID**。另外，Channel上可以绑定一些呼叫参数，称为**Channel Variable**（通道变量）。Channel中可能包含媒体（音频或视频流），也可能不包含。

FreeSWITCH的作用是将两个Channel（**a-leg**和**b-leg**，通常先创建的或占主动的叫a-leg）**桥接**（bridge）到一起，使双方可以通话。这两路桥接的通话（两条腿）在逻辑上组成一个通话，称为一个**Call**。

**Dialplan**即拨号计划，主要作用是对电话进行路由，决定和影响通话的流程。

拨号计划有三个核心要素：Dialplan、Context、Extension。

**inline Dialplan**（内联拨号计划）与XML Dialplan不同，inline Dialplan没有extension，也没有复杂的condition，只是像XML Dialplan中那样简单地叠加action。

APP又称**拨号计划工具**（Dialplan Tools）。

**B2BUA**（Back-to-back User Agent）即背靠背的用户代理。

FreeSWITCH的协商分为早协商和晚协商。当呼叫到达一个SIP Profile时，即某端口收到INVITE请求而未到达路由阶段时，就先行协商的，称为**早协商**。而如果等到路由阶段，到达拨号计划时再进行协商的，称为**晚协商**（Later Negotiation）。通过使用晚协商，用户可以有更多的自由去确定协商策略。

**透传**（Passthru）是指在不经过转码的情况下，将从一方收到的媒体流原样转给另一方。

**媒体绕过**（Bypass Media）是指媒体流使用点到点传输，根本不经过FreeSWITCH。

**媒体代理**（Proxy Media），即不管FreeSWITCH是否支持对该编码的转码，它都对RTP数据在不进行任何处理的情况下发给另一方。它跟透传的区别是，它只改变SDP中的“c=”部分，而不对RTP进行任何改变，如仍然保留其时间戳等数据。

为了解决监听和录音问题，FreeSWITCH在媒体流路径上放了一个**Media Bug**。

为了屏蔽不同语言提示的差异，FreeSWITCH实现了**Phrase**（短语）框架，可以将不同语言的日期、时间、货币、数字等以相同语法表示，并可以在必要时结合TTS实现更强大的语音提示。

只涉及一条腿（即一个Channel）的录音，称为**单腿录音**。

**SIP Profile**相当于一个SIP UA。一个系统中可以有多个SIP Profile，每个SIP Profile都可以监听不同的“IP地址:端口”对，一个“IP地址:端口”对唯一标志一个UA。

一个SIP Profile中有多个**Gateway**（网关），主要用于定义远端的SIP服务器，使FreeSWITCH可以通过网关向外拨打电话。

FreeSWITCH可以作为注册服务器，其他SIP客户端可以向它注册，这些SIP客户端所代表的用户救称为本地SIP用户，简称**本地用户**。其它机器上的用户称为**外地用户**。

**ACL**（Access Control List）即访问控制列表，通过一个列表矩阵控制哪些用户可以访问哪些资源。

如直接把TDM电话设备产生的双音频信号按与音频编码同样的方式进行编码并放到RTP数据中传输，则这种DTMF称为**带内DTMF**（Inband）。

**多租户**（Multitenancy）就是在一个系统中，支持多个彼此互相独立的PBX（如属于不同公司）应用，这些不同的PBX中可能有相同的分机号，而不会产生冲突。

FreeSWITCH中的Domain是指一个域，或指一个租户。实际上，它就是唯一标志一个域的字符串。

每小时能处理的呼叫称为小时呼，又称**BHCA**（Busy Hour Call Attempt），即最大忙时试呼次数。

每秒能处理的呼叫称为**CPS**（Call Per Second）。

FreeSWITCH的**事件**与SIP消息类似，包含一些事件头（Header）和可选的事件正文（Body），在内部使用C语言结构体表示，可序列化成类似SIP消息的简单文本格式（Plain）、JSON、XML。

**XML绑定**（XML Binding）：FreeSWITCH提供一种机制，可以在XML配置节点（Section）上绑定一些回调（函数或方法），然后当FreeSWITCH需要用到一段XML时，就调用该回调来动态取得XML。

**Event Socket**有两种模式：
* 内连（Inbound）模式：FreeSWITCH作为一个服务器，用户程序作为一个TCP客户端连接到FreeSWITCH。客户端连接后，可以订阅FreeSWITCH的一些内部事件。
* 外连（Outbound）模式：FreeSWITCH作为一个TCP客户端连接到一个TCP服务器上。

**ESL**（Event Socket Library），即Event Socket库。

ESL协议是纯文本的协议。它的设计思想来自于HTTP协议和SIP协议。

消息（Message）是与事件类似的另外一种传递（调用）方式，与事件不同的是，消息的发送总是同步进行的，实际上是直接调用接收消息的回调函数。

# 媒体

**媒体**（Media）。

**载体**（Carrier）。

从模拟信号变成数字信号的过程称为**模数转换**（Analog Digital Convert，AD）。AD转换要经过采样、量化、编码三个过程。**编码**（Code）就是指按照一定的规则将采样所得的信号用一组二进制或者其他进制的数来表示。经过编码后的数据便于在数字网络上传输，到达对端以后，再经过**解码**（Decode）过程变成原始信号，进而经过**数模转换**（Digital Analog Convert，DA）再恢复为模拟量，即转换为人们能够感知的信号。习惯上把编码与解码合起来称为**编解码**（Codec），即Co（de）与Dec（ode）的合成写法。

**PCM**（Pulse Code Modulation）即脉冲编码调制，又称**G.711编码**。PCM有两种压缩方式：**A律**（alaw）和**μ律**（ulaw），对应的编解码名称为**PCMA**和**PCMU**。我国和欧洲使用A律，北美使用μ律。A律也用于国际通信，因此凡是涉及A律和μ律转换的情况都由μ律的国家负责。

由于VoIP是在IP网上传输的，大多数网络都没有**QoS**（Quality of Service）保证，容易产生**丢包**（Packet Loss）、**延迟**（Delay）、**抖动**（Jitter）。

音频编码最基本的两个技术参数是**采样频率**和**打包周期**。采样频率越高，声音就越清晰，保留的细节就越多，也会占用更大的带宽。打包周期跟传输有关，打包周期越短，延迟越小，传输开销就会越多，需要更大的带宽；打包周期越长，带来的延迟就越大，如果传输过程中有丢包，对语音质量的影响就越大。

媒体流的协商过程称为**SOA**（Service Offer and Answer，提议/应答）。

**立体声**将通话的多个人的声音分别存放在不同的声道里。

**MRCP**（Media Resource Control Protocol）是一个支持访问网络上的媒体资源的协议，典型应用是TTS和ASR（自动语音识别）。

录像数据要按一定的格式存储在文件中，不同的文件格式称为不同的**容器**（Container），在这些容器中，通常会包含多个音频**轨道**（Track）和视频轨道，有的还含有同步信息。

每秒传送的帧的数量称为帧频，即**FPS**（Frame Per Second）。

视频是以一帧一帧的图象压缩后传送的。为了减少传送的数据量，先传送一个完整的帧，称为**关键帧**（Intraframe，即Key Frame）。然后后面只传送后面的帧与前面帧的不同的部分，称为**非关键帧**或**预测帧**（Interframe），这种帧需要引用前面的关键帧和预测帧才能还原原始图像。每隔一段时间需要重新发一个关键帧。有的视频系统也使用**双向预测帧**，这种帧需要前后向的引用，一般不用在实时通信中。

在MPEG编码（可以认为H264是MPEG的增强版）中关键帧称为**I帧**，非关键帧称为**P帧**，双向预测帧称为**B帧**。

# NAT

**NAT**（Network Address Translation），即网络地址转换，最初是为了克服IPv4网络地址不足而出现的技术。解决在NAT网络环境下的内、外网通信问题，称为**NAT穿越**。

**内部网**（Intranet），简称内网。

**打洞**（UDP Hole Punching）。

NAT有三种类型：静态NAT（Static NAT）、动态地址NAT（Pooled NAT）、网络地址端口转换（Network Address Port Translation，**NAPT**）。前两种类型都不能节省IP地址。

NAPT有四类，又可分为两种：
* 锥形NAT：对内外的同一个源地址和端口发往任何外网地址的数据都映射成同一外网地址和端口。
	* 全锥型NAT（Full Cone NAT）：内部主机向外打了一个洞，外网的任何主机都可以利用这个洞与它通信。
	* 限制锥型NAT（Restricted Cone NAT）：内部主机向某一外部主机打了一个洞后，只有该外部主机能利用这个洞。
	* 端口限制锥型NAT（Port Restricted Cone NAT)：内部主机向外部主机上某一程序（一个端口）打了一个洞，只有该程序可以利用这个洞，其他的不行。
* 对称型NAT（Symmetric NAT）：同一内部主机联系不同的外部主机时需要打不同的洞。同一外部主机任何端口都可以利用这个洞。

**STUN**（Session Traversal Utilities for NAT），即NAT会话穿越工具。原理是：在公网上部署一台STUN服务器，位于NAT后面的客户端设备向它发送一系列的UDP包先在NAT设备上打一个洞，STUN服务器取到UDP包的来源地址后，会回送相关的消息告诉该客户端它被映射的外网地址。STUN服务对于锥型NAT是有效的，但对于对称型NAT就无能为力。

**TURN**（Traversal Using Relay NAT），即使用转发方式进行穿越的NAT。需要在公网部署一台TURN服务器，该服务器作为中间人对双方的数据进行转发。主要用来解决对称型NAT的问题，对于其他NAT类型同样有效。

**ICE**（Interactive Connectivity Establishment），即交互式连通建立。它不是一个新的协议，而是综合利用STUN和TURN等技术，使之在最合适的情况下工作。在SIP通信中，ICE通过扩展SDP，为RTP媒体提供多个候选的地址（Candidate）。
