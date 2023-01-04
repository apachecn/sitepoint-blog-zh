# 拥有明亮数据的复杂网络抓取

> 原文：<https://www.sitepoint.com/bright-data-web-scraping/>

对 REST 或 GraphQL APIs 提供的结构化数据进行请求很容易。从任何网页中抓取任意数据更麻烦，但它带来了更多的机会。光明数据提供服务，使刮更容易，可靠，实用。

*我们与[光明数据](https://brightdata.grsm.io/a9of7qgqscqg)合作撰写了这篇文章。感谢您对使 SitePoint 成为可能的合作伙伴的支持。*

抓取数据是 web 开发人员的超能力，它让你超越了普通 web 用户的能力。是想找最便宜的航班，最优惠的酒店房间，还是最后剩下的次世代游戏主机？凡人用户必须定期手动搜索，他们需要很大的运气才能买到便宜货。但是网络抓取可以让你自动完成这个过程。一个机器人可以每隔几秒钟抓取数据，当超过阈值时提醒你，甚至以你的名义自动购买产品。

举个简单的例子，下面的`bash`命令使用 [Curl](https://curl.se/) 获取 SitePoint 博客索引页面返回的 HTML 内容。它通过 [Grep](https://www.gnu.org/software/grep/manual/grep.html) 将结果传递给最近文章的链接:

```
curl 'https://www.sitepoint.com/blog/' | \
  grep -o '<article[^>]*>\s*<a href="[^"]*"' 
```

一个程序可以每天运行一个类似的过程，与以前的结果进行比较，并在 SitePoint 发布新文章时提醒您。

在你试图从所有你喜欢的网站上抓取内容之前，试着在谷歌搜索或亚马逊链接中使用`curl`。您很可能会收到一个不可用的 HTTP 503 服务和一个简短的 HTML 错误响应。现场通常放置障碍物来防止刮擦，例如:

*   检查用户代理、cookies 和其他 HTTP 头，以确保请求来自用户的浏览器而不是僵尸网络
*   使用 JavaScript 支持的 Ajax 请求生成内容，因此 HTML 中的信息很少
*   要求用户在显示内容之前与页面进行交互，比如向下滚动
*   要求用户在显示内容之前登录，例如大多数社交媒体网站

您可以使用 **[无头浏览器](https://github.com/dhamaniasad/HeadlessBrowsers)** 修复大多数问题，这是一个真实的浏览器安装，您可以使用驱动程序来模拟用户交互，如打开标签、加载页面、向下滚动、单击按钮等。

您的代码将变得更加复杂，但这并不是问题的终结。一些网站:

*   仅在某些连接上可用，如移动网络
*   通过检查请求者的 IP 地址，将内容限制在特定国家(例如，[bbc.co.uk](https://www.bbc.co.uk/)对英国访问者开放，但会将其他国家的访问者重定向到内容和广告较少的[bbc.com](https://www.bbc.com/)
*   阻止来自同一 IP 地址的重复请求
*   使用验证码或类似的技术来识别机器人
*   使用诸如 [Cloudflare](https://www.cloudflare.com/) 这样的服务，可以防止在一个站点检测到的僵尸程序渗透到另一个站点

现在，您需要为相应的国家和网络提供代理服务器，最好有一个 IP 地址池来躲避检测。我们离结合一两个正则表达式的简单性还有很长的路要走。

幸运的是，[光明数据](https://brightdata.grsm.io/a9of7qgqscqg)为这些技术问题提供了解决方案，它承诺“将网站转化为结构化数据”。Bright Data 通过强大的网络连接提供可靠的抓取选项，您可以在几分钟内完成配置。

## 无代码明亮数据数据集

[明亮数据数据集](https://brightdata.com/products/datasets?gspk=anVhbml0YW5lc3Npbmdlcjg5ODc&gsxid=AaaKWOnIwYJk&utm_source=affiliates&utm_campaign=anVhbml0YW5lc3Npbmdlcjg5ODc)如果您需要来自以下方面的数据，这是最简单的入门方式:

*   电子商务平台，如沃尔玛和各种亚马逊网站(`.com`、`.de`、`.es`、`.fr`、`.it`、`.in`或`.co.uk`)
*   社交媒体平台，包括 Instagram、LinkedIn、Twitter 和抖音
*   商务网站包括 LinkedIn、Crunchbase、Stack Overflow、Indeed 和 Glassdoor
*   谷歌地图业务等目录
*   其他网站，如 IMDB

数据集的典型用途包括:

*   监控竞争对手的定价
*   追踪你最畅销的产品
*   投资机会
*   竞争情报
*   分析客户反馈
*   保护您的品牌

在大多数情况下，您会希望将数据导入数据库或电子表格来执行自己的分析。

数据集根据复杂性、分析和记录数量进行定价。像 Amazon.com 这样的网站提供数以百万计的产品，因此获取所有记录是昂贵的。然而，你不太可能要求所有的东西。您可以使用自定义子集过滤数据集，以返回感兴趣的记录。以下示例使用字符串`Novice to Ninja`搜索 SitePoint 图书标题。这返回的记录要少得多，所以只需几便士就可以得到。

![dataset custom subset](img/6239a2601211b9b7655d3c2a8dcc6c86.png)

您可以通过电子邮件、webhook、亚马逊 S3、谷歌云存储、微软 Azure Storage 和 SFTP 一次性或定时接收结果数据。

## 自定义数据集和 Web Scraper IDE

你可以使用**收集器**从任何网站收集定制数据，收集器是一个 JavaScript 程序，它控制 Bright Data 网络上的 web 浏览器。

下面的演示演示了如何在 Twitter 中搜索`#sitepoint`标签，并返回 JSON 格式的 tweets 和元数据列表。这个收集器将使用一个 API 调用来启动，所以你首先需要进入你的[帐户设置](https://brightdata.com/cp/setting?gspk=anVhbml0YW5lc3Npbmdlcjg5ODc&gsxid=AaaKWOnIwYJk&utm_source=affiliates&utm_campaign=anVhbml0YW5lc3Npbmdlcjg5ODc)并创建一个新的 API 令牌。

![create API token](img/a36fbecbfa7daa811cf8c7e270ad82f5.png)

光明数据会给你发一封确认号的邮件。将其输入面板，您将看到您的令牌(36 个字符的十六进制 GUID)。复制它，并确保您已经安全地存储了它:您将不会再次看到它，并且如果您丢失它，将需要生成一个新的令牌。

前往**数据采集平台**菜单中的[采集器面板](https://brightdata.com/cp/data_collector?gspk=anVhbml0YW5lc3Npbmdlcjg5ODc&gsxid=AaaKWOnIwYJk&utm_source=affiliates&utm_campaign=anVhbml0YW5lc3Npbmdlcjg5ODc)，选择一个模板。我们在这个例子中使用 Twitter，但是您可以选择任何您需要的或者从头开始创建一个自定义收集器:

![Bright Data collector](img/d18e365d361ca115bf527fd047705337.png)

这导致了 **Web Scraper IDE** ，在这里您可以查看和编辑收集器 JavaScript 代码。Bright Data 提供 API 命令，例如:

*   `country(code)`在特定国家使用设备
*   `emulate_device(device)`模拟特定的手机或平板电脑
*   `navigate(url)`在无头浏览器中打开 URL
*   `wait_network_idle()`等待未完成的请求完成
*   等待，直到没有进一步的 DOM 请求
*   `click(selector)`点击特定元素
*   `type(selector, text)`在输入栏中输入文本
*   `scroll_to(selector)`滚动到一个元素，使其可见
*   `solve_captcha()`解决显示的任何验证码
*   `parse()`解析页面数据
*   `collect()`向数据集中添加数据

帮助面板是可用的，尽管如果您已经编程了一个无头浏览器或者编写了集成测试，那么代码将是熟悉的。

在这种情况下，Twitter 模板代码不需要进一步编辑。

![Bright Data code editor](img/8d0f5fafaf5281ba272306a9bf717ce3.png)

滚动到底部，点击**输入**面板，删除示例标签并定义自己的标签(如`#SitePoint`)。现在点击**预览**按钮，在浏览器中观看代码的执行。完全加载 Twitter 并向下滚动页面以呈现选择的结果需要一两分钟。

![Bright Data collector preview](img/78cc3a27a436e3d0693be647c22ba6c2.png)

一旦执行完成，**输出**面板显示捕获和格式化的结果。您可以下载数据以及检查运行日志、浏览器控制台、网络请求和错误。

![Bright Data output](img/bae2de9e10bb4f65bf8c84b013647088.png)

使用菜单或顶部的返回箭头返回到[收集器面板](https://brightdata.com/cp/data_collector?gspk=anVhbml0YW5lc3Npbmdlcjg5ODc&gsxid=AaaKWOnIwYJk&utm_source=affiliates&utm_campaign=anVhbml0YW5lc3Npbmdlcjg5ODc)。显示您的新收藏者。

![Bright Data integration](img/5fc3c0ae3655e1a70dcf589f33478f77.png)

点击**集成到您的系统**按钮，并选择以下选项:

*   **实时(单次请求)**收集频率
*   **JSON** 为格式
*   **API 下载**为交付

![Bright Data JSON API settings](img/828a4aea8036ff4c3142a3f0aa8c97ff.png)

点击**更新**保存整合设置并返回到收集器面板。

现在，单击收集器旁边的三点菜单，并选择**由 API** 启动。

![Bright Data API initiation](img/7c94e889df40bb8f0e330e06c050d97d.png)

由 API 发起面板显示两个`curl`请求命令。

![Bright Data curl example](img/ab7458aebf201155afeda4dfd884962b.png)

第一个命令执行 Twitter hashtag 收集器。它需要您在上面创建的 API 令牌。将其添加到`Authorization: Bearer`标题的末尾。例如:

```
curl \
  -H "Authorization: Bearer 12345678-9abc-def0-1234-56789abcdef0" \
  -H "Content-Type: application/json" \
  -d '{"Hashtag - #":"#SitePoint"}' \
  "https://api.brightdata.com/dca/trigger_immediate?collector=abc123" 
```

它返回一个带有作业`response_id`的 JSON 响应:

```
{
  "response_id": "c3910b166f387775934ceb4e8lbh6cc",
  "how_to_use": "https://brightdata.com/api/data-collector#real_time_collection"
} 
```

您必须将作业`response_id`传递给 URL 上的第二个`curl`命令(以及授权头中的 API 令牌):

```
curl \
  -H "Authorization: Bearer 12345678-9abc-def0-1234-56789abcdef0" \
  "https://api.brightdata.com/dca/get_result?response_id=c3910b166f387775934ceb4e8lbh6cc" 
```

当收集器正在执行时，API 返回一个*挂起*消息:

```
{
  "pending": true,
  "message": "Request is pending"
} 
```

当收集器完成执行时，它最终会返回一个包含 tweet 数据的 JSON 结果。必要时，您可以将这些信息导入您自己的系统:

```
[
  {
    "post": "https://twitter.com/UserOne/status/111111111111",
    "date": "2022-10-17T19:09:00.000Z",
    "Author": "UserOne",
    "post body": "Tweet one content",
    "likes": 0,
    "comments": 0,
    "Shares": 0,
    "input": {
      "Hashtag - #": "#SitePoint"
    }
  },
  {
    "post": "https://twitter.com/UserTwo/status/2222222222222",
    "date": "2022-10-08T13:28:16.000Z",
    "Author": "UserTwo",
    "post body": "Tweet two content",
    "likes": 0,
    "comments": 0,
    "Shares": 0,
    "input": {
      "Hashtag - #": "#SitePoint"
    }
  },...
] 
```

结果也可以从明亮的数据面板上获得。

![Bright Data result](img/eadc66161e389918024477e2a2c64e1b.png)

## 明亮的数据代理

如果你的需求超出了抓取网站的范围，你可以利用 Bright Data 的代理网络。示例使用案例:

*   你有一个 Android 应用程序，想在印度的移动网络上测试
*   您有一个服务器应用程序，它需要下载数据，就好像它是服务器实际位置之外的一个或多个国家的用户一样

有一系列代理可用，包括:

*   **住宅代理**:安装在住宅中的真实设备上的一组旋转 IP
*   **ISP 代理**:高速数据中心中的静态和旋转高速住宅 IP
*   **数据中心代理**:静态和旋转数据中心 IP
*   **移动代理**:在真正的移动 3G、4G 和 5G 设备上轮流使用 IP
*   **网络解锁代理**:一个使用住宅网络的自动解锁系统，包括验证码求解
*   SERP API 代理:从搜索引擎结果中收集数据的选项

每个都提供了诸如自动重试、请求限制、IP 轮换、IP 阻塞、带宽减少、日志记录、成功度量和代理绕过等选项。根据网络不同，价格从每 GB 0.60 美元到 40 美元不等。

最简单的入门方法是使用浏览器扩展的 [Chrome](https://chrome.google.com/webstore/detail/bright-data/efohiadmkaogdhibjbmeppjpebenaool?hl=en) 或 [Firefox](https://addons.mozilla.org/en-US/firefox/addon/brightdata/) 。您可以将扩展配置为使用任何特定的代理网络，因此它是在特定位置测试网站的理想选择。

![Bright Data proxy extension](img/89d2975aa0c0c69f9f1a8e7d0623cbe2.png)

对于更高级的使用，您需要[代理管理器](https://brightdata.com/products/proxy-manager?gspk=anVhbml0YW5lc3Npbmdlcjg5ODc&gsxid=AaaKWOnIwYJk&utm_source=affiliates&utm_campaign=anVhbml0YW5lc3Npbmdlcjg5ODc)。这是一个安装在你的设备上的代理，作为你的应用程序和光明数据网络之间的中间人。在对您进行身份验证并连接到真正的代理之前，它使用命令行选项来动态控制配置。

版本可用于 Linux，macOS，Windows， [Docker](https://hub.docker.com/r/luminati/luminati-proxy/) ，并作为 [Node.js npm 包](https://www.npmjs.com/package/@luminati-io/luminati-proxy)。源代码是 Github 上的[。](https://github.com/luminati-io/luminati-proxy)[Bright Data 网站上的示例脚本](https://brightdata.com/cp/api_example?gspk=anVhbml0YW5lc3Npbmdlcjg5ODc&gsxid=AaaKWOnIwYJk&utm_source=affiliates&utm_campaign=anVhbml0YW5lc3Npbmdlcjg5ODc)说明了如何在 shell 脚本(`curl`)、Node.js、Java、C#、Visual Basic、PHP、Python、Ruby、Perl 等中使用代理。

代理使用可能会变得复杂，所以光明数据建议你联系你的客户经理讨论需求。

## 结论

多年来，随着网站试图阻止机器人、黑客和内容窃贼，抓取数据变得越来越困难。位置、设备和网络特定内容的复杂性增加了这项任务的挑战性。

[亮数据](https://brightdata.grsm.io/a9of7qgqscqg)提供了一条解决刮蹭的划算路线。您可以立即获得有用的数据，并随着需求的发展采用其他服务。Bright Data network 可靠、灵活、高效，因此您只需为成功提取的数据付费。

## 分享这篇文章