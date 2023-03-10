# 使用 ipdata 的地理位置数据来保护和定制您的站点

> 原文：<https://www.sitepoint.com/use-ipdatas-geolocation-data-to-protect-customize-your-site/>

*本文是与 [ipdata](https://synd.co/39JMDgE) 合作创作的。感谢您对使 SitePoint 成为可能的合作伙伴的支持。*

现代网站在根据访问者的位置定制内容方面变得越来越有效。它们可以用用户自己的语言将用户重定向到一个页面，以当地货币显示价格，用位置信息预先填充 web 表单，并显示正确时区的当前时间和日期。

[ipdata](https://synd.co/39JMDgE) 是一个低延迟 API，它根据 IP 地址(IPv4 和 IPv6)为网站所有者提供有关其访问者的各种信息。可以把它想象成一个 IP 地理定位和威胁情报 API。

通过使用访问者的 IP 地址，你可以了解他们的洲，国家，地区，城市，纬度和经度，组织或 ISP，以及时区。该 API 还检测代理和 Tor 用户，以及已知的垃圾邮件发送者和不良机器人。阻止这些风险将保护您的网站，并减少对验证码等安全策略的需求。

让我们具体看看 ipdata 可以提供帮助的一些方式，以及如何在自己的网站上实现它们。

### 重定向访问者和本地化内容

当您访问 [ipdata](https://synd.co/39JMDgE) 网站时，您会立即看到这项服务的强大功能。所有能从你自己的 IP 地址得知的信息都会显示出来。

![ipdata](img/4646d2eb3bcfb5a4ff07982f74fe0b26.png)

这些数据包括:

*   不管你是在欧盟，
*   你的城市，
*   州或地区(和地区代码)，
*   国家(和国家代码)，
*   洲(和洲代码)，
*   纬度和经度，
*   邮政编码，
*   国家呼叫代码，
*   你国家的国旗表情符号，
*   您的服务提供商的 ASN 和运营商信息，
*   语言，
*   货币(名称、代码、符号、复数)，
*   时区(名称和缩写、时差、夏令时、当前时间)，
*   威胁信息(Tor、代理、匿名、已知攻击者、已知滥用者、威胁、bogon)。

您可以在每个页面请求上调用 ipdata 的 API，以地理定位您的访问者并本地化他们的内容。这里有一些你可以实现的想法:

*   限制或阻止特定国家或大陆访问您的内容，
*   将用户重定向到特定国家(或特定语言)的站点或页面，
*   用它们的位置数据预先填充你的网络表单，
*   向你的访问者显示他们当地的时间和天气，
*   显示您的访问者附近的活动，或他们所在地区的可用航班，
*   提供基于位置的定向广告，
*   强制遵守 GDPR 法规，
*   使用正确的货币符号，自动将电子商务商店上的价格转换为当地货币，
*   更精准的分析你的流量来自哪里。

你可以使用 JavaScript 获得一个客户端的 IP 地址[，但是这有点麻烦。而是使用 ipdata 的 API。它在所有浏览器中都是超级快速和可靠的。代码如下:](https://docs.ipdata.co/use-cases/how-to-get-a-clients-ip-address-using-javascript)

```
 $.get("https://api.ipdata.co?api-key=test", function(response) {
        console.log(response.ip);
    }, "jsonp"); 
```

一旦你有了访问者的 API 地址，ipdata 的[文档](https://docs.ipdata.co/use-cases/get-the-country-location-from-an-ip-address)会用 26 种不同的语言告诉你如何获得他们的位置。您还可以找到关于如何针对各种用例进行编码的详细教程。这里有几个例子。

要按国家阻止(或允许)用户，请查找 [ISO 3166 ALPHA-2 国家代码](https://en.wikipedia.org/wiki/List_of_ISO_3166_country_codes)以获得您想要列入黑名单或白名单的用户。然后按照这个示例代码学习如何将它们列入黑名单或白名单。

```
 // List of countries we want to block
    // To see this in action add your country code to the array
    var blacklist = ['US', 'CA', 'UK', 'IN']

    // Getting the country code from the user's IP
    $.get("https://api.ipdata.co?api-key=test", function (response) {

      // Checking if the user's country code is in the blacklist
      // You could inverse the logic here to use a whitelist instead
      if (blacklist.includes(response.country_code)) {
        alert('This content is not available at your location.');
      } else {
        alert("You're allowed to see this!")
      }
    }, "jsonp"); 
```

**如果你有特定国家的在线商店，或者你有一个单独的页面，内容以他们的语言或特定国家的联系方式，按国家重定向用户**是有用的。

这里有一个例子，如何重定向您的访客位于德国和澳大利亚。他们将从 https://uk.store.ipdata.co 转向 https://de.store.ipdata.co 和 https://au.store.ipdata.co。

```
 // Getting the country code from the user's IP
    $.get("https://api.ipdata.co?api-key=test", function (response) {
      if (response.country_code == 'UK') {
        window.location.href = "https://uk.store.ipdata.co";
        } else if (response.country_code == 'DE') {
        window.location.href = "https://de.store.ipdata.co";
        } else if (response.country_code == 'AU') {
        window.location.href = "https://au.store.ipdata.co";
        }
    }, "jsonp"); 
```

您还可以根据用户的位置**个性化您网站的内容**。以下是一个仅向英国游客展示特惠信息的示例:

```
 // Getting the country name from the user's IP
    $.get("https://api.ipdata.co?api-key=test", function (response) {
      if (response.country_code == 'UK') {
        alert("Special offer for all our users from " +response.country_name+ "!");
        }
    }, "jsonp"); 
```

您可以深入到地区、城市或邮政编码，而不是针对整个国家。或者，你可以瞄准一个时区或特定的货币。

您可以通过显示用户的本地时间(根据 DST 调整)和本地货币符号来进一步个性化您的内容。要请求 IP 地址“3.3.3.3”的时区数据:

```
 $ curl https://api.ipdata.co/3.3.3.3/time_zone?api-key=test 
```

您将收到以下响应，其中包括时区的名称和缩写、UTC 偏移量、当前是否为 DST 以及当地时间:

```
 {
        "name": "America/Los_Angeles",
        "abbr": "PDT",
        "offset": "-0700",
        "is_dst": true,
        "current_time": "2019-03-27T01:13:48.930025-07:00"
    } 
```

货币检测也类似。以下是 IP 地址“203.100.0.51”的示例:

```
 curl https://api.ipdata.co/203.100.0.51/currency?api-key=test 
```

回应是:

```
 {
        "name": "Australian Dollar",
        "code": "AUD",
        "symbol": "AU$",
        "native": "$",
        "plural": "Australian dollars"
    } 
```

### 保护您的网站免受威胁

您还可以使用 ipdata 来识别针对您网站的潜在威胁。他们维护着超过 6 亿个恶意 IP 地址、开放代理、Tor 节点、垃圾邮件发送者、僵尸网络和攻击者的数据库。这些数据仅从高质量的权威来源汇总而来。您可以通过多种方式使用这些信息:

*   通过阻止已知的垃圾邮件发送者和不良机器人来保护您的评论，减少对验证码的需求，
*   通过确定他们的信用卡是否来自不同于他们所在的国家，
*   拦截匿名流量以消除来自此类网络的风险，
*   阻止高风险国家，例如您的大多数恶意软件和攻击源自的国家，
*   通过检测代理和 Tor 用户来防止“免费试用滥用”。

以下是访问 IP 地址“103.76.180.54”的威胁数据的方法:

```
 curl https://api.ipdata.co/103.76.180.54/threat?api-key=test 
```

该请求生成以下响应:

```
 {
        "is_tor": true,
        "is_proxy": false,
        "is_anonymous": true,
        "is_known_attacker": false,
        "is_known_abuser": false,
        "is_threat": false,
        "is_bogon": false
    } 
```

访问者正在使用 tor 网络。如果访问者是 tor 或代理用户，则为 true。你可以使用 ipdata 来**阻止匿名用户创建账户**。以下是来自官方文档的一些示例代码:

```
 // Getting the anonymity status from the user's IP
    $.get("https://api.ipdata.co?api-key=test", function (response) {
      if (response.threat.is_anonymous) {
        alert("You are not allowed to create an account.");
        }
    }, "jsonp"); 
```

您可以获得更具体的信息，例如，阻止代理用户，但允许 Tor 用户通过:

```
 // Getting the anonymity status from the user's IP
    $.get("https://api.ipdata.co?api-key=test", function (response) {
      if (response.threat.is_proxy) {
        alert("You are not allowed to create an account.");
        }
    }, "jsonp"); 
```

一些用户是惯犯，被其他网站的管理员多次举报垃圾邮件或恶意活动。如果以下字段之一为真，您可以通过阻止他们来阻止他们创建帐户:

*   `is_known_abuser`:被举报为垃圾邮件来源的 IP 地址，
*   `is_known_attacker`:已被报告为恶意活动来源的 IP。

### 为什么选择 ipdata？

与其他 IP 地理定位 API 相比，ipdata 更具优势。它是用 Python 3 编写的，平均执行时间为 2.9 毫秒。它的速度和可靠性足以让一长串客户满意，包括 Comcast、Redhat、Cooperpress、Sphero、AMD 和 NASA。

ipdata 高度可扩展，全球延迟低。该 API 每天以大约 65 毫秒的平均速度处理数百万个请求，并在全球 11 个数据中心运行:

*   4 在美国，
*   1 在加拿大，
*   2 个在欧洲(伦敦和法兰克福)，
*   1 个在印度(孟买)，
*   1 个在南美(圣保罗)，
*   1 个在欧洲(Seol)，以及
*   1 在澳大利亚(悉尼)。

根据 ipdata 的创始人 Jonathan Kosgei 的说法，通过在应用程序代码中不进行任何数据库读取或写入，执行时间可以保持在较低水平。“一个独立的授权器功能处理从 DynamoDB 获取使用数据，并根据用户是否在其配额内来授权用户。并且其结果被缓存。”

### 开始用 ipdata 定位你的访客

到现在为止，我相信你已经想出了十几种方法来使用 ipdata 来增强和保护你的网站，或者你的客户的网站。[免费报名](https://ipdata.co/sign-up.html)开始测试吧！

您可以在以下文章中了解有关 ipdata 基础架构的更多信息:

*   【ipdata 如何使用 AWS 服务于全球高度可扩展的 IP 地理定位 API (AWS Startups 博客)——更详细地介绍了 AWS，
*   【Ipdata 如何以每月 150 美元的价格为来自 10 个可无限扩展的全球端点的 2500 万次 API 调用提供服务(高可扩展性)——详细描述了他如何通过选择新的技术堆栈来应对黑色星期五的失败。
*   查看 ipdata 与所有顶级 IP 地理定位 API 的[对比。](https://medium.com/@ipdata_co/what-is-the-best-commercial-ip-geolocation-api-d8195cda7027)

## 分享这篇文章