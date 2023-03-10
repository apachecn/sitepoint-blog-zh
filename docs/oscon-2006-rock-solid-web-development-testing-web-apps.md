# OSCON 2006:坚如磐石的 Web 开发:测试 Web 应用

> 原文：<https://www.sitepoint.com/oscon-2006-rock-solid-web-development-testing-web-apps/>

本周，凯文·杨克将在俄勒冈州波特兰市的 [OSCON 2006](http://conferences.oreillynet.com/os2006/) 进行报道。
教程中的[幻灯片和辅助材料可在](http://transitionpoint.com/downloads/oscon2006.zip) [TransitionPoint 网站](http://transitionpoint.com/)上获得。

测试本身就是一门学科，还有像用户测试、功能测试和负载测试这样的子学科。桌面 web 开发世界(在某种程度上，企业 web 开发世界)正在以令人印象深刻的速度采用所有这些类型的测试，但是 web 开发人员所面临的一些独特挑战(例如，多种浏览器)意味着测试在那个世界中的应用更加缓慢。在这个演讲中，Ashenfelter 展示了每个子学科中测试的艺术状态，其中涉及的工具从令人生畏到鼓舞人心。

如果您还没有看到 Selenium 的运行，作为一名 web 开发人员，您至少应该下载 [Selenium Core](http://www.openqa.org/selenium-core/) ，提取它，打开`index.html`文件，并在 Selenium TestRunner 中运行内置的一组测试。观察您的浏览器(无论您使用哪种浏览器)立即成为一个测试框架，因为它会自动与一系列页面进行交互，并验证它们的行为是否正常。

如果你印象深刻，试着自己写几个测试(提示:你需要写的只是一个 HTML 表)，或者看一看 [Selenium IDE](http://www.openqa.org/selenium-ide/) ，一个 Firefox 扩展，它可以记录用户动作并动态生成测试脚本。

不那么直截了当的是 Grinder T1，这是一个用 Java 编写的免费测试工具，它是 Ashenfelter 的首选工具，不是因为它特别容易使用，而是因为它是唯一一个在现实世界中使用不会花费数万美元的工具。在这种情况下，它是开源的事实只是一个额外的好处。

Grinder 通过配置您的浏览器通过代理服务器连接到站点来记录您与站点的交互。这些交互产生了一个 Jython (Python on Java)脚本，您可以手动调整它。一旦你有了一个脚本，你就可以设置 Grinder 来一次运行几十(或几千)个该脚本的实例。

Ashenfelter 承认负载测试不是一个容易解决的问题。他甚至建议说，如果你的测试目标不简单，你最好付钱给一家咨询公司来为你做
测试。但是对于那些自己动手的人来说，研磨机是一个强大的(虽然笨重)工具。

将所有的测试工作结合在一起，并让你的开发伙伴使用测试工具并关注结果也需要一些工作，Ashenfelter 在这里的建议是使用标准工具，如 [CruiseControl](http://cruisecontrol.sourceforge.net/) ，并建立“信息辐射器”——开发人员以低噪音、高姿态的方式接收自动化测试结果的系统，不会被忽略(比如说，日常电子邮件会被忽略)。

## 分享这篇文章