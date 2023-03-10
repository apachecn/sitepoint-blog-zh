# 如何动态本地化 TinyMCE

> 原文：<https://www.sitepoint.com/dynamically-localize-tinymce/>

这篇文章将教你如何动态本地化你的 [TinyMCE](http://www.tinymce.com/) 编辑器。如果你不熟悉 TinyMCE，它是一个基于 JavaScript 的 web 富文本编辑器。

## 问题是

本教程假设您已经构建了一个 PHP 多语言站点/框架，并且您或者管理员希望 TinyMCE 自动切换到您站点的当前语言。一个实际的例子是:你刚刚把你的网站界面切换到西班牙语，但是你意识到编辑器的界面仍然是英语的，你希望它是西班牙语的。

尽管 TinyMCE 是一个很棒的 [wysiwyg](http://en.wikipedia.org/wiki/WYSIWYG) 编辑器，它提供了几乎所有已知语言的本地化文件，但它仍然没有自动语言切换功能。不过，推出自己的解决方案很容易，所以这就是我们现在要做的。

## 解决方案

首先，从[这里](http://www.tinymce.com/i18n/index.php?ctrl=lang&act=download&pr_id=1)获取你需要的额外语言文件，并相应地上传到你的 TinyMCE 目录。

让我们假设在你的站点/框架中，保存当前语言的变量叫做`$current_lang`。您需要检查您的`$current_lang`变量保存的值是否匹配 TinyMCE 的语言代码。简单来说，TinyMCE 的西班牙语代码是“es ”,如果`$current_lang`在需要时返回“sp”而不是“es ”,它就不会工作。因此，在这种情况下，您需要添加一两行代码:

```
<?php
...
if($current_lang == 'sp') {
    $current_lang = 'es';
} else {
    $current_lang = 'en'; //or whatever the default lang you wish
} 
...
```

你可以在这里查找代码[，看看它们是否与你已经在你的站点/框架上使用的代码相对应。](http://www.tinymce.com/i18n/index.php?ctrl=lang&act=download&pr_id=1)

现在让我们来看看你的站点/框架的头部分，在那里你已经初始化了 TinyMCE。我们将把我们的 php `$current_lang`变量导入到我们的 JS 代码中，就在您初始化 tinyMCE.js 的位置上方，如下所示:

```
<script type="text/javascript">

var cur_lang = "<?php echo $current_lang; ?>"; // do not forget the double quotes

tinyMCE.init({
...
```

最后，我们将一个语言参数/值添加到我们的
general options 部分，如果该选项还不存在的话。

```
<script type="text/javascript">

var cur_lang = "<?php echo $current_lang; ?>"; // do not forget the double quotes

tinyMCE.init({
        // General options
        width : "480",
        height : "680",
        mode : "textareas",
        theme : "advanced",
        language : cur_lang,  // Here we have added our language parameter, the value of which corresponds to our current language on the site.
        plugins : 
        ...
```

**注意:**如果已经安装了 TinyMCE 压缩器，不要忘记修改它的 Javascript 初始化代码片段。

就这样，从现在开始，你的 TinyMCE 编辑器的界面将以你的站点当前使用的语言出现。

## 结论

TinyMCE 是一个神奇的工具，但是人们经常跳过各种低效的陷阱来使它支持本地化。在本教程中，我们用短短几行代码实现了一种通用的方法。你用了别的方法吗？让我们知道！

## 分享这篇文章