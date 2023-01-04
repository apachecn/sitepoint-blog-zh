# 利用 Redis 缓存加速现有应用

> 原文：<https://www.sitepoint.com/speeding-up-existing-apps-with-a-redis-cache/>

在之前，我们已经介绍了 PHP 中 Redis 的基础知识[，但是现在是时候介绍一个真实的用例了。在本教程中，我们将把它添加到一个已经在](https://www.sitepoint.com/an-introduction-to-redis-in-php-using-predis/)[部署的应用程序](https://www.sitepoint.com/deploying-php-apps-digitalocean-dploy-io/)中，让这个应用程序看起来速度很快。

![art6ver1-01](img/6c301bdf6d830a36a29aa6542ce55f6a.png)

你可以通过克隆应用程序的 [0.6 版本来轻松跟进。](https://github.com/Swader/diffbot-sp-search/releases/tag/0.6)

## 问题是

在应用解决方案之前，我们需要对问题有一个清晰的定义。

[正在讨论的应用程序](http://search.sitepoint.tools)在执行查询时，运行到 [Diffbot 的 API](https://www.sitepoint.com/turning-crawled-website-search-engine-php/) 并使其查询数据集。然后返回并显示该子集。这可能需要 5 秒左右的时间，取决于 Diffbot 服务器的繁忙程度。虽然随着他们扩展计算能力，情况无疑会有所改善，但如果曾经执行过的查询能够被记住并在 24 小时内重用就好了，因为集合只是那么频繁地刷新。

"但是缓存单个查询有什么好处呢？"你可能会想。这不像大多数人会经常搜索同一个东西。

嗯……事实上，不仅有研究表明他们会经常搜索同一个东西(React 是趋势？“反应”查询的突然涌入)，他们也将非常可靠地搜索多产作者(或他们自己)。考虑到实现这个缓存实际上不花费我们任何成本(实际上通过减少服务器的压力降低了成本)，添加它是一个容易的胜利，即使它没有像人们希望的那样经常使用。没有理由不加它(T1)，只有(T3)对我们有利，它才能(T2)。

清楚地定义了问题之后，让我们来处理先决条件。

## 安装

首先，我们需要将 Redis 安装到开发和生产环境中(注意，如果您使用 Homestead 进行本地开发，Redis 已经安装了，尽管在撰写本文时是 3.0.1 版)。

我们可以通过操作系统的包管理器这样做:

```
sudo apt-get install redis-server
```

这是最简单和推荐的方法，但是我们也可以从头开始安装并手动配置它。根据[网站](http://redis.io/download#installation)上的说明，这是通过以下方式完成的:

```
sudo apt-get install gcc make build-essential tcl
wget http://download.redis.io/releases/redis-3.0.2.tar.gz
tar xzf redis-3.0.2.tar.gz
cd redis-3.0.2
make
make test
sudo make install
```

如果你在运行`make`后遇到致命错误提到`jemalloc.h`，只需运行`make distclean`，然后再次运行`make`。`make test`命令是可选的，但是很有用。

*注意:如果你正在读这篇文章，而版本 3.0.2 已经不是最新的了，只需将命令修改为最新的版本号。*

为了防止一些常见的警告(至少在 Ubuntu 上)，我们还预防性地运行了以下命令:

```
sudo sh -c 'echo "vm.overcommit_memory=1" >> /etc/sysctl.conf'
sudo sh -c 'echo "net.core.somaxconn=65535" >> /etc/sysctl.conf'
sudo sh -c 'echo "never" > /sys/kernel/mm/transparent_hugepage/enabled'
```

我们还确保最后一个命令被添加到`/etc/rc.local`中，正好在`exit 0`的上面，这样它会在每次服务器重启时重新发出。最后，我们可以用`sudo reboot`重启服务器，用`sudo redis-server`运行 Redis 检查是否一切正常。

最后，我们需要[确保 Redis 在服务器重启](http://redis.io/topics/quickstart#installing-redis-more-properly)后启动，所以我们按照他们的官方指示来完成。

## 预测

我们之前讨论过 Predis 的基础知识，我们也将在这个案例中使用它。让我们安装它:

```
composer require predis/predis
```

更进一步，假设我们已经从前面提到的 Predis 介绍中吸收了知识。

自从那篇文章发表以来，引入了一些小的差异(比如向名称空间的过渡)，但是我们需要的 API 或多或少是相同的。

## 履行

要在我们的应用程序中使用 Redis，我们需要遵循以下步骤:

*   查看当前查询的结果是否存在于缓存中
*   如果是，抓住他们
*   如果没有，获取它们，存储它们，转发给应用程序的其余部分

因此，实现非常简单:在“form submitted”检查下(查找“search”参数的检查)，我们实例化 Predis 客户机，计算执行的搜索查询的 md5 散列，然后检查它的结果是否已经被缓存。如果为 false，则之前的流程将继续，而不是结束于:

```
$result = ...
$info = ...
```

我们将结果序列化并直接保存到缓存中。然后，在程序块之外，我们立即从缓存中获取它们，应用程序的流程照常继续。因此,`index.php`文件中被修改的部分如下所示:

```
// Check if the search form was submitted
if (isset($queryParams['search'])) {

    $redis = new Client();
    $hash = md5($_SERVER['QUERY_STRING']);
    if (!$redis->get($hash . '-results')) {

        $diffbot = new Diffbot(DIFFBOT_TOKEN);

        // Building the search string
        $searchHelper = new SearchHelper();
        $string = (isset($queryParams['q']) && !empty($queryParams['q']))
            ? $queryParams['q']
            : $searchHelper->stringFromParams($queryParams);

        // Basics
        $search = $diffbot
            ->search($string)
            ->setCol('sp_search')
            ->setStart(($queryParams['page'] - 1) * $resultsPerPage)
            ->setNum($resultsPerPage);

        $redis->set($hash . '-results', serialize($search->call()));
        $redis->expire($hash . '-results', 86400);
        $redis->set($hash . '-info', serialize($search->call(true)));
        $redis->expire($hash . '-info', 86400);
    }

    $results = unserialize($redis->get($hash . '-results'));
    $info = unserialize($redis->get($hash . '-info'));
```

经过测试，我们可以看到它的工作原理非常神奇——如果我们刷新页面，执行一次的查询是即时的，或者执行另一个查询，然后返回到上一个查询。最后，我们可以添加、提交和推动部署:

```
git add -A
git commit -m "Added Redis cache [deploy:production]"
git push origin master
```

就是这样！我们的应用程序的最新版本现已上线，Redis 正在提供缓存数据。在这里测试一下！

*注意:如果你想知道我们如何在一次提交中从开发模式进入生产部署，你应该读一下[这个](https://www.sitepoint.com/deploying-php-apps-digitalocean-dploy-io/)。*

## 微调

为了进一步提升性能，Predis 建议安装 [phpiredis](https://github.com/nrk/phpiredis) ，这是对“*的 PHP 扩展，以降低序列化和解析 redis 协议*的开销。既然我们已经完全控制了服务器，为什么不呢？

```
cd ~
git clone https://github.com/redis/hiredis
cd hiredis
make
sudo make install
cd ~
git clone https://github.com/nrk/phpiredis
cd phpiredis
phpize && ./configure --enable-phpiredis
make
sudo make install

sudo touch /etc/php5/mods-available/phpiredis.ini
sudo sh -c 'echo "extension=phpiredis.so" > /etc/php5/mods-available/phpiredis.ini'
sudo php5enmod phpiredis
sudo service php5-fpm restart
```

这安装了先决条件并启用了扩展。现在我们要做的就是配置 Predis 客户端来使用 phpiredis 连接。我们需要更换:

```
$redis = new Client();
```

随着

```
$redis = new Client('tcp://127.0.0.1', [
        'connections' => [
            'tcp'  => 'Predis\Connection\PhpiredisStreamConnection',
            'unix' => 'Predis\Connection\PhpiredisSocketConnection',
        ],
    ]);
```

就是这样！我们的 Redis 安装现在更快了！

## 结论

在本教程中，我们将 Redis 与 Predis 库结合使用，使已经部署的应用程序看起来速度很快。我们利用 DigitalOcean droplet 的可用 RAM 每天保存一次查询结果，然后从缓存中返回这些结果，而不是往返于它们的来源。这确实意味着结果并不总是新鲜的，但是根据[这篇文章](https://www.sitepoint.com/crawling-searching-entire-domains-diffbot/)，结果并不会比那更频繁地被刷新。

希望本教程向您展示了在应用程序中添加内存缓存层是多么简单，并且在您需要缩短加载时间和降低服务器成本时会很有用。

还有其他建议吗？小贴士？评论？把它们留在下面！

## 分享这篇文章