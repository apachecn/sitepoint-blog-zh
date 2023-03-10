# Apache 与 Nginx 性能:优化技术

> 原文：<https://www.sitepoint.com/apache-vs-nginx-performance-optimization-techniques/>

几年前，[阿帕奇基金会的网络服务器](https://httpd.apache.org/)，简称为“阿帕奇”，是如此的无处不在，以至于它成了“网络服务器”的同义词。它在 Linux 系统上的守护进程名为 *httpd* (简单地说就是 *http 进程*)——并且预装在主要的 Linux 发行版中。

它最初发布于 1995 年，引用维基百科、*的话来说，“它在万维网的最初发展中发挥了关键作用”*。据 W3techs 报道，它仍然是最常用的网络服务器软件[。然而，根据这些报告显示的过去十年的趋势以及与其他解决方案的比较，其市场份额正在下降。由](https://w3techs.com/blog/entry/fact_20170828) [Netcraft](https://news.netcraft.com/archives/2017/09/11/september-2017-web-server-survey.html) 和 [Builtwith](https://trends.builtwith.com/web-server) 给出的报告略有不同，但都同意 Apache 的市场份额呈下降趋势，Nginx 呈增长趋势。

Nginx——发音为*引擎 x*——由[伊戈尔·塞索耶夫](https://www.wikiwand.com/en/Igor_Sysoev)于 2004 年发布，其明确意图是超越阿帕奇。Nginx 的网站上有[一篇值得一读的文章](https://www.nginx.com/blog/nginx-vs-apache-our-view/)比较了这两种技术。起初，它主要是作为 Apache 的补充，主要用于服务静态文件，但它一直在稳步增长，因为它已经发展到可以处理所有的 web 服务器任务。

它经常被用作[反向代理](https://www.nginx.com/resources/admin-guide/reverse-proxy/)、[负载平衡器](http://nginx.org/en/docs/http/load_balancing.html)，以及 [HTTP 缓存](https://www.nginx.com/blog/nginx-caching-guide/)。cdn 和视频流提供商使用它来构建性能至关重要的内容交付系统。

Apache 已经存在很久了，它有一个[大的模块选择](https://www.wikiwand.com/en/List_of_Apache_modules)。众所周知，管理 Apache 服务器是用户友好的。[动态模块加载](http://howtolamp.com/lamp/httpd/2.4/dso/)允许编译不同的模块并添加到 Apache 堆栈中，而无需重新编译主服务器二进制文件。通常，模块会在 Linux 发行版的存储库中，在通过系统包管理器安装它们之后，可以使用类似 a2enmod 的命令将它们优雅地添加到堆栈中。Nginx 还没有这种灵活性。当我们查看为 HTTP/2 设置 Nginx 的[指南时，模块是 Nginx 需要构建的——在构建时配置。](http://nginx.org/en/docs/http/ngx_http_v2_module.html)

对 Apache 的市场规则有贡献的另一个特性是[。htaccess 文件](http://www.htaccess-guide.com/)。它是 Apache 的银弹，这使它成为共享主机环境的首选解决方案，因为它允许在目录级别上控制服务器配置。Apache 服务的服务器上的每个目录都可以有自己的`.htaccess`文件。

Nginx 不仅没有等价的解决方案，而且[不鼓励](https://www.nginx.com/resources/wiki/start/topics/examples/likeapache-htaccess/)这样使用，因为这会影响性能。

![Server share stats, by Netcraft](img/e2e36a973034422a5ec0e2518c57bb3a.png)

【1995 年至 2005 年服务器厂商的市场份额。[数据由 Netcraft](http://www.netcraft.com/) 提供

[LiteSpeed](https://www.litespeedtech.com/products/litespeed-web-server) ，或 LSWS，是一个服务器竞争者，其灵活性水平可以与 Apache 相媲美，同时又不牺牲性能。它支持 Apache 风格的`.htaccess`、`mod_security`和`mod_rewrite`，对于共享设置值得考虑。它计划作为 Apache 的替代产品，可以与 cPanel 和 Plesk 一起使用。从 2015 年就开始支持 HTTP/2 了。

LiteSpeed 有三个许可证等级，OpenLiteSpeed，LSWS 标准和 LSWS 企业。标准版和企业版[带有一个可选的](https://www.interserver.net/tips/kb/litespeed-cache-lscache-details-advantages/)缓存解决方案，可与 Varnish、LSCache、[相媲美，它内置于服务器本身](https://www.litespeedtech.com/support/wiki/doku.php/litespeed_wiki:cache)中，可以通过重写规则在`.htaccess`文件(每个目录)中进行控制。[它还带有一些内置于](https://www.litespeedtech.com/products/litespeed-web-server/features/anti-ddos-advances)的 DDOS 缓解“电池”。这一点，加上其事件驱动的架构，使其成为一个坚实的竞争者，主要针对[性能导向的主机提供商](https://www.a2hosting.com/litespeed-hosting)，但即使对于较小的服务器或网站，它也是值得建立的。

## 硬件注意事项

在优化我们的系统时，我们必须充分重视我们的硬件设置。无论我们选择哪种解决方案，拥有足够的 RAM 都是至关重要的。当一个 web 服务器进程或者像 PHP 这样的解释器没有足够的 RAM 时，它们就开始交换，而交换实际上意味着使用硬盘来补充 RAM 内存。其结果是每次访问该存储器时延迟增加。这就把我们带到了第二点——硬盘空间。使用快速固态硬盘存储是我们网站速度的另一个关键因素。我们还需要注意 CPU 的可用性，以及服务器数据中心到目标受众的物理距离。

为了更深入地了解性能调优的硬件方面， [Dropbox 有一篇好文章](https://blogs.dropbox.com/tech/2017/09/optimizing-web-servers-for-high-throughput-and-low-latency/)。

## 监视

详细监控我们当前服务器堆栈性能的一个实用方法是 [htop](http://hisham.hm/htop/) ，它在 Linux、Unix 和 macOS 上运行，并为我们提供了一个彩色的进程概览。

![HTOP](img/2d42b528473ba22d6f4299657367de00.png)

其他监控工具有 [New Relic](https://newrelic.com/) ，这是一个具有一套全面工具的高级解决方案，还有 [Netdata](https://my-netdata.io/) ，这是一个开源解决方案，它提供了强大的可扩展性、细粒度的指标和可定制的 web 仪表板，适用于小型 VPS 系统和监控服务器网络。它可以通过电子邮件、Slack、pushbullet、电报、Twilio 等为任何应用程序或系统进程发送警报。

![Netdata dashboard](img/eea293606893af9b05d5bb3dfd5241d2.png)

**[Monit](https://www.cyberciti.biz/faq/how-to-install-and-use-monit-on-ubuntudebian-linux-server/)** 是另一个无头的开源工具，可以监控系统，可以配置为提醒我们，或者重启某些进程，或者在满足某些条件时重启系统。

## 测试系统

AB——Apache Benchmark——是 Apache 基金会的一个简单的负载测试工具，而[围攻](https://www.joedog.org/siege-home/)是另一个负载测试程序。这篇[文章](https://kalamuna.atlassian.net/wiki/spaces/KALA/pages/16023587/Testing+With+Apache+Benchmark+and+Siege)解释了如何设置它们，这里[这里](https://blog.getpolymorph.com/7-tips-for-heavy-load-testing-with-apache-bench-b1127916b7b6)我们有一些关于 AB 的更高级的提示，而对围攻的深入研究可以在这里找到[。](https://www.sitepoint.com/web-app-performance-testing-siege-plan-test-learn/)

如果你更喜欢 web 界面，有一个基于 Python 的工具 [Locust](https://locust.io/) ，可以非常方便地测试网站性能。

![Locust installation](img/8649a839b3c5dd8c05d4f32af047e490.png)

在我们安装了 Locust 之后，我们需要[在我们将要启动它的目录中创建一个 locustfile](https://docs.locust.io/en/latest/writing-a-locustfile.html) :

```
from locust import HttpLocust, TaskSet, task

class UserBehavior(TaskSet):
    @task(1)
    def index(self):
        self.client.get("/")

    @task(2)
    def shop(self):
        self.client.get("/?page_id=5")

    @task(3)
    def page(self):
        self.client.get("/?page_id=2")

class WebsiteUser(HttpLocust):
    task_set = UserBehavior
    min_wait = 300
    max_wait = 3000 
```

然后，我们只需从命令行启动它:

```
locust --host=https://my-website.com 
```

这些负载测试工具的一个警告是:它们具有 DDoS 攻击的效果，所以建议您将测试限制在自己的网站上。

## 调优 Apache

### Apache 的 mpm 模块

Apache 可以追溯到 1995 年和互联网的早期，当时服务器的一种可接受的操作方式是在每个传入的 TCP 连接上产生一个新的进程并回复它。如果有更多的连接进来，就会创建更多的工作进程来处理它们。产生新进程的成本很高，Apache 开发人员设计了一种 *prefork* 模式，其中预先产生了一些进程。每个进程中嵌入的动态语言解释器(如 [mod_php](https://stackoverflow.com/questions/2712825/what-is-mod-php) )仍然很昂贵，而且在 Apache 的默认设置下[服务器崩溃](https://serverfault.com/questions/823121/why-is-apache-spawning-so-many-processes/823162)变得很常见。每个进程只能处理一个传入的连接。

这个模型在阿帕奇的 [MPM](https://httpd.apache.org/docs/trunk/mpm.html#dynamic) (多处理模块)系统内被称为*[MPM _ prefork _ Module](http://httpd.apache.org/docs/2.4/mod/prefork.html)*。[根据 Apache 的网站](http://httpd.apache.org/docs/2.4/mod/prefork.html)，这种模式需要很少的配置，因为它是自我调节的，而且*最重要的是`MaxRequestWorkers`指令足够大，可以处理你期望接收的尽可能多的同时请求，但又足够小，以确保有足够的物理 RAM 用于所有进程*。

![libapache2-mod-php7 mpm_prefork HTOP report](img/8c472311a01b87cf066d250a45238ae1.png)

*一个小型的蝗虫负载测试显示了大量 Apache 进程的产生，以处理传入的流量。*

我们可以补充说，这种模式可能是 Apache 坏名声的最大原因。这可能会导致资源效率低下。

Apache 的第 2 版带来了另外两个 MPM，试图解决 *prefork* 模式存在的问题。这些是 [*工人模块*](http://httpd.apache.org/docs/2.4/mod/worker.html) ，或者 *mpm_worker_module* ，以及 [*事件模块*](http://httpd.apache.org/docs/2.4/mod/event.html) 。

工作模块不再基于流程；这是一种基于混合进程线程的操作模式。引用[阿帕奇的网站](http://httpd.apache.org/docs/2.4/mod/worker.html)，

> 单个控制流程(父流程)负责启动子流程。每个子进程创建一个固定数量的服务器线程，如在`ThreadsPerChild`指令中指定的，以及一个监听线程，监听连接并在连接到达时将它们传递给服务器线程进行处理。

这种模式更节省资源。

2.4 版本的 Apache 为我们带来了第三个 MPM——[事件模块](http://httpd.apache.org/docs/2.4/mod/event.html)。它基于工作 MPM，并添加了一个单独的侦听线程，在 HTTP 请求完成后管理休眠的 keepalive 连接。这是一种非阻塞的异步模式，占用内存较少。更多关于 2.4 版本的改进[在这里](https://www.slideshare.net/jimjag/apachecon-2017-whats-new-in-httpd-24)。

我们已经在一个虚拟服务器上加载了一个包含大约 1200 个帖子的 WooCommerce 测试安装，并在 Apache 2.4 上使用默认的 prefork 模式和 mod_php 进行了测试。

首先我们用 https://tools.pingdom.com 上的 [libapache2-mod-php7](https://packages.debian.org/sid/amd64/libapache2-mod-php7.0) 和 mpm_prefork_module 对其进行了测试:

![mpm prefork test](img/8af0d0e87cfc7411562cdcf8c3c38bbf.png)

然后，我们去测试活动 MPM 模块。

我们必须将`multiverse`添加到`/etc/apt/sources.list`中:

```
deb http://archive.ubuntu.com/ubuntu xenial main restricted universe multiverse
deb http://archive.ubuntu.com/ubuntu xenial-updates main restricted universe multiverse
deb http://security.ubuntu.com/ubuntu xenial-security main restricted universe multiverse
deb http://archive.canonical.com/ubuntu xenial partner 
```

然后我们做了`sudo apt-get update`并安装了`libapache2-mod-fastcgi`和 php-fpm:

```
sudo apt-get install libapache2-mod-fastcgi php7.0-fpm 
```

因为 php-fpm 是独立于 Apache 的服务，所以它需要重启:

```
sudo service start php7.0-fpm 
```

然后我们禁用了 prefork 模块，并启用了事件模式和 proxy_fcgi:

```
sudo a2dismod php7.0 mpm_prefork
sudo a2enmod mpm_event proxy_fcgi 
```

我们将这个片段添加到我们的 Apache 虚拟主机中:

```
&lt;filesmatch "\.php$"&gt;
    SetHandler "proxy:fcgi://127.0.0.1:9000/"
&lt;/filesmatch&gt; 
```

这个端口需要和`/etc/php/7.0/fpm/pool.d/www.conf`中的 php-fpm 配置一致。更多关于 php-fpm 设置的信息请点击这里。

然后我们调优了`/etc/apache2/mods-available/mpm_event.conf`中的 mpm_event 配置，记住我们用于这个测试的 mini-VPS 资源是有限的——所以我们仅仅减少了一些默认数字。阿帕奇[网站](http://httpd.apache.org/docs/current/mod/mpm_common.html)上关于每个指令的详细信息，以及针对 mpm [事件的提示在这里](http://httpd.apache.org/docs/2.4/mod/event.html)。请记住，已启动的*服务器*会消耗大量内存，不管它们有多忙。`MaxRequestWorkers`指令对允许的并发请求数量进行了限制:将`MaxConnectionsPerChild`设置为非零值非常重要，因为这可以防止可能的内存泄漏。

```
<ifmodule mpm_event_module>
        StartServers              1
        MinSpareThreads          30
        MaxSpareThreads          75
        ThreadLimit              64
        ThreadsPerChild          30
        MaxRequestWorkers        80
        MaxConnectionsPerChild   80
</ifmodule> 
```

然后我们用`sudo service apache2 restart`重启服务器(如果我们改变一些指令，比如 ThreadLimit，我们将需要用`sudo service apache2 stop; sudo service apache2 start`显式地停止和启动服务)。

我们在 [Pingdom](https://tools.pingdom.com) 上的测试现在显示页面加载时间减少了一半以上:

![Pingdom test](img/6ecb788821ef474168d76136c6c53a9a.png)

### 调优 Apache 的其他技巧:

**禁用。htaccess** : htaccess 允许为我们的服务器根目录中的每个目录设置特定的配置，而无需重启。所以，遍历所有的目录，寻找。每次请求 htaccess 文件都会导致性能下降。

引用自 Apache 文档:

> 一般来说，您应该只在无法访问主服务器配置文件时使用`.htaccess`文件。*
> …一般来说，应该尽可能避免使用`.htaccess`文件。您考虑放在`.htaccess`文件中的任何配置，都可以在您的主服务器配置文件的`<directory>`部分中有效地进行。*

解决方法是在`/etc/apache2/apache2.conf`中禁用它:

```
AllowOverride None 
```

如果特定目录需要它，我们可以在虚拟主机文件的部分启用它:

```
AllowOverride All 
```

更多提示包括:

*   **[用 mod_expires](http://httpd.apache.org/docs/current/mod/mod_expires.html)** 控制浏览器缓存——通过设置 expires 头。

*   保持**主机名查找**关闭— `HostNameLookups Off`是 Apache 1.3 以后的默认设置，但是要确保它保持关闭，因为它会导致性能下降。

*   **Apache2buddy** 是一个简单的脚本，我们可以运行它并获得调优系统的提示:`curl -sL https://raw.githubusercontent.com/richardforth/apache2buddy/master/apache2buddy.pl | perl`

![apache2buddy](img/33c57c375e98e1f44d244acead4129ff.png)

## Nginx

Nginx 是一个[事件驱动](https://www.nginx.com/blog/inside-nginx-how-we-designed-for-performance-scale/)的非阻塞 web 服务器。引用黑客新闻上的一张海报[，](https://news.ycombinator.com/item?id=8343350)

> 与事件循环相比，分叉流程的成本高得令人难以置信。基于事件的 HTTP 服务器必然会胜出。

这一说法在黑客新闻上引发了相当大的争论，但从我们的经验来看，仅仅从 mpm_prefork Apache 切换到 Nginx 通常就意味着挽救网站免于崩溃。简单地切换到 Nginx 本身通常是一种治疗方法。

![Nginx architecture](img/417bf18b9abd6c8d67c5bd13b5c6eda0.png)

关于 Nginx 架构更全面的直观解释可以在[这里](https://www.nginx.com/resources/library/infographic-inside-nginx/)找到。

### Nginx settings

Nginx 建议通过在`/etc/nginx/nginx.conf`中将`worker_processes`设置为`auto`(默认为 1)，将工作线程的数量与 PC 内核的数量绑定在一起(就像我们对 Apache 的 mpm_event 配置所做的那样)。

`worker_connections`设置每个工作进程可以处理的连接数。默认值为 512，但通常可以增加。

**[Keepalive 连接](https://en.wikipedia.org/wiki/Keepalive)** 是影响性能的一个服务器方面，[在基准测试](https://www.nginx.com/blog/http-keepalives-and-web-performance/)中通常是看不到的。

![keepalive.cf](img/0618b53be974d3ee1a8cb1b893800330.png)

[据 Nginx 网站](https://www.nginx.com/blog/http-keepalives-and-web-performance/)，

> HTTP keepalive 连接是一个必要的性能特性，它可以减少延迟并允许网页更快地加载。

建立新的 TCP 连接[可能会很昂贵](https://en.wikipedia.org/wiki/Handshaking)——更不用说当涉及到 *HTTPS* 加密的时候。 [HTTP/2](https://http2.github.io/) 协议通过其[复用特性](https://en.wikipedia.org/wiki/Multiplexing)缓解了这一问题。重用现有连接可以减少请求时间。

Apache 的 mpm_prefork 和 mpm_worker 受到并发性限制，这与 keepalive 事件循环形成对比。这在 Apache 2.4 的 mpm_event 模块中已经得到了一定程度的修复，并且是 Nginx 中唯一的默认操作模式。Nginx workers 可以同时处理数千个传入的连接，如果它被用作反向代理或负载平衡器，Nginx 就会使用本地的 keepalive 连接池，而没有 TCP 连接开销。

[`keepalive_requests`](http://nginx.org/en/docs/http/ngx_http_core_module.html?&_ga=2.26969269.942121935.1510206018-994710012.1508256997#keepalive_requests) 是一种设置，用于控制客户端可以通过单个保活连接发出的请求数量。
[`keepalive_timeout`](http://nginx.org/en/docs/http/ngx_http_core_module.html?&_ga=2.191644834.942121935.1510206018-994710012.1508256997#keepalive_timeout) 设置空闲保活连接保持打开的时间。

[`keepalive`](http://nginx.org/en/docs/http/ngx_http_upstream_module.html?&_ga=2.203640216.942121935.1510206018-994710012.1508256997#keepalive) 是与 Nginx 到上游服务器的连接相关的设置——当它充当代理或负载平衡器时。这意味着每个工作进程的空闲 keepalive 上游连接数。

启用上游保持活动连接需要将这些指令放入 Nginx 主配置中:

```
proxy_http_version 1.1;
proxy_set_header Connection ""; 
```

Nginx 上行连接由 [ngx_http_upstream_module](http://nginx.org/en/docs/http/ngx_http_upstream_module.html) 管理。

如果我们的前端应用程序不断轮询我们的后端应用程序的更新，增加`keepalive_requests`和`keepalive_timeout`将会限制需要建立的连接数。`keepalive`指令不应该太大，以允许其他连接到达我们的上游服务器。

这些设置的调整是基于具体情况进行的，并且需要进行测试。这可能是为什么`keepalive`没有默认设置的一个原因。

#### 使用 unix 套接字

默认情况下，Nginx 使用一个单独的 PHP 进程来转发 PHP 文件请求。在这种情况下，它充当代理(就像我们用 php7.0-fpm 设置 Apache 一样)。

通常，我们使用 Nginx 的虚拟主机设置如下所示:

```
location ~ \.php$ {
    fastcgi_param REQUEST_METHOD $request_method;
    fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
    fastcgi_pass 127.0.0.1:9000;
} 
```

由于 FastCGI 是与 HTTP 不同的协议，前两行将一些参数和头转发给 php-fpm，而第三行指定了代理我们的请求的方式——通过本地网络套接字。

这对于多服务器设置很实用，因为我们还可以指定远程服务器来代理请求。

但是，如果我们将整个设置托管在一个系统上，我们应该使用 Unix 套接字连接到监听 php 进程:

```
fastcgi_pass unix:/var/run/php7.0-fpm.sock; 
```

Unix 套接字[被认为比 TCP](https://stackoverflow.com/questions/257433/postgresql-unix-domain-sockets-vs-tcp-sockets/257479#257479) 具有更好的性能，并且这种设置被认为更安全。你可以在 Rackspace 的这篇[文章中找到更多关于这个设置的细节。](https://support.rackspace.com/how-to/install-nginx-and-php-fpm-running-on-unix-file-sockets/)

这个关于 Unix 套接字的技巧也适用于 Apache。更多详情[此处](https://wiki.apache.org/httpd/PHP-FPM)。

**gzip_static** :关于 web 服务器性能的公认观点是压缩我们的静态资产。这通常意味着我们将尝试妥协，尝试只压缩高于某个阈值的文件，因为对于每个请求，动态压缩资源可能是昂贵的。Nginx 有一个`gzip_static`指令，允许我们提供带扩展名的 gzipped 文件版本。广州——代替经常资源:

```
location /assets {
    gzip_static on;
} 
```

这样，Nginx 将尝试服务于`style.css.gz`而不是`style.css`(在这种情况下，我们需要自己处理 gzipping)。

这样，每个请求的动态压缩就不会浪费 CPU 周期。

### 使用 Nginx 缓存

如果不提到如何缓存内容，Nginx 的故事就不完整。Nginx 缓存是如此的高效，以至于许多系统管理员都不认为为 [HTTP 缓存](https://en.wikipedia.org/wiki/Web_cache)——像[Varnish](https://varnish-cache.org/)——设置单独的层有多大意义。也许它没有那么精致，但是*T5 简约是它的一个特点。使用 Nginx 启用缓存相当简单。*

```
proxy_cache_path /path/to/cache levels=1:2 keys_zone=my_cache:10m max_size=10g
  inactive=60m; 
```

这是一个我们放在虚拟主机文件中的指令，**在`server`块的**之外。`proxy_cache_path`参数可以是我们想要存储缓存的任何路径。`levels`指定 Nginx 应该在多少级目录中存储缓存内容。出于性能原因，两个级别通常是可以的。遍历目录的代价可能很高。`keys_zone`参数是一个用于存储缓存键的共享内存区域的名称，而`10m`是内存中用于这些键的空间(10MB 通常就足够了；这不是实际缓存内容的空间)。`max_size`是可选的，设置缓存内容的上限—这里是 10GB。如果不指定，它将占用所有可用空间。`inactive`指定内容在被 Nginx 删除之前，可以在缓存中保留多长时间而不被请求。

设置好之后，我们将添加下面一行，将我们的内存区域的名称添加到`server`或`location`块中:

```
proxy_cache my_cache; 
```

Nginx 的一个额外的容错层可以通过告诉它当它在源服务器或上游服务器上遇到服务器错误时，或者当服务器关闭时，从缓存中提供项目来实现:

```
proxy_cache_use_stale error timeout http_500 http_502 http_503 http_504; 
```

关于进一步调整 Nginx 缓存的`server`或`location`块指令的更多细节可以在[这里](https://www.nginx.com/blog/nginx-caching-guide/)找到。

指令是针对静态资产的，但我们通常希望缓存 web 应用程序的动态输出——无论是 CMS 还是其他什么。在这种情况下，我们将使用`fastcgi_cache_*`指令，而不是`proxy_cache_*`:

```
 fastcgi_cache_path /var/run/nginx-cache levels=1:2 keys_zone=my_cache:10m inactive=60m;
 fastcgi_cache_key "$scheme$request_method$host$request_uri";
 fastcgi_cache_use_stale error timeout invalid_header http_500;
 fastcgi_ignore_headers Cache-Control Expires Set-Cookie;
 add_header NGINX_FASTCGI_CACHE $upstream_cache_status; 
```

上面的最后一行将设置响应头，通知我们内容是否是从缓存中提交的。

然后，在我们的服务器或位置块中，我们可以为缓存设置一些例外—例如，当查询字符串出现在请求 URL 中时:

```
if ($query_string != "") {
    set $skip_cache 1;
} 
```

同样，在我们的`\.php`块中，在`server`内，如果是 PHP，我们将添加如下内容:

```
location ~ \.php$ {
    try_files $uri =404;
    include fastcgi_params;

    fastcgi_read_timeout 360s;
    fastcgi_buffer_size 128k;
    fastcgi_buffers 4 256k;
    fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;

    fastcgi_pass unix:/run/php/php7.0-fpm.sock;

      fastcgi_index index.php;
      fastcgi_cache_bypass $skip_cache;
      fastcgi_no_cache $skip_cache;
      fastcgi_cache my_cache;
      fastcgi_cache_valid  60m;
} 
```

上面的`fastcgi_cache*`行和`fastcgi_no_cache`行管理缓存和排除。
所有这些指令的详细参考可以在 Nginx docs 网站找到[。](http://nginx.org/en/docs/dirindex.html)

为了了解更多，Nginx 的人提供了一个关于这个主题的免费网络研讨会，还有一些电子书可以在[找到。](https://www.nginx.com/resources/library/)

## 结论

我们试图引入一些技术来帮助我们提高 web 服务器的性能，以及这些技术背后的理论。但是这个主题并没有穷尽:我们还没有讨论由 Apache 和 Nginx 组成的反向代理设置，或者多服务器设置。使用这两种服务器实现最佳结果是一个测试和分析具体的真实案例的问题。这是一个永无止境的话题。

## 分享这篇文章