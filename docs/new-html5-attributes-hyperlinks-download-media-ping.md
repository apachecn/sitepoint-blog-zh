# 超链接的新 HTML5 属性:下载、媒体和 ping

> 原文：<https://www.sitepoint.com/new-html5-attributes-hyperlinks-download-media-ping/>

自从网络出现以来，超链接就一直存在。但是随着 HTML5 的出现，三个新的属性被添加到不起眼的`<a>`标签中，以保持现有的属性，如`href`、`rel`和其他属性。

新的属性有:`download`、`media`和`ping`。在本文中，我们将快速了解这些新属性是什么，以及一旦浏览器支持得到改进，如何使用它们。

## `download`属性

`download`属性是 HTML5 中的新特性。它补充了现有的`href`属性，告诉浏览器`href`指向的资源应该直接下载，而不是访问(对于浏览器可以打开的文件，如 PDF，这可能会发生)。`download`属性的值用于下载文件的名称。

`download`属性可以与`href`属性中引用的文件相同，但这不是必须的。能够为`href`和`download`设置不同的值可以派上用场。例如，您可能需要在服务器上为每个用户的月度或年度报告动态地生成唯一的文件，但是当用户下载文件时，仍然要为他们提供一个有意义的文件名。因为下载属性可能与 href 不同，所以这很容易做到:

```
<a href="files/eid987jdien2i.pdf"
   download="Monthly Report for March 2014.pdf">Download March 2014 Report</a>
```

当用户点击下载链接时，他们将下载三月份 2014.pdf 的月度报告，而不是可爱的 eid987jdien2i.pdf。

理论上，对于您可以为`download`属性输入什么没有任何限制。实际上这并不完全正确，因为你需要记住操作系统对文件名中不能使用的字符的限制——比如 Windows 上的反斜杠“\”，或者*nix 和 OS X 上的正斜杠“/”——浏览器可能会相应地调整`download`属性的值。同样值得注意的是，`download`属性的值可以被 [`Content-Disposition` HTTP 头的`filename`参数](https://www.w3.org/Protocols/rfc2616/rfc2616-sec19.html#sec19.5.1)覆盖。

`download`属性可以与 [blob](https://developer.mozilla.org/en-US/docs/Web/API/URL.createObjectURL) 和[数据 URI](https://www.sitepoint.com/reducing-http-requests-with-generated-data-uris/) 一起使用，这在用户需要能够保存他们通过你的 web 应用程序(例如绘图应用程序)创建的动态内容时很有用。

通常，您会将属性`href`设置为 blob 或数据 URI，然后，与前面的月报示例一样，将属性`download`设置为更有意义的文件名。下面的例子展示了在使用 Canvas API 时如何做到这一点。

这是我们的 HTML:

```
<section>
    <canvas id="c" width="400" height="400"></canvas>
    <footer>
        <a id="download-canvas" href="#">Download</a>
    </footer>
</section>
```

还有 JavaScript:

```
(function() {
    var canvas = document.getElementById('c'),
        cxt = canvas.getContext('2d'),
        downloadLink = document.getElementById('download-canvas');

    cxt.fillRect(100, 100, 200, 200);
    cxt.clearRect(150, 150, 100, 100);

    downloadLink.href = canvas.toDataURL();
    downloadLink.download = "squares.png";
})();
```

这里有一个现场演示:

[JS 斌](http://jsbin.com/buhoreli/1/embed?html,output)