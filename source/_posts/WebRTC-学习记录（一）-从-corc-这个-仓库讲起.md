---
title: WebRTC 学习记录（一）| 从 corc 这个仓库讲起
date: 2021-11-14 11:29:55
tags:
---


## 3 文件传输以及 croc 代码分析

若是让我们做一个文件传输服务器， 我们应该怎么做？

### 3.1 文件传输的四种网络场景

我根据终端计算机所在的网络环境(是否有公网IP)划分了常见的4种场景:

| 场景 | 请求发起方 | 经过NAT | 中继服务器 | 例子 | 备注 |
|---|---|---|---|---|---|
| **AB处于同一局域网** |直接可以互通发起请求|NO|NO |scp\rsync\ftp||
| **A(B)拥有公网IP** | 需要B(A)主动发起请求 | YES | NO |scp\rsync\ftp ||
| **AB均拥有公网IP** | 直接可以互通发起请求 | NO |NO | scp\rsync\ftp | IPv6其实可以理解为这种情况 |
| **AB处于不同局域网** | 双方都需要先跟中继服务器 C 建立连接 | YES | YES | 微信\QQ\钉钉 ||

**总结：一般由没有公网IP的计算机主动发起请求**

关键概念(若是不理解，届时我可以画图示意一下， **TODO** ):

- **NAT** : 可以简单理解为 **端口映射** ，会将我们计算机的 iAddr:iPort 映射为 eAddr:ePort。(例如：192.168.1.5:45678→54.8.23.45:678234)
- **中继服务器(relay server)** : AB计算机都可以访问的一个服务器。常见类型有STUN/TURN等。

<!-- more -->

### 3.2 作为服务提供方，在文件传输中我们关注的点是什么？

在达到“允许**任何两台计算机**传输数据”的需求的前提下：

1. **带宽占用** : 我们使用多少流量？我们需要节省网络中的流量。带宽、流量怎么减。作为服务提供方，我们最关注的就是成本，而流量跟带宽就是成本的关键(究竟占多少还有待考究，**TODO**)。
2. **连接占用时长** : 这里指的速度不单单是文件传输的速度，而是你这次连接所占有我的服务器的时长，快吗？越早断开越好。
3. **安全** : 安全的重要性是毋庸置疑的。
4. 断点续传: 这是也一个必备项，但不是我们最关注的。
5. 秒传: 其实这不属于文件传输的范畴，这更多是属于文件存储服务的范畴。

### 3.3 让我展示一下使用croc的例子

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/21011c3a-27e8-46cf-872c-d3c60b35a146/Untitled.png](https://raw.githubusercontent.com/schollz/croc/master/src/install/customization.gif)

[1](这种 gif 的方案？ **TODO**)

### 3.4 `croc` 功能实现: 发送方功能实现

抛开上面的成本、速度以及安全先不谈，我们先保证它能够符合我们的使用场景（见3.1）。中继服务器如何工作？以及它如何切换传输方案？

- **主流程** :
    1. A在局域网中广播信息，同时AC建立连接

        ```go
        // from src/croc/croc.go#L358
        // Send will send the specified file
        func (c *Client) Send(options TransferOptions) (err error) {
            err = c.sendCollectFiles(options)
        
            // ... handle default args
            if !c.Options.DisableLocal {
            //... broadcast on local network
            }
        
            if !c.Options.OnlyLocal {
                // connect to relay server
            }
        }
        
        ```

- **局域网广播** 部分:
    1. 建立本地中继服务器，同时在 IPv6 以及 IPv4 网段进行广播

        ```go
        // from src/croc/croc.go#381
        c.setupLocalRelay()
        // broadcast on ipv4
        go c.broadcastOnLocalNetwork(false)
        // broadcast on ipv6
        go c.broadcastOnLocalNetwork(true)
        go c.transferOverLocalRelay(options, errchan)
        
        ```

    2. transferOverLocalRelay实现 ：与中继服务器交换密钥建立连接，并监听中继服务器信息，若收到 `handshake` 则成功建立连接，开始发送文件。

        ```go
        conn, banner, ipaddr, err := tcp.ConnectToTCPServer("localhost:"+c.Options.RelayPorts[0], c.Options.RelayPassword, c.Options.SharedSecret[:3])
        for {
            data, _ := conn.Receive()
            if bytes.Equal(data, []byte("handshake")) {
             break
            } else if bytes.Equal(data, []byte{1}) {
             log.Debug("got ping")
            } else {
         log.Debugf("instead of handshake got: %s", data)
            }
        }
        c.conn[0] = conn
        log.Debug("exchanged header message")
        c.Options.RelayAddress = "localhost"
        c.Options.RelayPorts = strings.Split(banner, ",")
        if c.Options.NoMultiplexing {
            log.Debug("no multiplexing")
            c.Options.RelayPorts = []string{c.Options.RelayPorts[0]}
        }
        c.ExternalIP = ipaddr
        errchan <- c.transfer(options)
        ```

- **AC服务器通信** 部分:
    1. 与中继服务器交换密钥建立连接, 并通过中继服务器获取到自己的路由器的NAT所映射的该服务器的公网IP`ipaddr`:

        ```go
        for i, address := range []string{c.Options.RelayAddress6, c.Options.RelayAddress} {
            // ... handle error
        
            // connect to relay server
            conn, banner, ipaddr, err = tcp.ConnectToTCPServer(address, c.Options.RelayPassword, c.Options.SharedSecret[:3], durations[i])
            if err == nil {
         c.Options.RelayAddress = address
         break
            }
        }
        ```

    2. 监听中继服务器消息，若收到 `ips?` 则将本地IP发送至中继服务器, 若收到 `handshake` 则成功建立连接，开始发送文件。

        ```go
        for {
            data, errConn := conn.Receive() //... handle error
            if bytes.Equal(data, []byte("ips?")) {
                // recipient wants to try to connect to local ips
                var ips []string
        
                // only get local ips if the local is enabled
                if !c.Options.DisableLocal {
                    // get list of local ips
                    ips, err = utils.GetLocalIPs() //... handle error
                    // prepend the port that is being listened to
                    ips = append([]string{c.Options.RelayPorts[0]}, ips...)
                }
        
                bips, _ := json.Marshal(ips)
                if err := conn.Send(bips); err != nil {
                    log.Errorf("error sending: %v", err)
                }
            } else if bytes.Equal(data, []byte("handshake")) {
                break
            }
            // else ... Determine if the connection is maintained or failed
        }
        
        c.conn[0] = conn
        c.Options.RelayPorts = strings.Split(banner, ",")
        if c.Options.NoMultiplexing {
            log.Debug("no multiplexing")
            c.Options.RelayPorts = []string{c.Options.RelayPorts[0]}
        }
        c.ExternalIP = ipaddr
        log.Debug("exchanged header message")
        errchan <- c.transfer(options)
        
        ```

### 3.5 `croc`功能实现: 接收方功能实现

- 第二步:B与C建立连接，通过密钥找到A服务器的信息。
同时: B尝试在局域网中寻找A服务器的信息
- **主流程** :
    1. 总的来说就是找中继服务器的IP，究竟是用局域网的，还是用公网上的, **优先局域网**，然后发送 `handshake` 建立连接，接受文件。

        ```go
        // src/croc/croc.go#L497
        func (c *Client) Receive() (err error) {
            // recipient will look for peers first
            // and continue if it doesn't find any within 100 ms
            // ...handle default args
        
            if !c.Options.DisableLocal && !isIPset {
                //... discover from local network
            }
        
            // connect to relay server
            c.conn[0], banner, ipaddr, err = tcp.ConnectToTCPServer(address, c.Options.RelayPassword, c.Options.SharedSecret[:3], durations[i])
            if err == nil {
           c.Options.RelayAddress = address
           break
            }
        
            if !usingLocal && !c.Options.DisableLocal && !isIPset {
           // ask the sender for their local ips and port
           // and try to connect to them
            }
        
            err := c.conn[0].Send([]byte("handshake"))
            //... handle error
            err = c.transfer(TransferOptions{})
            //... handle error
            return
        }
        
        ```

- **局域网发现** 部分:
    1. 在局域网内通过抓包尝试获取发送端服务器IP

        ```go
        // src/croc/croc.go#L522
        // attempt to discover peers
        go func() {
            defer wgDiscovery.Done()
            ipv4discoveries, err1 := peerdiscovery.Discover(peerdiscovery.Settings{
         //...
            })
            if err1 == nil && len(ipv4discoveries) > 0 {
         //...
         discoveries = append(discoveries, ipv4discoveries...)
         //...
            }
        }()
        // ...IPv6 same as above
        wgDiscovery.Wait()
        
        ```

    2. 若是局域网发现成功，则将 usingLocal 设置为 true，就不使用公网的中继服务器了

        ```go
        if err == nil && len(discoveries) > 0 {
            log.Debugf("all discoveries: %+v", discoveries)
            for i := 0; i < len(discoveries); i++ {
             log.Debugf("discovery %d has payload: %+v", i, discoveries[i])
             if !bytes.HasPrefix(discoveries[i].Payload, []byte("croc")) {
                log.Debug("skipping discovery")
                 continue
         }
         log.Debug("switching to local")
         portToUse := string(bytes.TrimPrefix(discoveries[0].Payload, []byte("croc")))
         if portToUse == "" {
               portToUse = "9009"
         }
         address := net.JoinHostPort(discoveries[0].Address, portToUse)
         if tcp.PingServer(address) == nil {
             log.Debugf("succesfully pinged '%s'", address)
             c.Options.RelayAddress = address
             c.ExternalIPConnected = c.Options.RelayAddress
             c.Options.RelayAddress6 = ""
             usingLocal = true
             break
                }
            }
        }
        
        ```

- **BC服务器通信** 部分:
    1. 询问A服务器IP

        ```go
        // ask the sender for their local ips and port
        // and try to connect to them
        c.conn[0].Send([]byte("ips?"))
        data, err = c.conn[0].Receive()
        //...get ips
        
        if len(ips) > 1 {
            port := ips[0]
            ips = ips[1:]
            for _, ip := range ips {
             ipv4Addr, ipv4Net, errNet := net.ParseCIDR(fmt.Sprintf("%s/24", ip))
           log.Debugf("ipv4Add4: %+v, ipv4Net: %+v, err: %+v", ipv4Addr, ipv4Net, errNet)
           localIps, _ := utils.GetLocalIPs()
           haveLocalIP := false
           for _, localIP := range localIps {
               localIPparsed := net.ParseIP(localIP)
               if ipv4Net.Contains(localIPparsed) {
                haveLocalIP = true
                break
               }
           }
           if !haveLocalIP {
             log.Debugf("%s is not a local IP, skipping", ip)
             continue
         }
        
         serverTry := fmt.Sprintf("%s:%s", ip, port)
         conn, banner2, externalIP, errConn := tcp.ConnectToTCPServer(serverTry, c.Options.RelayPassword, c.Options.SharedSecret[:3], 250*time.Millisecond)
         if errConn != nil {
             log.Debugf("could not connect to " + serverTry)
             continue
         }
         log.Debugf("local connection established to %s", serverTry)
         log.Debugf("banner: %s", banner2)
         // reset to the local port
         banner = banner2
         c.Options.RelayAddress = serverTry
         c.ExternalIP = externalIP
         c.conn[0].Close()
         c.conn[0] = nil
         c.conn[0] = conn
         break
            }
        }
        
        ```

### 3.6 `croc` 功能实现: 小总结

- 它使用的默认的中继服务器。 **而在局域网可用的情况下，用的是发送方自己开启的中继服务器。**

    ```bash
    --relay值中继的地址（默认："142.93.177.120:9009"） [$CROC_RELAY]
    --relay6值中继的IPv6地址（默认为："[2604:a880:800:c1::14c:1]:9009"） [$CROC_RELAY6]
    ```

- **`croc` 降低带宽占用** : 识别内网以及IPv6, 在B获取到A的信息之后，若AB是在同一个局域网或者是用IPv6地址，则AB直接建立文件传输连接而不经过C，反则需要经过C中转。
  - **优先内网** : 支持局域网发现，符合网络条件是可以不经过的中转的
    [https://github.com/schollz/croc/blob/d922808fd8bfc97571bdc4ddd6d115d979bc9ba7/src/croc/croc.go#L302](https://github.com/schollz/croc/blob/d922808fd8bfc97571bdc4ddd6d115d979bc9ba7/src/croc/croc.go#L302)[https://github.com/schollz/croc/blob/d922808fd8bfc97571bdc4ddd6d115d979bc9ba7/src/croc/croc.go#L497](https://github.com/schollz/croc/blob/d922808fd8bfc97571bdc4ddd6d115d979bc9ba7/src/croc/croc.go#L497)
  - **为什么要优先IPv6?**: IPv4 在大多数情况下需要使用 NAT 转换，在 `croc` 的设计上是需要使用中转服务器做流量转发的。所以它需要避开IPv4。(至于IPv6的覆盖率，我在后续章节会提到 #I40ONU)
- **`croc` 提升速度** : 如何实现全双工的实时通信层
  - **传输文件的常见方式是首先将数据上传到服务器，然后，一旦上传，就将链接分享给去下载的人。** 这种方法很简单，但速度很慢--文件的传输速度是上传和下载速度的谐波平均值的一半，这使得它比直接上传或下载都要慢。(例如，如果你以5MB/s的速度上传文件，然后在完成上传后，以8MB/s的速度下载，那么有效的传输速率是1/2 * 2/(1/5+1/8) = 3.1MB/s - 这比任何一种都慢。)
  - **更好的方法是使用在两台计算机之间建立一个全双工的实时通信层** ，使 "上传" 和 "下载" 在两台计算机之间同时发生。这有效地提高了传输率，因为它不是连续的，只受限于两种传输模式（上传或下载）中较慢的一种。关键是一边在上传的同时，另外一边可以下载。[2]
- **`croc` 保证安全** : 提供**端到端加密**（使用PAKE，我还没看懂, TODO）

### 3.7 参考资料

- [1] croc 仓库链接: [https://github.com/schollz/croc](https://github.com/schollz/croc)
- [2] croc 设计思路: [https://schollz.com/blog/croc6/](https://schollz.com/blog/croc6/)
- croc 项目的灵感来源: [https://github.com/magic-wormhole/magic-wormhole](https://github.com/magic-wormhole/magic-wormhole)
