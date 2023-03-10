# PHP 文章中的动态水印图像

> 原文：<https://www.sitepoint.com/watermark-images-php/>

让我们假设你刚刚收到今年最大的视频游戏的五张截图。你想分享你的截图，但功劳要记在该记的地方——毕竟，你不是为了好玩才甜言蜜语的公关代表，对吧？

解决方案是给图像加水印。但是在照片编辑应用程序中编辑图像的标准水印过程是耗时的。PHP 提供了一个更好的解决方案。

PHP 4+和 [GD 2.0+](http://www.boutell.com/gd/) 代表了动态创建或更改图像的强大组合。在本教程中，我们将看看一些 GD 和标准 PHP 函数，因为我们的水印图像的飞行。

##### 剧本

该脚本只有 13 行，以一个`header()`调用开始，告诉 Web 浏览器我们将输出 JPEG 格式的图像。如果没有这一行，通过 HTML `<img>`标签调用脚本的页面将收到一个损坏的图像，如果直接访问脚本，用户将看到纯文本形式的图像数据。

大多数 PHP 脚本都不需要指定内容类型，因为“text/html”是 PHP 脚本的默认内容类型。

```
header('content-type: image/jpeg');
```

既然浏览器已经为图片准备好了，我们可以开始计算水印位置的数学公式并加载图片。

##### 水印

名为“watermark.png”的文件(注意 png 格式的使用；GD 2.0+已经移除了与 GIF 图片的兼容性)应该与脚本位于同一个目录中。如果没有，请在下面的函数调用中更改文件路径，这将加载水印图像。该文件应该是 PNG-8 格式的文件，而不是 PNG-24。GD 当前版本有个 bug，不正确支持 PNG-24。

```
$watermark = imagecreatefrompng('watermark.png');
```

我们稍后在脚本中使用的操作将需要知道这个水印图像的精确高度和宽度，以像素为单位。这些尺寸将用于在图像上的相对位置放置水印。我们用`imagesx()`和`imagesy()`(不是 GD 函数，是标准的 PHP 函数):

```
$watermark_width = imagesx($watermark);  

$watermark_height = imagesy($watermark);
```

##### 图像

现在，我们将从 JPEG 文件创建一个想要添加水印的图像，并将其设置为一个变量`$image`。通过使用`imagecreatefromjpeg()`函数，我们使用 GD 库将文件加载到 PHP 脚本中。这让我们有机会使用我们的脚本来操作和/或输出图像。

```
$image = imagecreatefromjpeg($_GET['src']);
```

我们将通过使用一个变量`$_GET['src']`来保持脚本的简单，这个变量应该包含需要添加水印的文件的完整路径。或者，您可以在脚本中构建路径，这样会更加安全和私密。只要该函数接收完整路径作为其第一个参数，一切都会好的。

接下来，我们将对加水印的图像进行一些尺寸计算。这些尺寸计算将使用`getimagesize()`功能完成(不为别的，只是为了学习新功能)。`getimagesize()`函数的工作原理是返回一个数组，其中键“0”=宽度，键“1”=高度。

接下来三行代码的过程是:

1.  获取我们想要添加水印的图像的尺寸，$image

3.  从我们的水印图像中减去相应的维度

5.  添加 5 个像素的边距

最后，我们应该知道我们的水印图像(`$watermark`)应该放置在要加水印的图像(`$image`)上的确切像素(进一步称为“目的位置”):

```
$size = getimagesize($_GET['src']);  

$dest_x = $size[0] - $watermark_width - 5;  

$dest_y = $size[1] - $watermark_height - 5;
```

##### 合并图像和水印

下面是整个脚本中最复杂的函数:imagecopymerge()。该函数的官方语法是:

```
int imagecopymerge ( resource dst_im, resource src_im, int dst_x, int dst_y, int src_x, int src_y, int src_w, int src_h, int pct )
```

手册中的这段摘录比我更好地解释了这个功能:

<q>将`src_im`的一部分复制到`dst_im`上，从 x，y 坐标`src_x`、`src_y`开始，宽度为`src_w`，高度为`src_h`。定义的部分将被复制到 x，y 坐标，`dst_x`和`dst_y`上。两幅图像将根据 pct 进行合并，pct 的范围从 0 到 100。</q>

(来源: [PHP 手册](http://www.php.net))

简而言之，下面一行代码使用我们之前计算的目标位置来合并两幅图像:

```
imagecopymerge($image, $watermark, $dest_x, $dest_y, 0, 0, $watermark_width, $watermark_height, 100);
```

我们的最后几行代码将使用`imagejpeg()`把合并了水印的图像输出到浏览器。然后，他们将使用`imagedestroy()`清除我们用 GD 库加载的所有图像的 RAM。

```
imagejpeg($image);  

imagedestroy($image);  

imagedestroy($watermark);
```

你有它！现在你可以使用自由软件最致命的组合之一:PHP 和 GD image library 来动态地为你的 Web 服务器上的任何图像添加水印。完整的水印脚本如下。尽情享受吧！

```
<?php  

header('content-type: image/jpeg');  

$watermark = imagecreatefrompng('watermark.png');  

$watermark_width = imagesx($watermark);  

$watermark_height = imagesy($watermark);  

$image = imagecreatetruecolor($watermark_width, $watermark_height);  

$image = imagecreatefromjpeg($_GET['src']);  

$size = getimagesize($_GET['src']);  

$dest_x = $size[0] - $watermark_width - 5;  

$dest_y = $size[1] - $watermark_height - 5;  

imagecopymerge($image, $watermark, $dest_x, $dest_y, 0, 0, $watermark_width, $watermark_height, 100);  

imagejpeg($image);  

imagedestroy($image);  

imagedestroy($watermark);  

?>
```

## 分享这篇文章