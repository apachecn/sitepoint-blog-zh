# Glide:轻松动态按需调整图像大小

> 原文：<https://www.sitepoint.com/easy-dynamic-on-demand-image-resizing-with-glide/>

![Glide kayaks image](img/afb69d6bfd6a6ff8dc75fd9d746eb426.png)

[Glide](http://glide.thephpleague.com) 是建立在[干预](https://github.com/Intervention/image)之上的图像处理库。其目的是便于按需图像处理。这是一种奇特的说法，如果图像不存在，它会按照要求创建图像。

例如，您可能有一个大的，高质量的用户个人资料肖像。但是当在移动设备上打开时，下载 10 MB 的图像不仅浪费，而且速度很慢。相反，您可以将媒体查询添加到 CSS 中，如下所示:

```
@media (min-width: 400px) {
	.profilePic {
		background-image: urlimg/myprofile.jpg');	
	}
}

@media (max-width: 400px) {
    .profilePic {
		background-image: urlimg/myprofile-320.jpg');			
	}
} 
```

这将使所有宽度低于 400 像素的设备加载较小的背景图像，从而加快下载速度。但是手动调整你的应用程序中可能需要的每张图片的大小是乏味的，耗时的，并且容易出错。这就是 Glide 的用武之地。

Glide 可以被配置为通过从预先确定的源创建丢失的图像请求(例如上例中不存在的`myprofile-320.jpg`)来响应它们。简而言之，如果所请求的图像不存在，但是它的源存在，那么所请求的图像将从它创建。此外，图像可以保存到缓存中，这样将来的请求就不会调用该库，也不会在重新渲染时浪费宝贵的 CPU 资源。

我们来配置一下。

如果你想继续，请随意使用我们的[无框架应用程序](https://github.com/swader/nofw)和[家园改进版](https://www.sitepoint.com/quick-tip-get-homestead-vagrant-vm-running/)来快速设置环境和样本应用程序进行测试。

## 拔靴带

第一步是安装 Glide:

```
composer require league/glide 
```

然后，我们需要配置 Glide 服务器。在上面的 NOFW 项目中，这发生在`app/config.php`中，其中所有的服务都被配置为将来通过 PHP-DI 注入。事情是这样的:

```
// ...

    'glide' => function () {
        $server = League\Glide\ServerFactory::create(
            [
                'source' => new League\Flysystem\Filesystem(
                    new League\Flysystem\Adapter\Local(
                        __DIR__ . '/../assets/image'
                    )
                ),
                'cache' => new League\Flysystem\Filesystem(
                    new League\Flysystem\Adapter\Local(
                        __DIR__ . '/../public/static/image'
                    )
                ),
                'driver' => 'gd',
            ]
        );

        return $server;
    },

// ... 
```

现在，我们可以在需要时将`glide`实例推入(或拉出)控制器。`source`和`cache`值分别决定原始图像的位置，以及生成的图像应该存储在哪里，而`driver`键指定应该使用内置的 PHP GD 图像操作扩展来修改图像。

您应该更改图像路径以应用于您自己的路径。

虽然这些是主要的，但是还有很多其他的设置，你可以在空闲的时候研究它们。

## 按指定路线发送

接下来，我们需要定义一个路由，当请求一个不存在的图像时，该路由将被触发。[无框架应用](https://github.com/swader/nofw)使用[快速路由](https://github.com/nikic/FastRoute)，因此定义路由就像在`app/routes.php`中添加以下内容一样简单:

```
['GET', '/static/image/{image}', ['MyControllers\Controllers\ImageController', 'renderImage']] 
```

路径中的`{image}`的结果将作为`$image`传递给`ImageController`的`renderImage`方法(我们将在下面编写)。

## 处理

现在让我们来建造`ImageController`:

```
<?php

namespace MyControllers\Controllers;

use League\Glide\Server;
use Standard\Abstracts\Controller;

class ImageController extends Controller
{
    /**
     * @Inject("glide")
     * @var Server
     */
    private $glide;

    public function renderImage($image)
    {
        $width = $this->getWidth($image);

        $imageSource = str_replace('-'.$width, '', $image);
        $this->glide->outputImage($imageSource, ['w' => $width]);
    }

    /**
     * @param string $imagePath
     * @return int
     */
    private function getWidth(string $imagePath) : int
    {
        $fragments = explode('-', $imagePath);
        return (int)explode('.', $fragments[1])[0];
    }
} 
```

Glide 服务器实例通过 PHP-DI 的`@Inject`注释自动注入控制器。如果这令人困惑或有趣，请参见[文档](http://php-di.org/doc/best-practices.html)。然后，`renderImage`被给定图像路径，调用内部的`getWidth`方法，该方法从图像名称中提取宽度，最后调整图像大小并呈现图像。

*请注意，我们没有使用$_GET 参数来根据 Glide docs 传递调整大小设置——这是因为我更喜欢我的图像 URL 看起来像真实的 URL 而不是静态资源，以便它们可以在以后用作实际的静态资源。*

这是可行的，但是敏锐的读者可能会注意到一个问题…

## 保护

事实上，该应用程序容易受到大量图像编辑攻击。有人可以编写一个由数千个不同大小的图像组成的循环，将它们粘贴到他们可以从你的网站上获取的每一张图像上，并使你的服务器 24/7 转换成任意和无意义的大小。Glide 文档建议[对 URL](http://glide.thephpleague.com/1.0/config/security/) 进行签名，这样只有授权的 URL 才能通过，但是这既复杂又很难在处理外部样式表时实现。我们必须预处理我们的 CSS 并重新生成其中的所有图像 URLs 这个复杂的过程因为我们可能还有其他一些不相关的前端资产编译步骤而变得更加复杂(关于这一点，请参见[这里的](https://github.com/Swader/nofw/blob/master/docs/FRONTEND.md))。

相反，正确的方法是使用路线或特定规则来限制图像的大小。因为我们只有一条通向图像的路径，所以让我们采用后一种方法——只允许在处理逻辑中生成特定的大小。

```
<?php

namespace MyControllers\Controllers;

use League\Glide\Server;
use Standard\Abstracts\Controller;

class ImageController extends Controller
{
    /**
     * @Inject("glide")
     * @var Server
     */
    private $glide;

    private $allowedWidths = [
        320,
        768,
        992,
        1280,
        1920,
    ];

    public function renderImage($image)
    {
        $width = $this->getWidth($image);

        $imageSource = str_replace('-'.$width, '', $image);
        $this->glide->outputImage($imageSource, ['w' => $width]);
    }

    /**
     * @param string $imagePath
     * @return int
     */
    private function getWidth(string $imagePath) : int
    {
        $fragments = explode('-', $imagePath);

        if (count($fragments) < 2) {
            header(
                ($_SERVER['SERVER_PROTOCOL'] ?? 'HTTP/1.0') . ' 500 ' . 'Server Error ImgErr00'
            );
            die("Nope! Image fragments missing.");
        }

        $width = (int)explode('.', $fragments[1])[0];
        if (!in_array($width, $this->allowedWidths)) {
            header(
                ($_SERVER['SERVER_PROTOCOL'] ?? 'HTTP/1.0') . ' 500 ' . 'Server Error ImgErr01'
            );
            die("Nope! Disallowed width.");
        }

        return $width;
    }
} 
```

用一些原始的检查扩展了`getWidth`方法——第一个是确保图像有一个尺寸扩展(所以不再生成原始尺寸的图像！–这也节省了带宽)–第二，通过与控制器中的`$allowedWidths`数组属性进行比较，确保大小是允许的。

当然，这可以通过高度、尺寸模式和所有其他滑行选项进行扩展。

## 关于滑动储蓄的一个注记

我们的图像现在正在被安全地调整大小和渲染。

不幸的是，Glide 是“仅限 PHP”的，因为它不可能在图像生成后让服务器直接指向它。这是因为生成的图像看起来总是这样:

```
- images/
   - myprofile.jpg/
       lhgn3q489uncdue7b9qdny98xq3 
```

…而不是请求的`myprofile-320.jpg`。图像名称实际上将是文件夹，并且图像将是该文件夹中的匿名文件。这意味着为了取回`myprofile-320.jpg`图像，PHP 将*总是*必须调用 Glide，而*总是*必须检查图像是否存在，然后提供图像，如果不存在，则生成图像。

一般来说，这并不是一个障碍，因为这些图片都有非常持久的标题:

```
 header('Cache-Control:'.'max-age=31536000, public');
        header('Expires:'.date_create('+1 years')->format('D, d M Y H:i:s').' GMT'); 
```

…事实上，一个 PHP 请求提供一个图像之前，再次使用那些超长的到期头，不是*那*太多的开销，但这是你在规划下一个高流量应用程序时可能要记住的事情。这种情况可以通过在所有东西前面放置一个[拉区](https://www.maxcdn.com/one/faq/)，甚至一个更强大的缓存服务器如 [Varnish](https://www.varnish-cache.org/) 来进一步改善，但那是另外一个故事了。

### 或者…

要在后续渲染中完全绕过 PHP 循环，您可以这样做:

```
 public function renderImage($image)
    {
        $width = $this->getWidth($image);

        $imageSource = str_replace('-'.$width, '', $image);

        $imagePath = $this->glide->makeImage($imageSource, ['w' => $width]);
        $imageBase = $this->glide->getCache()->read($imagePath);
        $this->glide->getCache()->put($image, $imageBase);
        $this->glide->outputImage($imageSource, ['w' => $width]);
    } 
```

创建并保存图像，但只返回其路径。然后，我们从缓存中读取带有所述路径的图像内容。最后，我们以最初请求的名称重新保存图像，然后像以前一样输出。因此，只有第一次调用的开销很大(几次 I/O 操作和一次转换)，以后对这个图像 URL 的所有调用都将直接指向图像——完全绕过 PHP。事实上，如果您用`sudo service php-fpm stop`关闭 PHP，图像仍然会加载。

## 结论

在本教程中，我们看了一下 [Glide](http://glide.thephpleague.com) 的易用性，这是一个图像处理包，可以按需生成修改后的图像，省去了您在部署应用程序之前必须生成图像的麻烦。

你在用 Glide 吗？或许是另一种方法？让我们知道！

## 分享这篇文章