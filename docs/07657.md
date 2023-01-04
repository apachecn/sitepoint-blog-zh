# 更好的性能——加速你的 CakePHP 网站

> 原文：<https://www.sitepoint.com/speeding-up-your-cakephp-websites/>

似乎每当我向开发者社区提到 CakePHP，那些没有用过的人都认为它是一个缓慢的框架。事实上，根据许多基准测试的结果，它不是最快的——即开箱即用——但它在快速应用程序开发中肯定弥补了性能上的不足。

通过应用一些简单的修改，甚至一些更复杂的增强，CakePHP 可以加速很多。当你完成了这些变化的一半时，你的 CakePHP 站点的性能将可以与许多其他流行的 PHP 框架相媲美，其优点是你的开发速度永远不会下降！

我将在下一篇文章中描述两种类型的修改。第一个是代码变更，例如，这些将适用于任何人，即使你运行在一个共享的托管环境中。第二种类型面向拥有自己的专用或虚拟服务器的用户，他们可以根据需要添加和删除软件。

不过不要害怕，如果你能跟上第一盘，你不会失望的。

## 升级 CakePHP 版本

值得注意的是，人们对 CakePHP 速度的误解可能可以追溯到 1.1 或 1.2 版本。在 1.3 版本中，核心开发团队对 CakePHP 的底层架构进行了一次重大改革。事实上，我进行了几次基准测试，比较 CakePHP 版、1.3 版和 2.0 版。结果令人吃惊。通过简单地从 1.2 升级到 1.3，我发现加载一个页面的平均时间立即减少了 70%以上。

如果你一直在延迟升级你的 CakePHP 版本，并且你仍然在使用 1.2 或更低版本，你应该做的第一件事是至少升级到 1.3 版本，但是最好是 2.3.x 版本会更好。

CakePHP 指南提供了一些非常详细的迁移指南:

*   [从 CakePHP 1.2 迁移到 1.3](http://book.cakephp.org/2.0/en/appendices/migrating-from-cakephp-1-2-to-1-3.html "Migrating from CakePHP 1.2 to 1.3 - CakePHP Cookbook v2.x documentation")
*   [2.0 迁移指南](http://book.cakephp.org/2.0/en/appendices/2-0-migration-guide.html "2.0 Migration Guide - CakePHP Cookbook v2.x documentation")
*   [2.1 迁移指南](http://book.cakephp.org/2.0/en/appendices/2-1-migration-guide.html "2.1 Migration Guide - CakePHP Cookbook v2.x documentation")
*   [2.2 迁移指南](http://book.cakephp.org/2.0/en/appendices/2-2-migration-guide.html "2.2 Migration Guide - CakePHP Cookbook v2.x documentation")
*   [2.3 迁移指南](http://book.cakephp.org/2.0/en/appendices/2-3-migration-guide.html "2.3 Migration Guide - CakePHP Cookbook v2.x documentation")

## 禁用调试模式

不要笑！这似乎是显而易见的，但是在疯狂地将代码转移到产品中时，很容易忘记关闭调试。我遵循的典型设置是创建多个 app/Config/core.php 文件:一个用于我的本地环境，一个用于开发，一个用于试运行，一个用于生产。这些文件包含基于目标环境的不同设置。

要更改的关键语句是`Configure::write('debug', 2)`到`Configure::write('debug', 0)`。

该更改隐藏了所有错误消息，并且不再刷新模型缓存。这一点非常重要，因为在默认情况下，每次页面加载都会导致您的所有模型动态生成，而不是从第一次页面加载开始缓存。

## 禁用递归查找语句

当您使用`find()`方法执行查询时，默认情况下递归设置为 0。这表明 CakePHP 应该尝试加入任何一级相关模型。例如，如果您有一个包含许多用户注释的用户模型，那么每次您在 users 表上执行查询时，它也会加入 comments 表。

执行、返回和用这些数据创建一个关联数组的处理时间可能很长，我实际上已经看到 CakePHP 站点因为这个而崩溃了！

我确保默认递归为 none 的首选方法是通过添加以下代码来覆盖`app/Model/AppModel.php`中的设置:

```
<?php
class AppModel extends Model {
    public $recursive = -1;
}
```

## 缓存查询结果

这确实是我最喜欢的优化。我愿意认为是我自己发现了这个问题，但是我确信这将会引起争论，因为我已经看到其他文章讨论了类似的解决方案。

在许多 web 应用程序中，可能有许多查询会发送到数据库，而这些查询并不一定需要发送。通过在`app/Model/AppModel.php`中覆盖默认的`find()`函数，我可以很容易地缓存查询的完整关联数组结果。这意味着我不仅避免了访问数据库，甚至避免了 CakePHP 将结果转换成数组的处理时间。所需的代码如下:

```
<?php
class AppModel extends Model
{
    public $recursive = -1;

    function find($conditions = null, $fields = array(), $order = null, $recursive = null) {
        $doQuery = true;
        // check if we want the cache
        if (!empty($fields['cache'])) {
            $cacheConfig = null;
            // check if we have specified a custom config
            if (!empty($fields['cacheConfig'])) {
                $cacheConfig = $fields['cacheConfig'];
            }
            $cacheName = $this->name . '-' . $fields['cache'];
            // if so, check if the cache exists
            $data = Cache::read($cacheName, $cacheConfig);
            if ($data == false) {
                $data = parent::find($conditions, $fields,
                    $order, $recursive);
                Cache::write($cacheName, $data, $cacheConfig);
            }
            $doQuery = false;
        }
        if ($doQuery) {
            $data = parent::find($conditions, $fields, $order,
                $recursive);
        }
        return $data;
    }
}
```

需要对我们希望缓存的查询进行细微的更改。看起来像这样的基本查询:

```
<?php
$this->User->find('list');
```

需要更新以包含缓存信息:

```
<?php
$this->User->find('list', 
    array('cache' => 'userList', 'cacheConfig' => 'short')
);
```

添加了两个额外的值:应该使用的缓存名称和缓存配置。

必须对`app/Config/core.php`进行两项最终更改。必须打开缓存，并且必须定义所使用的`cacheConfig`值。首先，取消对以下行的注释:

```
<?php
Configure::write('Cache.check', true);
```

然后，添加一个新的缓存配置，如下所示(根据 name 和 expiry 的需要更新参数):

```
<?php
Cache::config('short', array(
    'engine' => 'File',
    'duration'=> '+5 minutes',
    'probability'=> 100,
    'path' => CACHE,
    'prefix' => 'cache_short_'
));
```

以上所有选项都可以更新，以延长持续时间、更改名称，甚至定义缓存的存储位置。

关于如何添加、更新和清除缓存的更详细的解释，请继续阅读我的博客中关于特定缓存优化的内容[。](http://www.endyourif.com/optimizing-cakephp-websites/ "Optimizing CakePHP Websites | End Your If")

不过，不要认为这是 CakePHP 缓存的终结。您也可以缓存控制器动作、视图，甚至助手函数。更多信息请浏览 CakePHP 手册中的[缓存组件](http://book.cakephp.org/1.2/view/213/Cache "Cache :: Core Helpers :: The Manual :: 1.2 Collection")。

## 安装基于内存的缓存

默认情况下，PHP 会话存储在磁盘上(通常在一个临时文件夹中)。这意味着每次访问会话时，PHP 都需要打开会话文件并解码其中包含的信息。问题是磁盘 I/O 可能非常昂贵。与磁盘 I/O 相比，从内存访问项目要快得多。

对于与会话相关的磁盘 I/O，有两种很好的方法。第一种是在服务器上配置一个 RAM 磁盘。配置完成后，该驱动器将像任何其他驱动器一样安装，并且`php.ini`中的`session.save_path value`将被更新以指向新的 RAM 磁盘。第二种方法是安装像 [Memcached](http://memcached.org/ "memcached - a distributed memory object caching system") 这样的软件，这是一个允许对象存储在内存中的开源缓存系统。

如果您想知道哪种方法最适合您，可以通过回答以下问题来决定:是否需要多台服务器同时访问这个内存？如果是，您将需要选择 Memcached，因为它可以安装在一个单独的系统上，允许其他服务器访问它。然而，如果您只是想提高单个 web 服务器的速度，那么选择 RAM 磁盘解决方案既好又快，而且不需要额外的软件。

根据您的操作系统，安装 Memcached 可以像输入`sudo aptitude install memcached`一样简单。
安装后，您可以配置 PHP 将会话存储在内存中，而不是通过更新您的`php.ini`:

```
session.save_handler = memcache
session.save_path = 'tcp://127.0.0.1:11211' 

```

如果 Memcached 安装在不同的端口或主机上，那么相应地修改您的条目。

在您的服务器上完成安装之后，您还需要安装 PHP memcache 模块。同样，根据您的操作系统，这些命令之一将为您工作:

```
pecl install memcache
```

或者:

```
sudo aptitude install php5-memcache
```

## 删除 Apache 并安装 Nginx

根据最近的统计数据，Apache 仍然是最受欢迎的[，但是当谈到当今互联网上流量最大的网站时，Ngnix 的采用正在加速。事实上，Nginx 正在成为 Apache 的一个非常受欢迎的替代品。](http://w3techs.com/technologies/cross/web_server/ranking "Usage Survey of Web Servers broken down by Ranking")

> Apache 就像 Microsoft Word，它有一百万个选项，但你只需要六个。Nginx 做了这六件事，而且其中五件比 Apache 快 50 倍。— [克里斯·李](http://maisonbisson.com/blog/post/12249/chris-lea-on-nginx-and-wordpress/ "Chris Lea on Nginx and WordPress")

Nginx 不同于 Apache，因为它是基于进程的服务器，而 Nginx 是事件驱动的。随着您的 web 服务器负载的增长，Apache 很快开始成为一个内存猪。为了正确处理新的请求，Apache 的工作进程增加了新线程的数量，从而导致创建新线程的内存和等待时间增加。同时，Nginx 异步运行，使用一个或很少的轻量级线程。

Nginx 在服务静态文件方面也非常快，所以如果你不使用内容交付网络，那么你肯定会考虑使用 Nginx。

最后，如果内存不足，Nginx 将消耗 20-30M 的内存，而 Apache 可能会消耗 200M 的内存。对于你的个人电脑来说，内存可能很便宜，但是对于云中的服务器来说就不便宜了！

要更深入地了解 Apache 和 Nginx，请访问 Apache vs Nginx WikiVS 页面。

HowToForge 有一篇关于在服务器上配置 Nginx 的优秀文章。我建议按照分步指南使用 php-fpm 安装和配置 Nginx。

## 配置 Nginx 使用 Memcached

一旦安装了 Nginx 和 Memcached，让它们互相利用将会进一步提高性能。尽管 CakePHP 应用程序是动态的，但很可能 80-90%仍然是相对静态的——这意味着它只在特定的时间间隔发生变化。

通过对 Nginx 配置文件进行以下编辑，Memcached 将开始为已经处理并存储在内存中的 Nginx 请求提供服务。这意味着只有很少的请求会真正调用 PHP，这大大提高了网站的速度。

```
server {
    listen 80;
    server_name endyourif.com www.endyourif.com;
    access_log /var/log/nginx/endyourif-access.log;
    error_log /var/log/nginx/endyourif-error.log;
    root /www/endyourif.com/;
    index index.php index.html index.htm;

    # serve static files
    location / {
        # this serves static files that exists without
        # running other rewrite tests
        if (-f $request_filename) {
            expires 30d;
            break;
        }
        # this sends all-non-existing file or directory requests
        # to index.php
        if (!-e $request_filename) {
            rewrite ^(.+)$ /index.php?q=$1 last;
        }
    }

    location ~ .php$ {
        set $memcached_key '$request_uri';
        memcached_pass 127.0.0.1:11211;
        default_type       text/html;
        error_page 404 405 502 = @no_cache;
    }

    location @no_cache {
        try_files $uri =404;
        fastcgi_split_path_info ^(.+.php)(/.+)$;
        fastcgi_pass unix:/var/run/php5-fpm.sock;
        fastcgi_index index.php;
        include fastcgi_params;
    }
}
```

上面的配置指定应该立即提供静态文件。PHP 文件应该被定向到 Memcached 来服务页面，如果它已经被缓存的话。如果它不存在于缓存中，则到达`@nocache`位置，它的作用类似于您之前的 PHP 设置，通过 php-fpm 为页面提供服务。

不要忘记更新您的`app/Config/core.php`来填充 memcache 缓存。以下内容:

```
<?php
$engine = 'File';
if (extension_loaded('apc') && function_exists('apc_dec') && (php_sapi_name() !== 'cli' || ini_get('apc.enable_cli'))) {
    $engine = 'Apc';
}
```

变成了:

```
<?php
$engine = 'Memcache';
```

我们之前缓存查询的代码也需要更新:

```
<?php
Cache::config('short', array(
    'engine' => 'Memcache',
    'duration'=> '+5 minutes',
    'probability'=> 100,
    'path' => CACHE,
    'prefix' => 'cache_short_'
));
```

## 移除 MySQL 并安装 Percona

我推荐的最后一个服务器修改是安装 MySQL 的 Percona 版本。Percona 团队花了多年时间来了解和微调数据库的架构，以获得最佳性能。最好遵循 Percona 的[安装说明，因为它描述了几种不同的安装选项。](http://www.percona.com/doc/percona-server/5.5/installation.html "Installing Percona Server 5.5 from Binaries  - Percona Server 5.5 Documentation")

## 摘要

有相当多的技术可以确保 CakePHP 快速运行。有些改变可能比其他的更难实现，但是不要气馁。无论你使用什么样的框架或语言，优化都是有代价的，一旦你的应用完成，CakePHP 让你快速开发极其复杂的 web 应用的能力将大大超过优化的努力。

<small>图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")</small>

## 分享这篇文章