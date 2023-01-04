# 与乘客一起平稳部署

> 原文：<https://www.sitepoint.com/smooth-your-deployment-with-passenger/>

我提到了 Phusion 的 Passenger T1(又名 mod _ rails)——一个 Apache 模块，可以运行多个 Rails 站点，就像 PHP 一样。这件事的意义非常重大。到目前为止，运行 rails 需要在 Apache 代理后面运行 Mongrel，或者使用 FastCGI(或者最好是 fcgid)。

虽然 Mongrel 做得很出色，但我从来不喜欢有两个失败点 Apache 或 Mongrel 的问题会使您的站点无法访问。不仅如此，增加系统容量需要添加许多 mongrel 实例，每个实例都有自己的端口和自己的内存开销，这对于共享主机提供商来说非常麻烦。

乘客将允许主机提供商安装一个 Apache 模块，要求客户只需上传他们的应用程序-没有权限问题，或麻烦的配置文件。然而，它对开发人员来说也是非常有用的——测试应用程序时不必运行`script/server`,您可以安装 Apache 的本地版本，设置一些虚拟主机，并即时访问您的测试站点。

目前，乘客只运行在*NIX 类型的环境，如 Linux 和 OSX。Phusion 团队非常努力地让安装尽可能简单。以下是基本步骤:

1.  安装 Apache(有关说明，请咨询您的供应商)
2.  运行`gem install passenger`
3.  运行`passenger-install-apache2-module`

乘客现在应该会问你几个问题，如果你回答正确，就应该完成安装。

重启 Apache 并创建一个虚拟主机文件，该文件指向您的 Rails 应用程序，然后您就可以离开了！下面的虚拟主机文件将告诉 Apache 使用/home/sites/test 中的 Rails 应用程序来响应 url http://test.localdomain。

```
<VirtualHost 127.0.0.1:80>
    ServerName test.localdomain
    DocumentRoot /home/sites/test
</VirtualHost>

```

如果您使用 passenger 进行开发，请确保您的 apache2.conf 文件包含行`RailsEnv development`。`` 

## ``分享这篇文章``