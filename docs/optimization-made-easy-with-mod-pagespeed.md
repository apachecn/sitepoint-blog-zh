# mod_pagespeed 简化了优化

> 原文：<https://www.sitepoint.com/optimization-made-easy-with-mod-pagespeed/>

上周 Google 的人发布了一个重大声明:他们为 Apache web 服务器发布了一个新模块:mod_pagespeed。mod_pagespeed 将一系列服务器端页面速度优化捆绑到一个易于使用的模块中。通过适当的配置选项，它将压缩和合并您的 CSS 和 JavaScript 文件，优化缓存设置，删除文件中的注释和空白，优化图像，等等。我们开着它转一圈，好吗？

## 装置

mod_pagespeed 模块是一个简单的安装包。这是个好消息:您不需要从源代码重新编译 Apache 来使用它。目前，它适用于 CentOS/Fedora 和 Ubuntu/Debian 系统，无论它们是 32 位还是 64 位。出于本演练的目的，我将使用 Ubuntu，但是您可以在[下载页面](http://code.google.com/speed/page-speed/download.html)上找到 CentOS/Fedora 的安装说明。下载。deb 文件，然后运行(当然，假设您已经安装了 Apache 2.2):

```
sudo dpkg -i mod_pagespeed_*.debsudo apt-get -f install
```

现在您只需要重启 Apache 来启用新模块:

```
sudo /etc/init.d/apache2 restart
```

## 配置

默认情况下，mod_pagespeed 的所有滤镜都是关闭的，所以实际上你不会看到它做任何事情。为了解决这个问题，我们需要编辑它的配置文件。默认情况下(在基于 Ubuntu 或 Debian 的系统上)，这个文件位于/etc/Apache 2/MODS-available/pagespeed . conf 中。

```
<IfModule pagespeed_module>  SetOutputFilter MOD_PAGESPEED_OUTPUT_FILTER  ModPagespeed on
```

那就好！如果您看到 ModPagespeed 关闭，请在继续操作之前将其更改为打开。文件再往下一点(我的版本中的第 29 行)，您会看到这样一个注释掉的行:

```
# ModPagespeedRewriteLevel CoreFilters
```

正如该行上面的注释块所解释的，CoreFilters 的默认重写级别将为您提供一组基本的优化，对大多数网页来说是安全的。取消注释，并重启 Apache 服务器。现在，如果您从服务器加载任何页面，您应该会注意到一些事情发生了。例如，多个小的 CSS 文件将被合并到页面中的一个内联标签中。mod_pagespeed 在处理这个问题的方式上很聪明:它权衡了缓存的潜在价值和额外 HTTP 请求的成本，缓存的潜在价值来自于单独提供 CSS 文件。因此，只有一定大小或更大的文件才会这样内联(当然，您可以自己配置这个大小阈值)。这些过滤器相当保守，所以让我们启用更多！在 conf 文件的下面(对我来说是第 46 行)，有这样一行:

```
# ModPagespeedEnableFilters collapse_whitespace,elide_attributes
```

`ModPagespeedEnableFilters`声明只是接受一个逗号分隔的您想要启用的过滤器列表。默认情况下，它用`collapse_whitespace` (顾名思义，它将折叠 HTML 文件中多余的空白字符)和`elide_attributes`写出。后者是一个棘手的问题(也是谷歌对字节计数有多认真的一个指标):它将缩短任何对浏览器没有影响的 HTML 属性。所以，比如说，`disabled="disabled"`的工作方式和刚才的`disabled`完全一样，所以 mod_pagespeed 会把前者改成后者。`type="text"`是`input`元素的默认值，所以 mod_pagespeed 将完全删除它。一旦您取消了该行的注释，您就可以通过将所有其他可用的过滤器串到它的末尾来进行试验。还有`remove_comments`，它从你的 HTML 中去除注释(但是足够智能*而不是*去除你的 IE 条件注释)。还有`optimize_images`，它对通过 img 标签加载的图像进行重新缩放、重新压缩和剥离元数据。对于完全痴迷的人来说，甚至还有`remove_quotes`，它去掉了 HTML 属性周围不必要的引号(所以`class="description"`将变成`class=description`)。在[项目的谷歌代码页](http://code.google.com/speed/page-speed/docs/filters.html)上可以找到可用过滤器的完整列表以及关于它们如何工作和如何使用的描述

## 还有什么？

mod_pagespeed 还提供了一些简单的方法来跟踪关于页面性能的统计数据。为此，它在页面的顶部和底部注入微小的 JavaScript 片段，并使用这些片段向服务器报告加载时间。这样，您可以测量各种不同滤镜的效果，并决定您想要继续使用哪些滤镜。那么，你会急着在你的服务器上设置 mod_pagespeed 吗？

**note:**Want more?

如果你想阅读更多关于 Louis 的文章，请订阅我们每周的科技极客时事通讯， *[《科技时报》](https://www.sitepoint.com/newsletter/)* 。

## 分享这篇文章