# 使用 Munee 实现自动资产优化

> 原文：<https://www.sitepoint.com/automatic-asset-optimization-with-munee/>

Munee 是一个资产管理工具，可以编译 LESS、SCSS 或 CoffeeScript，操作图像，缩小 CSS 和 JS，并在服务器和客户端上动态缓存资产。它适用于 PHP 5.3 和更新的版本。

![Stock photo, folder labeled "Assets"](img/469d1c0e91ac1a6f3ec1cf856e85947c.png)

在本教程中，我们将学习 Munee 如何轻松地在模板中包含资产，如何安装，如何工作以及如何使用它。

Munee 是 PHP 应用程序资产管理中避免 NodeJS 的另一种方式。

## 你为什么想用穆尼？

Munee 的目标是让资产管理更容易。它为我们动态地执行了许多任务(例如，当客户请求资产时)，这些任务是我们以前必须手动完成的，因此节省了时间。

以下是您可能想要使用 Munee 的一些原因:

1.  我们经常对 CSS、LESS、SCSS、JavaScript 和 CoffeeScript 文件做一些小的改动。每次我们做改变时，我们都需要编译和缩小它们。穆尼在飞行中为我们做这些。

2.  有许多共享的虚拟主机服务器没有启用 gzip。如果您使用的是 Munee，它会忽略这一限制，使用 PHP 压缩文件。

3.  您不必担心向*添加缓存指令。htaccess* 文件。Munee 将负责服务器端和客户端的资产缓存。

4.  Munee 可以即时处理图像，对于响应式网页设计来说非常方便。您不必为不同的维度维护不同的图像文件。

这些只是你能发现 Munee 非常有用的一些情况。

## 穆尼是怎么工作的？

一旦安装了 Munee，它会自动开始在服务器上缓存资产，发送适当的客户端缓存头，并开始发送 gzip 压缩响应，以及请求的 LESS、SCSS 和 CoffeeScript 文件的编译输出。

要向 Munee 提供操作图像或缩小 CSS 和 JS 文件的指令，您需要向资源路径添加查询字符串参数。

为了能够操纵或缩小资产，Munee 需要拦截客户对 CSS、LESS、SCSS、JS、CoffeeScript 和各种图像格式文件的请求。为了让 munee 拦截客户对这些资产的请求，我们需要向*添加一个内部重写规则。htaccess* 文件(Nginx 指令也跟在后面)。

Munee 使用其他第三方库如`imagine`、`leafo`、`meenie`、`tedivm`等。调整大小，操纵，编译和缩小资产。

旨在向 Munee 提供指令的查询字符串参数称为过滤器。

## Munee 如何缓存资产？

为了实现客户端缓存，它在为请求的资产发送响应时设置`Cache-Control: must-revalidate`头。它还读取 HTTP 请求中的缓存头，并根据客户端的缓存中是否有最新的资产来发送响应或`304 Not Modified`状态。

为了实现服务器端缓存，它在一个单独的目录中存储编译、缩小和操作的资产。

它可以在运行时检测原始资产的变化。当它检测到原始资产发生变化时，它会更新服务器缓存，并强制客户端使用最新的文件。

## 安装 Munee

安装方式:

```
composer require meenie/munee
```

如果您试图在共享的托管服务器上安装 Munee，请在您的本地计算机上手动安装`composer require` Munee，并将`vendor`目录上传到托管服务器。

现在我们需要创建一个 PHP 文件，它负责使用 Munee 优化、操作、编译和缩小资产。

为此，创建一个名为`munee.php`的 PHP 文件:

```
<?php require  "vendor/autoload.php"; echo \Munee\Dispatcher::run(new \Munee\Request());
```

现在我们需要将对 CSS、LESS、s CSS、JS、CoffeeScript 和各种图像格式文件的请求重定向到 munee.php。我们可以使用服务器的内部 URL 重写规则来做到这一点。

如果您使用的是 Apache，请将这段代码放在*中。munee.php*文件所在目录的 htaccess 文件:

```
RewriteEngine  On  RewriteRule  ^(.*\.(?:css|less|scss|js|coffee|jpg|png|gif|jpeg))$ munee.php?files=/$1 [L,QSA,NC]
```

如果你正在使用 Nginx，那么你必须根据[这个问题](https://github.com/meenie/munee/issues/4)和[这个要点](https://gist.github.com/pepebe/9738015)修改 URL 重写规则的实际虚拟主机设置。

如果在您的服务器上禁用了`.htaccess`，或者您不想使用`.htaccess`重写，那么您可以手动将文件路径传递到`munee.php`，而不是 HTML 中的资产路径:

```
<script  src="munee.php?files=index.js"/>
```

在本教程的其余部分，我们将假设您使用的是`.htaccess`。

安装 Munee 的目录树中的所有资产都将被优化。

## 编译 SCSS，少和 CoffeeScript

要将 SCSS/LESS 和 CoffeeScript 分别编译成 CSS 和 JavaScript，需要在 HTML 中指向这些文件。Munee 将在服务器端自动处理编译。

下面是演示这一点的一些示例代码:

```
<link  rel="stylesheet"  href="index.scss">  <link  rel="stylesheet"  href="index.less">  <script  src="index.coffee"/>
```

对于 SCSS 和更少的文件，响应头`Content-Type`将是`text/css`，响应正文将被编译成 CSS。

类似地，对于 Coffee 文件，响应头`Content-Type`将是`text/javascript`，响应体将被编译为 JS。

## 缩小 CSS 和 JS 文件

为了缩小 CSS 和 JS 文件或者编译得更少，SCSS 和 CoffeeScript 输出我们需要在 HTML 中添加`minify=true`到资产的路径:

```
<link  rel="stylesheet"  href="index.scss?minify=true">  <link  rel="stylesheet"  href="index.less?minify=true">  <link  rel="stylesheet"  href="index.css?minify=true">  <script  src="index.js?minify=true"  />  <script  src="index.coffee?minify=true"  />
```

## 操纵图像

Munee 让我们可以随时调整图像的大小、裁剪和着色。除此之外，它还可以让我们将图像转换为灰度，将图像转换为负片，还可以使用默认图像来代替丢失的图像。

### 调整大小，拉伸和裁剪图像

要动态调整图像大小，我们需要使用`resize`滤镜。`resize`过滤器的值是一组参数。

下面是一个例子，展示了如果图像的高度和宽度超过 100 像素，如何将它更改为 100 像素:

```
<img  src="myImage.jpg?resize=width[100]-height[100]">
```

如果我们想在保持纵横比的情况下调整大小，我们也可以只提供 height 或 width 参数。

为了裁剪图像，我们需要提供一个`exact[true]`参数以及高度和宽度。这里有一个例子来说明这一点:

```
<img  src="myImage.jpg?resize=width[100]-height[100]-exact[true]">
```

在这里，图像将被裁剪为 100 像素的高度和宽度。

我们还可以添加`stretch=true`来拉伸或调整图像到 100 像素的高度和宽度。这里有一个例子来说明这一点:

```
<img  src="myImage.jpg?resize=width[100]-height[100]-stretch[true]">
```

Munee 的这些特性对于响应性和适应性的网页设计非常有用。

### 缺失图像的占位符

如果服务器中缺少图像，那么我们可以使用占位符图像。对于此功能，没有过滤器。相反，您必须将这些指令添加到`munee.php`文件中:

```
require  "vendor/autoload.php"; echo \Munee\Dispatcher::run(  new \Munee\Request(array(  "image"  => array(  "placeholders"  => array(  "/tourism/*"  =>  "http://placedog.com/100/100",  "*"  => WEBROOT . DS .  "img"  . DS .  "my-placeholder-image.jpg"  )  )  ))  );
```

在这里的`placeholders`数组中，我们可以为不同子目录中丢失的图像占位符提供一个 URL 或路径。在上面的例子中，对于所有在`tourism`子目录中丢失的图像，我们使用`http://placedog.com/100/100`作为占位符，而对于其他丢失的图像，我们使用`my-placeholder-image.jpg`。

### 图像处理安全性

图像处理需要大量内存和 CPU 时间。黑客可以通过一次又一次地请求经过处理的图像来关闭您的服务器。Munee 有解决这个问题的内置方法:

1.  Referer 应该与图像所在的域相同。
2.  在 5 分钟的时间内，它不会保留一个图像的 3 个以上的操作版本。

## 合并资产

开发人员通常将 JavaScript 和 CSS 文件结合起来，以减少获取所有资产所需的 HTTP 请求的总数。Munee 可以结合 HTML，CSS，JS，LESS，SCSS 和 CoffeeScript 文件。

要组合资产，我们只需要提供 HTML 中逗号分隔的文件路径。

这里有一个例子来说明这一点:

```
<link  rel="stylesheet"  type="text/css"  href="index.css,index.scss?minify=true">  <script  src="index.js,index.coffee?minify=true"></script>
```

这里我们将`index.css`和`index.scss`合并成一个文件。我们也通过了`minify=true`来缩减产量。Munee 在发送响应之前会将`index.scss`编译成 CSS。

类似地，我们将`index.js`和`index.coffee`合并成一个文件。

## 穆尼 API

该库还为应用程序提供了一个 API 来手动优化、操作和缩小资产。如果您计划构建一个使用 Munee 作为依赖项的库，那么您只需要学习这个 API。

您可以在官方文档中找到关于这个 API 的更多信息。

## 结论

管理资产有许多其他的选择，但是如果你想让资产在运行中被管理，Munee 似乎是最好的选择。您可以在他们的官方文档中了解 Munee 提供的其他各种过滤器。

你如何管理你的资产？走 NodeJS 路线还是[避开](https://www.sitepoint.com/look-ma-no-nodejs-a-php-front-end-workflow-without-node/)？你试过穆尼吗？请在评论中告诉我们！

## 分享这篇文章