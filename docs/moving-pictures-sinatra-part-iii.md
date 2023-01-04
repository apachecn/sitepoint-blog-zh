# 辛纳屈电影公司，第三部

> 原文：<https://www.sitepoint.com/moving-pictures-sinatra-part-iii/>

![mp_sinatra](img/572437c41d503a65f21c45a780b1c2bf.png)

在第一部分中，我们做了一个小程序来从目录中读取 jpegs。在[第二部](https://www.sitepoint.com/moving-pictures-sinatra-part-ii/)中，我们创建了一个上传程序，所以我们现在可以添加新图片了。现在剩下要做的就是制作幻灯片。

## 让我们给这些图片配上动画

在本文中，Craig 介绍了 CSS 动画。让我们利用这里描述的[关键帧](https://www.sitepoint.com/css3-animation-properties/)技术来改变图像的不透明度。从零开始不透明度，到一，显示图像的所有荣耀。

我们希望一张图片显示大约五秒钟，然后永远循环播放。下面是一些示例代码。

```
img#animation1 {
  -webkit-animation:cycleone 5s infinite;
}

@-webkit-keyframes cycleone {
  0%   {opacity:0} /* not visible */
  10%  {opacity:1} /* visible     */
  90%  {opacity:1} /* visible     */
  100% {opacity:0} /* not visible */
}
```

动画持续时间中有 100 个关键帧。用它除以我们想要显示图片的时间，我们就知道每秒需要多少个关键帧。

```
100 keyframes / 5 seconds = 20 keyframes  
20 keyframes = 1 second
```

在上面的示例代码中，有半秒钟的淡入，图像显示了四秒钟，然后是半秒钟的淡出。

当有两张或七张图片时会发生什么？

### 数学时间到了

如果我们想要每张图片 5 秒，我们有 2 张图片，你如何找到正确的关键帧百分比？

我们可以将原始的关键帧百分比除以图片的数量，以找到设置第一张图片所需的关键帧百分比

```
* 0/2    => 0  
* 10/2   => 5  
* 90/2   => 45  
* 100/2  => 50
```

在第二张图片上，我们需要从之前停止的地方开始。

```
* 0/2 + 50    => 50  
* 10/2 + 50   => 55  
* 90/2 + 50   => 95  
* 100/2 + 50  => 100
```

### 是时候面对现实了。

让我们创建一个新文件来编程解决这个问题。我把我的叫做 *slideshow.rb*

```
$ touch slideshow.rb
```

我们需要为每个`img`标签编写 CSS。每张图片都有一个 ID，可以在一个循环中用来写出 CSS。
找出幻灯片放映的总时长。两张图片，每张图片五秒钟。

```
puts 2 * 5
```

如果你运行它，你就会看到答案。10.咄。是的，这是总的持续时间，但这并没有多大帮助。这个代码呢？

*slideshow.rb*

```
PICTURES = 2  

for i in 1..PICTURES  
  puts "img\#animation" + i.to_s + " {"  
  puts "    -webkit-animation:cycle" + i.to_s + " " + ( 2 * 5 ).to_s + "s infinite;"  
  puts "}"  
end
```

运行它，看看我们得到了什么。

```
$ ruby slideshow.rb  
img#animation1 {  
    -webkit-animation:cycle1 10s infinite;  
}  
img#animation2 {  
    -webkit-animation:cycle2 10s infinite;  
}
```

酷毙了。现在我们要做的就是编写动画规则。记住，我们需要将第一个周期的最后一个数字加到第二个周期中

*幻灯片放映. rb*

```
PICTURES = 2

@offset = 0

for i in 1..PICTURES
  puts "img\#animation" + i.to_s + " {"
  puts "    -webkit-animation:cycle" + i.to_s + " " + ( 2 * 5 ).to_s + "s infinite;"
  puts "}"

  puts "@-webkit-keyframes cycle" + i.to_s + " {"
  puts " #{@offset}% {opacity:0}"
  puts "  " + (10 / PICTURES + @offset).to_s + "% {opacity:1}"
  puts "  " + (90 / PICTURES + @offset).to_s + "% {opacity:1}"
  puts "  " + (100 / PICTURES + @offset).to_s + "% {opacity:0}"
  puts "}"

  #reset offset
  @offset = 100 / PICTURES + @offset
end
```

我们添加了一个变量来处理下一个周期的偏移。

继续运行它。

```
$ ruby slideshow.rb  
img#animation1 {
    -webkit-animation:cycle1 10s infinite;
}
@-webkit-keyframes cycle1 {
 0% {opacity:0}
 5% {opacity:1}
 45% {opacity:1}
 50% {opacity:0}
}
img#animation2 {
    -webkit-animation:cycle2 10s infinite;
}
@-webkit-keyframes cycle2 {
 50% {opacity:0}
 55% {opacity:1}
 95% {opacity:1}
 100% {opacity:0}
}
```

还不错。它做了我们想要的，但我认为它需要清理。首先，我们已经对持续时间进行了硬编码，因此将其转换为一个常量。也许我们应该把它们也移到方法中。是的，我很挑剔

*slideshow.rb*

```
PICTURES = 2  
SLIDEDURATION = 5  

@offset = 0

def slideShowDuration
  (PICTURES * SLIDEDURATION).to_s
end

def setIDs i
  a = "img\#animation" + i.to_s + " {\n"
  a = a + " -webkit-animation:cycle" + i.to_s + " " + slideShowDuration + "s infinite;\n"
  a = a + "}"
  puts a
end

def animation i
  a = "@-webkit-keyframes cycle" + i.to_s + " {\n"
  a = a + " #{@offset}% {opacity:0}\n"
  a = a + "  " + (10 / PICTURES + @offset).to_s + "% {opacity:1}\n"
  a = a + "  " + (90 / PICTURES + @offset).to_s + "% {opacity:1}\n"
  a = a + "  " + (100 / PICTURES + @offset).to_s + "% {opacity:0}\n"
  a = a + "}"
  puts a
  # increase the offset
  @offset = 100 / PICTURES + @offset
end

for i in 1..PICTURES
  setIDs i
  animation i
end
```

当您运行该命令时，您应该会看到与之前相同的输出。更改常量并重新运行该文件。请注意百分比和持续时间的变化。

## 把这一切联系在一起

在 *main.rb* 文件中，我们通过循环显示图片。

```
<% @pictures.each do |picture| %>  
  <img src="<%= picture.sub!(/public\//, '') %>" />  
<% end %>
```

我们需要给它们添加 id 选择器。在 *slideshow.rb* 文件中，我们使用了*img #动画 <image_number></image_number>* 。继续编码吧。

```
<% @pictures.each_with_index do |picture, i| %>  
  <img id="animation<%= i %>" src="<%= picture.sub!(/public\//, '') %>" />  
<% end %>
```

我用了[。each_with_index](http://ruby-doc.org/core-2.0/Enumerable.html#method-i-each_with_index) 可以轻松地将数字添加到图像标签的 id 中。

### 不浪费任何东西。

还记得我们写的 *slideshow.rb* 文件吗？让我们把它绑在这里。我们有几个常量，其中一个需要用变量替换。我把`PICTURES`换成了`@pictureslength`。我还删除了底部的循环。

如果我们让它那样，我们将有一个问题。`@offset`实例变量从不初始化。由于 nil 没有`#+`方法，我们将会得到一个错误。`nil can't be coerced into Fixnum`我们应该适当初始化@offset。

```
def initialize
  @offset = 0
end
```

这是我为 *slideshow.rb* 准备的

```
SLIDEDURATION = 5

def initialize
  @offset = 0
end

def slideShowDuration
  (@pictureslength * SLIDEDURATION).to_s
end

def setIDs i
  a = "img\#animation" + i.to_s + " {\n"
  a = a + " -webkit-animation:cycle" + i.to_s + " " +     slideShowDuration + "s infinite;\n"
  a = a + "}"
  a
end

def animation i
  a = "@-webkit-keyframes cycle" + i.to_s + " {\n"
  a = a + " #{@offset}% {opacity:0}\n"
  a = a + "  " + (10 / @pictureslength + @offset).to_s + "% {opacity:1}\n"
  a = a + "  " + (90 / @pictureslength + @offset).to_s + "% {opacity:1}\n"
  a = a + "  " + (100 / @pictureslength + @offset).to_s + "% {opacity:0}\n"
  a = a + "}"

  # increase the offset
  @offset = 100 / @pictureslength + @offset

  a
end
```

回到 *main.rb* 文件，你需要将`@pictureslength`变量设置为图片的长度。

```
get '/' do
  @pictures = load_pictures
  @pictureslength = @pictures.length
  erb :index
end
```

我们来看一下`@@index`模板。我们需要添加我们的 CSS 到它。在`title`标签后添加以下代码:

```
<style>
  <% @pictures.each_with_index do |picture, i| %>
    <%= setIDs i %>
  <% end %>
  <% @pictures.each_with_index do |picture, i| %>
    <%= animation i %>
  <% end %>
</style>
```

别忘了 *main.rb* 现在*需要*这个*的 slideshow.rb* 文件。继续把它添加到文件的顶部。

现在，如果你运行应用程序，图片会淡入淡出吗？

## 用更多的 CSS 使它变得更好。

因为这是幻灯片，我们想一次只看一张照片。我们可以设置所有图像的不透明度为零，过渡将使它们出现。图像也应该在页面的顶部，和浏览器窗口一样宽。哦，还有漂亮的深色背景

```
html, body {
    background-color: #333;
    margin:0;
    padding:0;
}
img {
  position: absolute;
  width: 100%;
  opacity:0
}
```

这就是全部。把它放在你的`style`部分开头的索引页上。

*main.rb*

```
require 'sinatra'
require './slideshow'

def load_pictures
  Dir.glob("public/slideshow_pictures/*.{jpg,JPG}")
end

get '/' do
  @pictures = load_pictures
  @pictureslength = @pictures.length
  erb :index
end

get '/upload' do
  erb :upload
end

post "/upload" do 
  File.open('public/slideshow_pictures/' + params['file'][:filename], "w") do |f|
    f.write(params['file'][:tempfile].read)
  end
  "uploaded."
end

__END__
@@index
<!DOCTYPE html>
<html>
  <head>
  <meta charset="UTF-8">
  <meta name="viewport" content="user-scalable=yes, width=device-width" />
<title>Moving Pictures</title>
  <style>
    html, body {
        background-color: #333;
        margin:0;
        padding:0;
    }
    img {
      position: absolute;
      width: 100%;
      opacity:0
    }
    <% @pictures.each_with_index do |picture, i| %>
      <%= setIDs i %>
    <% end %>
    <% @pictures.each_with_index do |picture, i| %>
      <%= animation i %>
    <% end %>
  </style>
</head>
<body>
  <% @pictures.each_with_index do |picture, i| %>
    <img id="animation<%= i %>" src="<%= picture.sub!(/public\//, '') %>" />
  <% end %>
</body>
</html>

@@upload
<!DOCTYPE html>
<html>
  <head>
  <meta charset="UTF-8">
  <meta name="viewport" content="user-scalable=yes, width=device-width" />
<title>Upload Moving Pictures</title>
</head>
<body>
  <form action="/upload" enctype="multipart/form-data" method="post">
    <label for="file">File to upload:</label>
    <input id="file" type="file" name="file">
    <input id="submit" type="submit" name="submit" value="Upload picture!">
  </form>
</body>
</html>
```

我们上次做测试是什么时候？你认为他们会通过吗？

```
$ ruby test/test.rb 
Run options: --seed 42735

# Running tests:

....

Finished tests in 0.073608s, 54.3419 tests/s, 81.5129 assertions/s.

4 tests, 6 assertions, 0 failures, 0 errors, 0 skips
```

咻。启动应用程序，观看所有不同阶段的直播

现在我们有了一个可以上传图片并以幻灯片形式观看的应用程序。

## 让我们回顾一下我们在这个系列中所做的事情。

*   从目录中读取文件。
*   约束文件类型。
*   Sinatra 应用程序的默认文件夹结构。
*   创建我们自己的错误消息。
*   如何写一篇糟糕的测试？
*   查看文件是否存在。
*   删除文件。
*   包括其他文件。
*   CSS 中的动画和关键帧。

我希望你喜欢这个系列，并对 Sinatra、Ruby 和/或 CSS 有所了解

## 分享这篇文章