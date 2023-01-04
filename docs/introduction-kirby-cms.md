# Kirby CMS 简介

> 原文：<https://www.sitepoint.com/introduction-kirby-cms/>

Kirby 是一个轻量级 CMS，可能介于 Drupal 等功能丰富的平台和 Jekyll 等静态站点生成器之间。

Kirby 与大多数 CMS 非常不同——在这个过程中更接近 Jekyll 是因为它没有数据库。相反，一切都存储为文件；一些用于配置，一些用于内容——除了通常的模板文件、部分和插件。

在这篇文章中，我将看看 Kirby，演示如何使用它，并评估它的一些优点和缺点。

## 许可、下载和安装

Kirby 是基于每个站点 39 美元的许可提供的。然而，你可以在你的本地机器上免费试用——只有当你投入生产时，许可费才是应该支付的。

这个软件包可以从 Kirby 下载页面作为[获得。zip 文件](https://github.com/bastianallgeier/kirbycms/zipball/master)，或者来自 Github 的[。](https://github.com/bastianallgeier/kirbycms)

要安装，解压缩/复制档案到您的 web 根目录，并指向您的虚拟主机在 Kirby 的根目录。差不多就是这样——马上，您应该会看到一些虚拟内容和 Kirby 的默认主题。

![The Default Kirby theme](img/56d4822aa2156bd954e6472024b18f4c.png)

## Kirby 的网站结构

假设我们计划使用默认提供的基本站点结构，但是在顶级添加一个名为“我们的工作”的新页面。我们希望它出现在菜单中的“联系人”之前。

首先将保存联系页面的目录从`content/03-contact`重命名为`content/05-contact`。

> 默认情况下，菜单系统使用目录名来确定页面在导航中的显示顺序。稍后，我们将为第四个位置创建一个新页面。

现在，创建一个名为`content/03-our-work`的新文件夹，并在其中创建一个名为`our-work.txt`的文件

> 默认情况下，内容文件应该总是有一个`.txt`扩展名，尽管它们的内容混合了 YAML 和 Markdown。要修改此行为，请更改`site/config/config.php`中的以下行:

```
c::set('content.file.extension', 'txt'); 
```

该文件应该具有以下结构:

```
Title: Our Work
----
Text: Lorem ipsum dolor sit amet, consectetuer adipiscing elit. Aenean commodo ligula eget dolor. 
```

现在，如果您浏览到该网站，您应该会在项目和联系人之间看到一个新的菜单项，其中包含我们刚刚在`04-our-work/our-work.txt`中定义的内容。

显然，您可以用同样的方式编辑现有的页面。你会在`content/home/home.txt`中找到主页的内容。

让我们看看菜单是如何构建的，以便深入了解正在发生的事情。菜单是作为一个片段实现的，它基本上是一个局部视图:

```
// site/snippets/menu.php

<nav class="menu">
<ul>
    <?php foreach($pages->visible() AS $p): ?>
    <li><a<?php echo ($p->isOpen()) ? ' class="active"' : '' ?> href="<?php echo $p->url() ?>"><?php echo html($p->title()) ?></a></li>
    <?php endforeach ?>
</ul>
</nav> 
```

注意，它调用了一个名为`visible()`的方法。以数字为前缀的内容——`01`、`02`等——是可见的；也就是说，它被设计成出现在导航中。没有的内容——如`home`和`error`——被隐藏。尝试创建一个名为`content/test`的文件夹，以及一个名为`test.txt`的相应内容文件——你会发现它不会出现在菜单上。如果您想隐藏附加页面，请更改`site/config/config.php`中的以下行:

```
c::set('content.file.ignore', array()); 
```

返回按目录名排序的页面，因此有数字前缀。你可以忽略这种行为；假设您想要一个按标题字母顺序排列的页面列表:

```
$a_to_z = $pages->visible()->sortBy('title', 'asc'); 
```

要列出当前打开的菜单项(即子菜单)下的页面，您可以执行以下操作:

```
if($root = $pages->findOpen()) {
$items = $root->children()->visible();    
} 
```

Kirby 网站上有更多关于管理菜单[的信息。](http://getkirby.com/blog/fun-with-menus)

## 主题

在新的安装中，页面使用`site/templates/default.php`来呈现。

我们来看看内容:

```
<?php snippet('header') ?>
<?php snippet('menu') ?>
<?php snippet('submenu') ?>

<section class="content">

<article>
    <h1><?php echo html($page->title()) ?></h1>
    <h2><?php echo html($page->subtitle()) ?></h2>
    <?php echo kirbytext($page->text()) ?>
</article>

</section>

<?php snippet('footer') ?> 
```

`snippet()`包括一个来自`site/snippets`的文件——我们很快就会看到。

您会注意到页面内容是作为一个名为`$page`的变量提供的，它读取在相应的文本文件中定义的属性。`html()`函数用于对值进行转义，`kirbytext()`应用了一些额外的解释，我们很快就会看到。

要为特定页面创建新模板，只需在`site/templates`中创建一个新文件，其名称与相关页面内容的文件名相匹配。例如，如果你想为主页创建一个单独的页面模板，你可以创建一个名为`site/templates/home.php`的文件。对于我们之前创建的“我们的工作”页面，您可以称之为`our-work.php`。

默认情况下，Kirby 附带了一个`assets`文件夹，包含`images`和`styles`子文件夹，但是你可以随意构建你的主题资源。

## 站点属性和片段

如果你打开文件`content/site.txt`，你会看到网站本身的一堆属性，比如标题、作者和版权声明。您可以自由添加其他属性。例如，如果添加以下内容:

```
----

Twitter: myusername 
```

现在你可以在你的模板或代码片段中使用一个神奇的变量方法来实现这一点。所以要把它放在页脚，打开`site/snippets/footer.php`并添加以下内容:

```
<a href="http://twitter.com/<?php print $site->twitter() ?>">Twitter</a> 
```

## 创建一个简单的博客

我们已经了解了如何创建内容、创建特定于页面的模板以及生成页面列表。有了这些知识，创建一个简单的博客就相当简单了。

创建一个名为`content/04-blog`的文件夹，并在其中创建一个名为`blog.txt`的文件:

```
Title: Blog
---- 
```

在该文件夹中，创建一堆博客文章(使用与现有页面相同的格式)，例如

```
content/04-blog/01-first-post/first-post.txt
content/04-blog/02-first-post/second-post.txt
content/04-blog/03-first-post/third-post.txt 
```

现在，为博客创建一个模板:

```
// site/templates/blog.php

<?php snippet('header') ?>
<?php snippet('menu') ?>

<section class="content">

    <h1><?php echo html($page->title()) ?></h1>

    <?php foreach ($pages->findOpen()->children()->visible()->flip() as $post): ?>
<article>
    <h2><a href="<?php print $post->url() ?>"><?php echo html($post->title()) ?></a></h2>
    <p><?php print excerpt($post->text()) ?></p>
    <p><a href="<?php print $post->url() ?>">Read more &raquo;</a></p>
</article>
    <?php endforeach ?>

</section>

<?php snippet('footer') ?> 
```

注意我们是如何调用`flip()`来颠倒页面的顺序的，所有页面都是当前页面的子页面——即`content/04-blog`文件夹中的页面。

这个模板还引入了`url()`函数，它返回相关页面的 URL，以及`excerpt()`，它返回文本内容的摘要。要更改返回的字符数，只需将其作为第二个参数提供:

```
print excerpt($post->text(), 250); // prints the first 250 characters 
```

## 柯比文本

Kirby 中的实际内容是用“Kirbytext”写的。它以文本属性的形式存储在 YAML 文件中，本质上是 Markdown 的扩展，但增加了一些宏。

最好用一个例子来说明这一点:

文件:`page.txt`:

```
Title: About
 ----
Somefield: Something
 ----
Body: This is some sample content. It's based on Markdown, so we can use **bold** or *emphasis*, and [write links](http://getkirby.com).

Kirbytext also has macros, that look a little like this:

(youtube: http://www.youtube.com/watch?v=ZZ5LpwO-An4 width: 300 height: 200) 
```

这里 Kirby 特定的宏/标签用于嵌入 Youtube 视频，但是您可以扩展 Kirbytext 来编写自己的视频。为此，首先在`site/plugins`中创建一个名为`kirbytext.extended.php`的文件:

```
class kirbytextExtended extends kirbytext {

function __construct($text, $markdown=true) {

    parent::__construct($text, $markdown);

}  

} 
```

现在，在构造函数中你可以定义新的标签，以及新的属性。假设我们想要添加 Gravatar 支持:

```
 // define custom tags
    $this->addTags('gravatar');

    // define custom attributes
    $this->addAttributes('size'); 
```

一旦您通过`addTags()`定义了一个新的标签，您只需实现一个同名的方法，该方法将被调用以呈现标签:

```
/**
*  Render the gravatar tag, e.g. (gravatar: joe.blogs@example.com)
*/
function gravatar($params) {

    $email = $params['gravatar'];

    $size = (isset($params['size'])) ? $params['size'] : 50;

    $url = sprintf('http://www.gravatar.com/avatar/%s?size=%d', md5($email), $size);

    return sprintf('<img src="%s" width="%d" height="%d" />', $url, $size, $size);

} 
```

你可以这样使用它:

```
(gravatar: joe.bloggs@example.com size: 64) 
```

> 注意，这里的 email 属性名为 gravatar 即与标签相同。

## 插件

Github 上有许多插件[可用，包括支持](https://github.com/bastianallgeier/kirbycms-extensions/tree/master/plugins) [Twitter](https://github.com/bastianallgeier/kirbycms-extensions/tree/master/plugins/tweets) 、 [Instagram](https://github.com/bastianallgeier/kirbycms-extensions/tree/master/plugins/instagram) 和 [Github](https://github.com/bastianallgeier/kirbycms-extensions/tree/master/plugins/github) ，以及类似[联系人表格](https://github.com/bastianallgeier/kirbycms-extensions/tree/master/plugins/contactform)、估计[阅读时间](https://github.com/bastianallgeier/kirbycms-extensions/tree/master/plugins/readingtime)和显示[相关页面](https://github.com/bastianallgeier/kirbycms-extensions/tree/master/plugins/related)的东西。

你可以在对 Kirby 的内部工作原理一无所知的情况下创建自己的插件——只需将一个类放入`site/plugins`中，然后根据需要从你的模板中使用它。

## 柯比面板

Kirby 实际上并没有提供任何现成的管理界面——你只需直接编辑文件。然而，有一个简单的接口作为一个单独的包提供，称为 Kirby 面板。

![The Kirby Panel](img/4a0b47acdda5424f74e8725699bb0d1e.png)

要安装 Kirby 面板，[从 Github](https://github.com/bastianallgeier/kirbycms-panel) 克隆它。

然后，将该文件夹复制到 Kirby 站点的`site`文件夹中，将其命名为`panel`，例如:

```
cp -R kirbycms-panel/ /var/www/yoursite/site/panel 
```

现在复制`panel\defaults`文件夹并将其放在站点根目录下:

```
cp -R /var/www/yoursite/panel/defaults /var/www/yoursite/panel 
```

要指定一些登录凭证，请将文件`site/panel/accounts/admin.php`重命名为`[username].php`，例如:

```
// site/panel/accounts/john.php

<?php if(!defined('KIRBY')) exit ?>
username: john
password: 5baa61e4c9b93f3f0682250b6cf8331b7ee68fd8
encrypt: sha1
language: en 
```

你可以输入一个明文密码(一个非常非常糟糕的主意)，MD5 散列(一个非常糟糕的主意)或者 SHA1 散列(稍微好一点)——你只需要明确指定你使用的是哪一个，或者不输入明文。

现在，如果你在网络浏览器中打开`http://yoursite.local/panel`，你将进入一个简单的管理界面。这允许您修改站点属性，以及添加/编辑/删除内容。

## 利弊

让我们简单看一下 Kirby 的一些优点和缺点。

#### 赞成的意见

*   安装极其简单
*   快的
*   产生干净的标记
*   非常浅的学习曲线
*   非常小的膨胀

#### 骗局

*   一些代码试图重新发明轮子，例如设备检测
*   一切都位于公共根中，这不是特别符合逻辑
*   像搜索这样的动态元素充其量也是有限的

## 摘要

我已经给了你一个关于 Kirby 的简单介绍，但是还有很多要探索——查看[文档](http://getkirby.com/docs)和[教程](http://getkirby.com/docs)，在论坛中寻找支持[，或者钻研源代码。特别是，看看`kirby/lib/kirby.php`，以及`kirby/lib`中的其他文件。](http://getkirby.com/forum)

你觉得 Kirby 怎么样，你可能用它做什么？你已经用在生产中了吗？请在评论中告诉我。

## 分享这篇文章