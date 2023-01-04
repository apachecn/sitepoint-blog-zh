# 热 PHP UTF-8 提示

> 原文：<https://www.sitepoint.com/hot-php-utf-8-tips/>

由于所有关于 UTF 8 的噪音，我收到了一封来自 T2 的邮件，里面有一些处理 UTF 8 的非常聪明的技巧。接下来是一个讨论，说明当你沉迷于性能和优化时会发生什么(请注意——可能很无聊，这取决于你的观点)。

## 用原生 PHP 实现超越 mbstring case 函数

原生 PHP strtolower / strtoupper 函数不理解 UTF-8——它们只能处理 ASCII 范围内的字符，并且(可能)检查您的服务器区域设置以获得更多的字符信息。后一种行为实际上使得在 UTF-8 字符串上使用它们变得" T0 "危险，因为 strtolower 可能会将 UTF-8 多字节序列中的字节误认为是应该转换成小写的，从而破坏编码。如果你正在为你控制的服务器编写代码，这不应该是一个问题，但是如果你正在为其他人编写软件，这就成问题了。

### 限制现场行为

原来您可以通过将您的语言环境限制为 [POSIX 语言环境](http://www.opengroup.org/onlinepubs/009695399/functions/setlocale.html)来禁用这种语言环境行为，这意味着只考虑 ASCII 范围内的字符(覆盖您的服务器的语言环境设置)，方法是执行以下命令:

```
 <?php
setlocale(LC_CTYPE, 'C'); 

```

那个*应该*在任何平台上工作(当然*基于 Nix 和 Windows)并且影响的不仅仅是 strtolower()/strtoupper()——其他 PHP 功能从本地获取信息，比如 PCRE /w 元字符、strcasecmp()和 ucfirst()，所有这些都可能对 UTF-8 产生负面影响。

在我看来，唯一的问题是，如果你正在编写可分发的软件；应该首先扰乱 setlocale。参见文档中的警告[此处](http://www.php.net/setlocale)–对于只有一个服务器进程的 Windows 来说可能是个问题–您可能会影响服务器上运行的其他应用程序。

### 快速大小写转换

为了能够在不依赖 [mbstring](http://www.php.net/mbstring) 的情况下进行大小写转换(例如 strtolow/upper)(因为谁知道共享主机有没有安装呢？)，像 [Mediawiki](http://www.mediawiki.org/) (如在维基百科中)和 [Dokuwiki](http://wiki.splitbrain.org) 这样的应用程序通过实现这些函数的纯 PHP 版本并使用像 [this](http://svn.wikimedia.org/viewvc/mediawiki/trunk/phase3/includes/Utf8Case.php) 或 [this](http://dev.splitbrain.org/view/darcs/dokuwiki/inc/utf8.php) (脚本末尾的 UTF8 _ LOWER _ TO _ UPPER 变量)这样的数组来解决这个问题，这样做是可行的，因为首先只有有限的字母选择具有大小写的概念——数组很大，但还没有大到足以造成可怕的性能开销。值得注意的是，这两个查找数组都包含 ASCII 范围内的字符。他们也支持许多字母表。

Mediawiki 然后(本质上)像这样做 str_to_upper(至少在 1.7.1 版本中——见`languages/LanguageUtf8.php`——这似乎在 SVN 的领导下有所改变)；

```
 // ... bunch of stuff removed
        return preg_replace( "/$x([a-z]|[\xc0-\xff][\x80-\xbf]*)/e",
              "strtr( "$1" , $wikiUpperChars )",
              $str
        ); 

```

…它定位每个有效的 UTF-8 字符序列，并通过回调——即/e 模式修饰符(时间到[电话](http://www.hardened-php.net/advisory_042006.119.html) [朋友](http://blog.php-security.org/))用查找数组执行 PHP 的 strtr()函数？)来转换案例。这使得内存使用最小化，以性能为代价(可能-不是基准)-许多回调/评估。

Dokuwiki(和 [phputf8](http://sourceforge.net/projects/phputf8) )使用类似的方法，但是首先将输入字符串分割成一个数组或 UTF-8 序列，并查看它们是否在查找数组中匹配。这是 PHP UTF-8 的实现，几乎是一样的(`utf8_to_unicode()`将一个 UTF-8 字符串转换成一个序列数组，表示字符，`utf8_from_unicode()`做相反的事情)；

```
 function utf8_strtolower($string){
    global $UTF8_UPPER_TO_LOWER;

    $uni = utf8_to_unicode($string);

    if ( !$uni ) {
        return FALSE;
    }

    $cnt = count($uni);
    for ($i=0; $i < $cnt; $i++){
        if ( isset($UTF8_UPPER_TO_LOWER[$uni[$i]]) ) {
            $uni[$i] = $UTF8_UPPER_TO_LOWER[$uni[$i]];
        }
    }

    return utf8_from_unicode($uni);
} 
```

这将在短时间内使用更多的内存，因为它将输入字符串作为数组复制(实际上这需要修复！)另外，数组需要更多的空间来存储与字符串相同的信息，但速度应该更快。

总之——进入 Marek 的方法，可以总结为:

```
 function StrToLower ($s)  {
     global $TabToLower;
     return strtr (strtolower ($s), $TabToLower);
} 

```

…其中`$TabToLower`是查找表(现在减去 ASCII 字符查找，由`strtolower`处理)。请注意，Marek 向我展示的代码使用了类——这只是一种简化。它依赖于正在设置的 POSIX 区域设置(否则 UTF-8 编码可能会被破坏)，并利用了 UTF-8 的设计，即有效的 UTF-8 字符串中的任何完整序列都是唯一的(不能被误认为是更长序列的一部分)。你还需要非常仔细地阅读 [strtr()](http://www.php.net/strtr) 文档…

> strtr()只能用两个参数调用。如果用两个参数调用它，它将以一种新的方式运行:from then 必须是一个数组，它包含将在源字符串中被替换的 string -> string 对。strtr()总是首先寻找最长的可能匹配，并且不会试图替换已经处理过的内容。

我还没有对此进行基准测试，但 Marek 告诉我，他发现它比同等的 mbstring 函数大约快 x3 倍，我相信这一点。

Marek 还采用了一些聪明的技巧来处理查找数组。dokuwiki 和 mediawiki 方法都定义了所有可能的大小写转换，即它们适用于多种人类语言。虽然这可能适用于用户提交的内容，但当你在做本地化 UI 之类的事情时，很可能你只会使用一种语言——你不需要完整的查找表，只需要那些适用于相关语言的查找表，假设你知道它们是什么。你也可以考虑从浏览器中查看传入的`$_SERVER['HTTP_ACCEPT_LANGUAGE']`。

无论如何——当我有时间的时候，我会弄清楚如何在 PHP UTF-8 中使用 Marek 的想法。

## 输出转换

Marek 的另一个聪明的技巧是，如何向无法处理 UTF-8 协议的客户提供内容，例如旧浏览器、手机(？).他的方法简单而有效——一旦你完成了输出页面的构建，在[输出缓冲区](http://www.php.net/manual/en/ref.outcontrol.php)中捕获它，检查客户端发送的可接受字符集(`$_SERVER['HTTP_ACCEPT_CHARSET']`)并在必要时用 [iconv](http://www.php.net/iconv) 转换(降级)输出。

您需要仔细检查标题的内容并正确处理它。您还需要确保在 XML 处理指令中重新声明了 Content-Type charset 加上任何 HTML 元字符或编码。但这肯定是解决 PHP 问题的严肃/可行的方法。

## 这个故事的寓意…

…与那些自鸣得意地使用 ISO-8859-1(它本身并不支持欧元符号 BTW)的国家的人相比，与那些实际上*需要* UTF-8 的人交谈是否值得？).

鉴于 Mediawiki 已经在 PHP 中“完成” [Unicode 规范化](http://en.wikipedia.org/wiki/Text_normalization)(这里是)，剩下的唯一难题是 [Unicode 排序](http://en.wikipedia.org/wiki/Collation)(例如用于排序)—[这里有一个很好的灵感来源](http://cpan.uwinnipeg.ca/module/Unicode::Collate)。之后——谁需要 PHP 6；)

## 分享这篇文章