# 文件 _ 存档

> 原文：<https://www.sitepoint.com/file-archive/>

我昨天从多伦多回来了，尽管我已经累得什么也做不了了，尽管我已经在早上 10 点左右到家了！但我现在准备继续定期发帖。

我准备发布我的一个产品的主要新版本(1.2)。其中一个功能是允许你自动生成你的产品的 zip、tar、gzip 和 bzip 文件，这样它们就可以分发给你的客户。您上传文件，应用程序生成存档。

当我编写这部分代码时，我花了相当长的时间来寻找帮助生成归档文件的类。我坚信重用库中可用的代码，而不是自己编写代码。节省了很多时间！我查看了很多地方，尝试了几个类——它们有效，但不完全有效。例如，存在 zip 文件损坏或目录未被维护的问题。我最终选定了一个名为 File_Archive 的 PEAR 类，这个类非常好！

File_Archive : File_Archive 可以让你轻松操作 tar、gz、tgz、bz2、tbz、zip、ar(或 deb)文件

它不仅做了你可能需要做的与档案有关的一切，而且它是积极开发的。我已经提交了几份 bug 报告，它们总是很快得到回复和修复——通常在报告后的几个小时内。当我开始使用它的时候，[可用的文档](http://poocl.la-grotte.org/)并不是很好，但是它们现在已经得到了很大的改进和更新。

在实际编写任何代码之前，决定要生成什么样的归档文件是很重要的。该文档很好地描述了需要注意的事项:

如果您想要高效的生成，文件格式的选择是很重要的。让我们看看有哪些可能性:

*   **Tar**
    优点:生成非常高效，内存使用量恒定，无需缓存
    缺点:不压缩(但无论如何图像或视频几乎无法压缩)，不像 Zip 那样广泛使用
*   **Tgz，Tbz**
    优点:压缩比非常高，内存使用量恒定
    缺点:不能缓存，每一代都需要大量 CPU
*   **Zip**
    优点:中间结果可以缓存、压缩，可以选择压缩级别，广泛使用
    缺点:压缩比低于 Tgz/Tbz

*来自[http://poocl.la-grotte.org/example.php](http://poocl.la-grotte.org/example.php)*

File_Archive 包括几个对处理文件及其存档有用的特性(因此得名！).这些例子是从 File_Archive 文档中截取的，用来说明这个库。

**读者**

该阅读器允许您读取文件，目录，以及档案文件/目录。生成的列表保存在一个对象中，以便以后归档或提供给用户时使用。例如，如果您想读取一个归档文件，并将其解压缩:

`$source = File_Archive::read('path/to/dir/archive.tar');`

**作家**

写入程序将把归档文件实际写入磁盘或内存。还有一个函数[可以将创建的档案作为电子邮件的附件发送出去。您必须在 writer 之前调用 reader，这样您就可以获得创建归档文件所需的内容。例如，本示例将通过电子邮件发送当前目录中的所有文件:](http://pear.php.net/package/File_Archive/docs/1.5.2/apidoc/File_Archive/File_Archive.html#methodtoMail)

`File_Archive::extract(
File_Archive::read('path/to/dir', '', 0, 0),
File_Archive::toMail('to@example.com', array('Subject' => 'path/to/dir directory', 'From' => 'example@example.com'), 'body'));`

**缓存**

> File_Archive 1.4 引入了使用缓存来存储 zip 压缩的中间结果的可能性。它使用 Cache_Lite PEAR 包来做到这一点。

> zip 文件由一个接一个的压缩文件组成。因此，如果您生成一个包含文件 A、B 和 C 的归档文件，然后生成另一个包含文件 A 和 C 的归档文件，您将对文件 A 和 C 进行两次压缩。使用缓存将允许在第一次压缩时保存文件 A、B 和 C 的压缩版本，以便在第二次压缩时再次使用它们。

> 在我的机器上(一台带有默认工厂设备的 thinkpad T42P)，生成一个 200MB 的 zip 存档文件在没有缓存的情况下需要大约 30 秒的 CPU，在没有缓存的情况下需要 32 秒的 CPU，如果所有要压缩的文件都已经在 cach 中，则需要 2 秒的 CPU

[http://poocl.la-grotte.org/tutorial/cache.php](http://poocl.la-grotte.org/tutorial/cache.php)

**有用的功能**

下面是我在产品中使用的一个函数，用于从现有目录(及其所有内容)中快速生成任何类型的受支持的归档文件:

这个函数有两个参数:要创建的归档类型–[ font = Courier New]string $ type[/font]，要归档的目录名–[ font = Courier New]string $ directory[/font]。然后，使用与目录相同的名称创建归档文件。传递给[font=Courier New]$type[/font]的选项可以是 tgz、tbz、tar、zip、gz、gzip、bz2、bzip2 或它们的任意组合之一(例如 tar.bz2 或 tar.bz2)。此参数的大小写并不重要。

`< ?php function archive($type, $directory) { if (file_exists($directory)) { $archive = File_Archive::read($directory, $directory); $archive->extract(File_Archive::toArchive($directory.$type, File_Archive::toFiles()));
}
else
{
trigger_error('Directory '.$location.' does not exist', E_USER_ERROR);
}
}
?>`

所以希望你会发现这在你自己的程序中有用。

**链接**

*   [梨包](http://pear.php.net/package/file_archive)
*   [教程](http://poocl.la-grotte.org/tutorial/)
*   [示例:图库归档文件的动态生成](http://poocl.la-grotte.org/example.php)
*   [课程概述](http://pear.php.net/package/File_Archive/docs/1.5.2/apidoc/File_Archive/File_Archive.html)

## 分享这篇文章