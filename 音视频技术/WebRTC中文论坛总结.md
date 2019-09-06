## 启动WebRTC PeerConnection 基本流程
初始化这个过程RTCPeerConnection有两个工作要做：

	1. 确定本地媒体条件，如分辨率和编解码器功能。这是用于提供和应答机制的元数据。
	2. 获取应用程序主机的潜在网络地址，成为候选项


让我们假设一个场景：Alice正在尝试呼叫Eve。下面是完整的提供/应答机制：
	
	 1. Alice创建一个RTCPeerConnection对象。
	 2. Alice使用RTCPeerConnection createOffer()方法产生一个提供（一个SDP会话描述）。
	 3. Alice用他的提供调用setLocalDescription()。
	 4. Alice将提供串联起来，并使用信令机制将其发送给Eve。
	 5. Eve用Alice的提供调用setRemoteDescription()，以便她的RTCPeerConnection知道Alice的设置。
	 6. Eve调用createAnswer()，以及success callback函数传入本地会话描述：Eve的应答。
	 7. Eve通过调用setLocalDescription()将其应答设置为本地描述。
	 8. Eve然后使用信令机制将她的字符串化的应答发回给Alice。
	 9. Alice使用setRemoteDescription()将Eve的应答设置为远程会话描述。

Alice和Eve也需要交换网络信息。“查找候选项”这个表达是指使用ICE框架查找网络接口和端口的过程。
	
	 1. Alice使用onicecandidate处理器创建一个RTCPeerConnection对象。
	 2. 处理器在网络候选变得可用时被调用。
	 3. 在处理器中，Alice通过其信令通道将字符串化的候选数据发送给Eve。
	 4. 当Eve从Alice那里获得候选消息时，她调用addIceCandidate()，将候选项添加到远端对等描述中。

JSEP支持ICE Candidate Trickling，它允许主叫方在最初的提供之后递增地向被叫方提供候选项，并使被叫方开始在通话中进行操作并建立连接而不用等所有候选项到达。

ICE试图找到连接对方的最佳途径。它会并行地尝试所有可能性，并选择最有效的选项。ICE首先尝试使用从设备操作系统和网卡获取的主机地址进行连接；如果不成功的话（对于NAT后面的设备就会失败），ICE会使用STUN服务器获取外部地址，如果还是失败的话，则通过TURN中继服务器路由数据。

	 # STUN服务器是用来获取外部地址的。
	 # TURN服务器是用来在直接连接（点到点）失败的情况下进行中继数据流量的
         
它提供了包括音视频的采集、编解码、网络传输、显示等功能。如果你想基于WebRTC开发实时音视频应用，需要注意，由于WebRTC缺少服务端设计和部署方案，你还需要将WebRTC与Janus等服务端类开源项目结合即可。

### WebRTC的回声消除算法：AEC和AECM

## SDP 传输ICE网络信息

typ host: host类型表示与本地网络上设备的连接。
typ srflx: srflx是服务器反身性（Server Reflexive）的缩写，用于表示获取公共IP地址.只需要STUN的对等连接
typ relay: relay用于描述TURN的连接性。当双方都提供这样的数据包时，那么连接是绝对可能进行的。

ssrc: 表示流源（Stream Source），提供给每个音频和视频媒体轨道。它详细介绍了相关轨道的吞吐量，并包含了诸如RTCP往返时间等有用的细节。

![](http://webrtc.org.cn/wp-content/uploads/2017/02/bwe3.png)

这个RTCP反馈默认100ms发送一次，但是实际上是动态适应的，只会使用5%的可用带宽（最小值是50ms，最大值是250ms）

## 关键术语与存在问题

带宽： 带宽是指数据可以在两个端点之间传输的速率。 m/s,kb/s,G/s。带宽是容量，而比特率是传输速率。

延时: 数据从一个网络端口传输到另一个端口所花费的时间。 

	1. 处理延时
	2. 排队延时
	3. 传输延时：传输延时是将数据包的数据推入线路所需的时间。
	4. 传播延时：传播延时是与从发送端传输到接收端的数据包的第一个比特相关的时间。

丢包：一段时间内数据包在数据流中被丢弃或者丢失的数量或者百分比

如果FEC不可用的话，那么智能的音频编码器（比如Opus）可以利用播放的波形来产生一个接近丢失音频包的近似波形—这项技术被称为丢帧隐藏（PLC）。

抖动：就是传输包前后延时的值不一致，通常会导致丢包和网络拥塞。要想消除抖动的影响就需要媒体接收端通过一个“抖动缓冲区”（jitter buffer）来处理数据。以达到可以平滑输出的目的，并且确保数据包处在一个正确的顺序中。

延时主要会影响用户体验。在严格的音频通话中，150毫秒的延时是非常明显的并且会影响用户。在严格的视频通话中，认为400毫秒是可辨识的。将这两种呼叫功能集中在一起后，联合的音频和视频呼叫应该保持同步，并且延时要少于150毫秒以不影响用户。但是，一般来说，延时尽可能低是非常重要的。无论如何，ITU建议将网络延时保持在100毫秒以下。



尽管SDP中单独一个媒体段的意义和作用可能会容易混淆。WebRTC 1.0采用了一种方法将每个m=段携带关于发送的单独MediaStreamTrack信息，接收的MediaStreamTrack或者二者都有。


	RTCPeerConnection：基本可以代表一切（发送以及接收轨道或者数据，本地以及远端SDP表示等等）
	RTCTransceiver：引用了SDP中一个m=段
	RTCRTPSender：收发器的发送部分。
	RTCRTPReceiver：收发器的接收部分。

## SDP的总结：

在浏览器端的时候，m=audio或者m=video部分传输用于发送或接收MediaStreamTrack的RTP参数



使用getDisplayMedia实现在Chrome中屏幕共享

## 音视频直播架构

### Mesh:
![](http://webrtc.org.cn/wp-content/uploads/2018/08/080303.png)

### MCU
![](http://webrtc.org.cn/wp-content/uploads/2018/08/08053.png)

### SFU
![](http://webrtc.org.cn/wp-content/uploads/2018/08/part302.png)

SFU提供了一种结构，它不像Mesh那么容易瘫痪，同时比MCU的花费低。

SFU背后的想法与MCU相同。它在中间有一台媒体服务器，所有peers向它发送流，唯一不同的是，它不会做繁重的处理，服务器将其引到其它peers，这样它们可以进行任何所需的处理。


[网速测试网站](https://www.speedtest.net/)

[本地webrtc 连接性测试](https://test.webrtc.org/)

Skype 研发的 Opus 已经成为实时音频主流的编码器。Opus 优点众多，编码计算量小、编码延迟 20ms、窄带编码-silk、宽带编码器 CELT、自带网络自适应编码等。





