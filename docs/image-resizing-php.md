# 用 PHP 简化图像大小调整

> 原文：<https://www.sitepoint.com/image-resizing-php/>

如果你像我一样，你可能很懒(如果你不是，给自己一个鼓励吧！).

最近，当我建立一个网站来展示一系列产品时，我意识到我不仅要一遍又一遍地使用相同的产品图片，而且要在整个网站上以不同的尺寸使用相同的图片！

我对自己说，“我不喜欢在我的服务器上为每个产品有 4000 个不同的缩略图，如果我在我的 HTML 中硬编码图像的宽度和高度，我也不想让图像看起来扭曲变形”。

我知道你在想什么。为什么我不能使用像 [ImageMagik](http://www.imagemagick.org) 这样的东西呢？

在大多数情况下，我会使用 ImageMagik，这样我就可以拥有每个图像的常规版本和缩略图版本。但在这种特殊情况下，我希望能够显示不同大小的同一张图像，而不是让 4000 个不同的缩略图占据空间。

那么我是怎么做到的呢？嗯，我即兴创作的。我知道这些事情:

*   我想每个产品一个图像。
*   我想改变图像的大小，以保持其纵横比，无论是风景还是肖像。
*   我想要一个脚本来动态地完成所有这些工作。

因此，凭借我几乎每天都在使用 PHP 所获得的知识，我坐下来开始计算一个函数来实现这些目标。我绝不是 PHP 高手——事实上，我在 2002 年夏天的某个时候自学了这门语言，所以从大多数标准来看，我仍然是个新手。但是我想我可以创建一个完全符合我要求的脚本…

##### 入门指南

假设你有一个很棒的袜子系列，你想通过你的网站销售。你为这些神奇的袜子感到骄傲，并希望人们尽可能多地看到它们:在产品视图页面、搜索页面、列表页面等。但这并不意味着您每次都必须使用默认的图像大小，也不意味着当图像被拉伸或压缩到一个空间中时，图像的质量会降低。有些袜子比其他的长，所以你的图像尺寸可能从 200×400 到 600×750 像素不等。

要开始编写函数，我们必须这样声明它…然后我们必须加入我们的属性。我们想要限制我们的图像，所以我们必须让函数知道我们想要限制它的尺寸，并且我们必须知道原始图像的尺寸是多少(我们一会儿就会讲到这一部分)。

```
<?php 
function imageResize($width, $height, $target) {

//takes the larger size of the width and height and applies the
formula accordingly...this is so this script will work
dynamically with any size image

if ($width > $height) {
$percentage = ($target / $width);
} else {
$percentage = ($target / $height);
}

//gets the new value and applies the percentage, then rounds the value
$width = round($width * $percentage);
$height = round($height * $percentage);

//returns the new sizes in html image tag format...this is so you
can plug this function inside an image tag and just get the

return "width="$width" height="$height"";

}

?>
```

在我们对新功能进行测试之前，我们需要获得想要显示的图像的宽度和高度。PHP 中有一个神奇的命令叫做 [getimagesize()](http://www.php.net/manual/en/function.getimagesize.php) 。如果使用得当，这个命令将返回图像的宽度、高度、类型，甚至是 HTML 图像标签格式的宽度和高度(width="x" height="y ")。

```
$mysock = getimagesize("images/sock001.jpg");
```

现在，`$mysock`是一个数组，它保存了我们想要显示的特定图像的重要信息。在索引 0 中，我们有宽度(`$mysock[0]`)，在索引 1 中，我们有高度(`$mysock[1]`)。这就是我们真正需要的，为了完成我们想要的。想看看功能……嗯，功能？开始了。

##### 运行中的功能

假设您想要显示一个漂亮袜子的列表，但是您想要页面上的空间将它们整齐地排成一行，并且要做到这一点，它们的高度或宽度不能超过 150 像素。

```
<?php 

//get the image size of the picture and load it into an array
$mysock = getimagesize("images/sock001.jpg");

?>

<!-using a standard html image tag, where you would have the
width and height, insert your new imageResize() function with
the correct attributes -->

<img src="images/sock001.jpg" <?php imageResize($mysock[0],
$mysock[1], 150); ?>>
```

就是这样！现在，无论原始文件大小如何，它的宽度或高度(或您指定的任何值)都不会超过 150 像素。

现在，你可能会想“这听起来好得不像是真的。有没有哪种情况下使用这种技术可能会导致灾难性的后果？”

其实是有的。请记住，您并没有改变文件的原始大小。你刚刚上传的那张 200×400，50 KB 的图片仍然是 200×400，50 KB。这个脚本只改变 HTML 中的高度和宽度属性，这样你的原始图片符合你认为在你的网页上最好看的高度和宽度。

话虽如此，如果你有一个列出 50 多种产品的页面，页面会按照你想要的方式显示，但用户必须下载所有 50 张 50 KB 的图片，这可能需要一些时间。因此，在一次只展示几张图片的情况下，我不得不推荐这种技术。

## 分享这篇文章