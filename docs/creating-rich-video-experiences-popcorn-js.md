# 使用 Popcorn.js 创建丰富的视频体验

> 原文：<https://www.sitepoint.com/creating-rich-video-experiences-popcorn-js/>

考虑到在网站上嵌入视频是多么容易，开发者经常止步于简单的旧嵌入视频是一个奇迹。如果你能为你的用户创造一个由视频本身引导的完整体验会怎么样？有了 Popcorn.js，你就可以做到这一点——本文将教你如何去做。

## Popcorn.js 基础

让我们从小处着手。我们将安装 Popcorn.js，然后立即创建一个“Hello World”示例并运行。

### 1.创建一个简单的 HTML 页面

我们将从创建一个包含以下内容的`index.html`文件开始。不出所料，`.video` `div`将是我们的视频最终出现的地方。

```
<!doctype html>
<html>
  <head>
    <title>Hello World Popcorn.js</title>
  </head>
  <body>
    <div class="video"></div>
  </body>
</html>
```

### 2.包括我们的定制风格

接下来，让我们为我们的项目包括一些基本的风格。上一个 HTML 页面更新后的`head`部分如下所示。

```
<head>
  <title>Hello World Popcorn.js</title>
  <style>
    .video, #map
    {
      width:300px;
      height:200px;
      float:left;
    }
  </style>
</head>
```

### 3.包含爆米花. js

接下来，我们需要包含 Popcorn.js 库本身。在 [Popcorn.js 下载页面](http://popcornjs.org/download)上有很多选项可供选择。我们将使用“完整”版本，包括所有官方 Popcorn.js 插件。在本教程中，我们将使用缩小版，但是如果您想了解源代码，也可以使用开发版。请注意以下代码示例中的附加导入。

```
<head>
  <title>Hello World Popcorn.js</title>
  <link href="http://netdna.bootstrapcdn.com/bootstrap/3.1.1/css/bootstrap.min.css" rel="stylesheet">
  <style>
    ...
  </style>
  <script type="text/javascript" src="http://cdn.popcornjs.org/code/dist/popcorn-complete.min.js"></script>
</head>
```

### 4.创建并包含`app.js`

接下来，让我们为我们的应用程序创建主 JavaScript 文件。这是我们初始化 Popcorn.js 实例的地方。创建`app.js`并将其包含在您的`index.html`文件中。`app.js`的内容如下所示。

```
// Wait for the DOM to load
window.onload = function() {
  // Create popcorn instance from the YouTube video and insert it into the .video div
  var pop = Popcorn.youtube(".video", "http//www.youtube.com/watch?v=x88Z5txBc7w");

  // Play the video automatically
  pop.play();
};
```

此处显示了更新后的`index.html` `head`部分。

```
<head>
  <title>Hello World Popcorn.js</title>
  <style>
    ...
  </style>
  <script type="text/javascript" src="http://cdn.popcornjs.org/code/dist/popcorn-complete.min.js"></script>
  <script type="text/javascript" src="app.js"></script>
</head>
```

在`app.js`中，我们使用了`Popcorn`对象的`youtube()`方法从 YouTube URL 创建一个视频实例。第一个参数是我们的`.video` `div`的选择器。Popcorn.js 将在这里插入我们的视频。第二个参数是 YouTube URL。在文件的最后，我们调用`pop.play()`,它确实如我们所说的那样——播放我们的视频。

### 5.灯光，摄像机，开拍！

为了确保 YouTube 播放器正常运行，我们需要从`http://`服务器提供这个页面，而不是简单地在我们的浏览器(`file:///`)中打开文件。在 Mac OS X 上，这就像在项目目录中运行以下命令一样简单。

```
python -m SimpleHTTPServer 2723
```

您现在可以在`http://localhost:2723`访问您的网站。Windows 用户可能会考虑 [lighttpd](http://www.lighttpd.net/) 或者[mongose](https://code.google.com/p/mongoose/)。

一旦你设置好了你的 HTTP 服务器，在你的浏览器中打开页面，你会看到 Yakko 在唱一首关于世界各国的歌！接下来，我们将使用 Popcorn.js 在视频播放时操作页面。

## 用 Popcorn.js 引导你的页面

我们将允许我们的视频通过在视频中的特定点运行一些定制的 JavaScript 片段来“指导”DOM。你可以在这里看到我们最终的现场演示:

见[码笔](http://codepen.io)上 Brad ( [@Haizyfox](http://codepen.io/Haizyfox) )的笔 [FudAq](http://codepen.io/Haizyfox/pen/FudAq/) 。