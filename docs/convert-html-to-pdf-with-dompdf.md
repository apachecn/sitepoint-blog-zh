# 用 Dompdf 将 HTML 转换成 PDF

> 原文：<https://www.sitepoint.com/convert-html-to-pdf-with-dompdf/>

PDF 是一种标准格式，最初由 Adobe 创建，用于在固定布局的文档中表示文本和图像。web 应用程序支持下载 PDF 格式的数据(如发票或报告)并不少见，因此在本文中，我们将介绍如何使用 PHP 轻松生成 PDF 文档。

Dompdf 是一个很棒的库，能够从 HTML 标记和 CSS 样式生成 pdf(它基本上符合 CSS 2.1，并支持一些 CSS3 属性)。我们可以使用这些熟悉的技术来决定我们的内容应该是什么样子，然后轻松地将其转换成固定的文档。该库还有许多其他有用和有趣的特性。

## 入门指南

Dompdf 在 GitHub 上可用，可以使用 Composer 安装。不可否认，安装并正确运行基于 Composer 的程序仍然有点棘手，所以我推荐使用 Git 来安装 Dompdf。

该库要求 PHP >= 5.0 并启用 mbstring 和 DOM 扩展。它还需要一些字体，这些字体一般在大多数机器上都有。

导航到您想要放置库的任何位置并执行:

```
git clone https://github.com/dompdf/dompdf.git
git submodule init
git submodule update
```

下载了 Dompdf 之后，让我们编写一个简短的例子来生成一个简单的 pdf。

```
<?php
set_include_path(get_include_path() . PATH_SEPARATOR . "/path/to/dompdf");

require_once "dompdf_config.inc.php";

$dompdf = new DOMPDF();

$html = <<<'ENDHTML'
<html>
 <body>
  <h1>Hello Dompdf</h1>
 </body>
</html>
ENDHTML;

$dompdf->load_html($html);
$dompdf->render();

$dompdf->stream("hello.pdf");
```

为了在项目中使用这个库，我们首先引入包含大部分 Dompdf 配置的`dompdf_config.inc.php`。它还会加载一个自动加载程序和一个自定义配置文件，我们可以在其中覆盖默认配置参数。

HTML 标记作为一个字符串提供给`load_html()`方法。或者，我们可以使用`load_html_file()`方法从文件或 URL 加载标记。它接受文件名或网页的 URL 作为参数。

`render()`方法将 HTML 呈现为 PDF，我们准备转储 PDF 文件。`stream()`将生成的 PDF 作为附件发送到浏览器。`stream()`方法有一个可选的第二个参数，一组选项:

*   `Accept-Ranges`–布尔值，发送“接受范围”标题(默认为 false)。
*   `Attachment`–boolean，发送“Content-Disposition: attachment”标头，强制浏览器显示保存提示(默认为 true)。
*   `compress`–布尔值，启用内容压缩(默认为 true)。

我们在这里生成了一个非常简单的 PDF，但这不是很实用。实际上，我们经常会对纸张大小、页面方向、字符编码等有所要求。我们可以设置许多配置选项，使 Dompdf 更适合我们的实际需求。所有这些都在`dompdf_config.inc.php`中列出并解释，将其设置为默认值。您可以通过更新自定义配置文件`dompdf_config.custom.inc.php`来更改这些值。一些重要的设置包括:

*   `DOMPDF_DEFAULT_PAPER_SIZE`–设置 PDF 文档的默认纸张尺寸。支持的纸张尺寸可在`include/cpdf_adapter.cls.php`中找到(默认值为“letter”)。
*   `DOMPDF_TEMP_DIR`–指定 Dompdf 使用的临时目录。请确保此位置可由 web 服务器帐户写入。
*   `DOMPDF_UNICODE_ENABLED`–设置 PDF 是否使用 Unicode 字体(默认为 true)。
*   `DOMPDF_ENABLE_REMOTE`–允许包含来自远程站点的图像或 CSS 样式(默认为 false)。
*   `DEBUG_LAYOUT`–是否在 PDF 文件中的每个 HTML 块周围绘制一个框，这对调试布局很有用(默认为 false)。

## 高级用法

现在来说说 Dompdf 的一些高级用法。也许我们希望将生成的 PDF 文档保存到磁盘，而不是发送到浏览器。方法如下:

```
<?php
$dompdf = new DOMPDF();
$dompdf->load_html($html);
$dompdf->render();

$output = $dompdf->output();
file_put_contents("/path/to/file.pdf", $output);
```

我们没有像前面的例子那样调用`stream()`，而是使用`output()`，它以字符串的形式返回 PDF。它也接受可选的选项数组，但是唯一可用的选项是 compress(缺省值为 true)。

Dompdf 还允许我们通过在生成的 HTML 中嵌入 PHP 脚本来添加页眉或页脚。但是因为处理任意代码可能会带来安全风险，所以默认情况下，控制该功能的配置值是关闭的。我们需要首先将`DOMPDF_ENABLE_PHP`选项设置为真。

一旦我们启用了内联 PHP 执行，PDF 对象将在脚本中可用，我们可以用它来操作页面。我们可以添加文本、线条、图像、矩形等。页面内的任何地方。

```
$html = <<<'ENDHTML'
<html>
 <body>
  <script type="text/php">
if (isset($pdf)) {
    // open the PDF object - all drawing commands will
    // now go to the object instead of the current page
    $footer = $pdf->open_object();

    // get height and width of page
    $w = $pdf->get_width();
    $h = $pdf->get_height();

    // get font
    $font = Font_Metrics::get_font("helvetica", "normal");
    $txtHeight = Font_Metrics::get_font_height($font, 8);

    // draw a line along the bottom
    $y = $h - 2 * $txtHeight - 24;
    $color = array(0, 0, 0);
    $pdf->line(16, $y, $w - 16, $y, $color, 1);

    // set page number on the left side
    $pdf->page_text(16, $y, "Page: {PAGE_NUM} of {PAGE_COUNT}", $font, 8, $color);
    // set additional text
    $text = "Dompdf is awesome";
    $width = Font_Metrics::get_text_width($text, $font, 8);
    $pdf->text($w - $width - 16, $y, $text, $font, 8);

    // close the object (stop capture)
    $pdf->close_object();

    // add the object to every page (can also specify
    // "odd" or "even")
    $pdf->add_object($footer, "all");
}
  </script>
  <h1>Hello Dompdf</h1>
 </body>
</html>
ENDHTML;
```

该脚本直接嵌入到 HTML 标记中，首先打开一个对象，这样我们就可以影响呈现。所有绘图将被记录到该对象中，我们可以将该对象添加到所有或选定的页面中(尽管有一些限制)。

接下来，我们获取页面的实际宽度和高度，以计算我们想要添加的页脚的坐标。此外，我们需要在添加文本内容时提供一个字体对象。让我们创建我们需要的对象。我们还使用 get_font_height()来计算页脚内容的位置，从而得到给定字体的高度。方法`get_font_width()`返回给定字体和字体大小的文本宽度，这也在我们的计算中使用。

`line()`方法从点(X1，Y1)到点(X2，X2)画一条线。请注意，我们提供的颜色值不是实际的 RGB 值。底层 PDF 类需要介于 0 和 1 之间的值，因此我们将 RGB 值转换成这些新值。为了得到一个更好的近似值，你可以把它除以 255。

我们使用 page_text()方法为每个页面添加页码，该方法接受 X 和 Y 位置、要添加的文本、字体对象、字体大小和颜色。Dompdf 自动替换每页中的`{PAGE_NUM}`和`{PAGE_COUNT}`的值，并使`$pdf`对我们可用。

当 PDF 呈现时，页脚部分将如下所示:

![dompdf-01](img/020608d0dba86938709146d8653cb2c1.png)

可以避免使用内联 PHP，直接从 PHP 获得相同的效果，如下所示:

```
<?php
$dompdf = new DOMPDF();
$dompdf->set_paper("A4");

// load the html content
$dompdf->load_html($html);
$dompdf->render();
$canvas = $dompdf->get_canvas();
$font = Font_Metrics::get_font("helvetica", "bold");
$canvas->page_text(16, 800, "Page: {PAGE_NUM} of {PAGE_COUNT}", $font, 8, array(0,0,0));
$dompdf->stream("sample.pdf",array("Attachment"=>0));
```

请注意，我们将代码放在调用`$dompdf->render()`之后，因为我们实际上是在修改呈现的 PDF。

## 结论

在本文中，我们讨论了如何使用 Dompdf 轻松地将 HTML 转换为 PDF。尽管 Dompdf 是一个很棒的库，但它不是生成 pdf 文档的可靠解决方案；它确实有一些限制和问题。Dompdf 并不真正容忍格式不良的 HTML，大表很容易导致内存不足。一些基本的 CSS 特性如 float 并不完全被支持，对 CSS3 的支持也很有限。如果您需要 Dompdf 不支持的特性，wkhtmltopdf 可能是一个更好的解决方案。不过，Dompdf 相当简单，适合大多数 pdf 导出需求。

在这样的文章中解释一个库提供的所有特性确实很困难，所以一定要查看文档和源代码，了解一些很酷的特性，比如添加回调、使用自定义字体等。另外，我很乐意在我有限的专业知识范围内帮助你。欢迎在评论区留下你的问题和分享你的经历。

图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")

如果你喜欢读这篇文章，你会爱上[可学的](https://learnable.com?utm_source=sitepoint&utm_medium=link&utm_campaign=learnablelink)；向大师们学习新技能和技术的地方。会员可以即时访问 SitePoint 的所有电子书和交互式在线课程，如 [Jump Start PHP](https://learnable.com/books/jump-start-php?utm_source=sitepoint&utm_medium=link&utm_campaign=learnablelink) 。

对本文的评论已经关闭。有关于 PHP 的疑问？为什么不在我们的[论坛](https://www.sitepoint.com/forums/forumdisplay.php?34-PHP?utm_source=sitepoint&utm_medium=link&utm_campaign=forumlink)上问呢？

## 分享这篇文章