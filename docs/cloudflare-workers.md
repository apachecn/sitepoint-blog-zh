# Cloudflare Workers 简介

> 原文：<https://www.sitepoint.com/cloudflare-workers/>

各种形式的云计算——SaaS、PaaS、IaaS——取得了巨大的成功。我们中的一些人仍然记得 2010 年以[2.12 亿美元收购 PaaS 提供商 Heroku，从架构上来说，当时它只不过是一个高级部署层。它有一个非常漂亮的宝石，可以平滑轻松地部署应用程序和框架，如运行在亚马逊基础设施上的 RoR、Python 或 Node 应用程序。](https://techcrunch.com/2010/12/08/breaking-salesforce-buys-heroku-for-212-million-in-cash/)[无服务器计算](https://en.wikipedia.org/wiki/Serverless_computing)的概念诞生了。

从那时起，云产品就有了许多不同的模式。随着供应商寻找最佳点，各种实验来了又去了，新的术语不断涌现，比如 T2 BaaS T3 和 MBaaS。

Protocol Labs 是一家寻求重新定义云模式的加密初创公司，[在其 2017 年](https://coincentral.com/filecoin-beginners-guide-largest-ever-ico/) [ICO](https://www.investopedia.com/terms/i/initial-coin-offering-ico.asp) 中筹集了 2.57 亿美元，打破了所有记录。 [Airtable](https://www.sitepoint.com/how-to-use-airtable-a-beginners-guide/) ，凭借其高级电子表格与数据库[产品](https://airtable.com/blocks)和 [api](https://airtable.com/api) ，[在 2018 年融资轮](https://www.fastcompany.com/90266220/airtable-is-a-unicorn-after-100-million-funding-round-puts-valuation-at-1-1-billion)中实现了 11 亿美元的估值。

## 无服务器计算

无服务器计算是云计算的一个子集术语，它强调摒弃传统的*服务器*产品，为开发人员提供一个高级环境来运行他们的代码，按使用计费，并使开发人员不必担心底层软件堆栈。

无服务器计算允许在为使用的处理能力付费方面有更大的灵活性，而不是像传统云那样为预先分配的包付费。

术语“无服务器”在语义上是错误的，因为代码仍然在服务器上执行，但是用户*在概念上*不再需要与服务器打交道。只要遵守某些约定，底层堆栈以及所有基础设施和部署问题都由供应商处理。

由此衍生出的主要产品类型是 FaaS——一种云执行环境，或者允许部署代码而没有任何样板文件的运行时。亚马逊的 Lambda 、[的 Oracle Fn](https://developer.oracle.com/java/fn-project-introduction) 和阿里巴巴的 [Function Compute](https://www.alibabacloud.com/products/function-compute) 就是一些例子。

## 云耀斑

Cloudflare 是一家成立于九年前的旧金山公司。这是一个[内容交付网络](https://en.wikipedia.org/wiki/Content_delivery_network)，从其*边缘节点*的全球网络为网站提供静态资产的交付。它还提供防火墙和 DDOS 保护，并以互联网上最快的 [DNS 服务](https://www.cloudflare.com/dns/)而闻名。

当谈到 Cloudflare 以及它给无服务器计算领域带来了什么时，有必要在我们这里使用的云术语列表中再添加一个术语— *边缘计算*。

正如维基百科上的解释:

> 边缘计算是一种分布式计算范例，其中计算大部分或完全在分布式设备节点上执行。边缘计算将应用程序、数据和计算能力(服务)从集中点推向离用户更近的位置。*

## 云闪工人

![Screenshot of a Kenton Varda tweet](img/e855c98f5d98c98c1cc2fb943a72d4bb.png)

Cloudflare Workers 是 FaaS(功能即服务)的一个独特而强大的化身，代码被立即部署到 Cloudflare 的全球节点网络，在尽可能靠近访问者的地方执行。

正如 Kenton Varda [在 Cloudflare 博客](https://blog.cloudflare.com/cloudflare-workers-unleashed/)上所说:

> 当响应新西兰的用户时，您的代码应该在新西兰运行。当处理数据库中的数据时，您的代码应该在存储数据的机器上运行。当与第三方 API 交互时，您的代码应该在托管该 API 的任何地方运行。当人类探险者到达火星时，他们不会乐意花半个小时等待你的应用程序响应。你的代码需要在火星上运行。

Cloudflare Workers 是在 V8 上设计的代码执行环境，V8 是为 Google Chrome 设计的 JavaScript 引擎，Opera、Node.js、Brave browser、Electron 和其他公司也使用它。

工人使用 *[服务工人 API](https://developer.mozilla.org/en-US/docs/Web/API/Service_Worker_API)* —但代码驻留在 Cloudflare 上，而不是浏览器中。

[他们可以](https://cloudflareworkers.com)“修改你站点的 HTTP 请求和响应，进行并行请求，甚至直接从边缘回复”。这意味着，如果我们愿意，我们的应用可以驻留在 CDN 上。

这也意味着，虽然我们运行的动态 JavaScript 代码不是传统意义上的服务器端代码，而且与服务器端应用程序和传统 FaaS 服务相比，它的延迟要好得多，但我们的代码仍然没有在浏览器中运行，这意味着它没有向公众开放。

按照它的首席开发人员的话来说，Workers 是某种东西的开端，这种东西在未来可能会发展成更加精致的产品。

在许多情况下，感觉速度和效率就像在访问者的浏览器中执行一样好，但是没有处理开销。这一切都发生在云中。

类似的技术(目前 NGINX 的用户可以使用)是 [Lua 脚本](https://skillsmatter.com/skillscasts/3362-scripting-nginx-with-lua)——不同之处在于 Cloudflare 在其边缘节点的网络上执行工人的代码，最接近访问者。

Cloudflare Workers 可以在[cloud flare Workers Playground](https://cloudflareworkers.com/)进行快速测试。

## 潜在用途

Cloudflare 已经将许多代码示例[上传到 GitHub repo](https://github.com/cloudflare/worker-examples) 中。其中有一个 [Slack bot](https://github.com/cloudflare/worker-examples/tree/master/examples/cryptocurrency-slack-bot) ，它从 CoinMarketCap 获取价格或加密货币，并将其缓存在 Cloudflare 基础设施上。代码非常简单，可以在[这里](https://github.com/cloudflare/worker-examples/blob/master/examples/cryptocurrency-slack-bot/index.js)找到。它展示了 Cloudflare Workers 的主要优势，即无需处理应用程序下的整个软件堆栈的简单性。

Cloudflare 工作人员让开发人员可以专注于应用程序逻辑。

还有一个更高级的例子——WordPress 的 edge 缓存解决方案——它通过将 Cloudflare Workers、Workers KV(键值存储)和 WordPress 的一个插件放在一起，提供了一个完美的缓存和清除 WordPress 的 HTML 输出的解决方案，它们协同工作，使得在出现一些 cookies 的情况下绕过缓存成为可能，从而使原子缓存清除成为可能，等等。

你可以在这里找到关于这个例子[的更多信息。](https://github.com/cloudflare/worker-examples/tree/master/examples/edge-cache-html)

然后是使用自定义逻辑在原点网站和互联网之间加一层保护的[代码示例。](https://github.com/cloudflare/worker-examples/tree/master/examples/security)

有一个使用 workers 逻辑重写网页 HTML 中包含的第三方脚本的[示例，使用内容哈希生成 URL，然后扩展其浏览器缓存，并将其缓存在 Cloudflare edge 节点上——因此每个脚本不需要三次往返第三方服务器。](https://github.com/cloudflare/worker-examples/tree/master/examples/third-party-scripts)

在某些情况下，这可以产生很大的性能差异，它的美妙之处在于工作人员使用 JavaScript 这意味着有一个丰富的可编程接口来调用 Cloudflare 基础架构的所有功能来执行我们的命令。

在他们的库中还有其他代码示例[。](https://github.com/cloudflare/worker-examples)

## 这是怎么做到的

当我们打开 Cloudflare 仪表板时，在顶部的图标菜单项中有一个 *Workers* 菜单项。

![The Workers menu option](img/f0c6c6d0e9b2b58d1b6bd485fddacc2a.png)

首先，我们需要打开顶部的*工人仪表板*链接，我们需要为我们的工人设置一个子域:

![setting up a subdomain for our workers](img/aeb51e3a6b898bba4339e70f2dc07867.png)

然后，我们将看到 workers 仪表板，在这里我们将能够部署新的工作人员，监控他们的使用情况，等等。

还有一个编辑器，我们可以用它来编辑工人的逻辑:

![The workers editor](img/4a74621887bf59d17ca14a0f63fbf48c.png)

正如我们所说的，工作人员使用 JavaScript V8 引擎，显然还有 JavaScript 语法。[文档](https://workers.cloudflare.com/docs)是可靠和详细的，并且有许多[模板](https://workers.cloudflare.com/docs/templates/)，所以我们可以快速开始我们的工作。

### 等等，还有呢！

对于我们这些希望能够将更多逻辑放入该层，并希望从该基础架构中获得更多性能的人来说，还有更多。2018 年 10 月， [Cloudflare 宣布支持 Workers](https://blog.cloudflare.com/webassembly-on-cloudflare-workers/) 的 WebAssembly 这意味着支持 C、C++和 Rust 这样的编译语言。这使得员工可能会非常认真，如果不是改变游戏规则的竞争者。

## 结论

无服务器计算提供了便利，吸引了越来越多开发者的注意。这让他们可以专注于应用程序逻辑，而不必担心底层的库和软件基础设施。

我们中的一些人必须处理数百兆字节的 npm 库，遇到不同依赖项版本的混乱，必须安装并使用 [Ruby Version Manager](https://rvm.io/) ，或 [PyEnv](https://github.com/pyenv/pyenv) ， [virtualenv](https://www.sitepoint.com/virtual-environments-python-made-easy/) 以及类似的美味佳肴，他们知道这种痛苦，并且知道解决这些问题可能需要一个人 80%的工作。

如今可用的无服务器云产品已经找到了市场，Cloudflare Workers 是这一细分市场中最有趣的产品之一。我希望这篇介绍对你有用！

## 分享这篇文章