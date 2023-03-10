# 用 PHP 和 Markdown 构建 ePub

> 原文：<https://www.sitepoint.com/building-epub-with-php-and-markdown/>

ePub 格式是建立在 XHTML、CSS、XML 等之上的发布标准。既然 PHP 非常适合与 HTML 和朋友一起工作，为什么不用它来制作电子书呢？在本文中，我们将了解如何构建一个从一组内容文件开始创建 ePub 包的工具。也许这是你下一部最畅销的网络科幻小说或你最新代码项目的文档…因为我们都为自己的项目写了很好的文档，不是吗？

为了方便作者，我们的工具将能够解析 Markdown 语法，产生的标记将使用 [RainTPL 库](http://www.raintpl.com/ "RainTPL - easy php templating engine")放入 HTML 模板中。

我为本文编写的示例工具名为 md2epub，已经成为 Github 上真正的[开源项目。目前还很不完善，但是可以随意分叉改进！](https://github.com/vtardia/md2epub "vtardia/md2epub - GitHub")

## 快速入门 ePub 理论

[IDPF(国际数字出版论坛)](http://idpf.org/epub "EPUB | International Digital Publishing Forum")对 ePub 格式的规范进行了详细说明，我们将参考目前使用最广泛的规范的 2.0.1 版本。其他有用的资源有[维基百科 ePub 页面](http://en.wikipedia.org/wiki/EPUB "EPUB - Wikipedia, the free encyclopedia")和 [Epub 格式构建指南](http://www.hxa.name/articles/content/epub-guide_hxa7241_2007.html "Epub Format Construction Guide - HXA7241 - 2007")，作者 [Harrison Ainsworth](http://www.hxa.name/ "HXA7241") 。

基本上，ePub 书是一个结构明确的压缩文档。内容包括 XHTML 文档、CSS 样式表、图像(gif、JPEGs、png 和 SVG)和开放式字体。还有其他具体的文件用来描述书的内容；这些是包和容器文件:

```
myBook/
    META-INF/
        container.xml
    mimetype
    content.opf
    toc.ncx
    Stylesheet.css
    BookCover.jpg
    HomePage.xhtml
    Chapter1.xhtml
    ...
    ChapterN.xhtml
    Index.xhtml
```

前四个文件是特定于 ePub 包的，其他的是我们的内容。`mimetype`文件必须包含 ASCII 格式的 *application/epub+zip* ,不能有行尾字符。它必须是存档中包含的第一个文件，并且不能被压缩；我们不能用 PHP 做到这一点，但是稍后我会向您展示一个解决方法。

文件`META-INF/container.xml`的名称和位置是固定的。这个文件的作用是告诉 OPF 文件(通常是 T1)相对于电子书根目录的位置。

`content.opf`是一个 XML 文件，包含图书的元数据、对所有内容资源的引用以及阅读器应用程序必须加载内容的顺序。

`toc.ncx`文件是电子书的可选但推荐的 XML 导航地图。

还有其他可选文件，现在不考虑。而且我也不会告诉你怎么加 DRM，抱歉。

## 我们的第一本电子书

那么我们如何为电子出版准备我们的内容呢？我建立了一个这样的测试书目录:

```
mybook/
    01-first-chapter.md
    02-second-chapter.md
    book.json
    cover.jpg
    coverpage.md
    index.md
    style.css
    titlepage.md
    media/
        *.jpg
```

大多数文件是出版物、降价文本和图像的内容文件。然后我们有了一个`style.css`文件(名称和位置现在应该是固定的)。请记住，ePub 格式只理解 CSS 2.1 规范的一个子集。

最后我们有一个特殊的文件叫做`book.json`。这个文件包含了我们的`md2epub`工具生成标准包文件所需的所有数据。这个文件中的大多数密钥都与 OPF 和 NCX 的文件直接匹配。JSON 文件的结构如下:

```
{
    "id": "com.acme.books.MyUniqueBookID",
    "title": "Sample eBook Title",
    "language": "en",

    "authors": [
        { "name": "John Smith", "role": "aut"},
        { "name": "Jane Appleseed", "role": "dsr"}
    ],

    "description": "Brief description of the book",
    "subject": "List of keywords, pertinent to content, separated by commas",
    "publisher": "ACME Publishing Inc.",
    "rights": "Copyright (c) 2013, Someone",
    "date": "2013-02-27",
    "relation": "http://www.acme.com/books/MyUniqueBookIDWebEdition/",

    "files": {
        "coverpage": "coverpage.md",
        "title-page": "titlepage.md",
        "include": [
            { "id":"ncx", "path":"toc.ncx" },
            "cover.jpg",
            "style.css",
            "*.md",
            "media/*"
        ],
        "index": "index.md",
        "exclude": []
    },

    "spine": {
        "toc": "ncx",
        "items": [
            "coverpage",
            "title-page",
            "copyright",
            "foreword",
            "|^c\d{1,2}-.*$|",
            "index"
        ]
    }
}
```

从`id`到`relation`的前三组关键字映射到 OPF 文件的`<metadata>`部分。它们包含关于书、作者、出版商等等的信息。需要按键`id`、`title`和`language`。所有其他的都是可选的，它们的含义不言自明，除了`relation`，它被用来链接到另一个使用 ISBN 格式的出版物或者一个站点 URL。`id`的值必须是唯一的，我选择了相反的域格式*com . publisher . series . bookid*，尽管您可以使用您喜欢的任何格式。

JSON 的`files`键映射到 OPF 的<清单>部分。这一部分包含了书中使用的每个文件的参考，包括字体、样式表、图像和特殊文件。这个部分中的每一项都需要一个惟一的 id、资源路径(href)及其 mimetype。`md2epub`将处理媒体类型，如果没有提供，将生成一个合适的 ID。

我还希望它能够使用通配符(如 **)来指定批量文件包含。md* 和 *media/** ，尽管此时也不会解析排除列表。

spine JSON 键映射到 OPF 中的`<spine>`,它描述了书中各个章节应该出现的顺序。

从这些数据开始，我们的`md2epub`将:

*   创建一个组织良好的临时图书目录
*   复制所有引用的文件
*   将所有的 markdown 文件转换并复制到 XHTML
*   生成 ePub 所需的文件
*   创建 zip/epub 归档并清理

最终文件可以(也应该)由官方的 EPUB 验证器进行验证，你也可以下载并在本地作为 Java 控制台应用程序运行。

## MD 2 epub–的制作

为了抓住本文的主题，我将只讨论最特定于 epub 的代码，而将“实用程序代码”留给您去探索。

完整的程序由一个控制台 PHP 脚本`md2epub.php`组成，它收集用户的输入，创建工作目录，并将所有数据传递给`EBook`类。`md2epub`文件是一个外壳包装器。

`EBook`类执行所有的操作和检查。它使用 RainTPL 库进行模板解析，使用 [PHP Markdown Extra 库](http://michelf.ca/projects/php-markdown/extra/ "Michel Fortin - PHP Markdown Extra")作为内容过滤器。关于这最后一个组件，有趣的是，`Markdown()`函数被提供给了`EBook`类，作为应用于`*.md`文件的外部过滤器。通过这种方式，我们可以注入任何其他文本过滤器，如 Textile 或 wiki 语法分析器。

共享目录包含公共文件，特别是 XHTML 和 XML 模板文件；您可以编辑这些文件的标记，以自定义您的特定图书类型。`mimetype.zip`文件是我前面提到的解决办法的一部分——因为 PHP Zip 库不允许我们在创建归档文件时指定压缩级别，所以我使用命令行 Zip 程序创建了这个归档文件存根:

```
zip -0 -D -X mimetype.zip mimetype
```

PHP 将复制这个基本的归档文件，并依次添加其他文件。

## 探索电子书对象

`EBook`类的构造函数有两个参数:书籍的源目录和一个可选的参数数组。这些参数指定了要解析的 JSON 文件的名称(默认为`book.json`)以及包含已编译书籍的内容文件的目录。我选择了`OEBPS`，它代表开放式电子书出版结构，因为这是我研究过的许多电子书使用的惯例。

`parseFiles()`方法的作用是规范化从 JSON 加载的文件部分。使用`glob()`扩展通配符路径，并生成 id 和媒体类型。最后，它给出了一个关联数组，其中每个文件都以这种方式表示:

```
'cover' => Array (
    'path' => 'cover.jpg',
    'type' => 'image/jpeg'
)
```

每个文件只能列出一次。该路径相对于内容目录，该目录将包含 OPF 文件。在这一步中，减价文件的类型是 text/plain，它们将在以后被转换。

`parseSpine()`方法做了类似的工作。它设置一个 NCX/TOC 文件，并编译一个声明的 id 列表。id 可以是正则表达式。例如，我已经指定了匹配章节 id 的模式`^cd{1,2}-.*$`(*c01-第一章*，*C02-第二章*，等等)。开头的“c”很重要，因为 XML `id`属性不能以数字开头。

然后，强大的`makeEpub()`方法开始了我们电子书的转换过程。需要的数据是:

*   目标。epub 文件路径
*   编译和复制所有资源的临时目录
*   模板文件的源目录
*   可选的内容过滤器，在这里是链接到 md 扩展的 Markdown

它首先检查输入参数，以确保它具有正确的文件系统权限，然后依次调用四个助手方法，对应于所需的步骤:

*   创建 META-INF 目录和数据
*   导出所有内容文件，编译模板并应用过滤器
*   创建 OPF 清单文件
*   创建 NCX 导航文件(如果需要)
*   创建压缩的 epub 归档文件

`createMetaInf()`方法是最简单的，有助于我们理解其他方法背后的模板逻辑:

```
<?php
protected function createMetaInf($workDir)
{
    // create destination directory
    if (!mkdir("$workDir/META-INF")) {
        throw new Exception('Unable to create content META-INF directory');
    }

    // compile file
    $tpl = $this->initTemplateEngine(
        array(
            'tpl_dir'   => "{$this->params['templates_dir']}/",
            'tpl_ext'   => 'xml',
            'cache_dir' => sys_get_temp_dir() . '/'
        )
    );
    $tpl->assign('ContentDirectory', $this->params['content_dir']);
    $container = $tpl->draw('book/META-INF/container', true);

    // write compiled file to destination
    if (file_put_contents("$workDir/META-INF/container.xml", $container) === false) {
        throw new Exception("Unable to create content META-INF/container.xml");
    }
}
```

`container.xml`模板只有一个变量需要设置，即`content.opf`文件相对于电子书基目录的路径。语法是`{$VariableName}`。

```
<?xml version="1.0"?>
<container version="1.0" >
 <rootfiles>
  <rootfile full-path="{$ContentDirectory}/content.opf"
   media-type="application/oebps-package+xml"/>
  </rootfiles>
</container>
```

首先，RainTPL 库使用模板目录、模板文件的扩展名和临时缓存目录进行初始化。然后模板变量被赋值(`$tpl->assign()`),`draw()`方法解析提供的模板文件。它的第二个参数告诉`draw()`是否返回结果。然后，编译后的文件被保存到目标路径。

`createOpf()`方法有相同的逻辑和更多要分配的变量。我选择了模式`Book<Varname>`,因此可选的元数据在循环中被分配:

```
<?php
$optParams = array(
    'authors',
    'date',
    'description',
    'publisher',
    'relation',
    'rights',
    'subject'
);

foreach ($optParams as $p) {
    if (isset($this->$p)) {
        $tpl->assign('Book' . ucfirst($p), $this->$p);
    }
}
```

它们以条件语法插入到模板中:

```
{if="$BookDescription"}<dc:description>{$BookDescription}</dc:description>{/if}
```

文件和 spine 变量在方法中格式化，并通过循环插入到模板中:

```
<manifest>
    {loop="$BookFiles"}<item id="{$key}" href="{$value.path}" media-type="{$value.type}"/>
    {/loop}
</manifest>
```

在`createOpf()`之前调用`processBookFiles()`，并使用所有引用的资源(`$this->files`)运行一个大循环。对于每个资源，它可以在两个操作之间进行选择:

*   处理和过滤文件
*   复制目标目录中的文件路径

应用于文件的过滤器必须是一个可调用的 PHP 函数或类方法，它接受一个参数:内容。使用`file_get_contents()`从原始源加载文件内容，并使用本机`call_user_func()`应用过滤器:

```
<?php
$content = call_user_func($filters[$ext], $content);
```

然后将处理过的内容注入到 XHTML 模板中，类似于前面的方法。在这种情况下，也可以使用自定义模板。如果在`templates`目录中有一个名为`.xhtml`的文件，该文件将被用来覆盖默认的`page.xhtml`。同样，如果一个`style.css`文件存在，那么`{$BookStyle}`模板变量被设置，CSS 被链接到页面。

然后，结果文件被写入目标目录，该文件的清单条目被更新为新的路径和媒体类型，为`createOpf()`做好准备。

`createNcx()`有点棘手，因为它必须查看 spine 部分的条目，并提取 H1 和 H2 标签的内容。`toc.ncx`文件有这样的结构，首先是一个标准化的头:

```
<?xml version="1.0" encoding="UTF-8"?>
<ncx  version="2005-1">
 <head>
  <meta name="dtb:uid" content="{$BookID}"/>

  <!-- 1 = chapters only, 2 = subchapters, 3 = third level section -->
  <meta name="dtb:depth" content="2"/>

  <!-- Both required but unused, can be 0 -->
  <meta name="dtb:totalPageCount" content="0"/>
  <meta name="dtb:maxPageNumber" content="0"/>
 </head>

 <docTitle>
  <text>{$BookTitle}</text>
 </docTitle>
```

定制的`dtb:uid` meta 表示我们的图书 ID，`dtb:depth` meta 标签表示我们的导航有多深。我在这里选择一个固定值 2，所以在解析代码中，我必须提取前两个级别的头。

在头文件之后是使用模板循环生成的`<navMap>`。

```
{$playOrder=1}
{loop="$BookChapters"}
 <navPoint id="{$key}" playOrder="{$playOrder++}">
  <navLabel>
   <text>{$value.title}</text>
  </navLabel>
  <content src="{$value.path}"/>
  {if="isset($value.sections)"}{loop="$value.sections"}
  <navPoint id="{$key}" playOrder="{$playOrder++}">
   <navLabel>
    <text>{$value.title}</text>
   </navLabel>
   <content src="{$value.path}"/>
  </navPoint>
  {/loop}{/if}
 </navPoint>
{/loop}
```

每个 H1 成为一个`<navPoint>`，`id`是对应的资源 ID(例如*c01-第一章*等)，H1 内容成为标签，`playOrder`属性表示全局阅读顺序。在我们的例子中，每个导航点可以有嵌套的 H2 字幕导航点。

在该方法中，双循环生成如下结构的数组:

```
$chapters['c01-first-chapter'] = array(
    'title' => 'Title for chapter 1',
    'path' => '01-first-chapter.xhtml',
    'sections' => array(
        'section-1-1' => array(
            'title' => 'Title for subsection 1.1',
            'path' => '01-first-chapter.xhtml#section-1-1',
        )
    )
)
```

在外部循环中，每个项目都作为一个`SimpleXML`对象加载:

```
<?php
$doc = simplexml_load_file("$workDir/{$this->params['content_dir']}/{$this->files[$item]['path']}");
if ($doc && isset($doc->body->h1)) {
    // chapter title
    $chapters[$item] = array(
        'title' => $doc->body->h1,
        'path'  => $this->files[$item]['path']
    );

    // subchapter title
    foreach ($doc->body->h2 as $section) {
        if (!empty($section['id'])) {
            $section_id = (string) $section['id'];
            $chapters[$item]['sections'][$section_id] = array(
                'title' => $section,
                'path' => $this->files[$item]['path'] . '#' . $section['id']
            );
        }
    }
}
```

如果文档有一个 H1 标题，第一级项目被插入到`$chapters`数组中。然后查询第二级标题，每个具有非空`id`属性的标题都包含在每章的小节中。模板中的`$chapters`数组变成了`{$BookChapters}`。然后，该模板被渲染并保存到其目标位置。

现在我们在`/tmp/path/com.publisher.BookID`的某个地方有了一个目标工作目录，它可以被最后一步`createArchive()`打包:

```
<?php
protected function createArchive($workDir, $epubFile)
{
    $excludes = array('.DS_Store', 'mimetype');

    $mimeZip = "{$this->params['templates_dir']}/mimetype.zip";
    $zipFile = sys_get_temp_dir() . '/book.zip';

    if (!copy($mimeZip, $zipFile)) {
        throw new Exception("Unable to copy temporary archive file");
    }

    $zip = new ZipArchive();
    if ($zip->open($zipFile, ZipArchive::CREATE) != true) {
        throw new Exception("Unable open archive '$zipFile'");
    }

    $files = new RecursiveIteratorIterator(new RecursiveDirectoryIterator($workDir), RecursiveIteratorIterator::SELF_FIRST);
    foreach ($files as $file) {
        if (in_array(basename($file), $excludes)) {
            continue;
        }
        if (is_dir($file)) {
            $zip->addEmptyDir(str_replace("$workDir/", '', "$file/"));
        } elseif (is_file($file)) {
            $zip->addFromString(
                str_replace("$workDir/", '', $file),
                file_get_contents($file)
            );
        }
    }
    $zip->close();

    rename($zipFile, $epubFile);
}
```

`$excludes`数组包含我们不希望在归档中出现的特殊文件和系统文件。为一个空的`$zipFile`创建一个临时路径，然后用一个加载到`ZipArchive`对象中的基本`mimetype.zip`的副本填充`$zipFile`路径。此时，使用`RecursiveIteratorIterator`和`RecursiveDirectoryIterator`对象将工作目录的内容递归加载到`$files`数组中。在下面的循环中，工作目录的结构被复制到 zip 存档中。最后，归档文件被移动到调用者指示的目标路径。

## 摘要

当该说的都说了，该做的都做了，我们有了闪亮的新电子书。我们有很好的机会通过 EpubCheck 应用程序的验证，最重要的是，我们有另一个有用的工具添加到我们的 PHP-super-hero belt！这只是一个起点，如果你愿意，你可以通过代码添加过滤器、特性、主题和风格等等。保持冷静，享受乐趣，写那些电子书！

图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")

## 分享这篇文章