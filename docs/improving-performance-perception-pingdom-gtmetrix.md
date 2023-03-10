# 使用 Pingdom 和 GTmetrix 改善性能感知

> 原文：<https://www.sitepoint.com/improving-performance-perception-pingdom-gtmetrix/>

本文是构建一个示例应用程序——一个多图片画廊博客——的系列文章的一部分，用于性能基准测试和优化。(点击此处查看[回购](https://github.com/sitepoint-editors/multi-image-gallery-blog/)。)

* * *

**在本文中，我们将使用我们在之前的指南中[解释过的工具](https://www.sitepoint.com/improving-page-load-performance-pingdom-yslow-gtmetrix)来分析我们的[图库应用程序](https://www.sitepoint.com/building-image-gallery-blog-symfony-flex-setup/)，并且我们将寻找进一步提高其性能的可能方法。**

根据[之前的帖子](https://www.sitepoint.com/server-side-optimization-with-nginx-and-pm-static)，请设置 [Ngrok](https://www.sitepoint.com/use-ngrok-test-local-site/) 并通过它连接到本地托管的应用程序，或者在您自己的演示服务器上托管应用程序。这个静态 URL 将使我们能够使用外部工具(如 GTmetrix 和 Pingdom 工具)测试我们的应用程序。

![GTmetrix first test](img/6128286e8af8f2e54359aa7077f3ee54.png)

我们用 GTmetrix 扫描了我们的网站，看看我们如何改进它。我们看到，结果虽然不是灾难性的糟糕，但仍有改进的余地。

第一个标签——页面速度——包含一个由谷歌推荐的[列表。PageSpeed 选项卡下的第一项——关于一致 URL 的警告——与我们随机输出图像的应用程序有关，因此我们将跳过这一项。我们可以做的下一件事是浏览器缓存。](https://developers.google.com/speed/pagespeed/)

## 浏览器缓存

![Browser caching](img/f0fdabe371b0c8bc5024d60a2a455d6d.png)

我们看到有一个`main.css`文件需要设置它的`Expires`头，图库中的图像也需要同样的东西。现在，这些静态文件的第一个想法是在我们的 Nginx 配置中进行设置:

```
location ~* \.(?:ico|css|js|gif|jpe?g|png)$ {
    expires 14d;
} 
```

我们可以简单地把它放在我们的`server`块中，交给 Nginx，对吗？

不完全是。这将会处理我们的静态文件，比如 CSS，但是我们被警告的`/raw`图像并没有那么静态。因此，我们 Nginx 配置中的这个片段不会很容易地解决这个问题。对于我们的图像，我们有一个实际的控制器，可以动态地创建这些图像，所以如果我们可以在控制器中设置我们的响应头，那将是非常理想的。出于某种原因，这些没有被[滑翔](https://www.sitepoint.com/improving-performance-perception-on-demand-image-resizing)正确设置。

也许我们可以在某种程度上设置 Nginx 指令来包含`raw`资源，但是我们觉得控制器方法更能适应未来。这是因为我们不确定还有哪些内容最终会以 *`raw`* 结尾——可能是一些视频，甚至是音频文件。

因此，我们在图片库应用程序中打开了`/src/ImageController.php`，在`serveImageAction()`中放下这两行，就在`return $response`之前:

```
// cache for 2 weeks
$response->setSharedMaxAge(1209600);
// (optional) set a custom Cache-Control directive
$response->headers->addCacheControlDirective('must-revalidate', true); 
```

这将通过添加适当的`Cache Control`和`Expires`头来修改我们的动态图像响应。

Symfony 有更全面的响应缓存选项，如这里的[所记录的](https://symfony.com/doc/current/http_cache.html)。

重启 Nginx 后，我们在 GTmetrix 中重新测试了我们的应用，你瞧:

![Browser Caching](img/f0c2468a269faedde61e6f2edd30027b.png)

## 压缩

接下来，GTmetrix 对我们的资源规模和压缩提出了警告:

![Resource compression](img/a345e80c593b3d0850a988661cc107cc.png)

在生产中，这是一件无关紧要的事情，在这种特殊的情况下，对它进行改进不会有太大的区别，只有几千字节的空间。但是，由于这些指南是为其他更重要的应用程序指明道路的，所以我们也将介绍这一改进。

图像可以提前优化，但由于这些是用 [Glide](http://glide.thephpleague.com/) 创建的动态图像，我们在[的另一篇文章](https://www.sitepoint.com/improving-performance-perception-on-demand-image-resizing)中讨论过，我们不会这样做。事实上，Glide [提供了设置图像质量的方法](http://glide.thephpleague.com/1.0/api/encode/#quality-q)。但有时我们不会使用 Glide 来处理我们的图像，所以我们将首先尝试另一种方法。

在我们的 Nginx `server`块中，我们将添加几行指令 Nginx 压缩我们的内容:

```
gzip on;
gzip_disable "msie6";

gzip_vary on;
gzip_proxied any;
gzip_comp_level 9;
gzip_buffers 16 8k;
gzip_http_version 1.1;
gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript image/jpeg; 
```

对这些设置的解释超出了本文的范围。Nginx 网站上的[对这些项目都有解释，但有一点值得讨论的是`gzip_comp_level`。](http://nginx.org/en/docs/http/ngx_http_gzip_module.html)

对于压缩，普遍的看法是有一个折衷。通过使文件变得更小，我们获得了一些网络带宽，但随后我们失去了一些服务器 CPU 周期，这些周期是对每个请求不断压缩我们的资源所必需的。或者正如[这篇 Cloudflare 博客文章](https://blog.cloudflare.com/results-experimenting-brotli/)(我们稍后将会谈到)所说:

> 在压缩速度和传输速度之间有一个折衷。如果您能够以比实际传输速度更快的速度减少必须传输的字节数，那么提高压缩比只会有好处。

这就是为什么人们很少像我们一样将`gzip_comp_level`设置为最大值 9。他们通常满足于 6 点左右。这样，访问者仍然得到压缩的资源，但 CPU 仍然不会承受很大的压力，尤其是在流量高峰期间。

但是我们不会遵循这个常见的建议，原因有两个:首先，在生产中，我们可能会将我们的应用程序部署在一个 [CDN](https://www.sitepoint.com/what-is-a-cdn-and-how-does-it-work) 上，这将完全消除我们服务器的负担；其次，即使我们不使用 CDN，我们也将使用页面缓存，所以我们服务器完成的这个 gzipping 将——希望——对每个资源只做一次。而且，随着我们的浏览器缓存完全打开，即使这些缓存的 gzipped 资源也不会经常被请求。

因此，这是将我们的`gzip_comp_level`设置为 9 的基本原理，但是如果我们不打算使用页面/HTTP 缓存，我们可能会将其设置为一个较小的数字。

通过这样做，我们能够改善我们的`gzip`结果:

![gzip 100%](img/1f4d19ae4cddb409279f842e4ddc6df1.png)

但是，我们无法在图像上实现同样的改进。因此，我们回到了[的 Glide 文档](http://glide.thephpleague.com/1.0/api/encode/#quality-q)，并找到了如何控制我们的图像质量:在我们的`serveImageAction()`中，在我们的`ImageController`中，我们找到了这样一行代码:

```
$cachePath = $glide->getGlide()->makeImage($file, ['w' => $size]); 
```

我们向第二个数组参数`makeImage()`添加了一个质量参数:

```
$cachePath = $glide->getGlide()->makeImage($file, ['w' => $size, 'q' => 60]); 
```

我们不想将图像质量设置得比这更低，因为这样看起来不太好:

![Low quality image display](img/e8fb1326730cf57a55235c0ee3d2bfd3.png)

然后我们删除了`/var/uploads/cache`文件夹里的所有图片，重新测试。我们在 GTmetrix 上的结果显示，我们能够提高 5%:

![5% image display improvement](img/c85f0512be91c39623255bfde9c22e4b.png)

仍有改进的空间，但在 99%的情况下，我们采取的改善形象的措施就足够了。

我们还去了 [Pingdom Tools](https://tools.pingdom.com) 查看我们的网站——我们惊讶地发现我们得到了 100%的分数。尽管页面加载时间仍然不尽如人意，但与我们之前获得的 92%的分数相比，这是一个显著的进步:

![Pingdom results](img/d970c757571901502ac2120fe111acf6.png)

这些建议是一个有用的指南，但是如果我们的加载时间保持在 4.21 秒，我们的 100%分数将只是一个虚荣的指标，所以我们打开了 Nginx 缓存，我们在[用 Nginx 和 pm-static](https://www.sitepoint.com/server-side-optimization-with-nginx-and-pm-static) 进行服务器端优化中提到过。启用缓存后，我们现在所有指标的结果都是 100%,加载时间不到 1 秒:

![Final Pingdom results](img/c46b23eb1c75eccf43d457006f63a075.png)

我们附上这次测试的 [HAR 文件。](https://uploads.sitepoint.com/wp-content/uploads/2017/12/1512523907100har.json_.zip)

## 结论

尽管我们使用 Pingdom 工具取得了 100 分中的 100 分，但在 YSlow 和 PageSpeed (GTmetrix)上仍有一些指标不能 100%令人满意。然而，这些事情超出了我们的控制范围——就像其他 cdn 提供的资源的缩减(jQuery 和 Bootstrap)。我们可以下载并缩小它们，但是考虑到这些资源被广泛采用，大多数人已经在他们的浏览器中下载了它们，这有多大用处是值得怀疑的。

有些东西我们在本文中没有涉及，但在生产中会被推荐——或者至少值得探索——比如 Brotli 压缩。Cloudflare 的人写了一篇有趣的[博文](https://blog.cloudflare.com/results-experimenting-brotli/),介绍他们使用这种压缩算法的结果。与所有其他事情一样，在实际实施之前，有必要对不同种类的资源和访问者的连接进行彻底的测试。在我们看来，启用 HTTP 缓存后，即使高压缩比可能导致的 CPU 压缩成本仍然是值得的，因为这是一次性成本。

Nginx 的 Brotli 压缩模块可以在这里找到[。这个话题将在](https://github.com/google/ngx_brotli)[这篇文章](https://css-tricks.com/brotli-static-compression/)中进一步探讨。

如果您知道任何其他可能显著影响性能的改进，请告诉我们！

## 分享这篇文章