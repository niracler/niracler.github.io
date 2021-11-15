---
title: WebRTC 学习记录（二）| WebRTC 如何建立连接
tags:
  - WebRTC
  - 学习记录
  - 网络
date: 2021-11-15 09:23:00
---


WebRTC 是一个宝库，他在数据传输方面也是做到极致了。

## 为什么是WebRTC？需要解决的问题是什么？我们关注的是什么？

在了解了 croc 的情况下，我们这边看一下该类服务还能够优化到什么地步。

1. 我们面对到的一个事实是“Relay 服务器实在太耗流量了！”
2. 我们这里先引入一个知识："NAT 是可以穿透的"。
3. TURN是STUN协议的中转扩展，croc工具自己有中转功能，没有去实现类似STUN的功能。其实 croc 那边也尝试过，但其实到现在都还没支持。[3]
    [![IgGpCT.md.png](https://z3.ax1x.com/2021/11/15/IgGpCT.md.png)](https://imgtu.com/i/IgGpCT)

<!-- more -->

## 什么是ICE Candidate (Interactive Connectivity Establishment Candidate)，以及候选者的类型

 **ICE是交互式连接建立的意思**，它是一种用于NAT（网络地址转换器）的技术，用于建立VOIP、点对点、即时通信和其他类型的交互式媒体的通信。通常情况下，ICE Candidate 的信息是关于数据将被交换的IP地址和端口。

端到端创建连接的关键是Candidate的收集。 ICE Candidate 表示 WebRTC 与远端通信时使用的协议、IP 地址和端口，一般由以下字段组成[4]：

```json
{
  IP: xxx.xxx.xxx.xxx,      // 本地 IP 地址
  port: number,             // 本地端口号
  type: host/srflx/relay,   // 候选者类型，包括 host、srflx 和 relay
  priority: number,         // 优先级
  protocol: UDP/TCP,        // 传输协议
  usernameFragment: string. // 访问服务的用户名
  ...
}
```

WebRTC将Candidates分为三种类型。

- **host 类型 (Host Candidate)**: 即本地内网的IP和端口。IPv6也是属于这一种。这个地址来源于本地的物理网卡或逻辑网卡上的地址，对于具有公网地址或者同一内网的端可以用。host 类型的 Candidate 是最容易收集的。
- **srflx 类型 (Server Reflexive Candidate)**: 这是本地机器上NAT映射后的外部网络的IP和端口。是通过信令方式向 STUN 服务器发送 binding request 找到NAT映射后的地址。
- **relay 类型 (Relayed Candidate)**: 即中继服务器的IP和端口。这个地址是端发送 Allocate 请求到 TURN 服务器 ，由 TURN server 用于中继的地址和端口

host 类型优先级最高，srflx 次之，relay 最低

> 补充:
    1. 其实还有一个 prflx 类型的 Candidate，也是NAT映射后的外部网络的IP和端口，不过获得的方式稍有不同。 [5]
    2. 也有 tcp 专门的 Candidate Type [6]

## 4.3 STUN/TURN和信令服务器之间的区别

WebRTC 中我们会遇到三种类型的服务器，分别是：STUN服务器、TURN服务器和信令服务器 [7]

**STUN服务器** :

- **用于帮助通信双方发现有关其公网 IP 的信息并打开防火墙端口**。这要解决的主要问题是，很多设备都在小型专用网络中的 NAT 路由器后面；NAT 基本上允许传出请求及其响应，但会阻止任何其他“未经请求的”传入请求。
- **STUN 服务器充当向其发出请求的临时中间人，它在 NAT 设备上打开一个端口以允许响应返回**，这意味着现在有一个已知的开放端口可供其他对等方使用。这就被称为NAT打洞技术。
- **是否能打洞成功需要看NAT的类型**。(关于NAT类型的讨论，详情见下一章：#I40ONU)

**TURN服务器** :

- **TURN 服务器是公共可访问位置的中继，以防无法进行 P2P 连接。** 仍然存在打孔不成功的情况，例如由于更严格的防火墙。在这些情况下，两个对等方根本无法直接进行P2P通信。
- **它们的所有流量都通过 TURN 服务器进行中继(非常占用带宽)** 。
- **一般是通信双方都可以不受限制地连接的第三方服务器**，并且只是将数据从一个对等点转发到另一个。TURN 服务器的一种流行实现是coTURN。

**信令服务器** :

- WebRTC 规范对信令服务器没有任何说明，因为每个应用程序的信令机制都是非常独特的，并且可以采用多种不同的形式。

我们可以简单理解为：**STUN 是 NAT 打洞的，而 TURN 是做流量转发的，TURN 这种流量会经过relay服务器**

> 这里需要说明一点，relay 服务是通过 TURN 协议实现的。所以我们经常说的 relay 服务器或 TURN 服务器它们是同一个意思，都是指中继服务器。上一章的 croc 的中继服务器其实就相当于这里的TURN服务器。

## 整个WebTRTC建立连接的流程的复杂图示

概念讲完了，我们上图:[1]

[![IgGAbR.png](https://z3.ax1x.com/2021/11/15/IgGAbR.png)](https://imgtu.com/i/IgGAbR)

ICE Candidate???

> 补充:
    - Offer: 在双方通讯时，呼叫方发送的 SDP 消息称为 Offer。
    - Answer: 在双方通讯时，被呼叫方发送的 SDP 消息称为 Answer。

## TURN的使用频率是多少，其用途是什么

我就这个问题在 Twitter 上问过 WebRTC 的 golang 社区 Pion [1]。总的来说就是 **需要考虑各种因素，你要监控你使用场景的流量** ：

- 不同的 NAT 类型的覆盖率。
- IPv6 的普及率。
- 地区差异（运营商选择的路由器）。
- 具体业务场景，是否只有数据或者是媒体和数据。
- 服务器还是浏览器客户端。

但是，说这么多都没有，你需要部署一个 TURN 和并监控它的流量。。。

> 网上流传的一个说法是: *“80% to 90% of devices are able to connect over WebRTC without TURN servers”*，我找了半天硬是没有找到出处或者对应的论文。。。

## 我应该做什么来建立 WebRTC 服务？(TODO)

见 golang 以及 typescript

## 参考资料

- [1] MDN Web Docs: [WebRTC connectivity 流程图](https://developer.mozilla.org/en-US/docs/Web/API/WebRTC_API/Connectivity#the_entire_exchange_in_a_complicated_diagram)
- [2] Twitter回复内容：<https://twitter.com/niracler/status/1413123595157446657>
- [3] croc 作者在尝试引入 WebRTC 的情况下的: [测试以及回复](https://github.com/schollz/croc/issues/108#issuecomment-689715146)
- [4] 极客时间:从0打造音视频系统的教程: [09 | 让我们揭开WebRTC建立连接的神秘面纱](https://time.geekbang.org/column/article/112325)
- Hacker News 上对于 WebRTC 的讨论: [WebRTC is now a W3C and IETF standard (web.dev)](https://news.ycombinator.com/item?id=25933016)
- [5] MDN Web Docs: [RTCIceCandidate.type](https://developer.mozilla.org/en-US/docs/Web/API/RTCIceCandidate/type#syntax)
- [6] MDN Web Docs: [RTCIceCandidate.tcpType](https://developer.mozilla.org/en-US/docs/Web/API/RTCIceCandidate/tcpType)
- [7] STUN/TURN和信令服务器之间的区别来源: [Difference between STUN/TURN(coTURN) servers and Signaling servers (written with socket.io/websocket) in WebRTC?](https://stackoverflow.com/questions/54388401/difference-between-stun-turncoturn-servers-and-signaling-servers-written-with)
