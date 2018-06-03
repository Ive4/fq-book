# ReQrypt与BarbaTunnel

## reqrypt简介

> 原文：[basil00/reqrypt](https://github.com/basil00/reqrypt) 编译：谷歌翻译

ReQrypt是一种隧道和加密Web浏览器请求的工具，可以将它们从本地（例如路由器级别或ISP级别）窥探和拦截中隐藏起来。ReQrypt可用于绕过ISP级别的URL审查/过滤/日志记录系统。

ReQrypt的工作方式与其他旁路方法完全不同。ReQrypt基于称为“三角路由”的技术。简而言之，ReQrypt的工作原理是这样的：

```text
(1) Tunneled request      +----------------+      (2) Forwarded request
           +------------->| ReQrypt server |-------------+
           |              +----------------+             |
           |                                             |
           |                                             V
   +----------------+                             +----------------+
   | PC web browser |<----------------------------|   Web server   |
   +----------------+                             +----------------+
                        (3) Web-page response
```

基本上：

1. 您的Web浏览器向Web服务器发出一个HTTP请求，该请求被加密并隧道传输到ReQrypt服务器。
2. ReQrypt服务器解密隧道数据包，并将其转发给网络服务器。
3. Web服务器响应HTTP请求，就好像它直接来自您的计算机，并且网页响应通过正常路由发回。

通常，HTTP请求直接发送到Web服务器，未加密。这意味着它可能会被本地窃听者（如您的ISP，工作场所或共享家庭路由器）读取和/或拦截。但是，使用ReQrypt时，传出的HTTP请求会被加密并隧道传输，使得它们无法被任何本地窃听者读取。

对于只攻击出站HTTP请求并忽略入站HTTP响应的系统，ReQrypt是有效的。这种系统非常常见的是ISP级别的审查和日志记录系统，因为处理URL流量（HTTP请求）比处理网页响应要容易得多。

最后，与代理，VPN，Tor等不同，ReQrypt不是一个匿名工具。它不会更改隧道数据包的IP地址。这可能是一件好事：它意味着Web响应直接发送到您的PC，这意味着ReQrypt通常比其他系统更快。

> 图表来源于[reqrypt官网](https://reqrypt.org/reqrypt.html)

| Filter/Proxy | ReQrypt? | Explanation |
| --- | --- | --- |
| DNS poisoning | ✗ | ReQrypt is overkill for DNS poisoning. Instead reconfigure your system to use a different DNS server, such as [Google Public DNS](https://developers.google.com/speed/public-dns/). |
| DNS blocking | ✓ | This occurs when your provider blocks access to alternative DNS servers. In this case reconfigure your system to use a different DNS server then run ReQrypt with Hiding modeset to "Off" and "DNS Hiding" enabled. |
| IP address blocking | ✓ | Provider blocks certain IP addresses. Use ReQrypt with "Active-Highest" mode enabled. |
| Proxy-ing | ✓ | Provider forces all HTTP traffic through proxy servers. In some cases ReQrypt can completely bypass the proxy servers with "Active-Highest" mode enabled. |
| Hybrid proxy-ing | ✓ | Provider forces some HTTP traffic through proxy servers based on IP address. As above use ReQrypt with "Active-Highest" mode enabled. |
| Hybrid filtering | ✓ | Provider blocks access to certain URLs at the Network level using Deep Packet Inspection \(DPI\). For Network-level filtering only the URLs need to be hidden. Use ReQrypt with "Passive", "Active-Low" or "Active-Medium" modes enabled. |
| Mirror filtering | ✓ | This is similar to hybrid filtering except the provider copies \(mirrors\) rather than selectively redirects packets. As above, use ReQrypt with "Passive", "Active-Low" or "Active-Medium" modes enabled. |
| URL sniffing | ✓ | Provider is sniffing packets to extract URLs for logging or data-retention purposes. User ReQrypt with "Active-Medium" mode enabled. |
| Strong filtering | ✗ | The various strong filtering methods used in countries like China or Iran. ReQrypt is generally ineffective against such systems since they typically block or severely restrict inbound traffic. |

## 关于不支持中国的问题

> 来自：ReQrypt项目作者对[issues\#15](https://github.com/basil00/reqrypt/issues/15)的解释说明 编译：谷歌翻译

ReQrypt是一种特殊的技术（单向隧道技术），而且这项技术（大部分）对检测入境流量的“强大”过滤系统（例如在中国和伊朗部署的那些过滤系统）无效。在大多数其他地方，由于几个不同的原因，检查入站流量在ISP级别是不常见的，即：跟踪TCP状态的成本，Web响应流量高，Web响应流量中没有URL，以及基于IP的愚蠢方法大规模过度阻塞。但在中国和伊朗等地，他们愿意为此付出代价。

也就是说，我听说过一些传闻，ReQrypt确实对中国的_一些_ ISP有效。

伊朗做了一些额外的伎俩。据报道，除了审查HTTP之外，还会限制非HTTP流量以阻止规避工具。像[BarbaTunnel](https://github.com/BarbaTunnelCoder/BarbaTunnel/)这样的项目通过HTTP隧道传输以获得全速。

要通过双向隧道强制所有流量，这对ReQrypt来说将是一个不同的项目。这更像是[Tallow](https://github.com/basil00/TorWall)所做的，它是一种通过Tor网络强制所有流量的工具。Tallow可能适用于其他类型的隧道，特别是支持SOCKs5的任何东西。

## BarbaTunnel

> 原文：[BarbaTunnelCoder/BarbaTunnel](https://github.com/BarbaTunnelCoder/BarbaTunnel/) 编译：谷歌翻译

BarbaTunnel是一款可以帮助您绕开防火墙和互联网审查的软件，它是一个点对点隧道，因此您需要防火墙网络之外的服务器。

在大多数情况下，您可以简单地使用VPN或任何代理服务器，但是当您使用VPN时，防火墙知道您使用VPN，它不知道您在做什么，VPN和其他代理服务器不会隐藏指纹。BarbaTunnel是您的网络上的一个层，并尝试使现有的VPN数据包查看传统数据包。实际上BarbaTunnel不能单独使用，它可以与VPN协同工作。

因此，如果您拥有VPN并且您没有任何问题，则不需要BarbaTunnel，但如果防火墙阻止您的VPN连接或防止VPN连接速度降低，则BarbaTunnel可能对您有所帮助。

注意： BarbaTunnel不是独立的隧道，您应该在运行BarbaTunnel后运行隧道应用程序或使用标准的VPN连接。

![](https://raw.githubusercontent.com/loremwalker/fq-book/master/docs/images/home_barbatunneldiagram.png)

### 我对reqrypt与BarbaTunnel的看法

目前，reqrypt由于软件原理构造无法应付国内墙大的处理机制，该软件在国内已经没有使用的必要了；而barbaTunnel多年没更新了，加上一些VPN自身方面连接的问题，使得这款软件不怎么好用，我测试了一下，也并不是很满意，直白一点就是不成功。

所以，以上，这些就当做了解好了
