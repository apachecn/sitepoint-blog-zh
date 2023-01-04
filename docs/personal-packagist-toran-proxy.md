# 带有 Toran 代理的个人包装商

> 原文：<https://www.sitepoint.com/personal-packagist-toran-proxy/>

读到这里的大多数人已经知道了作曲家。不了解的可以先看一下[我之前的一篇文章](https://www.sitepoint.com/php-dependency-management-with-composer/)再继续。我们都同意 Composer 给 PHP 世界带来了许多好东西。然而，如果一个人敢于寻找缺点，或者更好地说，没有包含的特性，他可以说，这是不可能的工作与私人仓库。这个论点不再成立，因为有了[托兰代理](https://toranproxy.com/)。

![](img/7bea4aadb10b51557aedc0d1062a9064.png)

## 萨蒂时代的终结

在 Toran 之前，那些真正想使用私有库的人可以使用 [Satis](https://github.com/composer/satis) 。Satis 使你能够生成一个私人托管的[包装商](https://packagist.org/)网站，带有镜像库和你自己的私人库。它与 bitbucket 和 github 上的私有项目兼容，并通过使用 SSH 密钥来保证安全性。通过镜像你正在使用的包(在你自己的服务器上托管它们)，你将获得更快的下载速度。Satis 是一个很好的解决方案，但是大约一个月前 Toran 作为一个更好的选择发布了。

## Toran:更难，更好，更快，更强

Toran 在其网站上使用了以下描述:

> Toran 充当 Packagist 和 GitHub 的代理。它应该安装在你自己的服务器上，甚至是你的办公室里。

这是一个比 Satis 使用的方法更好的方法:Satis urls 完全覆盖原始包。这意味着当您的 Satis 服务器不工作时，所有的包获取都将失败。有了 Toran，你总能回到最初的源头(Github 或者 BitBucket)。

下载速度也会提高:当你不住在美国东海岸(github 服务器的所在地)附近时，在你附近有一个服务器将帮助你更快地下载你的组件。Toran 镜像 git 库，这在 Satis 中是不可能的。这样源安装也会更快。

## 设置事物

如果你想验证我在这里说的一切，你可以从网站上下载个人版。[安装说明](https://toranproxy.com/download)很简单(只需复制粘贴)，一半可以通过使用 web 界面来完成。您需要输入主机域、子域和可选的 git 镜像位置。未来的更新可以通过执行一个简单的命令来完成。

## 家园上的 Toran 改进

我将快速演示如何建立自己的 Toran 站点。为了便于配置，我将使用 Homestead Improved，[如果你想了解如何设置它，请阅读此](https://www.sitepoint.com/quick-tip-get-homestead-vagrant-vm-running/)。

### 服务器设置

安装完成后，打开`Homestead.yaml`文件，在`sites:`下添加:

```
- map: toran.app
  to: /home/vagrant/Code/Toran/web 
```

还要将`toran.app`添加到您的 hosts 文件中。现在下载 Toran 的个人版，解压到你的 Homestead 文件夹里面的 Toran 文件夹中(Homestead 文件夹就是`Homestead.yaml`所在的位置)。如果你通过 SSH 连接到你的流浪者盒子，`/home/vagrant/Code`就是有问题的文件夹。这个主机名指向您刚刚提取的 Toran 文件夹中的 web 文件夹。

为了让 Toran 正常工作，只剩下一个服务器配置设置需要更改。通过 SSH 连接到您的 homestead box，并编辑`/etc/nginx/sites-available/toran.app`更改:

```
location / {
    try_files $uri $uri/ /index.php$query_string;
} 
```

收件人:

```
location / {
    try_files $uri /app.php$is_args$args;
} 
```

### 配置

这是所有需要的服务器配置。现在进入`/Toran/app/config/`并将`parameters.yml.dist`重命名为`parameters.yml`。打开它并更改以下设置:

```
toran_http_port: 8000
toran_host: toran.app 
```

最后，退出 VM 并用`vagrant reload`重新加载它。

### 巫师

您只需点击几下鼠标就可以使用 Toran。导航到 [http://toran.app:8000](http://toran.app:8000) 安装就会出现。您可以在这里输入您的许可证代码(见下一段)，但在我们的情况下，您可以选中下面的框，它是供个人使用的。

接下来，您将定义 cronjob 将做什么:如果您不想预取任何包，请使用 **lazy** 设置。如果你想预取所有定义的包，选择**所有**。之后，对于 git 克隆将存储在哪里以及将使用什么前缀 URL，有一些可选的设置。这是在你的库不公开的情况下。

如果您以前有 Satis 配置，您可以在最后一个文本框中输入它。点击安装按钮，我们就完成了。

## 进一步维护

现在，您需要使用以下命令手动更新所有内容:

```
php app/console toran:update 
```

之后，您可以设置一个 cronjob 来自动完成这项工作。当您导航到[http://toran.app:8000/post-install](http://toran.app:8000/post-install)时，会给出针对您的服务器的具体说明。

## 执照

随着 Toran 的发展，它的主要(也是目前唯一的开发者)Jordi Boggiano 决定不开源。虽然 Composer 和 Packagist 仍然是开源的，但 Toran 附带了许可证和费用。你个人可以免费使用 Toran，但是如果你想用 Toran 来做生意，你必须支付年费。准确的价格可以在[网站](https://toranproxy.com/)上找到，也可以要求定制价格。

## 结论

我要感谢 Jordi Boggiano 对这个项目的进一步说明。如果你有一个企业，想简化你的依赖关系管理，一定要看看这个应用程序。评论？反馈？留在下面！

## 分享这篇文章