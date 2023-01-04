# PDF、Linux 和网络

> 原文：<https://www.sitepoint.com/pdfs-linux-and-the-web/>

不管 pdf 的使用对你的网站访问者来说是有用的还是麻烦的，它们都是日常网络的一部分。通常，这是将复杂文档分发到任何平台的最有效的方法。

如果您的 Linux 工作站或服务器包含 Ghostscript，那么您有一些创建和优化 PDF 文件的优秀开源替代方案。

Ghostscript 是 PostScript 和可移植文档格式(PDF)文件以及复杂图像文件的解释器。它可以翻译和输出文件到浏览器(Ghostview for one，以及 Acrobat Reader)的许多 Unix 变种，当然包括 Linux，以及翻译 postscript 文件打印到非 PostScript 打印机。

在 Ghostscript 应用程序中有两个实用程序，ps2pdf 和 pdfopt。ps2pdf 使您能够生成与 Acrobat Reader 完全兼容的 pdf 文件，包括调整 Adobe Distiller 参数(几十个)和设置输出分辨率的能力，例如屏幕查看、打印甚至印前。

还有一个很好的小工具叫做 pdfopt，它可以优化 pdf 文件，通常可以将文件大小缩小 50%甚至更多。

有关这些工具的信息，请参见以下链接:

ghostscript –>[完整概述](http://www.cs.wisc.edu/~ghost/doc/gnu/7.05/Readme.htm)

PS 2 pdf –>[不使用 Acrobat/Distiller 创建 PDF 文件](http://www.cs.wisc.edu/~ghost/doc/gnu/7.05/Ps2pdf.htm)

PDF opt –>[PDF 优化器](http://gd.tuwien.ac.at/linuxcommand.org/man_pages/pdfopt1.html)

## 分享这篇文章