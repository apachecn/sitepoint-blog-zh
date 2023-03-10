# 如何在 Node.js 中使用 SSL/TLS

> 原文：<https://www.sitepoint.com/how-to-use-ssltls-with-node-js/>

在 2020 年，你的网站没有理由不使用 HTTPS。访问者期待它，谷歌使用它作为排名因素，浏览器制造商将高兴地命名和羞辱那些不使用它的网站。

在本教程中，我将通过一个实际的例子向您展示如何向 Express.js 服务器添加一个由 [Let's Encrypt](https://letsencrypt.org/) 生成的证书。

但是用 HTTPS 保护我们的网站和应用是不够的。我们还应该要求与我们对话的服务器进行加密连接。我们将看到，即使 SSL/TLS 层在默认情况下没有启用，也有可能激活它。

*注意:如果你正在寻找如何使用 NGINX 设置 SSL 的指导，当配置它作为一个节点应用的反向代理时，请查看我们的快速提示，“[使用 Node.js 配置 NGINX 和 SSL](https://www.sitepoint.com/configuring-nginx-ssl-node-js/)”。*

让我们先简单回顾一下 HTTPS 的现状。

## HTTPS 无处不在

[HTTP/2 规范](https://en.wikipedia.org/wiki/HTTP/2)于 2015 年 5 月发布为 RFC 7540，这意味着它现在是标准的一部分。这是一个重要的里程碑。现在我们都可以升级我们的服务器来使用 HTTP/2。最重要的方面之一是向后兼容 HTTP 1.1 和选择不同协议的协商机制。尽管该标准没有规定强制加密，但目前没有浏览器支持 HTTP/2 不加密。这给了 HTTPS 另一个动力。最终我们会让 HTTPS 无处不在！

我们的筹码实际上是什么样的？从运行在浏览器中的网站的角度来看(在应用程序级别)，我们必须穿过以下层才能到达 IP 级别:

1.  客户端浏览器
2.  超文本传送协议
3.  SSL/TLS
4.  传输控制协议（Transmission Control Protocol）
5.  互联网协议(Internet Protocol)

HTTPS 只不过是 SSL/TLS 之上的 HTTP 协议。因此，HTTP 的所有规则仍然适用。这额外的一层实际上给了我们什么？有多重优势:我们通过拥有密钥和证书获得认证；由于连接是以非对称方式加密的，因此保证了某种隐私和机密性；数据完整性也得到了保护，因为传输的数据在传输过程中不会被更改。

最常见的误解之一是使用 SSL/TLS 计算量很大，会降低服务器的速度。这当然不再是真的了。我们也不需要任何带有加密单元的专用硬件。甚至对于 Google 来说，[SSL/TLS 层只占不到 1%的 CPU 负载，与 HTTP 相比，HTTPS 的网络开销低于 2%](https://www.imperialviolet.org/2010/06/25/overclocking-ssl.html) 。总而言之，为了一点开销而放弃 HTTPS 是没有意义的。

正如伊利亚·格里戈利克所说，只有一个性能问题:

> TLS 恰恰有一个性能问题:它没有得到足够广泛的使用。其他一切都可以优化:[https://t.co/1kH8qh89Eg](https://t.co/1kH8qh89Eg)
> 
> —伊利亚·格里戈利克(@ igrigorik)[2014 年 2 月 20 日](https://twitter.com/igrigorik/status/436618170397646848?ref_src=twsrc%5Etfw)