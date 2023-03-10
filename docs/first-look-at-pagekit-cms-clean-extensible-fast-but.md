# 首先看看 page kit CMS——干净、可扩展、快速，但是…

> 原文：<https://www.sitepoint.com/first-look-at-pagekit-cms-clean-extensible-fast-but/>

Pagekit 最近发布了第一版，我一直在关注个人博客引擎，所以我觉得应该去看看。诚然，博客仅仅是 Pagekit 能够提供的功能的一个子集，但仍然是一个很好的基本测试子集。

![Pagekit logo](img/54d4f3447a2fb5a81b34e2b2355bf8ab.png)

## 装置

*注意:我们将使用 [Homestead Improved](https://www.sitepoint.com/quick-tip-get-homestead-vagrant-vm-running/) 作为测试环境。所有的命令，如果有的话，都将被列出。如有必要，适应您自己的操作系统。*

为了安装，我们下载并解压缩他们的档案，然后将 web 服务器指向新创建的文件夹。Pagekit 会立即显示安装程序屏幕。

![Pagekit installation screen](img/78be4f5712aba08dac28fcfdf816fc5c.png)

经过一个短暂但非常顺利的安装过程后，我们来到了仪表板上。

![Pagekit dashboard](img/466c6548edd5429bb7c596beaef1ccdd.png)

从仪表板，我们可以访问网站的所有其他部分，如管理用户，配置新的页面和路线，安装主题和扩展，处理部件，等等。

权限/角色子系统在默认状态下有点受限，只支持经过身份验证的用户、管理员和访客，但是对于博客，也就是我们在这里测试的内容，这已经足够了。如果需要，以后可以在角色屏幕中添加更多角色。

## 建立 Pagekit 博客

### 自定义页面

首先，让我们建立一个关于页面。如果我们前往网站，然后页面，我们可以建立一个新的页面。方便的是，Pagekit 支持开箱即用的[Markdown](https://pagekit.com/docs/user-interface/editor)，因此我们可以使用它来编写内容。

![Creating an about page](img/97bd3e398ed291d49ab83f08da847d13.png)

该页面(以及指向它的链接)将立即出现在我们网站的主页上:

![The about page rendered](img/adc20a2597eef726b47d0326bcb53abc.png)

### 博客

为了建立博客，我们需要安装博客扩展。幸运的是，它已经是默认的了，可以在主菜单中找到。还有一个示例帖子:“Hello Pagekit”。

作为一名开发人员，我希望编写大量的代码片段，所以有必要看看它们在帖子中会是什么样子。让我们修改一下示例帖子，放入一些技术内容和一两张图片。我将使用之前在 SitePoint 上发表的一篇文章。

![A published post, syntax not highlighted](img/fbfeaf5d2b9baece43be0b2392b4282d.png)

它看起来很好——远远好于人们对默认主题的预期——但似乎只有内联代码被突出显示。剩下的就简单了。让我们看看如何解决这个问题。

### 扩展ˌ扩张

Pagekit 附带了一个令人惊奇的市场，虽然仍然很空，但是扩展/主题安装/下载程序已经很难了。它简单易用，而且相对容易开发，不会将开发的包变成一堆糟糕的代码。

方便的是，marketplace 也已经有了一个高亮扩展，显然从第一天起就迎合了开发者。

![Syntax highlight extension in the Marketplace](img/b52ed9fb9930e1cbf8ad3c88e404bc14.png)

安装很简单，而且让每个 PHP 开发人员高兴的是，它是通过 Composer 实现的。

![Composer installation of Highlight extension](img/096d0289769a8a70ca65d941605f1e59.png)

只需刷新博客文章的渲染就可以看到它的效果——它是自动启用的！

![Syntax highlighted code](img/cc92227acbc240487d02aaae448c0008.png)

好多了！

### 自定义主题

不过，默认主题有点太标准了。这很美，但随着越来越多的人使用 Pagekit，这些网站将开始变得相似，变得有点太容易辨认——就像“引导瘟疫”。我们再装一个吧。最小的主题看起来很棒，所以让我们用那个。

不到一分钟，整个过程就完成了——主题被安装并激活，刷新站点就会显示出来。

![Minimal theme installed and in use](img/788a4a8bbd10df4035876098419c1eec.png)

当然，它远没有演示的那么漂亮，但是当内容差异如此之大时，人们很难期望它会如此。没什么小改动解决不了的。

## 漂亮的网址

在没有激活重写模块的 Apache 服务器上，以及在 Nginx 上，Pagekit URLs 将默认为带有`index.php`的丑陋 URL。要在 Apache 上获得漂亮的 URL，需要启用该模块——请参考您的服务器设置文档。在 Nginx 中，来自 [Homestead Improved](https://www.sitepoint.com/quick-tip-get-homestead-vagrant-vm-running/) 的默认设置将支持漂亮的 URL(即它们将*工作*),但 Pagekit 默认不会在内部启用它们，因为它无法检测到它们在运行。为了解决这个问题，我们可以在服务器配置中添加一个自定义值:

```
fastcgi_param HTTP_MOD_REWRITE On; 
```

一旦我们用`sudo service nginx restart`重启服务器，这将使 Pagekit 认为 Apache 的重写模式已经打开，这反过来又会全面激活漂亮的 URL。完整的服务器配置示例如下:

```
server {
    listen 80;
    listen 443 ssl;
    server_name pagekit.app;
    root "/home/vagrant/Code/pagekit";

    index index.html index.htm index.php;

    charset utf-8;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location = /favicon.ico { access_log off; log_not_found off; }
    location = /robots.txt  { access_log off; log_not_found off; }

    access_log off;
    error_log  /var/log/nginx/pagekit.app-error.log error;

    sendfile off;

    client_max_body_size 100m;

    location ~ \.php$ {
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        fastcgi_pass unix:/var/run/php/php7.0-fpm.sock;
        fastcgi_index index.php;
        include fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_param HTTP_MOD_REWRITE On;
        fastcgi_intercept_errors off;
        fastcgi_buffer_size 16k;
        fastcgi_buffers 4 16k;
        fastcgi_connect_timeout 300;
        fastcgi_send_timeout 300;
        fastcgi_read_timeout 300;
    }

    location ~ /\.ht {
        deny all;
    }
} 
```

## 安全性

奇怪的是，Pagekit 违背了最佳实践，将应用程序的入口点(`index.php`)放入根文件夹，而不是`public`子文件夹。在 Nginx(以及不读取本地`.htaccess`文件的 Apache)上，这有严重的安全隐患，因为它允许任何人通过地址栏访问关键文件(如`pagekit.db`——网站的整个数据库)，只需输入`mypagekit.com/pagekit.db`。

为了解决这个问题，我们需要用一行相当原始的代码进一步修改服务器脚本，这些代码几乎一字不差地取自他们的`.htaccess`文件。在`location ~ \.php$ {`上方添加以下内容:

```
location ~ /(composer.lock|pagekit.db|installed.json|composer.json|package.json|bower.json|CHANGELOG.md|README.md|gulpfile.js|webpack.config.js|pagekit) 
{ 
        deny all; 
} 
```

![403 error on forbidden files](img/f9ad17fa38b1298fec51552f24530ad7.png)

这是一个非常原始和老派的解决方案，但它很有效。人们只能希望 Pagekit 团队在不久的将来会离开这种文件夹结构。

## 部署 Pagekit 站点

部署 Pagekit 站点就像上传静态站点一样简单，因为它默认为数据库的 SQLite，并且数据库文件保存在同一个文件夹中。你可以在这里找到完整的指南，使用 Laravel Forge 和原始的 DigitalOcean + Github 方法，所以你可以选择！

## 结论

乍一看，Pagekit 似乎是其他 CMS 的一个不错的选择。用户界面非常棒，他们的开发者生态系统非常友好，他们的插件系统构建得非常好，而且经久耐用。它附带了一些漂亮的默认功能，如帖子复制、降价、UI 中的 VueJS，为网站和博客/评论方面提供了足够的默认设置，当前设置中缺少的一切都可以很容易地通过扩展添加进来。

然而，它并非没有缺点。

首先，它的安全性取决于某人设置服务器的好坏这一事实意味着，如果不进行大量修改，它就不会像应该的那样跨平台——我们都知道 Nginx 和 Apache 配置文件不是最直观的编辑文件。此外，内置的 PHP 服务器不能真正用于测试它，这限制了在某些机器上的“玩耍”因素，并且在为生产证明 PHP 原生服务器做好准备方面肯定削弱了应用程序，如果它曾经被开发的话。Pagekit 团队绝对应该加入一些 PHP 端的安全检查，甚至不要试图使用`.htaccess`文件。

其次，此时没有办法从数据库中导出数据。这使得一旦用户厌倦了 SQLite，将应用程序移植到 MySQL 变得不切实际，并限制了内容的可移植性——想象一下，在 SQLite 中设置好一切并在本地编写帖子，然后将数据上传到使用 MySQL 的实时应用程序中。

最后，我们还可以列出无法设置完全自定义的存储(目前只在本地保存静态文件——尽管这可以通过扩展来实现)和会话生存期短(可以通过简单的 keepalive ping 来解决)，但这些确实都是小问题。

市场仍然很空，但我毫不怀疑一旦球开始滚动，它会很快被填满——我们即将推出的教程肯定会对此有所帮助——但它是“WordPress 杀手”吗？还没有。它当然比 Ghost 这样的伪装者更有潜力，但它需要努力完善 MVP，而不是为了取悦观众而删除 MVP 中的 M。我们需要一个可以轻松扩展的功能较少的 CMS，但是它在默认状态下可以很好地工作。

你试过 Pagekit 吗？你对此有何感想？请在评论中告诉我们！

## 分享这篇文章