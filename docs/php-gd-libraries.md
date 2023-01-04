# 用 PHP 处理图像——GD 库

> 原文：<https://www.sitepoint.com/php-gd-libraries/>

**GD 库是用于图像处理的主要 PHP 模块，可从[Boutel.Com 公司](http://www.boutel.com)获得**

如果你足够幸运，可以托管(或者拥有)一个运行 GD2.0 或更高版本的服务器，你将能够使用 jpeg 格式的真彩色图像(如果是 2.0.4 以上版本，还可以使用 png ),因此，你不会真的从阅读本教程中受益。使用 GD2.0+的用户应该选择使用`ImageCreateTrueColor` 代替`ImageCreate`，使用`ImageCopyResampled`代替`ImageCopyResized`，以确保使用最高的色彩级别。如果你运行的是 2.0 以下的版本，请继续阅读！

##### 创建图像

GD 中的函数`ImageCreate(x_dimension,y_dimension)`被限制为 256 种颜色的调色板，因此，它很少输出大多数网页设计者可以接受的质量的图像。幸运的是，有一种方法可以绕过这个限制，将调色板最大化到 1670 万色。要做到这一点，您需要一个能够零压缩保存 jpegs 的图形编辑器，一个支持 GD 的服务器，以及几分钟的空闲时间来阅读本教程。

概念的快速解释…

如果用`ImageCreateFromJPEG($image_pointer`)；您的可用调色板将完全模仿您指向的图像。所以，如果我们扔掉`ImageCreate(width,height)`函数，专注于尝试使用`ImageCreateFromJPEG`，我们应该会走向更好的调色板。熟悉 GD 的人(或者思维更敏捷的人)可能已经注意到，从托管图像创建不允许我们为我们创建的图像指定宽度和高度。如果我们的资源图像是 400 像素乘 200 像素，我们想创建一个最大尺寸为 100 像素的缩略图，那么我们需要一个 100 像素乘 50 像素的基本图像。

这使我们只有两种选择:

1.  要么我们上传脚本所需的所有宽度和高度变化的基本图像，并通过一点基本数学来决定使用哪一个，或者

3.  我们使用一个图像，它的大小可以容纳我们需要的任何尺寸，并且它将只绘制到指定大小的区域，留下任何空白/页面颜色。

如果所有显示的图像大小相同，就不会有什么大问题需要解决——您可以简单地将图像建立在一个大的空白调色板上，该调色板已预先调整到您需要的尺寸。如果你想探索第一个选项并上传多个基本图像，你应该可以根据这里的提示来处理。

本教程的其余部分将处理第二个选项。我们将看看如何推断出要在创建的图像的哪个区域进行绘制，这里提供的技巧和提示应该可以让您快速上手！

***基本缩略图规则***

**1。善待你的服务器**

很容易显示一组缩略图，只需遍历一个目录中的图像，并通过一个缩略图脚本将它们全部推送出去。因为它太简单了，有些人在每次页面加载时都这样做，这显然有点消耗服务器资源。一个更好的替代方法是将实际的缩略图保存到服务器上，然后简单地显示出来。

**2。保护您的缩略图脚本**

为了通用性，您需要一些变量来控制缩略图的输出。您至少需要一个图像指针，可能需要一个允许您在需要时将副本保存到服务器的开关，还可能需要一个压缩设置。有了这些，你就要确保没有人能输入一个会覆盖你通常设置的 URL。会话或的典型选项。htaccess 会做得很好。

##### 剧本

目前，我们正在尝试创建一个新的图像，代表另一个图像的尺寸是未知的之前运行脚本。

正如我们所讨论的，我们希望有一些可用的变量，允许我们指定资源映像，控制是否在服务器上保存副本，以及控制输出压缩率。

但是我们也可以使用会话检查来控制脚本是否运行。也许我们的管理面板可以注册一个名为'`allow_thumbs`'的会话来启用脚本。我们将使用`<img>`标签的`src`属性调用这个脚本，方式如下:

```
<img src="thumb.php?basepath=foldername/ 

&img_ref=nicebig.jpg&create=yes&compress=65">
```

因为我们每次都将使用完全相同的基本图像，所以我们也可以将尺寸硬编码到标签中，并附带一个`alt=`。毫无疑问，你会找到你自己的系统来建立呼叫标签，基于你当前的管理设置。

关于通过`<img>`标签的`src`调用，另一件要注意的事情是，脚本中发生的任何解析错误都不会回显到页面上。所有会发生的是，你会在你的页面上看到一个找不到图像的空间。如果出于调试目的需要查看错误返回，您需要临时移除会话保护，并通过 URL 调用直接访问脚本。

下面的脚本将通过使用一个空白的基础图像而不是`ImageCreate()`函数来生成一个缩略图。具体语法的解释如下。

```
session_start(); 

if($HTTP_SESSION_VARS["allow_thumbs"] == "yes") 

{ 

header ("Content-type: image/jpeg"); 

// define the small, square image that will be  

// used as the thumbnail base 

$palette_image = 'foldername/large_palette_base_image.jpg'; 

/****** You shouldn't need to edit below here ******/ 

// Set some defaults values for variables that have not  

// been passed to the script through the url 

if(!isset($HTTP_GET_VARS['create']))  

{$HTTP_GET_VARS['create'] = 'no';} 

if(!isset($HTTP_GET_VARS['basepath']))  

{$HTTP_GET_VARS['basepath'] = '';} 

if(!isset($HTTP_GET_VARS['compress']))  

{$HTTP_GET_VARS['compress'] = 100;} 

// establish where on the thumbnail we can draw to 

$thumbsize = getImageSize($palette_image); 

$maxdim = $thumbsize[0]; 

$draw_from = $HTTP_GET_VARS['basepath'].$HTTP_GET_VARS['img_ref']; 

$dim = GetImageSize($draw_from); 

if($dim[0]>$dim[1]) 

{ 

$to_w = $maxdim; 

$to_h = round($dim[1]*($maxdim/$dim[0])); 

$to_x = 0; 

$to_y = round($maxdim-$to_h)/2; 

} 

else 

{ 

$to_h = $maxdim; 

$to_w = round($dim[0]*($maxdim/$dim[1])); 

$to_y = 0; 

$to_x = round($maxdim-$to_w)/2; 

} 

// create some base images to start designing from  

// and make initial basic thumbnail 

if($dim[2]==1) {$from = ImageCreateFromGIF($draw_from);} 

elseif($dim[2]==2) {$from = ImageCreateFromJPEG($draw_from);} 

elseif($dim[2]==3) {$from = ImageCreateFromPNG($draw_from);} 

$thumb = ImageCreateFromJPEG($palette_image); 

// $set_bg_colour = ImageColorAllocate($thumb,255,0,0); 

// $fill_bg_colour = ImageFill($thumb,0,0,$set_bg_colour); 

ImageCopyResized($thumb, $from, $to_x, $to_y, 0,  

0, $to_w, $to_h, $dim[0], $dim[1]); 

/******* Image Manipulation Scripting *******/ 

// extra image manipulation can go here 

/***** End Image Manipulation Scripting *****/ 

// output the created thumnbnail onto the calling page  

// and, if $create has been set to 'yes', also create  

// a copy of the thumbnail on the server 

ImageJPEG($thumb,'',$HTTP_GET_VARS['compress']); 

if($HTTP_GET_VARS['create'] == "yes") 

{ 

ImageJPEG($thumb,$HTTP_GET_VARS['basepath'].substr 

($HTTP_GET_VARS['img_ref'],0, 

strpos($HTTP_GET_VARS['img_ref'],'.')).'_thumb.jpg',  

$HTTP_GET_VARS['compress']); 

} 

// destroy all the temporary images used by the  

//server while executing this  

scriptlet (tidying up) 

ImageDestroy($from); 

ImageDestroy($thumb); 

}
```

上面的脚本将获取一个资源图像(由调用 url 中的 img_ref=引用),并将其缩小为适合调色板图像中心的缩略图(用于扩展调色板的空白图像)。结果缩略图上的调色板索引将增加到反映调色板图像的最大尺寸。您可能希望复制上面的脚本，并在下面的解释中引用它。

##### 语法解释

现在让我们一步一步地看这个脚本，并探索在每一点上发生了什么。

*   `**Session**` - is simply there for security. You could do `session_start(); $allow_thumbs = "yes"; session_register("allow_thumbs");` in your admin page to activate the thumbnailing.

    或者，您可以删除会话部分，并将缩略图脚本放在有保护措施的文件夹中。htaccess 文件。无论哪种方式，只要确保人们不能通过输入 URL 来访问脚本。

    *   `**header()**` - to output to a page or file, a header type must be sent. We chose jpeg due to its compression ability.*   `**$palette_image**` - the path (absolute from root, or relative to this script) and name of the square blank image that is to be used as a thumbnail base. I emphasized "square" because you must use a square image with the above script. To create a nice truecolour image, simply:*   open your graphics program,*   做一个新的正方形(我有没有提过应该是正方形？)图片，*   increase colour depth to maximum (if it isn't already), and*   save it as a .jpg.

    你可能还想在保存之前用与页面背景相同的颜色填充它。最后，上传它并更改`$palette_image`的值来引用这个新文件。

    *   **默认值**——为了避免总是通过 URL 调用传递每个变量=值对，我们只设置一些默认值，如果变量没有设置的话就用。例如，如果我们用`img src="thumb.php?img_ref=blat.png"`调用一个图像，脚本会自动设置为`create=no, basepath='' and compress=100.`*   **establish draw area** - using `GetImageSize()`, we find the height and width of both our resource image, and thumbnail base. We check which is bigger on the resource image (whether it is portrait or landscape) and reduce that to mimic the space available on the thumbnail base. Some reasonably easy mathematics can then be used to deduce the other dimension and the top left pixel of our draw area. We have...*   `$to_h` - height of the area to draw to*   `$to_w` - width of the area to draw to*   `$to_x` - horizontal position of first pixel, counted from the left*   `$to_y` - vertical position of first pixel, counted from the top*   **create some images** - for GD to do any image manipulations it needs to create a copy of the designated image and work from that. We need it to create two images, the thumbnail base (which we called `$thumb`), and a copy of the resource image (which we called `$from`). Our earlier use of `GetImageSize` on the resource image also yielded an index that holds the filetype. A quick bit of value testing will reveal that we can create our copy of that image -- be it a .gif, .png or .jpg -- which means our script is a bit more versatile now.*   **`// $set_bg_colour`** - if you didn't fill your thumbnail base image with the same colour as your page background, you should un-comment this line and the $fill_bg_colour line. You should insert rgb values into the `ImageColorAllocate($thumb, red, green, blue)` call to mimic your page colour. You could even pass these in as variables in the calling URL.*   **`ImageCopyResized`** - using all the mathematically deduced values, we use this function to take the entire area of our resource image, shrink it and copy it to the rigidly defined area of our thumbnail base.*   **`/* image manipulation scripting */`** - if we wanted to perform any further manipulations on our thumbnail, we would do so in this area. Further along in this tutorial are a couple of code snippets that you could plug in here.*   **`ImageJPEG`** - this is the call that outputs an image to the browser.*   **`if (create = "yes")`** - to save a copy of the thumbnail we've created onto the server, we need to use the middle parameter of the `ImageJPEG` function. We test if our variable create has explicitly been set to "yes" and, if so, we rip apart the resource image name to remove the extension and create a file based upon [that image name]_thumb.jpg.

    请注意，您需要将保存缩略图的目录更改为相当高的权限。你可能还想把缩略图放在它们自己的单独目录中，在这种情况下，你会想修改路径——例如到`$HTTP_GET_VARS['basepath']. 'thumbnail_folder/' .substr($....`

    *   **`ImageDestroy`** - because GD had to create images to work from, we end all manipulation scripts by destroying those temporary images from the server.

    我已经用 GD1.6.2 和 GD1.8.4 测试了这个脚本，两个版本的表现都令人钦佩——当然比使用`ImageCreate()`时允许的 256 种颜色有所改进。

    现在我们已经有了基础，让我们看看如何使用进一步的图像处理功能...

    ##### 进一步操作

    在之前的脚本中，有一个区域被注释为`/* image manipulation scripting */`，它是空的。如果我们想在我们的缩略图上尝试一些奇怪而奇妙的效果，我们可以在这里添加代码，集成我们之前定义或推导的所有变量。

    一个示例想法可能是给缩略图添加阴影斜面，也许在顶部和左侧添加浅色阴影，在右侧和底部添加深色阴影。为了让你对所涉及的技术有一个概念，我将采用阴影斜面的想法，并一步一步慢慢来。

    ***阴影斜面***

    GD 中一个有用的功能是`ImageCopyMerge,`，因为它允许我们将一幅图像的一部分合并到我们的缩略图上。它特别有用，因为我们还可以定义合并部分的不透明度(对你我来说，这意味着阴影)。如果我们使用一个简短的 for 循环来计算我们离缩略图的每个边缘有多远，我们也可以使用这个递增的数字来计算不透明度，我们将使用它来绘制我们的深色和浅色线条。

    ```
    // create a dark image and a light image   

    $dark_shadey = ImageCreate($maxdim,$maxdim);   

    $nadir = ImageColorAllocate($dark_shadey,0,0,0);   

    $light_shadey = ImageCreate($maxdim,$maxdim);   

    $nadir = ImageColorAllocate($light_shadey,255,255,255);   

    // decide how wide we want our edge shading   

    $edge_width = 10;   

    for($edge_pixel = 0; $edge_pixel < $edge_width; $edge_pixel++)   

    {   

    // work out the opacity relative to how far from the edge we are   

    $opacity =  100 - (($edge_pixel+1) * (100 / $edge_width));   

    // merge a bit of the light image along the top and left side   

    // merge a bit of the dark image along the base and right side   

    ImageCopyMerge($thumb,$light_shadey,$to_x+($edge_pixel-1),   

    $to_y+($edge_pixel-1),0,0,1,$to_h-(2*$edge_pixel),$opacity);   

    ImageCopyMerge($thumb,$light_shadey,$to_x+($edge_pixel-1),   

    $to_y+($edge_pixel-1),0,0,$to_w-(2*$edge_pixel),1,$opacity);   

    ImageCopyMerge($thumb,$dark_shadey,$to_x+($to_w-($edge_pixel+1)),   

    $to_y+$edge_pixel,0,0,1,$to_h-(2*$edge_pixel),$opacity-20);   

    ImageCopyMerge($thumb,$dark_shadey,$to_x+$edge_pixel,$to_y+   

    ($to_h-($edge_pixel+1)),0,0,$to_w-(2*$edge_pixel),1,$opacity-20);   

    }   

    // destroy the two new images that we used   

    ImageDestroy($dark_shadey);   

    ImageDestroy($light_shadey);
    ```

    你可能会注意到我在这里使用了一些奇怪的语法，比如在`ImageCopyMerge` 调用中减少 20 的暗图像不透明度。这仅仅是因为如果黑暗不像光那样黑暗，那么合成的图像看起来会更好。如果您决定编写自己的操作代码，您将(很可能)必须在自己的脚本中添加一些类似这样的变通语法。

    只是为了让你继续下去，这里有几个操作可以剪切并粘贴到主教程脚本的 `/* image manipulation scripting */` 区域。

    ***蜘蛛网***

    这个小插件 scriptlets 产生了一个蜘蛛网效果，它以定义为`$zenith`的颜色绘制在缩略图上。这个脚本简单地从左下角各种角度画几条线，然后画以同一个角为中心的椭圆弧。

    ```
    $zenith = ImageColorAllocate($thumb,255,255,255);   

    for($draw = 0;$draw<$to_h;$draw+=12)   

    {   

    ImageLine($thumb,$to_x,($to_h+$to_y),($to_w+$to_x),   

    (($to_h-$draw)+$to_y),$zenith);   

    }   

    for($draw = 0;$draw<$to_w;$draw+=12)   

    {   

    ImageLine($thumb,$to_x,($to_h+$to_y),($draw+$to_x),   

    $to_y,$zenith);   

    }   

    for($draw = 1;$draw<14;$draw++)   

    {   

    ImageArc($thumb,$to_x,($to_h+$to_y),$draw*($to_w/4),$draw*   

    ($to_h/4),270,0,$zenith);   

    }
    ```

    ***甜甜圈***

    这个更大的插件 scriptlet 产生一个椭圆形阴影甜甜圈缩略图。背景和阴影颜色由`$zenith`定义。这个操作的工作原理是一次一个像素地遍历缩略图，并决定在该位置合并一个小点时应用多少不透明度。

    ```
    $dot = ImageCreate(1,1);   

    $zenith = ImageColorAllocate($dot,255,255,255);   

    for($ypos=0;$ypos<$to_h;$ypos++)   

    {   

    for($xpos=0;$xpos<$to_w;$xpos++)   

    {   

    $xdist = abs(($to_w/2)-$xpos);   

    if($xdist==0) {$xdist = 0.01;}   

    $ydist = abs(($to_h/2)-$ypos);   

    $dist = sqrt(pow($xdist,2)+pow($ydist,2));   

    $angl = atan($ydist/$xdist);   

    $el_dist =    

    sqrt(pow(abs(cos($angl)*$to_w/2),2)+pow(abs(sin($angl)*$to_h/2),2));   

    if($dist>$el_dist || $dist<$el_dist/6)   

    {   

    ImageCopyMerge($thumb,$dot,$xpos+$to_x,$ypos+$to_y,0,0,1,1,100);   

    }   

    else {   

    $dnut_dist = ($el_dist/12)*5;   

    $offset_dist = abs((($el_dist/12)*7)-$dist);   

    $uppy = sin(acos($offset_dist/$dnut_dist))*$dnut_dist;   

    $opac = 100-((100/$dnut_dist)*$uppy);   

    ImageCopyMerge($thumb,$dot,$xpos+$to_x,$ypos+$to_y,0,0,1,1,$opac);   

    }   

    }   

    }   

    ImageDestroy($dot);
    ```

    我希望该教程和额外的小 scriptlets 已经教会了您 GD 库是多么的多才多艺。也许您甚至已经发现了图像操作错误，并渴望开始编写自己的转换代码！祝你好运。

## 分享这篇文章