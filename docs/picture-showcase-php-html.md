# 使用 PHP 和 HTML 建立一个 PHP 多图片展示

> 原文：<https://www.sitepoint.com/picture-showcase-php-html/>

我最近决定为我的一个网站创建一个展示区。我需要随机展示我的产品的一排五个缩略图。我以为找到这段 PHP 代码会很容易，但事实证明并非如此，所以我卷起袖子写了自己的代码。其结果是一种新的旋转图像的方式(至少对我来说是这样的)——我希望你会发现它和我一样有用。

问题是我并不真的想要一个随机的展示。我想要随机的，但没有重复的。我有大约 25 张图片，所以在一排五张图片中重复出现的可能性很大——而且会非常不专业！

让情况更复杂的是，我想避免使用 PHP 页面(这会极大地简化事情)，而是从我的 HTML 代码中调用 PHP 脚本，如下所示:

```
<img src="mysite.com/rotate.php?i=0" /> 

<img src="mysite.com/rotate.php?i=1" /> 

<img src="mysite.com/rotate.php?i=2" />
```

使用类似于`'?i=0'`的参数提供了一种区分不同调用的方法。这将是避免重复图像的关键。

##### 一个简单的解决方案:PHP 页面

如果我要使用一个 PHP 页面，代码会非常简单:只需获取一个图像 URL 数组，打乱该数组，然后“分发它们”，首先显示条目 0，然后 1，然后 2，等等，就像这样:

```
<?php 

// display a series of random images - PHP form solution 

// (c) 2004 David Pankhurst - use freely, but please leave in my credit 

$images=array( "img1.gif","img2.gif","img3.gif","img4.gif","img5.gif"  ); 

srand(time()); 

shuffle($images); 

for ($i=0;$i<5;++$i) // display my five images 

  echo "<a href='$images[$i]'>image $i</a><br>"; 

?>
```

但是，当我偶尔需要使用 HTML 表单而不是 PHP 时，我决定从 HTML 页面中调用 PHP 会更好，尽管这使得问题更难解决。

原因是上面的代码都是一次性完成的。对 PHP 脚本的请求很大程度上是无状态的(排除 PHP 会话，这在本例中是不切实际的)。这使得调用一个脚本五次(每个图像一次)并获得相同的“状态”(打乱顺序)变得困难。但另一种选择是图像完全随机，在这种情况下，我会以重复结束。

##### 更好的选择:`srand()`

为了解决这个问题，我使用了一个与上面显示的一个 PHP 脚本非常相似的解决方案:我创建了一个混洗数组。但是这次我使用了查询字符串参数作为数组的索引，来决定显示哪张图片。

为了在调用之间保持这个数组的稳定，我需要一个伪随机值，这个值是由 PHP 及其种子随机函数`srand()`提供的。

PHP 的随机数，尽管对大多数应用来说足够随机，但一点也不随机——它们基于一个种子值。这意味着，如果您每次都使用相同的种子值，它生成的“随机”数字序列将是相同的。这种可重复性使得这个函数非常适合我的需要，其中包括一个长时间保持不变的混合图像列表。通过每次播种相同的数字，并用这些数字混洗列表，我的数组每次都将处于相同的“随机”顺序。

在这种情况下，我使用`time()`作为每个调用的种子值。除以 10 得到一个种子值，它每 10 秒钟改变一次，这意味着数组每次以相同的顺序洗牌 10 秒钟。

结果是数组看起来是随机的，但是顺序保持不变，直到发生五次 PHP 调用——每次从列表中选择不同的图像。

下面是新代码:

```
<?php 

// rotate images randomly but w/o dups on same page - format: 

// <img src='rotate.php?i=0'> - rotate image #0 - use 'i=1' 

// for second, etc 

// (c) 2004 David Pankhurst - use freely, but please leave in my credit 

$images=array( // list of files to rotate - add as needed 

  "img1.gif", 

  "img2.gif", 

  "img3.gif", 

  "img4.gif", 

  "img5.gif" ); 

$total=count($images); 

$secondsFixed=10; // seconds to keep list the same 

$seedValue=(int)(time()/$secondsFixed); 

srand($seedValue); 

for ($i=0;$i<$total;++$i) // shuffle list 'randomly' 

{ 

  $r=rand(0,$total-1); 

  $temp =$images[$i]; 

  $images[$i]=$images[$r]; 

  $images[$r]=$temp; 

} 

$index=(int)($_GET['i']); // image index passed in 

$i=$index%$total; // make sure index always in bounds 

$file=$images[$i]; 

header("Location: $file"); // and pass file reference back 

?>
```

`shuffle()`函数现在已经被我自己的混洗循环所取代，部分原因是我对这种情况下`shuffle()`的输出不满意，部分原因是我想确保“我的”随机数列表总是用于混洗数组(这是保持数组可重复的关键)。

##### 在应用程序中使用代码

要使用此代码，您需要执行以下操作:

1.  用自己的条目替换`$images`中的条目(根据需要包括路径)。

3.  改变`$secondsFixed`来决定列表在重新排序前保持不变的时间。时间范围应该足够大，以便在分配的时间内轻松完成所有 PHP 调用。

5.  Upload the script, and call it from your HTML like this:

    ```
    <img src='mysite.com/rotate.php?i=0'>image #1 

    <img src='mysite.com/rotate.php?i=1'>image #2 

    <img src='mysite.com/rotate.php?i=2'>image #3
    ```

    诸如此类…

当然，如果你的调用比数组中的图像多，它会绕回；否则，你会得到随机的，非重复的图像。

*注意:将`$secondsFixed`值设置为 10 可以确保随机列表每十秒钟重新排序一次。因此，如果 PHP 调用的页面跨越了这一界限——有些在 10 秒界限内，有些在 10 秒界限之外——列表仍然可能是很差的随机化。为了避免这种情况，您可以增加该值。例如，在 3600，列表会保持不变一个小时，并且很少有页面负载会正好越过这个界限。*

除了这个小问题，这是一个有用的 PHP 技术，允许我显示随机选择的一系列图像，没有重复。我已经成功地把它放在了我的一个网站上，以及一个客户的易贝拍卖会上(不时地“更新”展示)。我希望它也能对你有用。

## 分享这篇文章