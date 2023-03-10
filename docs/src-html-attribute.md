# src (HTML 属性)

> 原文：<https://www.sitepoint.com/src-html-attribute/>

## 描述

`src`属性指示浏览器应该在服务器的什么地方寻找要呈现给用户的图像。这可能是同一目录中的映像、同一服务器上其他位置的映像或存储在另一台服务器上的映像。

该示例引用了一个图像，该图像与调用它的网页位于同一目录中，但是如果该图像存储在比引用文档高一级的目录中，则语法如下:

```
<img src="*../*giant-prawn.jpg" alt="The Giant Prawn at Ballina"/>
```

这里，`../`相当于“在层次结构中上移一个目录。”

您还可以引用相对于网站根目录(即域名后的任何文件或文件夹)的图像:

```
<img src="/giant-prawn.jpg" alt="The Giant Prawn at Ballina"/> 
```

这基本上意味着“显示可以在 www.example.com/."找到的图像 giant-prawn.jpg 这是一种非常方便的引用文件的方式，因为你可以将引用该图像的文档移动到文件系统上的任何位置，而不会断开链接。

如果您引用的是保存在另一台服务器上的图像，您可以使用完整的 URI 来表示 src，如下所示:

```
<img *src="http://www.example.com/giant-prawn.jpg"*
    alt="The Giant Prawn at Ballina"/> 
```

## 例子

该图像的`src`属性显示该图像位于与网页相同的目录中:

```
<img *src="giant-prawn.jpg"* alt="The Giant Prawn at Ballina"/>
```

## 价值

该属性的值是图像相对于引用文档的位置、相对于服务器根目录的位置，或者是包含`http://`协议、服务器名称和该服务器上文档的路径的完整 URI。

## 分享这篇文章