# 使用 Imagick 添加文本水印

> 原文：<https://www.sitepoint.com/adding-text-watermarks-with-imagick/>

在之前的一篇文章中，Timothy Boronczyk 讲述了如何使用覆盖图像用 Imagick 创建水印。在本文中，我将向您展示如何使用纯文本实现类似的效果。

在撰写本文时，几乎没有关于 PHP 的 [Imagick API 文档](http://php.net/imagick "PHP: ImageMagick - Manual")的文档，尽管在 [ImageMagick 网站](http://www.imagemagick.org/Usage/annotating/#wmark_text "Annotating -- IM v6 Examples")上可以找到大量的命令行示例，所以我们将从那里开始。将示例中的命令行代码转换成 PHP 只是一件单调乏味的事情，要找到执行相同功能的适当方法。

对于我的例子，我将使用下面的一些完全随机英俊的绅士形象。

![](img/6d6a24342d1616e31f84bdcba00dbb08.png)

## 在图像上绘制文本

最容易创建的文本水印是覆盖在图像上的字符串。

命令行示例如下:

```
convert image.png  -font Arial -pointsize 20 
    -draw "gravity south
        fill black  text 0,12 'Copyright'
        fill white  text 1,11 'Copyright'" 
    result.png
```

PHP 的等价物是:

```
<?php
// Create objects
$image = new Imagick('image.png');

// Watermark text
$text = 'Copyright';

// Create a new drawing palette
$draw = new ImagickDraw();

// Set font properties
$draw->setFont('Arial');
$draw->setFontSize(20);
$draw->setFillColor('black');

// Position text at the bottom-right of the image
$draw->setGravity(Imagick::GRAVITY_SOUTHEAST);

// Draw text on the image
$image->annotateImage($draw, 10, 12, 0, $text);

// Draw text again slightly offset with a different color
$draw->setFillColor('white');
$image->annotateImage($draw, 11, 11, 0, $text);

// Set output image format
$image->setImageFormat('png');

// Output the new image
header('Content-type: image/png');
echo $image;
```

结果是:

![](img/783d74f8f5a5f1134d8af73983f894f8.png)

这个例子非常简单明了，代码的注释也说明了这一点，所以你不需要太多额外的解释。但是，虽然这种方法有效，但文本从图像中过于突出，效果可能应该更加微妙。

## 使用字体遮罩的透明文本

为了使水印文本的效果更加平滑，可以使用字体遮罩使文本字符串透明。

命令行示例:

```
convert -size 300x50 xc:grey30 -font Arial -pointsize 20 
    -gravity center -draw "fill grey70  text 0,0  'Copyright'" 
    fgnd.png
convert -size 300x50 xc:black -font Arial -pointsize 20 -gravity center 
    -draw "fill white  text  1,1  'Copyright'
        text  0,0  'Copyright'
        fill black  text -1,-1 'Copyright'" 
    +matte mask.png
composite -compose CopyOpacity  mask.png  fgnd.png  stamp.png 
    mogrify -trim +repage stamp.png
composite -gravity south -geometry +0+10 stamp.png  image.png 
    result.png
```

相当于 PHP:

```
<?php
// Create objects
$image = new Imagick('image.png');
$watermark = new Imagick();
$mask = new Imagick();
$draw = new ImagickDraw();

// Define dimensions
$width = $image->getImageWidth();
$height = $image->getImageHeight();

// Create some palettes
$watermark->newImage($width, $height, new ImagickPixel('grey30'));
$mask->newImage($width, $height, new ImagickPixel('black'));

// Watermark text
$text = 'Copyright';

// Set font properties
$draw->setFont('Arial');
$draw->setFontSize(20);
$draw->setFillColor('grey70');

// Position text at the bottom right of the image
$draw->setGravity(Imagick::GRAVITY_SOUTHEAST);

// Draw text on the watermark palette
$watermark->annotateImage($draw, 10, 12, 0, $text);

// Draw text on the mask palette
$draw->setFillColor('white');
$mask->annotateImage($draw, 11, 13, 0, $text);
$mask->annotateImage($draw, 10, 12, 0, $text);
$draw->setFillColor('black');
$mask->annotateImage($draw, 9, 11, 0, $text);

// This is needed for the mask to work
$mask->setImageMatte(false);

// Apply mask to watermark
$watermark->compositeImage($mask, Imagick::COMPOSITE_COPYOPACITY, 0, 0);

// Overlay watermark on image
$image->compositeImage($watermark, Imagick::COMPOSITE_DISSOLVE, 0, 0);

// Set output image format
$image->setImageFormat('png');

// Output the new image
header('Content-type: image/png');
echo $image;
```

结果是:

![](img/31ae55368c2ea97a170b28fc39269771.png)

在这个例子中创建了几个图像。第一个图像`$watermark`是灰度的，第二个图像`$mask`对我想要透明的部分使用纯黑色，对我想要保留的部分使用白色。当我通过合并图像来应用蒙版时，任何在`$mask`中发现的灰色阴影都将是半透明的，并产生更平滑的边缘。

在命令行版本的代码中，透明图像的外部边缘在水印覆盖之前被剪切，但是[似乎有一个 bug](https://bugs.php.net/bug.php?id=59095 "Bug #59095 setGravity() and setImageGravity() have no effekt on compositeImage()") ，它阻止了`compositeImage()`方法保留由`setGravity()`定义的位置。这基本上意味着，如果我要修剪边缘，那么我的水印将失去其在右下角的位置，并重新定位在左上角。为了避免这种愚蠢的行为，我创建了与源图像尺寸相同的调色板，这样就不会发生裁剪。

## 平铺文本

最后一个例子将文本平铺在整个图像上，这使得移除文本变得更加困难。

命令行:

```
convert -size 140x80 xc:none -fill grey 
    -gravity NorthWest -draw "text 10,10 'Copyright'" 
    -gravity SouthEast -draw "text 5,15 'Copyright'" 
    miff:- | 
composite -tile - image.png  result.png
```

PHP:

```
<?php
// Create objects
$image = new Imagick('image.png');
$watermark = new Imagick();

// Watermark text
$text = 'Copyright';

// Create a new drawing palette
$draw = new ImagickDraw();
$watermark->newImage(140, 80, new ImagickPixel('none'));

// Set font properties
$draw->setFont('Arial');
$draw->setFillColor('grey');
$draw->setFillOpacity(.5);

// Position text at the top left of the watermark
$draw->setGravity(Imagick::GRAVITY_NORTHWEST);

// Draw text on the watermark
$watermark->annotateImage($draw, 10, 10, 0, $text);

// Position text at the bottom right of the watermark
$draw->setGravity(Imagick::GRAVITY_SOUTHEAST);

// Draw text on the watermark
$watermark->annotateImage($draw, 5, 15, 0, $text);

// Repeatedly overlay watermark on image
for ($w = 0; $w < $image->getImageWidth(); $w += 140) {
    for ($h = 0; $h < $image->getImageHeight(); $h += 80) {
        $image->compositeImage($watermark, Imagick::COMPOSITE_OVER, $w, $h);
    }
}

// Set output image format
$image->setImageFormat('png');

// Output the new image
header('Content-type: image/png');
echo $image;
```

结果:

![](img/c98f4755f3cbcf3f3cb3c4e143a8f0a8.png)

注意，我已经用`setFillOpacity()`设置了透明度，而不是使用图像蒙版。

## 摘要

对我来说，PHP 中的图像操作已经成为该语言最令人愉快的方面之一，我只能希望 Imagick 在未来的版本中成为捆绑包。如果您正在寻找一种贡献的方式，我鼓励您将其他命令行示例转换为 PHP，然后将您的结果发布在官方 PHP 手册上，这样其他人就可以学习和享受了。

图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")

## 分享这篇文章