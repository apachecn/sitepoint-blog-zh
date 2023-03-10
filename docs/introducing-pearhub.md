# pearhub 简介

> 原文：<https://www.sitepoint.com/introducing-pearhub/>

新年快乐。

在过去的一年里，我在 Ruby on Rails 上花了大量的时间，在 Ruby 世界中工作得更好的东西之一是包分发系统(gems，正如他们所称的)。许多 Ruby 开发人员在 github 上提供他们的库，直到最近，github 还集成了一个功能，可以将代码发布为 gem。最近他们停止了这项服务，取而代之的是一个专门的网站， [gemcutter](http://gemcutter.org/) 出现了。Gemcutter 严格来说是一个包托管服务/渠道。

我认为像这样的服务是 gems 在 Ruby 开发者中如此受欢迎的一个重要原因，我认为 PHP 确实需要类似的东西。所以在圣诞节期间，我开始酝酿一个服务，现在已经足够稳定，我将向整个社区提供它。

[pearhub.org](http://pearhub.org/)提供了一个地方，你可以在这里注册一个托管在 Github、Google code 或类似平台上的项目(目前只支持 git 和 subversion)。该服务将生成一个 PEAR 包，并将其放在 PEAR 通道上。这意味着可以从 PEAR 安装程序进行安装和升级。通过在链接的存储库中制作一个标签来制作新的版本。Pearhub 将定期检查任何新的标签，如果有，就生成一个新的包。

我希望你会觉得有用，当然也欢迎反馈。

## 分享这篇文章