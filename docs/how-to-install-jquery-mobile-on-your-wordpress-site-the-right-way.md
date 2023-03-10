# 如何正确地在你的 WordPress 站点上安装 jQuery Mobile

> 原文：<https://www.sitepoint.com/how-to-install-jquery-mobile-on-your-wordpress-site-the-right-way/>

所以，你想在你的 WordPress 站点上开始利用一些奇妙的新 jQuery Mobile 特性，但是你可以使用一些指导来快速启动和运行。没问题！通过这个快速演练，我将向您展示让 WordPress 为您加载 jQuery Mobile 的“正确方法”。

我想利用的一个主要特性是可以使用 jQuery Mobile 构建的新弹出窗口。这对于清晰的情景对话和其他通过提示或表格与网站访问者交流的用户友好方式来说是非常好的。但是，在 WordPress 中正确地实现它们可能有点困难。正如 WordPress 的安全性和可靠性意味着我们得到了一个强大、健壮的框架；这也意味着我们在实现新工具时必须采取一些额外的步骤。

### wp _ 入队 _ 脚本

首先也是最重要的，在 WordPress 中调用 JavaScript 函数或语句的唯一推荐方法是通过 **wp_enqueue_script** 函数，该函数内置于 WordPress 中。这只是让你的 WordPress 安装知道你有一个你想要加载的文件，并确保它在没有任何冲突或其他问题的情况下被集成。总的来说，这很棒，可以让你的 WordPress 安装保持有序，并为我们省去很多潜在的麻烦。不过，从短期来看，这可能是一种痛苦。

下面是加载 JavaScript 需要做的事情:

1.  创建一个函数
2.  用正确的参数调用 wp_enqueue_script
3.  使用 add_action 函数正确调用包含 wp_enqueue_script 的函数

下面是代码本身的样子:

【source code language = " PHP "】
<？php
函数 get _ jqm(){
WP _ enqueue _ script(
' jqm _ js '，
' https://code . jquery . com/mobile/1 . 2 . 0/jquery . mobile-1 . 2 . 0 . min . js '，
array('jquery ')，
' 1 . 2 . 0 '
)；
}
add _ action(' WP _ enqueue _ scripts '，' get _ jqm ')；
？>
[/源代码]

wp_enqueue_script 函数只有两个要求:你要调用的脚本的唯一名称(给它你想要的任何名称，最好是一个合理的、有语义的名称)和脚本的来源。在我们的例子中，我们从 jQuery.com CDN 调用 JS 文件。

我还包括了一些额外的信息。具体来说，我包括了版本号，这是可选的，但仍然是一个很好的实践。当新版本发布时，您可以使用新信息更新您的代码，浏览器将下载最新版本的 jQuery Mobile。否则，它会缓存并为访问者节省大量的带宽，为你的 WordPress 网站带来更快、更快的页面加载。因此，如果可以的话，请尽可能包含版本号。

### wp_register_style 和 wp_enqueue_style

没有 CSS，jQuery Mobile 是不完整的！我看到这种错误经常发生。JavaScript 已经就绪，但是专门为 jQuery Mobile 设计的定制 CSS 文件还没有准备好。所以，让我们确保这种悲剧不会发生在你身上。

CSS 文件有点不同，需要更多的步骤才能正确。您必须首先注册样式，然后将其排入队列:

[sourcecode language="php"]

wp_register_style(
'jqm_css '，
' https://code . jquery . com/mobile/1 . 2 . 0/jquery . mobile-1 . 2 . 0 . min . CSS '，
，
' 1 . 2 . 0 '
)；
WP _ enqueue _ style(
' jqm _ CSS '，
' https://code . jquery . com/mobile/1 . 2 . 0/jquery . mobile-1 . 2 . 0 . min . CSS '，
"，
' 1 . 2 . 0 '
)；
[/源代码]

你会注意到这种模式的，我肯定。这几乎是相同的技术，但是这种方法让我们可以灵活地决定文件在浏览器中实际加载的条件。所以我们可以在 WordPress 中注册 CSS，但是我们不会要求用户下载它，直到一个特定的事件发生并且他们确实需要 CSS。在本文中，我不打算讨论这些偶发事件和依赖性，但可以说，这可能是一个很少开发人员充分利用的强大功能。

### 将代码放在一起

现在，您已经看到了如何将 JavaScript 正确入队，同时注册和入队 CSS，让我们看看最终的代码:

[sourcecode language="php"]
函数 get _ jqm(){
WP _ enqueue _ script(
' jqm _ js '，
' https://code . jquery . com/mobile/1 . 2 . 0/jquery . mobile-1 . 2 . 0 . min . js '，
数组(' jquery '，
' 1 . 2 . 0 '【T6])；

WP _ register _ style(
jqm _ CSS，
https://code . jquery . com/mobile/1 . 2 . 0/jquery . mobile-1 . 2 . 0 . min . CSS '，
，
【1 . 2 . 0】；
WP _ enqueue _ style(
【jqm _ CSS】，
【https://code . jquery . com/mobile/1 . 2 . 0/jquery . mobile-1 . 2 . 0min . CSS】，
，
【1 . 2 . 0】
)；
}
add _ action(' WP _ enqueue _ scripts '，' get _ jqm ')；
[/源代码]

你可以将这段代码复制粘贴到你的 WordPress 安装的 functions.php 文件中，或者用它来[建立你自己的插件](https://www.sitepoint.com/design-a-wordpress-plugin-in-five-minutes-or-less/ "Design a WordPress Plugin in Five Minutes or Less")。无论哪种方式，这都会正确地将你的代码连接到 WordPress，这样你就可以充分利用你的 WordPress 网站的最新、最好的 jQuery Mobile 特性和功能。

## 分享这篇文章