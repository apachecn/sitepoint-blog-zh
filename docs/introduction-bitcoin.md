# 比特币简介

> 原文：<https://www.sitepoint.com/introduction-bitcoin/>

![bitcoin](img/2661533e6a80968fde43421a599275de.png)

比特币是一种“数字货币”，最近受到了相当多的关注。它从哪里来，它的价值是如何确定的？用比特币支付/接收款项是一种可接受的方式吗？

比特币是一种开源的、点对点的虚拟货币，纯粹以数字形式存在。它是第一个分散的点对点支付网络，由用户驱动，没有中央机构或中间人。

## 谁创造的？

比特币的概念是由中本聪引入并发展的。2008 年 11 月，一篇题为“比特币:一种点对点电子现金系统”的论文发表在邮件列表中。这篇论文描述了使用对等网络来生成被描述为“不依赖于信任的电子交易系统”的方法。

2009 年 1 月，随着第一个开源比特币客户端的发布，比特币网络应运而生。Satoshi 在 2010 年末离开了这个项目，没有透露太多关于他自己的信息。此后，该社区呈指数级增长，许多新开发人员致力于比特币协议。

## 比特币是如何运作的？

比特币是一种允许匿名货币交易的系统。没有人会知道该支付或关于该支付的所有其他信息，包括谁发送的、谁接收的等等。

比特币网络——一个点对点网络——不是由中央权威机构控制，而是由贡献者和自由爱好者组成的网络控制。从本质上讲，人们可以进行金钱交易，没有任何机构或组织会知道这件事。

## 它从哪里来？

一种货币要有价值，硬币的数量必须是有限的。当为 [Satoshi 的数学问题](http://www.businessinsider.com/heres-the-math-problem-that-made-alleged-bitcoin-inventor-shinichi-mochizuki-a-mathematical-rock-star-2013-5 "Satoshi's math problem (maybe?)")找到一组新的解决方案时，一个新的比特币就产生了。

使用计算机为 Satoshi 的数学问题找到新解决方案的过程通常被称为“比特币采矿”。新硬币是通过遵循一套双方同意的规则慢慢开采出来的。一个挖掘比特币的用户正在运行一个软件程序，该程序不知疲倦地搜索一个非常困难的数学问题的解决方案，这个问题的难度是精确已知的。

这个数学问题的一个有趣特征是，随着每个连续的解(比特币)被找到，下一个解变得越来越难找到。

## 优点和缺点

比特币相对于传统货币的优势在于:

*   **方便快捷的支付**:比特币支付一般都很方便快捷。比特币用户只需要知道他打算将硬币转移到的地址。交易将在几秒钟内完成。

*   **安全**:由于比特币协议使用了高度安全的加密技术，它确保了交易的安全性。只能从钱包中授权交易。然而，钱包的安全性取决于用户和他们采取的保护措施。

*   **匿名交易**:比特币用户和比特币钱包的身份是私有的。
    为比特币交易创建的地址也是私有的。然而，比特币交易是公开的、可追踪的，并且永久存储在比特币网络上。比特币网络上用户的 IP 地址也会被记录下来。因此，如果你想匿名的话，建议你使用不同的 IP 地址或者隐藏你的电脑的 IP 地址。

*   **低交易费或无交易费**:比特币交易费通常是一种自愿性的费用，以便更快地确认您的交易。

比特币也有它的缺点:

*   **不可逆支付**:使用比特币的交易通常是不可逆的，因为没有中央机构监控这些交易的欺诈和逆转。获得退款的唯一方法是收到资金的人退款。

*   **接受度**:作为一种新的货币形式，比特币的接受度是有限的。并非所有(甚至很多)网商都接受比特币。

*   **学习曲线**:使用比特币进行支付需要用户获得一些关于使用比特币的知识，尤其是关于保持比特币钱包安全的知识。这包括知道如何创建钱包、保证钱包的安全、保证钱包所有者身份的私密性和安全性、对不同的交易使用不同的地址等等。

*   实验性货币:由于比特币仍处于开发阶段，没人能确定它的未来。系统中可能存在尚未发现的技术缺陷或漏洞。

*   **波动**:由于没有一个中央权力机构可以控制比特币的价值，它的价值可能会非常波动。

考虑到上述利弊，应该由用户来决定他们是否应该使用比特币。在使用它之前，学习如何正确使用它以及如何保持安全是明智的。

## 在 Ruby 中，比特币是一种支付方式

比特币网络可以通过 JSON-RPC 访问。在[比特币维基](https://en.bitcoin.it/wiki/API_reference_(JSON-RPC)中给出的以下代码，提供了如何连接到比特币网络并执行一些基本请求的详细信息。

```
require 'net/http'
require 'uri'
require 'json'

class BitcoinRPC
  def initialize(service_url)
    @uri = URI.parse(service_url)
  end

  def method_missing(name, *args)
    post_body = { 'method' => name, 'params' => args, 'id' => 'jsonrpc' }.to_json
    resp = JSON.parse( http_post_request(post_body) )
    raise JSONRPCError, resp['error'] if resp['error']
    resp['result']
  end

  def http_post_request(post_body)
    http    = Net::HTTP.new(@uri.host, @uri.port)
    request = Net::HTTP::Post.new(@uri.request_uri)
    request.basic_auth @uri.user, @uri.password
    request.content_type = 'application/json'
    request.body = post_body
    http.request(request).body
  end

  class JSONRPCError < RuntimeError; end
end

if $0 == __FILE__
  h = BitcoinRPC.new('http://user:password@127.0.0.1:8332')
  p h.getbalance
  p h.getinfo
  p h.getnewaddress
  p h.dumpprivkey( h.getnewaddress )
end
```

API 调用的完整列表在这里描述

## 结论

围绕比特币有很多传言，一些国家禁止使用比特币，有人试图对比特币交易所进行黑客攻击。与此同时，比特币为接管基于互联网的金融交易提供了广泛的可能性，为消费者和卖家提供了大量的价值和机会。

## 分享这篇文章