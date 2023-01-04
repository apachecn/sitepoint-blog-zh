# 使用 ImageMagick 裁剪图像并调整其大小

> 原文：<https://www.sitepoint.com/crop-and-resize-images-with-imagemagick/>

如果你的网站允许用户上传照片，图像裁剪/调整功能肯定会派上用场。但用户可能无法使用 Photoshop 等图像处理工具，因此通过提供裁剪/调整大小功能，用户可以从任何设备(如平板电脑或手机)上传照片，而不必担心最终尺寸。此外，您可以创建同一张图片的不同版本，还允许用户裁剪上传图片的特定部分。

在本文中，我将向您展示如何在 ImageMagick PHP 扩展的帮助下创建一个图像裁剪工具。本教程假设您已经安装了扩展，如果没有，那么请务必阅读手册。

## 变得熟悉

ImageMagick 扩展使用 ImageMagick 库执行图像处理。ImageMagick 提供了许多 API 方法，通过这些方法可以操作图像。它提供了一个简单的面向对象的接口来使用 API 您只需要创建一个`Imagick`类的实例，然后调用适当的方法开始操作图像。

因为我们要创建一个图像裁剪器，我们将主要使用两种方法:`cropImage()`和`thumbnailimage()`。

### 作物种植

`cropImage()`方法接受四个参数。前两个参数表示裁剪区域的高度和宽度，后两个参数表示裁剪区域左上角的 X 和 Y 坐标。

例如:

```
<?php
$inFile = "test.jpg";
$outFile = "test-cropped.jpg";
$image = new Imagick($inFile);
$image->cropImage(400,400, 30,10);
$image->writeImage($outFile);
```

我们首先创建一个`Imagick`对象，将我们图像的文件名传递给它的构造函数。然后我们用适当的参数调用`cropImage()`。在这种情况下，代码将生成一个大小为 400×400px 的裁剪图像，从原始图像的顶部开始 30px，从左侧开始 10px。最后，`writeImage()`为我们将结果保存回磁盘。

### 缩略图图像

`thumbnailImage()`方法只接受调整后的图像的高度和宽度，可以如下使用:

```
<?php
$inFile = "test.jpg";
$outFile = "test-thumbnail.jpg";
$image = new Imagick($inFile);
$image->thumbnailImage(200, 200);
$image->writeImage($outFile);
```

上面的代码产生了一个 200×200 像素的图像。如果`thumbnailImage()`的宽度或高度参数设置为 0，则纵横比保持不变。

我们还可以通过第三个论点，称为*最佳*；如果设置为 true，图像的大小将被调整，新的尺寸将包含在指定的高度和宽度内。例如，在 1200×768px 的图像上调用`thumbnailImage(400, 400, true)`将产生 400×200px 的版本。新尺寸小于或等于指定尺寸。

现在您已经熟悉了这两种方法，我们可以开始了。

## 上传和裁剪

当然，我们需要创建一个用户可以上传照片的 HTML 表单:

```
<form action="upload.php" method="post" enctype="multipart/form-data">
 <label for="file">Image:</label>
 <input type="file" name="file" id="file"><br>
 <input type="submit" name="submit" value="Upload and Crop">
</form>
```

一旦用户点击上传按钮，POST 请求将被发送到一个脚本，该脚本将处理文件上传过程，并向用户显示上传的图像以进行裁剪。请记住，上传文件时，您需要将表单的`enctype`属性设置为“多部分/表单数据”。

如果需要，上传脚本处理图像上传和调整图像大小。

```
<?php
if (isset($_FILES["file"])) {
    $tmpFile = $_FILES["file"]["tmp_name"];
    $fileName = ... // determine secure name for uploaded file

    list($width, $height) = getimagesize($tmpFile);
    // check if the file is really an image
    if ($width == null && $height == null) {
        header("Location: index.php");
        return;
    }
    // resize if necessary
    if ($width >= 400 && $height >= 400) {
        $image = new Imagick($tmpFile);
        $image->thumbnailImage(400, 400);
        $image->writeImage($fileName);
    }
    else {
        move_uploaded_file($tmpFile, $fileName);
    }
}
```

在接受用户的文件时，你应该非常小心，因为如果你不创建一个安全的上传系统，别人可以上传一个恶意的文件到你的服务器。要了解更多信息，请阅读文章[用 PHP](https://www.sitepoint.com/file-uploads-with-php/ "File Uploads with PHP") 上传文件，特别注意“安全注意事项”一节。

如果文件不是图像，`getimagesize()`函数返回一个空的高度和宽度，所以如果代码检测到文件不是图像，它只是将用户重定向到其他地方。此外，您还可以使用`getimagesize()`检查文件的图像类型和大小，但是在这种情况下，我们只是根据空值检查文件是否是图像。

由于此时脚本已经知道了上传图像的宽度和高度，因此它可以确定图像是否应该向下调整大小。如果尺寸超过 400×400 像素，则通过调用`thumbnailImage()`调整尺寸以创建 400×400 像素的缩略图。正如我前面解释的，该方法有两个参数:宽度和高度。如果您想保持图像的纵横比，建议将 0 作为参数之一传递。

最后，通过调用`writeImage()`保存调整后的图像，或者如果它已经是合适的尺寸，则用`move_uploaded_file()`移动。

保存的图像被输出到浏览器，以便用户有机会裁剪它。为了允许用户选择图像的特定部分，我使用了一个名为 [ImageAreaSelect](http://odyniec.net/projects/imgareaselect/ "imgAreaSelect - image selection/cropping jQuery plugin - odyniec.net") 的 jQuery 插件。要使用这个插件，jQuery 库以及插件的 CSS 和 JavaScript 文件都需要可用。

```
<script type="text/javascript" src="scripts/jquery.min.js"></script>
<script type="text/javascript" src="scripts/jquery.imgareaselect.pack.js"></script>
<link rel="stylesheet" type="text/css" href="css/imgareaselect-default.css">
```

要初始化插件，您可以使用以下代码:

```
selection = $('#photo').imgAreaSelect({
    handles: true,
    instance: true
});
```

`#photo`是显示给用户的图像的 id。通过将`handles`属性设置为 true，你可以在选择区域显示调整大小手柄，而`instance` true 获得一个实例并将其保存到变量`selection`。通过`selection`,当用户完成裁剪后，您可以轻松地检索选择区域。

给你所需要的一切。例如，`getSelection().width`给出了选择区域的宽度。同样，你可以使用`getSelection().x1`和`getSelection().y1`来找到选择区域左上角的坐标。

将所有这些放在一个页面中，您很可能会注册一个对链接或按钮的`onclick`回调，用户可以单击它来完成选择。单击按钮时，将检索选择区域的宽度、高度和左上角坐标。

```
$("#crop").click(function(){
    var s = selection.getSelection();
    var width = s.width;
    var height = s.height;
    var x = s.x1;
    var y = s.y1;
    ...
});
```

Ajax 请求被发送到裁剪脚本，将这些值作为请求参数传递，因为需要它们来通过 ImageMagick 裁剪图像。此外，您需要发送图像名称，因为脚本需要知道它将要裁剪的图像的名称。

```
var request = $.ajax({
    url: "crop.php",
    type: "GET",
    data: {
        x: x,
        y: y,
        height: height,
        width: width,
        image: $("#photo").attr("src")
    }
});
```

当请求完成时，图像被重新加载，这样新的裁剪版本将代替旧的图像显示。

```
request.done(function(msg) {
    $("#photo").attr("src", msg);
    selection.cancelSelection();
});
```

当裁剪完成时，请求返回裁剪图像的名称，并通过更改`src`属性加载新图像。

那么裁剪脚本是什么样子的呢？

```
<?php
$file = basename($_GET["image"]);
$cropped = "cropped_" . $file;
$image = new Imagick($file);
$image->cropImage($_GET["width"], $_GET["height"], $_GET["x"], $_GET["y"]);
$image->writeImage($cropped);
echo $cropped;
```

脚本首先提取需要裁剪的图像的名称；图像名称将是一个完整的 URL，格式为*http://example.com/path/image.jpg*，但只需要 image.jpg 的*部分。接下来，字符串“cropped”将作为原始名称的前缀，这样它看起来就像 *cropped_image.jpg* 。这允许在不覆盖原始图像的情况下保存裁剪后的图像。*

`cropImage()`方法用于使用发送给脚本的与选择区域相关的四个参数来裁剪原始图像，然后将裁剪后的图像保存到新文件中。然后，裁剪图像的名称被回显给 Ajax 请求，以便在浏览器中显示。

## 结论

在本文中，我创建了一个简单的裁剪工具，向您展示 ImageMagick 扩展的强大功能和易用性。您可以了解更多关于它的知识，并通过使用它强大的 API 来发挥创造力，做出更有用的东西。例如，如果您想扩展我在这里介绍的内容，您可以让用户选择下载多种尺寸的图像。GitHub 上有本文附带的示例代码，可以帮助您入门。

<small>图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")</small>

如果你喜欢读这篇文章，你会爱上[可学的](https://learnable.com?utm_source=sitepoint&utm_medium=link&utm_campaign=learnablelink)；向大师们学习新技能和技术的地方。会员可以即时访问 SitePoint 的所有电子书和交互式在线课程，如 [Jump Start PHP](https://learnable.com/books/jump-start-php?utm_source=sitepoint&utm_medium=link&utm_campaign=learnablelink) 。

对本文的评论已经关闭。有关于 PHP 的疑问？为什么不在我们的[论坛](https://www.sitepoint.com/forums/forumdisplay.php?34-PHP?utm_source=sitepoint&utm_medium=link&utm_campaign=forumlink)上问呢？

## 分享这篇文章