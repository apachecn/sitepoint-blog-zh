# 作业调度的网络漏洞:破解还是解决？

> 原文：<https://www.sitepoint.com/web-bugs-for-job-scheduling-hack-or-solution/>

PHP 的一个奇怪的现实是，许多项目都是围绕最底层的恶魔——共享 web 主机——进行设计的，原因很明显。web 主机对使用其帐户的限制，例如无外壳访问、只读文件系统或设置文件系统权限的能力有限、奇怪的 PHP 配置等。浮现在脑海中的一个例子:最近我又看了一下 Drupal 的，然后在[看到了这个关于缓存的片段](http://drupal.org/node/15367)；

> 打开缓存后，Drupal 会将匿名用户访问的任何页面的所有 HTML 代码直接存储到数据库中。当另一个对同一页面的请求出现时，Drupal 知道从数据库中取出这个页面，而不是从头开始重新生成。结果是数百个查询被一个查询所取代，从而大大减轻了 web 服务器的负载。

我假设这样做是因为普通 Drupal 用户很容易在共享主机上进行控制。这并不是说它不是一个有效的解决方案，只是它通常会更快地缓存到文件中。

无论如何，也许这种性质的最臭名昭著的黑客之一是[网络漏洞](http://en.wikipedia.org/wiki/Web_bug)。虽然网络臭虫名声不好，但作为一种在用户不知情的情况下跟踪用户的机制，它们的用途不一定是邪恶的。事实上,[伪龙](http://www.bitfolge.de/pseudocron-en.html)显示了当人们无法接近外壳时，他们愿意走多远。

通过在 HTML 图像标签中链接到伪像，你可以在你的网站上“包含”伪像。它显示一个 1×1 的透明图像，然后根据伪代码调度文件执行 PHP 脚本。这种方法的优点是浏览器发出一个单独的 HTTP 请求，对应于一个单独的 Apache 进程，因此任何由伪代码执行的代码都是在主 PHP 脚本的“带外”运行的——如果“cron 作业”碰巧正在运行，您站点的访问者不会受到长时间的延迟。嗯，几乎…

最近 [Dokuwiki](http://wiki.splitbrain.org) 获得了一个[索引器](http://dev.splitbrain.org/reference/dokuwiki/nav.html?lib/exe/indexer.php.source.html)来提高其搜索功能的性能。Dokuwiki 不使用数据库——wiki 页面直接存储在文件中，直到最近，每个新的搜索请求都对内容进行“全面扫描”(缓慢且资源密集——此处有些注释)。新的索引器解决了这个问题，也是用网络 bug“触发”的。每次请求时，它都会检查当前的 wiki 页面(包含 web bug)是否已经更改，是否需要重新索引。

在包含索引的第一个版本中，一些用户报告了“页面加载”变得非常慢的问题。事实证明，web bug 的工作方式是，只有在索引完成后，才会显示 1×1 的图像。这意味着，尽管浏览器已经收到了完整的维基内容，但它还在等待图像的到来(通过一个开放的 HTTP 连接)。这导致[跳动](http://en.wikipedia.org/wiki/Throbber)持续跳动，给用户页面仍在加载的印象。我很惊讶地发现伪距也有类似的问题——当图像立即显示时，它没有向浏览器显示图像已经完成，因此可以保持连接打开。

为了在 Dokuwiki 中解决这个问题，1×1 图像被立即呈现在索引器脚本中，并且浏览器发送了一个内容长度标头，该标头将指示浏览器在获得完整图像后断开连接。通常，当 HTTP 连接被客户端丢弃时，相应的 PHP 脚本将被终止。但是这种行为可以用 [ignore_user_abort()](http://www.php.net/ignore_user_abort) 来否决，于是 indexer.php 的开始变成了；

```
 <?php
/**
 * DokuWiki indexer
 *
 * @license    GPL 2 (http://www.gnu.org/licenses/gpl.html)
 * @author     Andreas Gohr
 */

/**
 * Just send a 1x1 pixel blank gif to the browser and exit
 */
function sendGIF(){
    $img = base64_decode('R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAEALAAAAAABAAEAAAIBTAA7');
    header('Content-Type: image/gif');
    header('Content-Length: '.strlen($img));
    header('Connection: Close');
    print $img;
    // Browser should drop connection after this
    // Thinks it's got the whole image
}

// Make sure image is sent to the browser immediately
ob_implicit_flush(TRUE);

// keep running after browser closes connection
@ignore_user_abort(true);

sendGIF();

// Browser is now gone...

// Switch off implicit flush again - we don't want to send any more output
ob_implicit_flush(FALSE);

// Catch any possible output (e.g. errors)
// - probably not needed but better safe...
ob_start();

// Start the real work now... 
```

到目前为止一切顺利。但是使用网络窃听器还有另一个负面影响。对于用户发出的每个页面请求，都会有两个 Apache 进程被运行的 PHP 脚本所捆绑。如果 web bug 脚本是长时间运行、资源密集型的东西，大量的访问者可能会导致大量的 web bug 并行运行，锁定 Apache 子进程，同时消耗 CPU 和内存。顺便提一下，对比一下乔治给 T2 的建议。

> 卸载静态内容。如果您的 Web 应用程序中的平均页面包含 9 个图像，那么只有 10%的 Web 服务器请求实际上使用了分配给它们的持久连接。换句话说，90%的请求浪费了宝贵的(从可伸缩性的角度来看，也是昂贵的)Oracle[持久的]连接句柄。您的目标应该是确保只有需要 Oracle 连接性(或者至少需要动态内容)的请求才由动态 Web 服务器提供服务。这将增加每个进程完成的与 Oracle 相关的工作量，进而减少生成动态内容所需的子进程数量…最简单的方法是将所有映像卸载到单独的 Web 服务器(或一组 Web 服务器)上。

事实上，对 Dokuwiki 来说，这*并不是*的问题，这要归功于 Andi 谨慎索引方法的副作用。

为了避免多个索引处理同时运行的竞争情况，我应用了一个简单的规则，即在任何给定的时间只允许一个索引器运行。为了实现这一点，他添加了一个锁定机制，索引器在开始运行时创建一个目录(总是使用相同的目录名/路径),然后在运行结束时删除它。创建一个目录应该比创建一个文件更有效(并且代码行更少)，因为这只是更新 [inode](http://en.wikipedia.org/wiki/Inode) 数据库的问题。如果 Dokuwiki 索引器不能获得锁(锁目录存在),它将立即退出，这将释放 Apache 子进程以进行更多的工作。

最终，Dokuwiki 使用的网络 bug 可能是最有效和最健壮的。虽然总的概念可能还记得从仓鼠轮子发电，但给出了一个有效的解决方案，它回避了一个问题:如果你有一台主机，它允许你使用 T2 的克隆，你还会被诱惑与 Dokuwiki 的网络 bug 一起生活吗？仍然是一个狡猾的黑客还是一个有效的解决方案？

## 分享这篇文章