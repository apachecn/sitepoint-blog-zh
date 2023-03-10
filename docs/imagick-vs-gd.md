# Imagick vs GD

> 原文：<https://www.sitepoint.com/imagick-vs-gd/>

## 介绍

如果你想创建一个缩略图，应用一个过滤器到一个图像或者以任何其他方式转换它，你将不得不在你的 PHP 应用程序中使用一个图像处理库。意思是你很可能会选择 [GD](http://www.php.net/manual/en/intro.image.php) 或者 [ImageMagick](http://www.php.net/manual/en/intro.imagick.php) 。但是哪一个支持的图像格式范围更广呢？也许其中一个比另一个慢？选择合适的库时，还应该考虑哪些标准？请阅读文章了解详情！

## 有效性

GD 和 ImageMagick 都可以在 PHP 中使用，前提是它们与 PHP 一起安装和配置。从 PHP 4.3 开始，默认情况下包含 GD 库，因此您可能能够在大多数服务器环境中的项目中使用它。另一方面，ImageMagick 可能并不总是可用的，一些托管公司并没有将它包括在他们的服务中。

您可以运行几行代码来检查这两个库的可用性。ImageMagick `queryFormats()`和 GD `gd_info()`函数还列出了每个库支持的图像格式:

```
if(extension_loaded('gd')) {
    print_r(gd_info());
}
else {
    echo 'GD is not available.';
}

if(extension_loaded('imagick')) {
    $imagick = new Imagick();
    print_r($imagick->queryFormats());
}
else {
    echo 'ImageMagick is not available.';
}
```

## 支持的文件类型

执行代码后将打印出支持的图像格式列表，这是 ImageMagick 库比其他库提供更多功能的第一个标志。GD 仅支持 JPG、PNG、GIF、WBMP、WebP、XBM 和 XPM 文件，这与 ImageMagick 库处理的一百多种文件类型相比并不算多。

您可能认为您可能永远不会使用 ImageMagick 支持的所有这些不常见的文件类型，但这可能不是真的。在我的一个项目中，我不得不从 GD 切换到 ImageMagick，因为第一个不支持 TIFF 文件。

## 功能

GD 和 ImageMagick 都提供了一些基本的功能，例如:
–调整图像大小和裁剪图像，
–创建由自定义形状、文本和其他图像文件组成的图像，
–应用图像过滤器(改变亮度、对比度、着色等。).

如果您想以更高级的方式处理图像，请检查 ImageMagick 库的所有功能。如 ImageMagic 示例页面所示——第一个和第二个——你可以用无数种方式变换、修饰或扭曲图像。

PHP `ImageMagick`类本身提供了 **331 个方法**这是一个相当可观的数字(不，我没有手动统计，我用的是`ReflectionClass`；)).一方面，它显示了 ImageMagick 库的巨大影响力，而另一方面，它使寻找和实现特定用例的正确方法变得困难。

## 表演

说实话，如果您只是想创建一组缩略图或对图像应用简单的变换，您不应该关心比较每个图像处理库的性能。

在我对典型服务器配置进行的一系列测试中，使用 ImageMagick 从数码相机创建 3MB·JPG 图像的缩略图花费了大约**0.6 秒**，使用 GD 花费了大约**0.5 秒**。所以整个过程不管用哪个库都不会花太多时间。在浏览网页并查找两个库的速度测试后，您会很快注意到它们在性能方面都不突出。有时 GD 库运行得更快，有时是 ImageMagick——这完全取决于用例。在决定使用 GD 还是 ImageMagick 时，不要把这个标准作为至关重要的标准。

## 编程风格

如果比较使用 GD 和 ImageMagick 库编写的负责相同图像转换的代码，您会很快注意到有几个不同之处。GD 库可通过一组[函数](http://php.net/manual/en/ref.image.php)获得，如`getimagesize()`或`imagecreatetruecolor()`，因此整个图像处理脚本需要以过程化的风格编写。让我们来看一个创建 JPG 图像缩略图的示例:

```
$src_img = imagecreatefromjpeg('source.jpg');
if(!$src_img) {
    die('Error when reading the source image.');
}
$thumbnail = imagecreatetruecolor(800, 800);
if(!$thumbnail) {
    die('Error when creating the destination image.');
}
$result = imagecopyresampled($thumbnail, $src_img, 0, 0, 0, 0, 800, 800, 1600, 1600);
if(!$result) {
    die('Error when generating the thumbnail.');
}
$result = imagejpeg($thumbnail, 'destination.jpg');
if(!$result) {
    die('Error when saving the thumbnail.');
}
$result = imagedestroy($thumbnail);
if(!$result) {
    die('Error when destroying the image.');
}
```

由于在出错时不会抛出异常，所以所有的错误处理都必须通过检查每个 GD 函数的结果来实现。你还必须处理有十个参数的怪异函数，比如`imagecopyresampled()`或`imagecopyresized()`。我确信如此多的参数不是一个好的编码实践的例子。

另一件可能不太方便的事情是，负责读取和保存图像的函数根据图像类型而不同。因此，如果您希望缩略图生成器脚本处理不同的文件类型，您需要添加如下代码:

```
switch($image_type) {
    case 'gif' :
        $src_img = imagecreatefromgif($path);
        break;
    case 'png' :
        $src_img = imagecreatefrompng($path);
        break;
    case 'jpg' :
    case 'jpeg' :
        $src_img = imagecreatefromjpeg($path);
        break;
    default:
        return false;
        break;
}

//continue with creating the thumbnail
```

然后，您必须根据图像类型执行不同的功能，以正确的格式保存目标图像。如你所见，GD 代码变得很复杂。

只需查看负责相同操作的 ImageMagick 代码，您就会注意到不同之处:

```
try {
    $imagick = new Imagick();
    $imagick->readImage('source.jpg');
    $imagick->thumbnailImage(800, 800);
    $imagick->writeImage('destination.jpg');
}
catch(Exception $e) {
    die('Error when creating a thumbnail: ' . $e->getMessage());
}
```

ImageMagick 库可以通过`Imagick`类访问。因此，我们可以受益于所有面向对象编程范例的优势。最简单的例子就是处理错误的方式。当使用 ImageMagick 库时，您可以将所有代码包装在一个 try-catch 块中，您的应用程序可以安全地执行。

正如您在上面看到的，负责创建缩略图的 ImageMagick 脚本不包含任何与源图像类型相关的代码。相同的代码可用于处理 JPG 图像以及 PNG 或 TIF 文件。如果您需要将源图像转换成另一种类型，只需在执行`writeImage()`方法之前添加一行代码:

```
$image->setImageFormat('PNG');
```

不就是更清楚了吗？在我看来，使用 GD 库函数处理图像不如使用 ImageMagick 方便。当然，有各种各样的包装器可用于 GD，使其面向对象，但在这一点上，它开始感觉像打补丁。

## 流行

由于 GD 库默认包含在所有新的 PHP 版本中，您可能会比 ImageMagick 更经常地在各种项目中看到这个库。当我需要在我的 [CakePHP](http://cakephp.org/) 项目中包含一个负责处理图片上传和缩略图生成的组件时，我很快找到了适合我需求的 [one](https://github.com/tute/Thumbnail-component-for-CakePHP) ，基于 GD。有时你可能会发现一些写得很好的模块，让你在两个图像处理库之间进行选择——比如 [Kohana 框架图像库](http://docs.kohanaphp.com/libraries/image)，但恐怕它们并不常见。

## 可供选择的事物

当决定如何处理应用程序中的图像处理时，您不需要坚持使用某个 PHP 库。还有其他值得考虑的解决方案:

**1。使用 PHP 应用程序之外的图像处理脚本。**
在我的一个应用程序中，我必须创建一个网页，允许访问者在浏览器窗口中在线转换图像。我决定使用 [Caman.js](http://camanjs.com/) JavaScript 图像处理库，它做得非常好。这个库也可以作为后台脚本嵌入到 [node.js](http://nodejs.org/) 平台中，这个平台已经越来越受欢迎。

**2。采用基于云的图像处理平台。**
基于云的解决方案可以帮你完成这项工作——发送源文件后，你可以获取不同大小的缩略图或由各种过滤器转换的图像。您不需要编写太多代码，也不会受到服务器能力的限制。只要[打开谷歌](http://www.google.com/search?q=cloud+image+processing)就能找到一些提供此类服务的公司。

**3。检查你正在使用的组件的功能。**
你可能会惊讶地发现，你可以通过使用已经连接到你的应用程序的服务来改变你的图像。例如， [Dropbox API](https://www.dropbox.com/developers/core/docs#thumbnails) 提供了`thumbnails`方法，允许你以五种可用维度之一获取 JPG 或 PNG 图像。检查你的库和 API 的文档，也许你会发现它们可以做你需要的事情。

## 摘要

如您所见，每个图像处理库都有其优点和缺点。GD 库是广泛可用的，所以它可能在任何地方都可以工作。由于它很受欢迎，你会很容易地找到许多使用这个库的例子和组件。获得一些帮助也可能更容易，因为更多的人可能熟悉 GD 库而不是 ImageMagick。

ImageMagick 支持更多的文件类型，可以用比 GD 库更多的方式转换图像。它还允许您编写更清晰、质量更高的代码。

最后，还有云图像处理服务等替代服务，它们可能会完全消除对这两种服务的需求。希望这篇文章对你的选择有所帮助。

如果你对这篇文章有任何问题或评论，欢迎在下面发表评论或通过 [Google+](https://plus.google.com/112138584619019192671?rel=author) 联系我。

## 分享这篇文章