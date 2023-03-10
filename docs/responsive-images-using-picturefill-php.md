# 使用 Picturefill 和 PHP 的响应图像

> 原文：<https://www.sitepoint.com/responsive-images-using-picturefill-php/>

响应式网页设计的一个关键挑战，也是近年来讨论最多的一个话题，就是如何处理图像。设置图像元素的最大宽度可以让设计者允许它们的大小适应页面尺寸，但是这种方法本身会导致下载的图像比需要的大得多。

解决这个问题的一个办法是“源集”——引用不同大小的单独图像文件(通过扩展，文件大小)，以不同的分辨率请求和显示。事实上，srcset 属性正在由 Webkit 实现。您可以通过使用 Javascript 以跨浏览器兼容的方式直接使用类似的方法；一个这样的方法是[图片填充插件](https://github.com/scottjehl/picturefill)。

本质上，Picturefill 允许您为图像指定不同的`src`属性，每个图像文件对应一个不同的媒体查询。因此，如果且仅当屏幕尺寸需要时，大图像才会被获取，同样，图像的移动优化版本也会被获取并适当显示。

然而，这种方法需要更多的努力——图像本身需要以适当的尺寸创建。这是本文的重点。

## 我们将创造什么

我将演示一个简单的应用程序，用于根据需要生成响应图像或图像衍生物，即不同版本/大小的图像。

本质上，当请求的文件不存在时，我们将“劫持”对图像的特定版本的请求。当它不存在时，我们将创建它，并将其保存到文件系统以备后续请求。

## 入门指南

我将这个例子基于 [SlimBootstrap](https://github.com/tboronczyk/SlimBootstrap) ，这是一个使用 [Slim 框架](http://www.slimframework.com/)的框架应用程序。或者，你可以从 [Github](https://github.com/phpmasterdotcom/LukasWhite-PicturefillAndPHP) 克隆或下载本教程的完整代码。

一旦您克隆或下载了框架应用程序，在开始编码之前还有几个步骤。

在这个例子中，我使用的是 [ImageMagick](http://www.imagemagick.org/) ，所以你需要确保它与 [Imagick PHP 扩展](http://www.php.net/manual/en/book.imagick.php)一起安装——根据你的操作系统，请参考[安装说明](http://www.php.net/manual/en/imagick.setup.php)了解详细信息。如果你愿意，你可以使用 GD 或者像 [Imagine](https://github.com/avalanche123/Imagine) 这样的库重写这个例子。

然后你需要[下载图片填充库](https://github.com/scottjehl/picturefill)。我将两个相关的文件——match media . js 和 picture fill . js——放在 public/js/lib 中。

创建配置文件 config/config . PHP——在同一个文件夹中有一个框架示例——并添加以下内容:

`'images.dir' => $basedir . 'public/img/',
'logs.dir' => $basedir . 'logs/'` 

最后，确保 images 目录既可以被 web 服务器写入，又可以创建子目录—`chmod 775`应该可以做到这一点。建议您对日志目录也这样做——这样，如果您遇到任何错误，就可以方便地在那里打印出来。

## 使用图片填充

Picturefill 采用一个“源集”来引用不同版本的图像，并使用媒体查询来选择下载和显示哪一个。Github 上有一个[简单演示](http://scottjehl.github.io/picturefill/)。

要使用它，您需要创建一个具有以下结构的`<span>`元素:

```
<span data-picture data-alt="Image description goes here">
    <span data-src="img/small.jpg"></span>
    <span data-src="img/medium.jpg"     data-media="(min-width: 400px)"></span>
    <span data-src="img/large.jpg"      data-media="(min-width: 800px)"></span>
    <span data-src="img/extralarge.jpg" data-media="(min-width: 1000px)"></span>

    <!-- Fallback content for non-JS browsers. Same img src as the initial, unqualified source element. -->
    <noscript>
        <img src="img/small.jpg" alt="Image description goes here">
    </noscript>
</span>
```

`data-src`属性包含每个图像派生的 URL，data-media 属性接受任何有效的媒体查询，例如最小或最大宽度，或最小分辨率。

## 图像路径和路由

这里有一个我们将要提供的路径的例子:

`/img/large/uploads/blog/photo.jpg`

分解它；

`img`是图片目录

`large`是导数；在下面的例子中，这个
也可以是`small`、`medium`或`extralarge`

`uploads/path`是一个子目录——在本例中是两层——用于组织图像

`test.jpg`是文件名

如果这个路径引用的文件存在，那么浏览器将直接获取它，绕过我们设置的任何路径。但我们需要定义这条路线，以便拦截对尚不存在的衍生品的请求。

我们可以使用以下模式匹配所有此类请求:

```
$app->get(
    '/img/:parts+',
    function ($parts) use ($app, $c) {
```

上面的路径导致$parts 变量被填充如下:

```
array(4) {
      [0]=>
          string(5) "large"
      [1]=>
          string(7) "uploads"
      [2]=>
          string(4) "blog"
      [3]=>
          string(9) "photo.jpg"
}
```

提取相关部分很容易，只需对这个数组进行删减:

```
// Grab the filename
$filename = array_pop($parts);

// Grab the derivative
$derivative = array_shift($parts);

// Get the path
$path = implode("/", $parts);
```

因为我们已经从数组中取出了导数和文件名，所以文件有多少层并不重要，因为我们只是内爆剩余的数组来创建路径。

下一步是检查目标目录是否存在，如果不存在，就创建它。

```
// assemble the destination path
$destination_path = $c['config']['images.dir'] .     $derivative . '/' . $path;

// Create the directory, if required
if (!file_exists($destination_path)) {
    mkdir($destination_path, 0777, true);
}
```

mkdir 的第三个参数很重要；递归地创建目录是必要的。
配置衍生产品

让我们创建一个灵活的配置，为每个衍生工具定义名称(将构成 URL 的一部分)、适当的大小和相应的媒体查询。

下面是 JSON 中的一个例子:

```
{
    "jpeg_compression": 80,
    "sizes": {
        "small" : {            
            "width" : 180            
        },
        "medium" : {            
            "width" : 375,
            "query" : "(min-width: 400px)"
        },
        "large" : {            
            "width" : 480,
            "query" : "(min-width: 800px)"
        },
        "extralarge" : {            
            "width" : 768,
            "query" : "(min-width: 1000px)"
        }
    }
}
```

在这个例子中，我只定义了宽度，但是我要使用的 resize 函数允许您定义宽度或高度，或者两者都定义。它还提供了裁剪功能，但为了简单起见，我只使用 scale image–[参见文档](http://www.php.net/manual/en/book.imagick.php)了解详细信息。

## 后续步骤

下一步是找到要处理的文件，加载配置，然后调整图像的大小。

```
// Now get the source path
$source_path = $c['config']['images.dir'] . $path;

// grab the config
$config = json_decode(file_get_contents('../config/images.json'), true);

// get the specs from the config
$specs = $config['sizes'][$derivative];

// Create a new Imagick object
$image = new Imagick();

// Ping the image
$image->pingImage($source_path . '/' . $filename);

// Read the image
$image->readImage($source_path . '/' . $filename);

// Resize, by width & height OR width OR height, depending what's configured
$image->scaleImage(
    (isset($specs['width'])) ? $specs['width'] : 0,
    (isset($specs['height'])) ? $specs['height'] : 0
);
```

注意，`scaleImage`函数可以接受宽度和/或高度。

既然我们已经适当地调整了图像的大小，我们需要做两件事。

首先，我们需要将图像保存到适当的位置，这意味着任何后续请求都将直接获取图像本身——绕过我们的调整大小功能。

第二，我们仍然需要尊重最初的请求，这意味着输出图像，同时设置适当的头。

```
// save the file, for future requests
$image->writeImage($destination_path . '/' . $filename);

// set the headers; first, getting the Mime type
$finfo = finfo_open(FILEINFO_MIME_TYPE);
$mime_type = finfo_file($finfo, $destination_path . '/' . $filename);
$app->response->headers->set('Content-Type', $mime_type);

// Get the file extension, so we know how to output the file
$path_info = pathinfo($filename);
$ext = $path_info['extension'];

// output the image
echo $image;

// Free up the image resources
$image->destroy();
```

这就是全部了！为了清楚起见，我做了一些简化，因为在生产中您需要考虑一些事情:

*   对配置进行健全性检查
*   确保文件确实是图像
*   更好的错误处理，在某些情况下生成错误图像
*   跟踪图像衍生配置中的任何更改，并根据需要重新生成图像

## 输出图像

我特意在服务器端配置中包含了媒体查询；因为 Picturefill 需要相当多的 HTML 来操作，所以生成它可能是视图助手的一个很好的选择。这超出了本文的范围，但是如果你有合适的东西，请在评论中告诉我，或者在 [Github](https://github.com/phpmasterdotcom/LukasWhite-PicturefillAndPHP) 上提交一个 pull 请求。

## 摘要

在本文中，我描述了一种解决自适应图像问题的可能方案，它结合了服务器端的、按需创建的图像衍生物，用于 Picturefill Javascript 库。随着像`srcset`这样的标准的发展，第三方 Javascript 库可能会变得多余，但是对服务器端解决方案的需求可能会继续存在。

当然，不需要仅仅使用 Picturefill 或类似的代码，它也可以用于内容管理系统，为不同的显示器生成不同版本的图像。你还能想到别的什么吗？

## 分享这篇文章