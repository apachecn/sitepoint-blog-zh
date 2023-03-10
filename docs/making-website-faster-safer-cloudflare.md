# 使用 Cloudflare 让您的网站更快、更安全

> 原文：<https://www.sitepoint.com/making-website-faster-safer-cloudflare/>

**Cloudflare 是[内容交付领域](https://www.sitepoint.com/what-is-a-cdn-and-how-does-it-work)的行业领导者，为数以百万计的网站减少负载并提高速度。**

![](img/e3f2cc5657b809d9ef266989da3cbe37.png)

这个提供商的特别之处在于，它并不是以加速/性能工具开始的，而是诞生于“蜜罐”项目，该项目被认为是垃圾邮件和黑客攻击防护服务。直到今天，这还是 Cloudflare 的主要卖点之一:DDoS 检测和保护。他们的算法会记录访问者的 IP 地址、有效载荷、请求的资源和请求频率，以检测恶意访问者。

因为它是网站和所有传入流量之间的代理，Cloudflare 能够显著降低服务器的压力，以至于 DDoS 攻击甚至不会到达原始网站，如本简介中的[所述。Cloudflare 还提供 Always Online 选项，该选项会缓存用户网站的一个版本，并在原始服务器中断的情况下(当原始网站返回 5xx 或 4xx 错误时)提供有限的版本。它还具有成熟的页面缓存。](https://www.sitepoint.com/what-is-a-cdn-and-how-does-it-work)

这些特性可能是一个巨大的优势:它们可以在重负载下挽救挣扎中的 web 服务器，并且在服务器出错的情况下，可以给开发人员一些喘息的空间来解决问题。

![Always Online feature](img/4a1605b41a267566262a413cc3ed1dae.png)

它也是免费的。当然，有高级层，也有需要付费的东西(如额外的页面规则)，但 Cloudflare 免费层的范围本身就值得了解它的来龙去脉。

[比较](https://goldfirestudios.com/blog/142/Benchmarking-Top-CDN-Providers) [基准](https://www.cdnperf.com/cdn-provider/cloudflare-cdn)在速度方面，Cloudflare 处于中间位置，但很难说它是市场上最有价值的 CDN。

## 设置 Cloudflare

使用 Cloudflare 设置站点非常简单。在(cloud flare . com)[http://www . cloud flare . com]注册后，我们可以添加一个新的网站。当系统扫描给定域名的 IP 地址和其他详细信息时，我们会看到一段介绍视频。完成后，我们将获得新的域名服务器，以便与我们的注册服务商建立联系。

![Adding the website to Cloudflare](img/d510a20a68388c5d783fd45fd04398e0.png)

我们需要向我们的注册机构注册这些域名服务器，并等待更改在互联网上传播。可能需要 24 小时。

这一变化意味着将我们领域的所有控制权交给 Cloudflare。这也意味着，如果我们在该域上有电子邮件(MX 记录)，我们需要将这些记录传输到 Cloudflare。如果我们有任何子域，它们也需要在 Cloudflare 的仪表板中设置为相应的 A 记录。

**我们的域名注册商或托管提供商设置的所有现有域名记录都需要移动/复制到 Cloudflare。**

一些托管服务提供商可能会进一步简化/自动化这一过渡过程。

![Cloudflare DNS dashboard](img/aba4a0957657415b6d54f19a515df04a.png)

对于我们的每个域记录，我们可以决定让所有流量直接通过我们的服务器，这意味着我们可以为某些子域设置例外，或者我们可以关闭所有 Cloudflare 功能，例如，当我们在网站上进行一些更改时。

![switching CDN on](img/57d011da3738dc4b87fc23be2fdf215c.png)

一旦我们设置了域，这基本上就是 Cloudflare 仪表盘之外所需的所有工作。网站本身，或者源服务器就没什么可做的了。所有进一步的调整都在 Cloudflare 网站上完成。

## 设置加密

SSL 证书是 Cloudflare 免费计划的一部分。SSL 设置有四个选项，我们可以在仪表板中的 **Crypto** 选项卡下找到它们。

*   **OFF**–这是不言自明的。所有流量都将被重定向到不安全的协议(http)
*   **灵活**–不管我们的服务器采用何种协议，也不管我们的服务器上是否有现有的 SSL 证书，Cloudflare 都会通过 **https** 将我们的所有页面提供给最终访问者。从 Cloudflare 到源服务器的连接将通过不安全的连接进行。
*   **FULL**—cloud flare 将通过 https 与您的服务器通信，但不会在源上验证证书。从 Cloudflare 到访问者的流量通过 https 提供。
*   **完全严格**–cloud flare 将要求源服务器上的有效(非自签名)SSL 证书。从 Cloudflare 到访问者的流量通过 https 提供。

有了这些设置，我们需要确保设置是合理的，因为我们在最终用户和服务器内容之间有两层，所以这里的省略会导致重定向循环，或者太多的重定向会降低网站的速度。

Cloudflare 还提供了购买自定义证书的选项，对于需要额外安全或关心其市场形象的高级用户，它提供了上传自定义/高级证书的选项。这是保费计划的一部分。

## 保护网站

这是 Cloudflare 的一个亮点:它免费提供了前所未有的价值。在整个主机领域，DDoS 保护是一项高级服务，即使是付费用户也不总是能获得。

Cloudflare 在免费层提供未计量的 DDoS 保护，以及一些其他相当复杂的工具，在恶意流量到达网站之前在基础架构级别保护网站。它提供*——根据用户定义的、可定制的规则对访问
进行节流。它提供了智能防火墙规则，国家块，浏览器完整性检查，验证码保护，等等。*

 *今天，当僵尸网络统治互联网，新安装的网站或服务器有时会在上线几分钟内淹没在暴力入侵尝试中，当垃圾邮件发送者自动化网络评论，[推荐垃圾邮件](https://www.wikiwand.com/en/Referrer_spam)即使没有任何入侵也很猖獗，[帖子攻击](https://www.digitalocean.com/community/questions/how-do-i-stop-brute-force-attacks-against-xmlrpc-php-from-crashing-my-wordpress-server)和利用非正统手段的缓慢攻击[并不罕见。这种保护可以成就或摧毁中小型网站。](https://security.radware.com/ddos-knowledge-center/ddospedia/tors-hammer/)

还有刮保护，拒绝某些访问者配置文件的某些资源，或混淆电子邮件。

高级层级提供了更多选项。

## 速度和性能

默认情况下，使用 Cloudflare，我们可以缓存静态内容— JavaScript、字体、样式表，甚至图像。我们可以设置缓存过期的规则。对于各种 CMS 系统，有一些插件可以连接到 Cloudflare，以便我们可以从我们的网站中清除缓存。

这些静态资源由 Cloudflare 的基础架构在 DNS 设置时“接管”,然后根据访问者的位置从其遍布全球的数据中心网络提供服务。

资源可以被缓存(或从源获取),这取决于查询字符串，或由我们的源服务器设置的缓存头。例如，在 PHP 中，我们可以这样设置缓存过期时间:

```
header('Cache-Control: max-age=3600'); 
```

如果这还不够，我们可能需要删除*杂注*、*过期*和*缓存控制*头:

```
header_remove('Pragma');
header_remove('Expires');
header_remove('Cache-Control'); 
```

我们也可以在服务器级别上这样做。在 nginx 中，我们将在虚拟主机块/文件中设置这些规则:

```
if ($http_cookie ~* "some-cookie") {
    #removing headers that stop Cloudflare cloudflare from caching
    more_clear_headers 'Pragma';
    more_clear_headers 'Expires';
    more_clear_headers 'Cache-Control';
    add_header Cache-Control "no-cache, no-store, max-age=0";
}

location ~* "(!?.*some-url-path.*)" {
    #removing headers that stop Cloudflare cloudflare from caching
    more_clear_headers 'Pragma';
    more_clear_headers 'Expires';
    more_clear_headers 'Cache-Control';
    add_header Cache-Control "max-age=3600";
} 
```

Apache 用户会在他们的`.htaccess`文件中放置类似这样的内容:

```
<IfModule mod_headers.c>
Header unset Pragma env=LONGCACHE
Header unset Expires env=LONGCACHE
Header set Cache-Control "max-age=3600" env=LONGCACHE
</IfModule> 
```

显然，上面的 *some-cookie* 和 *some-url-path* 需要用合适的值替换。**页面缓存也需要这些规则，下面会提到。**

我们可以选择缩小我们的 CSS，JS 和 HTML。有一个选项可以利用 [AMP](https://www.ampproject.org/) 进行输出 AMP 链接。还有一个 *beta* 特性 **Rocket Loader** 异步捆绑和服务 JS，使用浏览器的本地存储来缓存它。但是要小心，因为资源加载顺序上的小错误会破坏你的网站。

付费用户可以使用 Railgun 特性，这解决了缓存系统的主要问题——动态内容。它试图加速来自源服务器的动态内容交付。它需要在原始服务器上安装软件，并通过压缩交付的内容和“*为多个同时请求保持单个持久连接，消除网络连接延迟*”来工作。这让人想起了 HTTP/2 协议的[多路复用特性——只是在这里，在源服务器和 Cloudflare 之间。](http://qnimate.com/what-is-multiplexing-in-http2/)

还有图像压缩/优化的专业计划，在波兰和幻影机制。在 **Traffic** 选项卡下，有跨越 Cloudflare 全球数据中心的 Argo 路线优化和负载平衡选项。

### 整页 HTML 缓存

全页面缓存意味着缓存我们系统的全部输出——CMS、应用程序、博客系统或其他任何东西。

在**页面规则**下，有一颗隐藏的宝石可以将你的 Cloudflare 帐户转变为一个成熟的缓存系统，可以与 [Varnish](https://varnish-cache.org/) 等专业解决方案竞争，并且——由于遍布全球的数据中心——可能会超过它。

![Full page caching setup](img/1b34754a12fd4671ce87c65d8edf9d0c.png)

设置为*缓存所有内容*的**缓存级别**规则将缓存网站的整个 HTML 内容，以及所有其他资源。

通过对这些规则进行排序，我们可以实现复杂的缓存/排除解决方案，甚至以最小的延迟交付动态生成的内容。第一个字节的时间提高了 15 倍( [TTFB](https://www.wikiwand.com/en/Time_to_first_byte) ) [也不是没有听说过](https://guides.wp-bullet.com/cloudflare-cache-wordpress-posts-and-pages-guide/)。

这些规则支持通配符，并且需要按优先级排序:

```
*some-website.com/static/*
[/static/ subdirectory for static HTML pages]

*some-website.com/*.shtml
[.shtml file extension to signify HTML that is static]

*some-website.com/*?*static=true*
[adding static=true query parameter] 
```

如果我们将此规则与 **Bypass** 设置结合起来，如下图所示，我们可以在不同的条件下将需要缓存/从缓存中忽略的网站部分分开——例如管理部分等。

![Cache bypass](img/4984365d05bbb6b3f1cff2890e6557f7.png)

如果页面缓存正在工作，我们应该在浏览器的检查器中的响应标题中看到 *CF-Cache-Status: HIT* 。

付费用户可以根据 cookie 选择缓存或绕过缓存。这里，cookie 名称可以用通配符设置，内容也可以根据设备类型进行缓存。

这些规则更加复杂，并不是所有类型的网站都需要这些规则。免费用户有三个页面规则可用，但可以购买额外的规则。

### HTTP/2 和服务器推送

![Browser inspector shows parallel, asynchronous loading of resources on HTTP/2 protocol](img/860af8841fb938592dba92eadf11cbbc.png)

浏览器检查器显示 HTTP/2 协议上的并行、异步资源加载

默认情况下，免费用户和企业用户会打开 HTTP/2，这是对 HTTP/1.1 协议的一大改进，包括:

*   **多路复用**–可以在单个连接中以异步方式发送多个资源
*   **报头压缩**
*   **服务器推送**–服务器预测对资源的请求，并在浏览器发出请求之前就发送出去。

虽然认识到需要根据具体情况来判断 Cloudflare 的改进和价值，但公平地说，总体而言，Cloudflare 提供的价值(尤其是其免费层)远远高于平均水平。

## 结论

本文介绍了如何在一个网站上使用和配置 Cloudflare，以使其快速、可靠和防 DDoS 攻击。这并不是说一个 CDNed 网站是不可渗透的。事实上，它带来了新的漏洞。

当人为错误导致 Cloudflare 服务在 2016 年瑞典 Telia 网络提供商的案例中遭受损失时，这是欧洲互联网的一个主要问题。据估计，全球排名前 100 万的网站中有 6.2%使用 Cloudflare 服务。

![In 2016, Telia's misconfigured router allegedly sent most of Europe's traffic to Asia](img/0b82f8d212653011f3999e9c31b07d1e.png)

来自 [Cloudflare 关于 Telia 中断的事后报告](https://blog.cloudflare.com/a-post-mortem-on-this-mornings-incident/)的信息图，当时一个错误配置的路由器将欧洲网络的大部分流量重新路由到亚洲，导致数据包丢失。

无论如何，我们不应该因为这样的事件而害怕 cdn。它们数量很少，而且相距甚远，最终有助于构建一个更加强大的网络。

## 分享这篇文章*