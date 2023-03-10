# OSCON 2006: Ajax 优化技术

> 原文：<https://www.sitepoint.com/oscon-2006-ajax-optimization-techniques/>

本周，凯文·杨克从俄勒冈州波特兰市的 [OSCON 2006](http://conferences.oreillynet.com/os2006/) 发回报道。

Zimbra 的 Kevin Henrikson 做了一个简短的演讲，介绍了他的组织所学到的一些经验教训，以及为提高依赖于大型 JavaScript/CSS 代码库的 web 应用程序的性能而实施的“卑鄙手段”。这是他所涉及项目的快速浏览。演讲的[幻灯片](http://www.zimbra.com/blog/archives/2006/07/oscon_ajax_slid.html)在 [Zimbra 博客](http://zimbra.com/blog/)上。

首先，一个发现问题的好工具:[网页分析器](http://www.websiteoptimization.com/services/analyze/)将报告组成任何给定页面的 HTML、CSS、JavaScript 和图像的相对大小。有时候，开发人员会非常努力地压缩他们的图像，只为了提供数百千字节的 JavaScript 代码，这个工具可以让您很快发现这样的问题。

Kevin 的第一条建议是不要害怕将多个文件合并成一个文件。这适用于 JavaScript 和 CSS，尽管它没有减少数据的大小，但它可以显著地缩短加载时间，因为浏览器一次只会请求一定数量的文件。

正如我在之前报道的那样，压缩 JavaScript 可能是一个棘手的问题。有很多工具完全是垃圾。但是这里有几个工作得相当好的:

*   [JSmin](http://www.crockford.com/javascript/jsmin.html) —攻击性，但不修改名称
*   [ShrinkSafe](http://alex.dojotoolkit.org/shrinksafe/) —不太激进(更可读的输出)，但减少了变量名
*   [mod_gzip](http://sourceforge.net/projects/mod-gzip/) ， [mod_deflate](http://httpd.apache.org/docs/2.0/mod/mod_deflate.html) — Apache 模块，在将网页内容发送到支持解压缩的浏览器之前对其进行压缩

一旦优化了 JavaScript(和其他代码)，就应该确保代码在客户端得到了正确的缓存。这里的关键是要知道你的 HTTP 头，最好的方法是获得一个类似于 [Tamper Data](http://tamperdata.mozdev.org/) 的工具，这是一个 Firefox 扩展，可以让你分析 HTTP 请求和后台的响应。

凯文接着看了几个他处理过的案例研究。第一篇围绕着他大约七个月前写的一篇高调的博客文章， [AJAX 和 CSS 优化](http://www.zimbra.com/blog/archives/2006/01/zimbra_ajax_css_digg.html)，批评了新推出的(当时)版本的 [Digg](http://digg.com/) ，其前端包含超过 80%的 JavaScript 代码，他使用上述 JavaScript 压缩工具成功地大幅缩减了这些代码。

他的第二个案例研究是[lala.com](http://lala.com/)，其 HTTP 缓存是完美的，但该网站需要浏览器下载如此多的文件，加载时间因同时请求数量的限制而大大降低。当使用诸如篡改数据这样的工具查看站点的请求时间线时，这一点很明显。

## 分享这篇文章