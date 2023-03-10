# 如何通过自动化管道免费托管静态网站

> 原文：<https://www.sitepoint.com/how-to-host-static-sites-for-free-with-an-automated-pipeline/>

你知道你可以在一些高级服务上免费托管静态网站吗？这不仅节省了资金，还意味着您将部署到全球分布的 cdn 并实现流程自动化。

![](img/f78819f018124c82757172780094de2c.png)

*提供:Netlify*

在之前的一篇文章中，我们回顾了 100 个 Jamstack 工具、API 和服务来支持你的站点，其中包括许多托管服务。

在本文中，我们将亲自动手，向您展示如何使用自动化部署管道托管静态站点。

这听起来复杂吗？这比你想象的要容易。

## 免费的云托管，但是有附加条件

你实际上可以免费托管网站——甚至是动态网站——有 [AWS 免费层](https://aws.amazon.com/free/)(亚马逊网络服务) [GCP 免费层](https://cloud.google.com/free)(谷歌云平台)和 Windows Azure(有[一些变通办法](https://www.reddit.com/r/AZURE/comments/a66q63/how_to_get_150_azure_credits_for_free/))。

但是当您尝试这样做时，您会遇到许多条件、细则考虑和实现约束:

*   需要多大的计算能力？
*   请问你的信用卡号码？
*   您想部署到哪里？
*   你的账户是全新的吗？
*   不超过一年？
*   其实什么服务？

通常这更像是一次试用，而不是一次真正的免费赠送(这就是为什么 [AWS Amplify Storage](https://docs.amplify.aws/lib/storage/getting-started/q/platform/js) 不在这个列表中的原因)。虽然一些高级用户可能会利用这些好处，但如果你事先不熟悉这些平台，你会发现开始使用这些服务的学习曲线非常陡峭，对于你打算使用的每个云服务，你需要首先了解每个提供商以何种方式重新发明轮子，然后才能实际将其投入任何免费部署。

现在让我们看看一些小公司是如何通过免费的主机服务脱颖而出的，这些服务实施起来并不麻烦，附带的条件也更少。

## 云部署很难，然后出现了网络生活

[![](img/fa31ddeeaba6eaa9bfdebb818c4f0eb2.png)](https://www.netlify.com/)

创造了 Jamstack 一词的 Netlify 公司于 2015 年 3 月成立。在短短的业务时间里，它投入了大量的精力来兑现成为*、【最快的方式建立最快的网站】*的承诺。

### 净下降

Netlify 实际上开发了一个[拖放服务](https://app.netlify.com/drop)，其前提是:

> 拖放一个包含站点的 HTML、CSS 和 JS 文件的文件夹。我们会现场发布它们，并给你一个分享的链接。

你甚至不需要一个网络账户！没有比这更简单的了。您可以立即获得这些特性:

*   自由 HTTPS
*   全球部署到 [Netlify Edge](https://www.netlify.com/products/edge/) ，分布式多云 [CDN](https://www.sitepoint.com/what-is-a-cdn-and-how-does-it-work/)

还有更多:

*   您可以申请该站点(此时您确实需要一个帐户)
*   免费附加一个域名(也有免费的 HTTPS)
*   通过 [Netlify 工作流程](https://www.netlify.com/products/workflow/)、[功能](https://www.netlify.com/products/functions/)和[更多功能](https://www.netlify.com/products/)更进一步



[https://www.youtube.com/embed/-LRlQ_jaLAU](https://www.youtube.com/embed/-LRlQ_jaLAU)



### 自动化部署

如果你真的有一个账户，你会发现*链接一个 GitHub、GitLab 或 Bitbucket 库来进行自动化部署是非常容易的*。

其工作方式非常简单:你向你的存储库提交变更，Netlify 通过 [webhooks](https://en.wikipedia.org/wiki/Webhook) 立即得到通知，并且它立即在线部署这些变更。就是这样——没有信用卡，也没有以后会让你大吃一惊的小字(据我所知)。如果部署出现任何问题，不用担心:您可以在 web 界面上一键回滚！

这一工作流程虽然易于实现，但与[持续集成和持续交付](https://www.sitepoint.com/automate-cicd-visual-app-center/) (CI/CD)管道非常合拍，后者在 [DevOps 实践](https://www.sitepoint.com/devops-by-example-tools-pros-and-cons-of-a-devops-culture/)中获得了支持，这些实践如今非常受欢迎。

## GitHub 页面和 GitLab 页面

如果你现在还不知道，*你可以免费托管静态网站，直接从你的库*中的 [GitHub 页面](https://pages.github.com/)和 [GitLab 页面](https://about.gitlab.com/stages-devops-lifecycle/pages/)中。

GitHub 页面和 GitLab 页面都非常好用。请遵循以下指导原则:

*   [GitHub 页面入门](https://guides.github.com/features/pages/)
*   [在 GitLab.com 托管 GitLab 页面](https://about.gitlab.com/blog/2016/04/07/gitlab-pages-setup/)

### 拥有一个动态网站？让它静止！

[![](img/96fa465a44c6e0ff79ef593287fe59dc.png)](https://pages.github.com/)

将动态网站转化为静态 HTML/CSS 资产，可以极大地提高速度和安全性，这是 web 开发的一个上升趋势。有了合适的工具集，部署*就容易多了*。我们不会在这里讨论这个过程，但是你可以查看我们关于[把 WordPress 变成一个静态网站速度提高 10 倍的文章。](https://www.sitepoint.com/migrate-wordpress-static-site-generator/)

[![](img/fda171c69f5ed6e64c4ab24491ebf681.png)](https://about.gitlab.com/stages-devops-lifecycle/pages/)

作为一种替代方法，您可以创建一个自动化的管道来*将一组静态资产转化为行为非常像动态站点的东西*。例如，您可以将 Markdown 文件推送到您的存储库中，并自动将这些文件构建到网页中，以便将其部署到您的网站上。怎么会？[杰基尔](https://jekyllrb.com/)。

GitHub 为此有一套很好的教程:[用 Jekyll 建立 GitHub Pages 站点](https://help.github.com/en/github/working-with-github-pages/setting-up-a-github-pages-site-with-jekyll)。

另请查看这些指南:

*   [为 GitLab 页面创建和调整 git lab CI/CD](https://docs.gitlab.com/ee/user/project/pages/getting_started_part_four.html)
*   [哲基尔:GitHub Pages](https://jekyllrb.com/docs/github-pages/)

### 比较

用户抱怨说 [GitHub 页面非常慢](https://www.google.com/search?q=github+pages+slow)，有时仅部署少量 HTML 文件就要花费 20-30 分钟。导航可能会遇到性能问题，延迟高达五秒钟。考虑到 GitHub Pages 使用(或者应该使用)CDN，这真的很奇怪。(见[更快，更牛逼的 GitHub 页面](https://github.blog/2014-01-07-faster-more-awesome-github-pages/))。

虽然微软(GitHub 的母公司)可能已经解决了其中的一些问题，但总的来说，GitLab 提供的免费功能的数量和质量是惊人的。

|   | github pages-github 页面 | GitLab 页面 |
| --- | --- | --- |
| 静态站点生成器(SSR) | 吉基尔博士 | 盖茨比，哲基尔，雨果，中间人，竖琴，Hexo，早午餐，等等 |
| 插件支持 | 不 | 是 |
| 构建配置 | [特拉维斯 CI](https://travis-ci.org/) | 特拉维斯·CI |
| HTTPS 支持 | [部分/错误](https://github.com/isaacs/github/issues/156) | [是，包括自定义域](https://docs.gitlab.com/ee/user/project/pages/custom_domains_ssl_tls_certification/) |

## Heroku

[![](img/3ae694ce7c168ee3fc1b888f7b20e13d.png)](https://www.heroku.com/)

Heroku 是一个非常有趣的案例，因为与前面提到的提供商不同，*允许免费托管动态站点*，也非常注重[持续集成](https://www.heroku.com/continuous-integration)和[持续交付](https://www.heroku.com/continuous-delivery)。

Heroku 使用名为 [Dynos](https://www.heroku.com/dynos) 的专有轻量级容器技术来运行应用程序，因为您的环境将被容器化(参见[了解 Docker、容器和更安全的软件交付](https://www.sitepoint.com/docker-containers-software-delivery/)了解更多信息)，您可以有效地用几乎*任何*编程语言部署您的应用程序——例如 [Python](https://www.heroku.com/python) 、 [PHP](https://www.heroku.com/php) 、 [Node.js](https://www.heroku.com/nodejs) 或[许多其他](https://devcenter.heroku.com/categories/language-support)。您还可以将它与一个 [PostgreSQL](https://www.heroku.com/postgres) 或 [Redis](https://www.heroku.com/redis) 数据库集成…甚至与一个“ [Kafka-as-a-service](https://devcenter.heroku.com/articles/kafka-on-heroku) ”集成！

Heroku 的灵活性意味着这项服务不像 Netlify 那样容易使用。但它远没有 AWS、GCP 或 Azure 那么难。事实上，如果你以前已经使用过容器，你可能很快就会在 Heroku 找到你的路。

### 问题是

是的，这里有一个陷阱:**你的应用程序将在 30 分钟不活动后“休眠”**。因此，如果你的网站在短时间内空闲(没有访问)，Heroku 会将分配给运行你的 web 应用程序的资源置于待机状态，直到服务器收到新的请求。

如果你想向同事或客户展示你的应用，当你第一次点击页面时，等待时间(请求得到服务所需的时间)可能会很长，因为当应用“醒来”时，需要再次分配资源。之后，只要它保持活动状态至少 30 分钟，延迟应该是正常的。

这种资源节约机制的结果是，这种免费品适合在线测试想法，但不适合其他任何东西。

## Firebase 托管

[![](img/9319f301b8508bb5c22fb92f4a11f8b5.png)](https://firebase.google.com/products/hosting)

谷歌 Firebase，移动、网络和 Unity 开发的云套件，也搭载了 Firebase 主机。

您可以*免费使用该服务，每月可获得高达 10GB 的存储空间和 10GB 的数据传输量* *，包括免费的 SSL 证书，甚至可用于自定义域和支持每个项目的多个站点。那挺好的！

### 部署

与 Netlify 不同，使用 Firebase 托管进行部署并不简单。对于透明的 CI/CD 管道，Firebase 不会从存储库中读取 webhooks。相反，你必须使用命令行界面工具， [Firebase CLI](https://firebase.google.com/docs/cli) 。

来自 [Firebase 文档](https://firebase.google.com/docs/hosting#implementation_path):

> 1.  **Install Firebase CLI** . Basecli makes it easy to set up a new managed project, run a local development server and deploy content.
> 2.  **Set the project directory** . Add your static assets to your local hosting project folder, and set up cloud function or cloud operation for your [dynamic content and microservices](https://firebase.google.com/docs/hosting/serverless-overview) . Then, you can test your site locally by running [`firebase serve`](https://firebase.google.com/docs/hosting/deploying#test-locally) .
> 3.  **Deploy your site** . When everything is normal, run [`firebase deploy`](https://firebase.google.com/docs/hosting/deploying#deploy) to upload the latest snapshot to our server. The new version is released, so you never have to worry about the unfinished deployment. However, if something really goes wrong, you can click [to roll back](https://firebase.google.com/docs/hosting/manage-releases#roll-back) .
> 4.  **link a firebase web app *(optional)*** . By linking your site to a [Firebase web application](https://firebase.google.com/docs/web/setup) , you can use [Firebase performance monitoring](https://firebase.google.com/docs/perf-mon/get-started-web) to get an in-depth understanding of the performance characteristics of your site.

只是一点设置，但也不难。在这里，您可以查看部署的[完整文档。](https://firebase.google.com/docs/cli#deployment)

## 免费托管很有趣，但是公司从中获得了什么呢？

您需要考虑到这些公司已经有了一个大规模的基础设施，我们主要是在谈论运行时不需要预处理的静态资产。所以运行这些静态站点的开销很小。Heroku 的动态支持最好地证明了这种对比——它在站点空闲时节省资源，对于小型站点来说，这种情况在大多数时间都存在。

公司也从中获得了一些东西:聚光灯！你可能是为了免费啤酒而来，但如果场地和菜单足够吸引人，你也可能会留下来，在合适的时候为晚餐买单。

## 结论

有一些公司提供免费计划，但我们没有覆盖，如 **[Surge](https://surge.sh/)** 和 **[Vercel](https://vercel.com/)** (以前的 ZEIT Now)，这两家公司都是面向 Jamstack 的，很像 Netlify。但是希望您从这篇文章中获得的不仅仅是“免费托管”:当您实现部署管道时，您真的将您的工作流带到了一个新的层次，因为您自动化了那些繁琐的、容易犯令人痛苦的错误的、并且经常是不可逆转的过程(例如，在 FTP 上覆盖文件)。

还有谁不喜欢免费托管呢？但是，同样，当你在全球范围内部署 cdn，让你的网站在全球范围内飞速发展时，还有更多事情要做

所以，你几乎可以忘记为你的许多项目支付一个静态网站，并在这个过程中启动你的工作流程。你真幸运！💥

## Jamstack 基础

跟上 Jamstack 的速度。我们的 Jamstack Foundations 系列可帮助您迈出进入 Jamstack 和超越 Jamstack 的第一步，并且我们会不断增加它。我们将为您带来成为职业选手所需的教程。您可以随时参考我们的索引，因为它在 Jamstack 简介的末尾进行了更新:

➤ [贾姆斯塔克地基](https://www.sitepoint.com/learn-jamstack#foundations)

## 分享这篇文章