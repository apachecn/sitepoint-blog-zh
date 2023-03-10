# 100 个 Jamstack 工具、API 和服务为您的站点提供支持

> 原文：<https://www.sitepoint.com/jamstack-tools-services-apis/>

**我们已经[解释了 Jamstack](https://www.sitepoint.com/learn-jamstack/) ，这是一种构建安全、可伸缩、高性能网站的流行新方法。现在我们将向您介绍支持 Jamstack 站点的工具、服务和 API。**

Jamstack 中的 *A* 代表 *API* 。API 可以为你做任何事情，从发送表单到认证用户，或者从实时存储和检索数据到购买产品。

在本文中，我们将对现有的 API 进行广泛的回顾，并比较它们之间的差异。这篇评论不可能涵盖你可以集成到你的网站中的第三方 API 的全部范围，但是希望你仍然会发现它足够有启发性。

在这篇文章中，我们会大量使用“无头”、“分离”和“无服务器”这些词。如果您还没有，请查看[我们对 Jamstack](https://www.sitepoint.com/learn-jamstack/) 的介绍，它涵盖了所有基础知识。

## 托管(大部分是免费的)

[![](img/f78819f018124c82757172780094de2c.png)](https://jamstack.org/)

*提供:Netlify*

托管 Jamstack 站点通常涉及自动部署管道。例如，您可能在 GitHub 中有一个存储库，每次推送时，它都会自动触发一次在线部署(通过 [webhooks](https://en.wikipedia.org/wiki/Webhook) ，运行必要的[构建工具](https://en.wikipedia.org/wiki/List_of_build_automation_software)(如 Jekyll)和[回归测试](https://en.wikipedia.org/wiki/Regression_testing)(通过 Travis CI)。

听起来很难？这可能是一个非常简单的过程！

这里的大多数服务都包括这些现成的好东西:

*   固态硬盘
*   cdn 部署
*   免费 SSL(包括自定义域)
*   命令行部署和回滚

注意:请关注本系列中的另一篇文章，它将很快出现，介绍如何使用这些服务。

### 服务

#### 谷歌 Firebase 和 AWS Amplify

[![](img/c6b35917deb3af8a539679a857e7906f.png)](https://firebase.google.com/products/hosting)

**[Firebase Hosting](https://firebase.google.com/products/hosting)** 是谷歌的一项托管服务，易于理解和实现，并且免费使用([限制适用](https://firebase.google.com/pricing))。Firebase 的主干实际上位于[谷歌云平台](https://cloud.google.com/) (GCP)之上，事实上你可以通过 GCP 控制台访问和调整一些 Firebase 部署。但是，通过实现某种“网关”(Firebase)，为我们透明地处理 GCP 资源，谷歌给了开发者一个全新的、高度改进的用户体验(UX) …而 [Firebase 的 YouTube 频道](https://www.youtube.com/user/Firebase)简直太棒了！👏

[![](img/ae62506af0e8315952558f85de4bfabe.png)](https://aws.amazon.com/amplify/)

[AWS Amplify](https://aws.amazon.com/amplify/) 也是为了减少用于网络和移动部署的[亚马逊网络服务](https://aws.amazon.com/) (AWS)的复杂性，该服务并不完全提供免费托管，而是为其 **[存储的新帐户提供 12 个月的免费使用，Amplify](https://docs.amplify.aws/lib/storage/getting-started/q/platform/js)** 是 [AWS 免费层](https://aws.amazon.com/free/)的一部分。

谷歌通过将 Firebase 系列产品从 GCP“分离”出来，确实迈出了一步，但亚马逊在 AWS Amplify 上走了一半。与常规的 AWS 工作流程相比，这无疑是一个巨大的改进，尤其是对于新手用户来说，而且它的[文档中心](https://docs.amplify.aws/)非常棒，比亚马逊通常记录服务的方式更加务实。但是 Amplify 仍然可以从同一个旧的[(臃肿得可怕)控制台](https://aws.amazon.com/console/)访问。你仍然需要一张信用卡来开户，部署仍然是地区特定的(没有内置的 CDN，真的吗？)，而且与 Firebase 或 Netlify 相比，工作流程没有那么简单。

#### GitHub 页面和 GitLab 页面

[![](img/96fa465a44c6e0ff79ef593287fe59dc.png)](https://pages.github.com/)

Git 存储库的两个托管服务都有一个内置的服务来托管静态页面，完全免费: [GitHub 页面](https://pages.github.com/)和 [GitLab 页面](https://about.gitlab.com/stages-devops-lifecycle/pages/)。

[![](img/fda171c69f5ed6e64c4ab24491ebf681.png)](https://about.gitlab.com/stages-devops-lifecycle/pages/)

在以后的文章中，我们将介绍如何使用这些服务，但同时请确保查看这些易于遵循的指南:

*   [GitHub 页面入门](https://guides.github.com/features/pages/)
*   [在 GitLab.com 托管 GitLab 页面](https://about.gitlab.com/blog/2016/04/07/gitlab-pages-setup/)

#### Netlify 和 Heroku

[![](img/fa31ddeeaba6eaa9bfdebb818c4f0eb2.png)](https://www.netlify.com/)

在很短的时间内， **[Netlify](https://www.netlify.com/)** 不仅创造了 *Jamstack* 这个术语，还将自己定位为所有静态事物的去处。虽然您肯定可以通过精心设计的 AWS 管道完成更多工作，但 Netlify 提供的简单性和无与伦比的易用性是无与伦比的。想托管一个静态站点吗？只要[把它放在这里](https://app.netlify.com/drop)就可以上线了。想要自动更新吗？[链接一个回购](https://docs.netlify.com/configure-builds/get-started/)并直接提交。还包括电池—即时构建、全球 CDN、免费 SSL、CLI 工具、点击回滚等。

[![](img/3ae694ce7c168ee3fc1b888f7b20e13d.png)](https://www.heroku.com/)

**[Heroku](https://www.heroku.com/)** 是这个列表中唯一允许你托管动态页面的服务:Node.js、Ruby、Python、Java、PHP、Go、Scala 和 Clojure(查看他们的[语言支持](https://devcenter.heroku.com/categories/language-support)页面)。所以如果你还没有准备好去静态化，这可能是一个免费在线测试你的动态网站的好方法。

#### 其他服务

[![](img/04094937f4d2110ec44eabd099f47ccd.png)](https://workers.cloudflare.com/)

截至 2020 年， [Cloudflare](https://www.cloudflare.com/) 拥有 194 个数据中心，从多项指标来看，[是全球 DNS 和 CDN 服务延迟最低的公司。他们为大公司服务，但也有许多面向开发者的服务，如](https://www.dnsperf.com/dns-providers-list/)**[工人网站](https://workers.cloudflare.com/sites)** 。这项服务不是免费的(最低收费 5 美元/月)，但它是你能得到的最高性能，而且相当容易使用。

其他针对静态页面的工具包括 **[特技](https://www.aerobatic.com/)** ，提供不需要信用卡的免费试用，支持国际化(i18n)和全文搜索[内置插件](https://www.aerobatic.com/docs/plugins/)； **[Surge.sh](https://surge.sh/)** 带 [npm 运行脚本](https://surge.sh/help/getting-started-with-surge)和 [CI 服务](https://surge.sh/help/integrating-with-travis-ci)；以及 **[Vercel](https://vercel.com/)** (之前的 ZEIT Now)与 [Edge Network](https://vercel.com/edge-network) 服务 Twilio 和《华盛顿邮报》等大牌。

### 比较

| 服务 | 免费计划 | 易于使用 | 工具作业 |
| --- | --- | --- | --- |
| 特技飞行 | 1 个月 | 容易的 | 好的 |
| [Firebase 托管](https://firebase.google.com/products/hosting) | 是 | 容易的 | 很好 |
| github pages | 完全自由 | 容易的 | 贫穷的；贫困的 |
| [GitLab Pages](https://about.gitlab.com/stages-devops-lifecycle/pages/) | 完全自由 | 容易的 | 好的 |
| [Heroku](https://www.heroku.com/) | 是 | 有点容易 | 很好 |
| [Netlify](https://www.netlify.com/) | 是 | 极其容易 | 很好 |
| [用放大器存储](https://docs.amplify.aws/lib/storage/getting-started/q/platform/js) | 1 年期(新账户) | 有点容易 | 很好 |
| [Surge.sh](https://surge.sh/) | 是 | 容易的 | 好的 |
| 韦尔塞尔 | 是 | 容易的 | 好的 |
| [工人工地](https://workers.cloudflare.com/sites) | 不 | 有点容易 | 好的 |

## 存储和检索数据:实时 NoSQL 数据库

像 MongoDB 这样的 NoSQL 解决方案已经与 MySQL 这样的关系数据库共存了一段时间(参见[差异](https://www.sitepoint.com/sql-vs-nosql-differences/)和[如何选择](https://www.sitepoint.com/sql-vs-nosql-choose/))，但[实时](https://en.wikipedia.org/wiki/Real-time_database)处理通过启用云存储进行状态管理，如用户输入他们的名字或单击单选按钮，将 NoSQL 带到了一个新的水平。

如果你熟悉分别用于状态管理的 [Redux](https://redux.js.org/) 和[Vuex](https://vuex.vuejs.org/)——React 和 Vue.js 库——考虑将*的概念与云存储提供商集成。*

### 服务

[![](img/4e552560c6691b66c5338f55b1ae331d.png)](https://aws.amazon.com/dynamodb/)

**[亚马逊 DynamoDB](https://aws.amazon.com/dynamodb/)** 是一个*“全面管理、多区域、多主控、持久的数据库，内置安全性、备份和恢复，以及针对互联网规模应用的内存缓存”*。但是和 AWS 的许多东西一样，它很难实现并且*很难*调试(参见[为什么 Amazon DynamoDB 不适合所有人](https://read.acloud.guru/why-amazon-dynamodb-isnt-for-everyone-and-how-to-decide-when-it-s-for-you-aefc52ea9476)，作者 Forrest Brazeal)。为亚马逊说句公道话，他们也用一种更直接的方法将 **[DataStore](https://docs.amplify.aws/lib/datastore/getting-started/q/platform/js)** 构建到 AWS Amplify(带有 [GraphQL](https://graphql.org/) 和 REST API 支持)中，与 Amplify 其余产品的简单性保持一致。

[![](img/e0af38dbd1aecb99a5053f4c588944d6.png)](https://firebase.google.com/products/firestore)

Google Firestore 让实时 NoSQL 数据库——这本身就是一个相当复杂的话题——变得尽可能简单，几乎拥有 DynamoDB 的所有功能。这是[非常好的记录](https://firebase.google.com/docs/firestore)(与[介绍剪辑](https://www.youtube.com/playlist?list=PLl-K7zZEsYLluG5MCVEzXAQ7ACZBCuZgZ)是有趣的观看)。而 React 和 Vue.js 分别用 [react-redux-firebase](http://react-redux-firebase.com/) 和 [Vuexfire](https://vuefire.vuejs.org/vuexfire/) 对 Firestore 进行了包装。

Cloudflare 当然知道如何将性能发挥到极致，而用于应用程序的无服务器键值存储 **[Workers KV](https://www.cloudflare.com/products/workers-kv/)** 就是一个很好的例子，它展示了一个设计良好的产品的样子。Workers KV 的前提是，你可以访问一个*键*，就好像它是你的 app 内的一个本地文件，内容将是为那个键存储的*值*。就是这样——无需实现 API，无需额外编码。由于 Cloudflare CDN 无与伦比的性能，这种方法实际上比查询 NoSQL 数据库更快。虽然简单，但它可以无缝扩展到数百万个请求。👏

[![](img/918dd23df9a3942d25089b0a67b2a928.png)](https://fauna.com/)

最后，还有一家初创公司，它用原生 GraphQL 和[简单定价](https://fauna.com/pricing)(包括免费计划)精心打造了一个可以在几分钟内实施的解决方案。

## 管理内容:无头 CMS

在“单一”的做事方式中，每当我们使用给定的[内容管理系统](https://en.wikipedia.org/wiki/Content_management_system)——比如 WordPress、Django 或 Joomla！—这意味着我们*也*需要使用附属于它的前端引擎，因为后端和前端是一个软件的“耦合”组件(参见[我们对 Jamstack](https://www.sitepoint.com/learn-jamstack/) 的介绍，了解更多关于[紧耦合与松耦合](https://en.wikipedia.org/wiki/Coupling_(computer_programming))站点的信息)。

进入无头 CMS——只有后端没有前端。由于无头 CMS 通常会以 Markdown 或 HTML 文件的方式公开一个 API 或生成静态内容，前端实际上可以在任何地方。事实上，可以为网站、移动应用和物联网(IoT)应用创建多个同步界面。

### 产品和服务

有许多无头 CMS，既可以作为软件下载并在您自己进行部署的地方进行配置，也可以以软件即服务(SaaS)模式提供，在这种模式下一切都会为您处理。

您可能会发现一些特性:

*   [本地化(l10n)和国际化(i18n)](https://en.wikipedia.org/wiki/Internationalization_and_localization)
*   [微服务](https://en.wikipedia.org/wiki/Microservices)架构，带有 [RESTful API](https://en.wikipedia.org/wiki/Representational_state_transfer)
*   编辑器界面
*   用户化
*   [版本控制](https://en.wikipedia.org/wiki/Versioning_file_system) *

* *由于一些 headless CMS 可以与您的 git repo 顺利集成，版本控制功能实际上是对常规 CMS 的显著改进。*

#### 自托管无头 CMS

[![](img/9b362bc98eb3a000424a5ac27692d612.png)](https://ghost.org/)

**[幽灵](https://ghost.org/)** ，*《头号开源无头节点. js CMS》*，当然是 GitHub 上[星星最多的一个。Ghost 不仅可以处理内容，还提供了许多集成来管理支付(Stripe)、电子邮件列表(MailChimp)、购物(Shopify)等等。然后还有](https://github.com/TryGhost/Ghost)**[Ghost(Pro)](https://ghost.org/pricing/)** ，这是官方托管的有商业支持的 Ghost。

[![](img/f582cb07cd3d3a1bf2bd573bf6902fdf.png)](https://strapi.io/)

与 Ghost 相当受欢迎的是，REST 和 GraphQL APIs，以及 Heroku、AWS 和 DigitalOcean 上的一键部署。它有与 Gatsby 无缝协作的“启动器”(模板项目)，与 Nuxt.js 无缝协作的 Vue.js，与 Next.js 无缝协作的 React，以及 Angular。它也运行在 Node.js 上，并且支持[许多数据库引擎](https://strapi.io/documentation/v3.x/guides/databases.html)。

[![](img/4508ed87e1146807c8cd028b31664c43.png)](https://www.netlifycms.org/)

**[Netlify CMS](https://www.netlifycms.org/)** 也是作为单页 React 应用程序构建的一个流行选项。有 **[Directus](https://directus.io/)** ，它用 API 包装定制的 SQL 数据库，并提供直观的管理应用程序来管理其内容，还有商业伙伴 **[Directus Cloud](https://directus.io/services.html)** 。还有 **[TinaCMS](https://tinacms.org/)** ，也是基于 React 的，还有**[Ponzu](https://docs.ponzu-cms.org/)****[copc kpit](https://getcockpit.com/)**等等，你可以在[headlesscms.org](https://headlesscms.org/)提供的综合列表中探索。

#### SaaS 无头不育系

**[CloudCannon](https://cloudcannon.com/)** 是*Jekyll 的云 CMS*(我们将在后面的“静态站点生成器”部分回顾 Jekyll)，与 GitHub、Bitbucket 和 Dropbox 平滑集成。他们也有免费计划，但是没有全球 CDN 托管。

[![](img/e70eb68f7f91aee9bf4f162b17bb3c70.png)](https://www.contentful.com/)

Contentful 与众不同:它是一个内容中心，企业所有者、营销人员、开发人员和项目经理都可以在这里设置和管理一个组织的所有数据源。而[他们的无头 CMS](https://www.contentful.com/resources/the-ultimate-guide-to-headless-cms/) 只是*那个策略的一部分*。令人满意的平台是[功能齐全的](https://www.contentful.com/features/)，[有很好的文档](https://www.contentful.com/resources/)，有[大量开源工具](https://github.com/contentful)。虽然定价有点混乱，但实际上有一个只需要[归属](https://www.contentful.com/r/knowledgebase/how-attribution-works/)的免费计划。

还有其他有免费计划的 SaaS 无头 CMS，如 **[DatoCMS](https://www.datocms.com/)** 和 **[Sanity](https://www.sanity.io/)** 有类似 Contentful 的命题； **[林业](https://forestry.io/)** ，支持多台静止发电机； **[GraphCMS](https://graphcms.com/)** ，有 GraphQL 支持；还有 **[Prismic](https://prismic.io/)** 。这些只是众多选择中的几个。

## 发送信息:没有代码的表单

通过表单发送数据一直是服务器端处理的主要用途之一。在静态站点上解决这个问题有两种方法，各有利弊。

### 来自构建器、嵌入和托管表单

[![](img/29866a43fa790e86f14d92bc594af0c0.png)](https://www.google.com/forms/about/)

很多时候，集成一个“powered by”外部表单就足以收集电子邮件地址或接收反馈。

自 2008 年以来，谷歌表单(Google Forms)完全免费地提供了这种可能性，它有一个简单的界面，可以在[谷歌表单(Google Sheets)](https://www.google.com/sheets/about/)上存储提交的内容，并可以在每次有提交内容时通过电子邮件发送提醒。

**[Formstack](https://www.formstack.com/)** 通过提供支持数字签名、文档生成、Salesforce 集成等的集成工作流，将表单带到了另一个层次。他们提供[免费试用，但没有免费计划](https://www.formstack.com/pricing)。

[![](img/e4117712bece157380e40cbfa0b492bc.png)](https://www.typeform.com/)

然后你有非常容易使用的表单生成器 **[JotForm](https://www.jotform.com/)** 和 **[Wufoo](https://www.wufoo.com/)** ，它们集成了处理付款等功能，或者 **[Typeform](https://www.typeform.com/)** ，它们使表单和调查…漂亮吗？他们都提供免费计划。

### 外部 API:表单处理即服务(FPaaS)

有时构建器不能满足要求，因为您需要更多的灵活性来呈现信息和字段，或者将表单的外观和感觉与网站的其余部分完全集成。为此，您需要集成一个 API。

这些服务的工作方式出奇地简单:您指定一个提交 URL，它将为您完成处理。最多你将不得不设置一些东西，但最可能不需要做任何额外的编码。

有许多提供商提供免费计划，如 **[表格。IO](https://www.form.io/)** ， **[Formcarry](https://formcarry.com/)** ， **[Formspark](https://formspark.io/)** ， **[Netlify Forms](https://www.netlify.com/products/forms/)** 。它们都以相似的方式工作，并且非常容易实现。

**[FormDen](https://formden.com/)** 和 **[FormKeep](https://formkeep.com/)** 都是*也是*表单构建器，否则只能用作后端，尽管它们都不提供免费计划。

## 服务器端逻辑编程:功能即服务(FaaS)

找不到你想要的 API？创造它！您不需要求助于托管的后端系统来处理服务器端逻辑，以及随之而来的所有麻烦(维护、账单、凭证、安全补丁)。相反，您可以用自己选择的语言(通常是 JavaScript、Python 或 Go)实现微服务，将逻辑封装到函数中，并通过 RESTful API 提供它们。

和这个列表中的几乎所有东西一样，你不会为一个没有被经常使用的功能支付任何费用，所以不要担心把它放在那里(但是要小心，流量的峰值也可能触发额外的账单)。

### 提供者

[![](img/0e6dd926740a690b098800e2a9fb60f3.png)](https://aws.amazon.com/lambda/)

虽然 **[AWS Lambda](https://aws.amazon.com/lambda/)** 、 **[Azure 函数](https://azure.microsoft.com/services/functions/)** 和 **[谷歌云函数](https://cloud.google.com/functions)** 的实现细节可能有所不同，但它们的工作方式基本相同，你需要对 AWS、Azure 或 GCP 有一定的熟悉程度。AWS Lambda 拥有最丰富的语言支持(还有**[Amazon API Gateway](https://aws.amazon.com/api-gateway/)**帮助你用监控工具将你的功能包装成可维护的 API)，而 Azure——毫不奇怪——拥有最好的支持。NET 框架和。NET Core(用[不同版本支持不同运行时](https://docs.microsoft.com/en-us/azure/azure-functions/functions-versions)甚至 [TypeScript 转 JavaScript](https://docs.microsoft.com/en-us/azure/azure-functions/functions-reference-node#typescript) )。但是要知道，Azure 被系统地列为最慢的服务。

[![](img/dc3b9745d5cc0ba84d8964e30eb115d7.png)](https://firebase.google.com/products/functions/)

Firebase 的 **[云函数](https://firebase.google.com/products/functions)** 和 **[Netlify 函数](https://www.netlify.com/products/functions/)** 分别是 Google 云函数和 AWS Lambda 的包装器。它们极大地简化了云上功能的管理，因为你甚至可以在没有此类服务帐户的情况下离开。使用 Netlify，代码部署和版本控制变得很简单 Netlify 也有很好的[社区支持](https://community.netlify.com/)——因为它将与您的回购提供阶段、预览以及单击(或提交)回滚顺利集成。简单自然是以失去一些灵活性为代价的(参见 Pier Bover 的[Firebase Cloud Functions:the great，the meh，and the ugly](https://www.freecodecamp.org/news/firebase-cloud-functions-the-great-the-meh-and-the-ugly-c4562c6dc65d/) )。

[![](img/111a730ee186e38e52983e0d8f77b879.png)](https://cloud.ibm.com/functions/)

**[IBM 云函数](https://cloud.ibm.com/functions/)** (基于[Apache open whish](https://openwhisk.apache.org/))和**[Cloud flare Workers](https://workers.cloudflare.com/)**是你可能想看看的其他服务。IBM 有一个令人印象深刻的支持语言列表，包括用自己的运行时部署 Docker 容器的选项。然而，它在性能方面的排名有些糟糕。正如 Netlify 系统地设法让事情变得最简单一样，Cloudflare 再次让事情变得最快(与[相差](https://web.archive.org/web/20200511043849/https://serverless-benchmark.com/))。

### 比较

| 服务 | 语言 | 开销* | 冷启动* | 困难 | 支持 |
| --- | --- | --- | --- | --- | --- |
| [AWSλ](https://aws.amazon.com/lambda/) | [C#，Go，Java，JavaScript，PowerShell，Python，Ruby](https://docs.aws.amazon.com/lambda/latest/dg/lambda-runtimes.html) | 86🟢女士 | 589 毫秒 | 高的 | 👍很好 |
| [Azure 功能](https://azure.microsoft.com/services/functions/) | [C#、F#、Java、JavaScript 和 TypeScript、PowerShell、Python](https://docs.microsoft.com/azure/azure-functions/supported-languages) | 760 毫秒🔴 | 5907 毫秒🔴 | 高的 | 👎很穷 |
| [火情云函数](https://firebase.google.com/products/functions) | JavaScript 和[类型脚本](https://firebase.google.com/docs/functions/typescript) | 642 毫秒🔴 | 168 毫秒 | 低的 | 👍很好 |
| [Cloudflare 工人](https://workers.cloudflare.com/) | [JavaScript](https://blog.cloudflare.com/introducing-cloudflare-workers/) ， [COBOL](https://blog.cloudflare.com/cloudflare-workers-now-support-cobol/) | 70🟢女士 | 76🟢女士 | 中间的 | [中级](https://community.cloudflare.com/tags/workers) |
| [谷歌云功能](https://cloud.google.com/functions) | [Go，Java，Node.js，Python](https://cloud.google.com/functions/docs/writing) | 642 毫秒🔴 | 168 毫秒 | 高的 | 👍很好 |
| [IBM 云功能](https://cloud.ibm.com/functions/) | 。C#、Go、Java、JavaScript、PHP、Python、Ruby、Swift 和 Docker 容器 | 136 毫秒 | 2103 毫秒🔴 | 高的 | 没有信息 |
| [网络功能](https://www.netlify.com/products/functions/) | [Go](https://docs.netlify.com/functions/build-with-go) ， [Node.js](https://docs.netlify.com/functions/build-with-javascript) | 86🟢女士 | 589 毫秒 | 极低 | 👍很好 |

* *根据 [λ无服务器基准](https://serverless-benchmark.com/)测量，**开销**是从请求到响应的时间，不包括函数花费的时间(对于 50 的并发量)，而**冷启动**是每 3 小时查询一次服务器响应的时间；值越低越好。*

## 认证用户:身份即服务

身份即服务(IDaaS)，有时也称为*身份验证即服务* (AaaS)，涉及仅使用 API 管理完整的用户注册、确认和身份验证。“无状态身份验证”的*艾斯特*是用户将针对第三方进行身份验证，并向您返回一个有效的“令牌”，您可以验证该令牌，或者在需要时撤销该令牌。

在某些情况下，提供商甚至可能提供一个“嵌入式”用户界面(UI ),可以在桌面和移动设备上无缝工作，所有这些都有可能节省您很长时间的工作。

### 服务

[![](img/2f2eb0de1c39dd27a2d5620dbac69eaf.png)](https://auth0.com/)

**[Auth0](https://auth0.com/)** 经营时间最长，拥有[针对多种场景的快速入门指南](https://auth0.com/docs/quickstarts/)。如果您想实现一个复杂的解决方案，并且已经有了一些实现身份验证的经验，那么它是一个很好的提供商。但正如他们所指出的，*“身份复杂，应对自如”*。如果您刚刚开始这个主题，那么大范围的 Auth0 服务(通用登录、单点登录(SSO)、多因素身份验证、分支机构密码检测等等)可能会让人不知所措。

**[Firebase 认证](https://firebase.google.com/products/auth)** (带其[即用型 UI](https://opensource.google/projects/firebaseui) )和 **[带 Amplify](https://docs.amplify.aws/lib/auth/getting-started/q/platform/js)** 的认证也非常全面和灵活，比 Auth0 更直观。Firebase 还提供匿名认证！好奇？看看这个视频:



[https://www.youtube.com/embed/ApG8L2RKrSI](https://www.youtube.com/embed/ApG8L2RKrSI)



[![](img/b4538bd62ff18194f357fdc8dd9e1afd.png)](https://docs.netlify.com/visitor-access/identity/)

Netlify 似乎又一次提出了最容易实现的解决方案，用 **[Netlify 身份](https://docs.netlify.com/visitor-access/identity/)** 和它的开源零配置 [netlify-identity-widget](https://github.com/netlify/netlify-identity-widget) 到[在 10 分钟内创建一个安全登录](https://www.youtube.com/watch?v=Nd9SpZwhTEA)！但是当然，也有一些限制(查看 Jean Cochrane 的《网络身份中的四个交易破坏者》。

还可以查一下**[Okta](https://www.okta.com/)****[fusion auth](https://fusionauth.io/)**和 **[LoginRadius](https://www.loginradius.com/)** ，都是有自由计划的。 [Ping 身份](https://www.pingidentity.com/)、 [OneLogin](https://www.onelogin.com/) 、 [Ubisecure](https://www.ubisecure.com/) 没有免费赠送，更面向企业部门。最后，考虑一下 [Cloudflare 访问](https://teams.cloudflare.com/access/)，因为 Cloudflare 所做的一切都非常可靠。

## 走向科学技术:静态站点生成器(SSG)

我可以听到你们中的一些人说*“所有这些对未来的项目来说可能没问题，但是我的网站已经是动态的了，所以该怎么办呢？”这里是静态站点生成器进入画面的时候。*

您可以两全其美——既有熟悉的 CMS *和*静态页面的便利，又有[代码和数据分割](https://webpack.js.org/guides/code-splitting/)、[预加载](https://en.wikipedia.org/wiki/Preload_(engineering))、缓存、图像优化和各种性能增强。SSR 将通过查询您的数据库并从中生成静态输出(例如， [Markdown](https://en.wikipedia.org/wiki/Markdown) pages)来弥合这一差距，并通过一些设置来设置您的模板，您将一切就绪。

与不断增加的 SSR 列表相比，这里的列表很小。更多信息请看 [StaticGen](https://www.staticgen.com/) 。

### 主要产品

[![](img/3a324ea1a2a5a68b680102f3fb5df41c.png)](https://www.gatsbyjs.org/)

**[GatsbyJS](https://www.gatsbyjs.org/)** 由 React.js 和 webpack 提供支持，这意味着它可以生成渐进式 web 应用程序(又名 PWAs，看起来和感觉上都像应用程序的网站)。它还支持 GraphQL(参见[用更好的构建模块编写应用](https://www.gatsbyjs.com/how-it-works/write-modern-apps/))并且它有[+1000 个插件从任何地方获取数据](https://www.gatsbyjs.com/how-it-works/data-from-anywhere/) (WordPress、Drupal、Contentful、GraphCMS、DatoCMS 等等)。见【GatsbyJS 如何将其与主要竞争对手 Hugo 和 Jekyll 进行比较。

所有这些灵活性都是有代价的，因为设置和定制 GatsbyJS 可能是一个耗时的过程，如果您对 React 以及 JavaScript 没有足够的了解，您将无法充分利用它。这就是 **[盖茨比云](https://www.gatsbyjs.com/)** 的用武之地，免费或收费提供构建和维护盖茨比站点[的支持](https://www.gatsbyjs.com/pricing/)，在那里你可以自动化你的快速构建、访问预览、生成每日编辑，并轻松启动 Netlify、Cloudflare、AWS CloudFront 或 Akamai 的部署。

[![](img/90b949f7cd050af5e8149108c39fa422.png)](https://gohugo.io/)

**[Hugo](https://gohugo.io/)** 号称是*【世界上最快的建站框架】*，它肯定能在毫秒内生成海量站点。凭借内置模板(字面意思是[数百个可用的模板](https://themes.gohugo.io/))和对国际化的本地支持(i18n)，它也是最受欢迎的 SSG 之一。Hugo 是一款 [go 应用](https://golang.org/)，虽然 Go 不难设置和学习，但如果你不熟悉它，你肯定需要经常查看文档。

[![](img/d7d386b21378e44e9ca1e3dcf2b76e69.png)](https://jekyllrb.com/)

与 GatsbyJS 不同，配置和部署 **[Jekyll](https://jekyllrb.com/)** 是一个相当简单的过程。此外，Jekyll 是唯一由 [GitHub Pages](https://pages.github.com/) 支持的 SSR(Jekyll 的创建者 Tom Preston-Werner 也是 GitHub 的联合创始人)，并且可以在 GitHub repos 之外免费平稳地部署静态站点！Jekyll 用的是 Shopify 的[液体模板语言](https://shopify.github.io/liquid/)，也很易学。缺点是什么？作为一个 [Ruby 应用](https://www.ruby-lang.org/en/)，Jekyll 可能很难在 Windows 环境下设置，并且默认情况下不包括优化，如最小化 JavaScript 代码和图像预加载。事实上，Jekyll 甚至不打算生成 PWA，而只是纯粹的静态站点——这可能仍然很好，取决于您的需要。

#### 比较

| 产品 | 语言 | 模板 | 环境 | GitHub stars |
| --- | --- | --- | --- | --- |
| [盖茨比](https://www.gatsbyjs.com/) | Java Script 语言 | React.js | 困难的 | [https://ghbtns.com/github-btn.html?user=gatsbyjs&repo=gatsby&type=star&count=true](https://ghbtns.com/github-btn.html?user=gatsbyjs&repo=gatsby&type=star&count=true) |
| [雨果](https://gohugo.io/) | 去 | Go ( [库](https://themes.gohugo.io/)) | 中间的 | [https://ghbtns.com/github-btn.html?user=gohugoio&repo=hugo&type=star&count=true](https://ghbtns.com/github-btn.html?user=gohugoio&repo=hugo&type=star&count=true) |
| 哲基尔 | 红宝石 | [液体](https://shopify.github.io/liquid/) | 容易的 | [https://ghbtns.com/github-btn.html?user=jekyll&repo=jekyll&type=star&count=true](https://ghbtns.com/github-btn.html?user=jekyll&repo=jekyll&type=star&count=true) |

### 其他人

**[WP2Static](https://wp2static.com/)** 是专门为 WordPress (WP)设计的 SSR。它有一组很小但非常有趣的[插件](https://wp2static.com/addons/)，比如 Algolia search、Cloudflare Workers 和 Netlify deployments。 **[HardyPress](https://www.hardypress.com/)** 实际上是一个生成静态 WP 网站的 SaaS 解决方案，收费的[你会有一个管理面板，在那里你可以输入一些凭证来访问你的在线 WP 安装以管理一切:关闭已经导入的 WP 安装，透明部署到全球 CDN，HTTPS，表单，搜索。其他有商业支持的 WP 相关 SSG 还有](https://www.hardypress.com/pricing/)**[移位器](https://www.getshifter.io/)****[Strattic](https://www.strattic.com/)****[site sauce](https://sitesauce.app/)**。

由于我不可否认地偏向于 [Vue.js](https://vuejs.org/) ，我不得不包括 **[VuePress](https://vuepress.vuejs.org/)** ，它旨在生成单页应用程序(spa)并具有以 markdown 为中心的文件的最小设置，并且它也由 [webpack](https://webpack.js.org/) 提供支持。 **[Gridsome](https://gridsome.org/)** 和 **[Nuxt.js](https://nuxtjs.org/)** 是更具特色的 Vue.js powered 框架，具有 SSR 功能。

## 销售和处理支付:无头购物车

无头电子商务的架构和优势与无头 CMS 并没有太大的不同:大幅降低成本(托管、许可、维护)，更少的[上市时间](https://en.wikipedia.org/wiki/Time_to_market)，无缝集成，以及——对商业而言很重要的——[全渠道](https://en.wikipedia.org/wiki/Omnichannel)能力。

无头购物车与 Shopify 等服务有本质上的不同，因为你不局限于一个店面，你可以将数据转移到各种媒体和各种来源。一个无头的电子商务提供商不会对你如何呈现你的内容或访问你的数据固执己见，也无法控制它。

正是这一点实现了全渠道战略，在这一战略中，您不仅可以向多个渠道(网络、移动、物联网)交付内容，还可以跨不同渠道集中流程管理，以实现全面、改进的 UX。请注意，[哈佛商业评论](https://hbr.org/2017/01/a-study-of-46000-shoppers-shows-that-omnichannel-retailing-works)和 [Aspect 软件](https://loyalty360.org/content-gallery/daily-news/survey-businesses-that-use-omni-channel-strategies-have-far-better-customer)的研究表明，这种策略提供了*超越“多渠道”或“单独在线”方法的显著优势。*

### 服务

**[Foxy.io](https://www.foxy.io/)** 和 **[Snipcart](https://snipcart.com/)** 提供了一个简单的服务，可以非常容易地集成到许多格式中。Foxy.io 是基于[订阅的](https://www.foxy.io/pricing)，而 Snipcart 收取[一定比例的交易费](https://snipcart.com/pricing)。

在更大的企业层面上， **[Elastic Path](https://www.elasticpath.com/)** 为汽车、制造和医疗保健行业等提供无头电子商务服务。 **[Salesforce 商务云](https://www.salesforce.com/products/commerce-cloud/)** 利用 Salesforce 平台进入企业对消费者(B2C)和企业对企业(B2B)的无头电子商务。

## 找到你想要的:搜索即服务

通过[搜索即服务](https://en.wikipedia.org/wiki/Search_as_a_service)，网络搜索*也可以*集成到我们的网站中，让第三方为我们进行搜索结果的索引和排序。

[![](img/806d8ad43aeb69415f270cfb25e33b75.png)](https://developers.google.com/custom-search)

**[谷歌定制搜索](https://developers.google.com/custom-search)** (并更名为[可编程搜索引擎](https://programmablesearchengine.google.com/about/))是这一领域的先驱，但尽管非常易于使用和实现，它并没有很好地与网站的外观和感觉相结合，搜索结果取决于谷歌之前的索引。

[![](img/67265f14fcf9a33a91c1b0fc050c774d.png)](https://www.elastic.co/)

有 **[Elasticsearch](https://www.elastic.co/)** 和托管解决方案，如[亚马逊 Elasticsearch 服务](https://aws.amazon.com/elasticsearch-service/)，以及其他人工智能服务，如 [Azure 认知搜索](https://azure.microsoft.com/en-us/services/search/)、[亚马逊 Kendra](https://aws.amazon.com/kendra/) 和[亚马逊 CloudSearch](https://aws.amazon.com/cloudsearch/) 和 [Apache Solr](https://lucene.apache.org/solr/) 。它们都很优秀，如果实施得好，会产生很好的结果。但这正是问题所在——实施。精心设计一个切实可行的解决方案需要大量的时间和技能。

### 制品

[![](img/0096375954112165d08d85fcc2789ca1.png)](https://www.algolia.com/)

**[Algolia](https://www.algolia.com/)** 是一项将综合搜索提升到另一个水平的服务，其*超*快速[搜索 API](https://www.algolia.com/products/search/) 可用于[多种情况](https://www.algolia.com/solutions/)(网站、语音、地理、移动、电子商务、媒体等)。它提供了慷慨的免费“社区”计划。

**[Klevu](https://www.klevu.com/)** 和 **[Sooqr Search](https://www.sooqr.com/)** 是专门针对电子商务的搜索解决方案，可以更容易地将购物者与您提供的产品联系起来。Sooqr 提供了多达 100 种独特产品的免费计划，而 Klevu 没有。

**[Expertrec](https://www.expertrec.com/)** 和 **[Swiftype](https://swiftype.com/)** 是你可能想查看网站和应用搜索的其他服务。不过，没有免费计划。

## 保持最新:通知

拥有一个跨多个渠道进行通信的策略是刺激用户参与的关键，然而最好是以一种编程的方式将它们结合起来，用一个 API 跨所有平台发送消息。

基本上有四种互补的途径可供您使用:

*   **移动推送**是一种高度可见的发送(希望)相关信息的方式，这样用户就会回到你的应用。
*   Web push 遵循相同的原则，但适用于桌面和移动浏览器。
*   应用内消息是留住用户的另一种方式，提供用户可能需要的帮助。
*   还有**电子邮件**，因为即使到了 2020 年，有了所有的即时消息和社交应用，电子邮件营销仍然是一件事。

### 服务

你听说过大多数人认为自己高于平均水平吗？似乎公司也陷入了偏见，因为 **[OneSignal](https://onesignal.com/)** ， **[Pushwoosh](https://www.pushwoosh.com/)** 和 **[Truepush](https://www.truepush.com/)** 都宣称自己是*【头号推送通知服务】*。在任何情况下，他们都有慷慨的免费计划(以及从你通过他们发送的数据中获得的大量营销见解)，OneSignal 提供了更多的集成。



[https://www.youtube.com/embed/znzJNm9YQeA](https://www.youtube.com/embed/znzJNm9YQeA)



还可以查看 **[飞艇](https://www.airship.com/)****[弹射器](https://www.catapush.com/)****[lean plum](https://www.leanplum.com/)****[Pushover](https://pushover.net/)**[多了](https://vwo.com/engage/)…

此外，如果你已经在使用谷歌 Firebase，你可能想看看和 **[应用内消息](https://firebase.google.com/products/in-app-messaging)** 。同样，对于 AWS Amplify，检查 **[推送通知](https://docs.amplify.aws/lib/push-notifications/getting-started/q/platform/js)** 。

## 额外:通过分析获得洞察力

**[Google Analytics for Firebase](https://firebase.google.com/products/analytics)**是一个类似于 [Google Analytics](https://analytics.google.com/) 的手机包装器，也是为了简化网络体验。它由用户友好的[谷歌 Firebase 控制台](https://console.firebase.google.com/)管理。

**[Netlify Analytics](https://www.netlify.com/products/analytics/)** 在这里确实有优势。这是*如此简单*设置，实际上没有设置！使用这项服务的一个要求(除了支付每个站点每月 9 美元的费用)是你已经在 Netlify 中托管了你的站点。这样，他们已经有了他们需要的所有信息*“直接来自事实的来源”*来为您呈现统计数据——不需要 JavaScript 代码，不需要 cookies，不需要像素跟踪。虽然这不能像 Google Analytics for Firebase 那样扩展到移动应用程序，但它提供了更好的性能(对渲染时间没有影响)和更准确的数字(会话在服务器上运行，而不是在客户端)。

**[Analytics with Amplify](https://docs.amplify.aws/lib/analytics/getting-started/q/platform/js)**是亚马逊的一种服务方式，其工作方式与 Google Analytics for Firebase 非常相似，适用于 Android、iOS 和网络。

## 结论(或者为什么要为你可以自己开发的东西付费？)

如果你已经做到这一步，你可能已经注意到，我们介绍的大多数服务都可以在一定程度上免费使用——通常是在你的网站或应用程序起飞的时候。那么，为什么不开发自己的搜索或认证解决方案，或者管理自己的服务器并创建专门的通知来满足您的需求呢？

那么，这里有一个问题:你有没有注意到，即使是拥有金钱可以买到的最好的 IT 部门的大公司也使用这些服务？

*   Twitch 使用 Algolia
*   Spotify 和 Airbnb 使用 GatsbyJS
*   阿特拉斯使用 Auth0
*   网飞使用云炮
*   阿里巴巴使用 Firebase
*   Mozilla 使用 Ghost
*   几乎每个人都使用 AWS 和 Cloudflare
*   …等等，等等

最终的问题是——*你* *真的*要通过转移你的资源和注意力来解决一个不是你的业务核心的问题，并且一个更有资格的团队已经解决了这个问题，从而节省资金和/或有更多的控制权吗？如果是这样，那就去做吧！或者你可以接受价格标签，忘记这个问题，继续下一个问题。

## Jamstack 基础

跟上 Jamstack 的速度。我们的 Jamstack Foundations 系列可帮助您迈出进入 Jamstack 和超越 Jamstack 的第一步，并且我们会不断增加它。我们将为您带来成为职业选手所需的教程。您可以随时参考我们的索引，因为它在 Jamstack 简介的末尾进行了更新:

➤ [贾姆斯塔克地基](https://www.sitepoint.com/learn-jamstack#foundations)

## 分享这篇文章