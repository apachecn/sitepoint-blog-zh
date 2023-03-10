# 使用 mod_deflate 和 Apache 2.0.x 压缩 Web 输出

> 原文：<https://www.sitepoint.com/mod-deflate-apache-2-0-x/>

**在[我之前的文章](https://www.sitepoint.com/blog/)中，我们讨论了使用`mod_gzip`来动态压缩 Apache 1.3.x 服务器的输出。随着 Apache 2.0.x 系列 Web 服务器的使用越来越多，出现了一个问题，即我们如何在这个服务器中执行类似的 GZIP 编码功能。凭借远见卓识，Apache 2.0.x 服务器的开发人员在该服务器的代码库中包含了一个执行该任务的模块。**

##### 编译并启用`mod_deflate`

```
mod_deflate
```

包含在 Apache 2.0.x 源代码包中。它的编译很简单，只需将其添加到 configure 命令中，就像这样:

```
./configure --enable-modules=all --enable-mods-shared=all  

--enable-deflate
```

当服务器被制造和安装时，文档的 GZIP 编码很容易被启用。我们只需在 httpd.conf 文件中添加三行代码:

```
 SetOutputFilter DEFLATE 

  SetEnvIfNoCase Request_URI .(?:gif|jpe?g|png)$  

no-gzip dont-vary 

  SetEnvIfNoCase Request_URI .pdf$ no-gzip dont-vary
```

##### 它是做什么的？

这使得除图像和 PDF 文件之外的所有 MIME 类型在离开服务器时都能够自动进行 GZIP 编码。

图像文件和 PDF 文件被排除在外，因为它们已经是高度压缩的格式。事实上，如果通过`mod_deflate`或`mod_gzip`进一步压缩，Adobe 的 Acrobat Reader 就无法读取 pdf 文件。

在本文中我们用来测试`mod_deflate`的服务器上，没有向访问者提供 Windows 可执行文件或压缩文件。如果您交付这些类型的文件，请在 httpd.conf 文件中添加以下行，以防止它们以 GZIP 编码的格式发送。

```
 SetEnvIfNoCase Request_URI .(?:exe|t?gz|zip|bz2| 

sit|rar)$ no-gzip dont-vary
```

对于 exclude 语句中指出的文件类型，服务器被明确告知不要发送 Vary 头。Vary 报头向任何代理或高速缓存服务器指示将导致该响应不同于对同一请求的其他响应的特定条件。

如果客户端发送的请求不包含 Accept-Encoding: gzip 头，则缓存的项不会被返回。如果 Accept-Encoding 头不匹配，则存储在缓存中的项目不能返回给发出请求的客户端。在这种情况下，请求必须直接传递到源服务器，以获得非编码版本。实际上，代理服务器可能会存储同一文件的两个或更多副本，这取决于导致服务器响应不同的客户端请求条件。

删除未处理对象的变化响应要求意味着，如果对象没有由于服务器上的任何其他指令(例如，浏览器类型)而变化，则可以在没有任何附加请求的情况下提供缓存的对象，直到缓存的对象的生存时间(TTL)到期。

##### 比较起来怎么样？

在检查`mod_deflate`与`mod_gzip`的性能时，似乎区别`mod_deflate`的一个项目是使用的压缩算法。`mod_deflate`算法使用 ZLIB，在压缩文件方面似乎不如用于 Apache 1.3.x 的`mod_gzip`的 GZIP 方法有效。下面的例子表明，对于相同的文件，`mod_gzip`的压缩算法比`mod_deflate`多产生 4-6%的压缩。

![1129_test1](img/b63b07d389854bbf4451691cdbd24731.png)
表 1-/compress/home page 2 . html

![1129_test2](img/3d9a34f3c243e8c96edf6191ee27a754.png)
表 2-/documents/spierzchala-resume . PS

尝试使用为该模块提供的指令来增加`mod_deflate`的压缩比，但传输的文件大小没有进一步减小。来自作者之一`mod_deflate`的评论指出，该模块是专门为确保使用这种压缩方法不会降低服务器性能而编写的。在这个模块的未来版本中，`mod_deflate`的作者可能想要将他们的算法与`mod_gzip`中使用的算法进行比较，看看是否有办法在不损害服务器性能的情况下提高`mod_deflate`中实现的压缩率。

##### 结论

尽管压缩算法不如 Apache 1.3.x 中的`mod_gzip`有效，但在 Apache 2.0.x 中使用`mod_deflate`仍然是减小发送给客户端的文件大小的一个很好的方法。对于任何和所有 Apache 2.0.x 部署来说，任何事只要花这么少的力气就可以节省 50%到 80%的带宽，这是绝对应该考虑的。

## 分享这篇文章