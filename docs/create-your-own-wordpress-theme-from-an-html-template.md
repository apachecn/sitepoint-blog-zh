# 从 HTML 模板创建你自己的 WordPress 主题

> 原文：<https://www.sitepoint.com/create-your-own-wordpress-theme-from-an-html-template/>

WordPress 已经成为世界上使用最广泛的博客平台，估计今天有 2.5 亿在线网站在使用它。WordPress 是一个博客，也是一个简单的内容管理系统，可以使用搜索引擎友好的 URL 和完全有效的 HTML 和 CSS。有如此多的网站使用 WordPress，而 WordPress.org 上只列出了大约 1200 个主题，不可避免地会有许多网站看起来完全一样。你和你的客户需要脱颖而出，否则最终会被贴上“只是另一个 WordPress 博客”的标签在这篇文章中，我将向你展示如何将一个现有的 HTML 和 CSS 站点模板转换成 WordPress 的主题。当然，WordPress 主题化是一个非常庞大的主题，不可能在一篇文章中完全涵盖，所以我会在最后提供一些资源来帮助你进一步学习。不过，希望这篇文章能作为一个很好的介绍，给你一个坚实的基础，开始学习创建你自己的 WordPress 主题。我假设你已经知道 HTML 和 CSS，并且已经有了一个你想要适应 WordPress 的网站设计。熟悉 PHP 是不必要的，因为我会在我们使用它的时候解释你需要的每一个函数。

## WordPress 主题基础

WordPress 主题位于 WordPress 安装目录下的一个文件夹中。每个主题的文件夹都包括一些基本组件:

*   一个主 CSS 文件。这只是一个普通的 CSS 文件，除了顶部的一些额外的细节，我很快会解释。当然，如果需要的话，可以有更多的 CSS 文件(例如，打印样式表)。
*   当你使用主题时，WordPress 加载的主索引文件。

页面`index.php`的内容也可以拆分成几个其他文件，然后包含在`index.php`中。通常，这些是:

*   `header.php`:包含模板的第一部分，通常从 doctype 开始，一直延伸到页面标题之后(包括站点和页面标题，以及导航元素)。
*   `sidebar.php`:类似于`header.php`文件，它包括出现在侧边栏中的元素。你可以让它和 WordPress 中的小部件一起工作，或者，如果你愿意，你可以直接把内容输入到主题文件中。
*   `footer.php`:通常位于页面的最后，通常从页面内容的末尾一直放置到网页的底部。
*   `comments.php`:定义每篇文章评论区的结构。如果你把这个从你的主题中去掉，将会使用默认主题中的`comments.php`文件。

可以有其他的 PHP 文件，但是这些是可选的。您可以添加为某些页面提供特定布局的文件，例如类别页面、单篇文章或带有给定标签的文章。像 404 这样的站点错误模板也很常见。一旦你有了你准备转换的 HTML 模板——无论你是从头开始写，设计，还是从模板市场购买——你可以在很短的时间内将它转换成 WordPress 主题。

## 开始你的主题

理想情况下，在你开始之前，你需要安装并运行 WordPress，它可以从 WordPress.org 的[免费获得。当您创建主题时，最简单的方法是在本地或本地虚拟机上处理文件，尽管您也可以通过 FTP 在 web 服务器上工作。首先，你需要一个主题文件夹。这需要在 WordPress 安装目录的`/wp-content/themes/`中创建。这就像创建一个与你的主题相关的新目录一样简单。在其中，您将从您的样式表开始，该样式表还包括一些关于稍后将在管理面板中使用的主题的信息。创建一个名为`style.css`的 CSS 文件，并在文件顶部添加以下内容:](http://wordpress.org)

```
/*Theme Name: [A unique theme name here]Theme URI: [The theme website]Description: [A description]Author: [Your name]Author URI: [Your website].[Any other comments].*/
```

这是一个评论块(由`/*`和`*/`包围)，但是 WordPress 会读取这些信息，并在管理界面的主题选择屏幕上显示出来。您需要为每个项目插入内容。它们主要是针对将要发布的主题，所以如果你只打算在你自己的站点上使用这个主题，大部分的值都是无关紧要的。确保该主题的名称不同于您已经安装的任何其他主题，否则会导致问题！还有添加带有`Version:`标签的版本号的选项。此时，如果您正在转换一个现有的 HTML/CSS 站点，那么从原始模板的 CSS 中复制并粘贴您的所有样式信息到这个文件中应该很容易。

## `index.php`

接下来，是`index.php`文件。最简单的方法是将站点模板中 mainHTML 文件的所有内容复制并粘贴到这个新文件中。我们将从用 WordPress 动态生成的动态内容替换文件中的一些硬编码信息开始。WordPress 有一个名为`bloginfo`的内置函数，用于访问关于安装和主题的所有类型的信息。我们将使用它来获取样式表 URL 和 RSS 提要的位置。`bloginfo`使用起来极其简单:

```
<?php bloginfo('stylesheet_url'); ?>
```

在这个例子中，我已经包含了样式表 URL 然而，它适用于整个参数范围，包括`charset`、博客描述和模板目录。完整的列表，请参见[。查看您的模板，您现在想用这样一行代码替换指向您的样式表的`link`元素:](http://codex.wordpress.org/Function_Reference/bloginfo)

**例 1。(节选)**

```
<link rel="stylesheet" href="<?php bloginfo('stylesheet_url'); ?>" type="text/css"  />
```

`<?php`和`?>`之间的所有内容都将被函数的返回值替换，在这种情况下，返回值将是指向您的样式表的 URL。这是完美的，因为你的网站现在将包含这样一行:

```
<link rel="stylesheet" href="http://example/blog/wp-content/themes/style.css" type="text/css" />
```

**important:** Wax On, Wax Off

构建 WordPress 主题的关键是采用我们刚刚经历过的过程，并为你网站上的每一点内容重复这个过程:使用你现有的 HTML 并找到应该动态包含的部分。然后找到将返回您想要的值的 WordPress 函数，并将其插入 HTML 标记之间或属性内部，就像我们对样式表 URL 所做的那样。

## 资产

当然，你的 CSS 文件很可能引用了大量的图片，所以现在有必要将这些图片转移到你的主题目录中。虽然对于如何在主题文件夹中命名图像和其他资源目录没有固定的规则，但在这里添加一个名为`assets`的新文件夹是值得的，它将包括图像和 JavaScript 文件等内容。如果你愿意，你可以有单独的`images`和`js`文件夹，但是为了这篇文章，我假设你坚持使用一个`assets`文件夹。将所有图像移动到新文件夹。您需要将模板转换为 WordPress 主题之前存在的旧图片位置的引用更改到新的位置。“查找和替换”几乎可以在所有文本编辑器中使用，并且是实现这一点的最简单的方法。查找对旧路径的引用(例如，`images/`直到文件名之前，包括尾部斜杠)，并用以下内容替换它们:

```
<?php bloginfo('template_directory') ?>/assets/
```

这将改变所有对你的主题所在的新文件夹的路径的引用。`assets`目录是图像现在存放的地方。如果图像相对于 CSS 文件的位置发生了变化，那么快速查找并从旧文件夹名替换到新文件夹名应该很快就能解决这个问题。现在，你应该有一份 HTML、CSS 和图片的拷贝，它们都已经在 WordPress 中设置好并工作了。要检查，保存它并尝试设置 WordPress 使用你的主题，看看会发生什么。你应该会收到一个原始 HTML 模板的副本，只是现在它是由 WordPress 生成的。不过，现在应该还没有 WordPress 的内容。

## 页眉、页脚和侧栏

下一个任务是将内容分成页眉、页脚、边栏(如果有的话)和页面的其余部分。记住，页眉、页脚和侧栏代码在所有页面上都保持不变(作为一般规则)，从

```
index.php
```

直到到达侧边栏或主页内容的开头(取决于哪一个在源代码中是第一个)，并选择从开头到这一点的所有内容。这通常包括你的页面标题、徽标和导航菜单。将它剪切并粘贴到一个新文件中，并将其保存在与

```
index.php
```

名为的文件

```
header.php
```

。文件名很重要，因为当你要求 WordPress 在你的页面中插入标题时，它会寻找这个文件。说到这里，让我们告诉 WordPress 这样做。在你删掉的代码的地方

```
index.php
```

，放下面一行:

```
<?php get_header(); ?>
```

代码告诉 WordPress 包含你的

```
header.php
```

文件在页面中的位置。接下来，我们将为页面的页脚做同样的事情:从

```
index.php
```

并将其粘贴到一个名为

```
footer.php
```

，替换为:

```
<?php get_footer(); ?>
```

最后，对侧边栏做同样的操作，将其保存为

```
sidebar.php
```

并将其替换为

```
<?php get_sidebar(); ?>
```

。值得再次检查，看看您的页面在这一点上仍然工作。我们没有做任何改变，只是把它分成单独的文件，但是验证一切仍然工作是很好的。

## 模板标签

此时，你的站点只是显示包含在你的模板中的静态 HTML，而不是从 WordPress 获取动态数据。是时候改变这种状况了。在 WordPress 中，你使用模板标签来告诉 WordPress 获取和插入什么内容到页面中。在 WordPress Codex 上有一个明确的

```
get_header
```

,

```
get_sidebar
```

,

```
get_footer
```

，以及

```
bloginfo
```

都是模板标签。这些标签可以添加到主题中的任何 PHP 文件中，所以一个好的起点是在站点的顶部，用

```
header.php
```

文件。让我们从文件的最开头开始。这

```
Doctype
```

可以保持原样。开始的 html 标签可以包含阿郎属性，WordPress 用

```
language_attributes
```

模板标签。因此，我们可以将 html 标记替换为:

```
<html  <?php language_attributes(); ?>
```

这将生成一个如下所示的属性:

```
<html  lang="en-US">
```

如果您已经包含了任何脚本，那么值得将它们移到

```
assets
```

文件夹，并像对图像一样更改对它们的任何引用。如果您使用全局查找/替换来修改图像路径，那么脚本路径也有可能被修改，尽管您仍然需要移动 JavaScript 文件本身。对于博客来说，在你的标题中包含指向你的 RSS 提要和 pingback URL 的链接是一个好主意，因为这些链接会被许多浏览器自动识别。这两个链接都可以使用添加

```
bloginfo
```

通过包含这些行:

```
<link rel="alternate" type="application/rss+xml" title="<?php bloginfo('name'); ?> RSS Feed" href="<?php bloginfo('rss2_url'); ?>" /><link rel="pingback" href="<?php bloginfo('pingback_url'); ?>" />
```

完成后，现在需要包含

```
wp_head
```

标签。这个标签将获取你正在使用的 WordPress 插件所需的任何 JavaScript 文件或样式表。这是至关重要的，因为这些插件可能无法按预期运行。你只需要一句台词:

```
<?php wp_head(); ?>
```

HTML 头中的最后一个元素是页面标题。大多数 WordPress 主题使用以下的一些变体:

```
<title><?php bloginfo('name'); ?> <?php wp_title('-'); ?></title>
```

这将包括博客的名称，可以在 WordPress 设置中定义，然后是页面标题。例如，如果页面是一篇文章，它将显示文章的标题。这

```
'-'
```

括号中是分隔符，它将放在

```
wp_title
```

内容，如果(且仅如果)有标题显示。这意味着我的博客主页的标题将是“我的博客”，而我的博客上的文章标题将是“我的博客:文章标题”WordPress 是智能的，只有在需要的时候才会包含分隔符。还在

```
header.php
```

文件，我们现在将移动到身体标签。这一部分会根据你的模板的结构而有所不同，但是仔细阅读它，寻找任何应该从 WordPress 中提取的内容。例如，如果网站标题在代码中显示为

```
<h1>My Blog</h1>
```

，应该替换为

```
<h1><?php bloginfo('name'); ?></h1>
```

。此时您最可能需要的模板标签是:

```
    get_option('home');
    ```

    :博客主页的 URL

```
    bloginfo('rss2_url');
    ```

    :博客 RSS 源的 URL

```
    bloginfo('description');
    ```

    :博客的描述，如 WordPress 设置中所定义的

和我们看到的其他模板标签一样，这些需要放在里面

```
<?php ?>
```

标签；它们可以放在 PHP 文件中的任何地方，甚至可以放在 HTML 标签中。将你网站的导航移到 WordPress 可能会有点棘手。有些模板标签可以为您提供类别列表或页面列表，然后您可以使用它们来创建导航的各个部分。模板标签是

```
<?php wp_list_categories(); ?>
```

和

```
<?php wp_list_pages(); ?>
```

，并将为您提供超链接列表，您可以像设计静态导航列表一样设计这些列表的样式。但是，有时您需要页面以特定的顺序出现，或者您需要排除某些页面。通过将某些参数传递给

```
wp_list_pages
```

。要了解更多关于如何操作这个标签的信息，请阅读 Codex 上的内容。继续，如果你已经包含了侧边栏，你会想要包含一些其他的元素，比如类别和页面的链接，或者标签云。即使你的布局没有侧边栏，主题的其他区域也可以添加这些标签。

## 小工具

如果你计划向更广泛的社区发布你的主题，重要的是你要*拓宽*侧边栏。小部件是可以通过 WordPress 管理面板添加到主题预定义区域的内容块。例如，你可以在侧边栏的顶部有一个小部件区域，博客所有者可以在那里轻松地添加页面列表或任意的 HTML 块。小部件超出了本教程的范围，但是你可以在 [WordPress Codex](http://codex.wordpress.org/WordPress_Widgets) 上阅读更多关于它们的内容。您可能想添加到侧边栏的其他标签包括(同样包含在

```
<?php ?>
```

标签):

```
    wp_list_authors();
    ```

    :将博客的所有作者列为 <sgmltag class="element">li 元素。如果他们写了文章，他们的名字将会是一个链接，链接到一个显示他们所有文章的页面。</sgmltag>

```
    wp_list_bookmarks();
    ```

    :输出已经添加到管理面板的<guilabel>链接</guilabel>部分的链接，这些链接也包含在<sgmltag class="starttag">李标签中。</sgmltag>

```
    wp_tag_cloud();
    ```

    :显示已添加到帖子中的所有标签的云。

既然您已经对模板标签有所了解，那么您的页脚部分应该很容易处理。如果您需要输出我们在这里没有涉及的内容，请确保在 Codex 中寻找合适的模板标签。请记住关闭您的中打开的所有标签

```
header.php
```

文件，如正文或任何包装 div。当您完成这些工作时，您可能尝试在不同的点查看页面来测试它。不管是不是这样，当你现在查看页面时，它应该包括所有从 WordPress 中提取的内容，除了页面的主体。

## 博客的主要内容

现在我们已经定义了页眉、页脚和侧边栏的内容，是时候转向页面的核心内容了。我们现在回到我们的

```
index.php
```

文件并实现 WordPress 循环。这个循环是 WordPress 用来循环显示给定页面上的文章，并输出每个文章的内容的机制。它是这样开始的:

```
<?php if (have_posts()) : ?><?php while (have_posts()) : the_post(); ?>
```

这两行检查是否提供了任何帖子。根据您正在查看的页面，会有不同的帖子可用。例如，在主博客页面上，会显示一定数量的最新帖子。如果您正在查看一个特定的帖子，那么只有该帖子将被提供给循环。对于类别、标签或作者页面，将显示属于该标签、类别或作者的所有帖子。有了这两条线，我们现在进入了循环的内部。我们现在需要指示 WordPress 为文章集中的每篇文章分别执行几行代码。例如:

```
<h2><a href="<?php the_permalink() ?>" rel="bookmark" title="Permanent Link to <?php the_title(); ?>"><?php the_title(); ?></a></h2><?php the_time('F jS, Y') ?> by <?php the_author() ?> 
```

这个示例部分以包含文章标题的 h2 标签开始，该标签链接到文章的页面。在这下面，列出了发布时间和作者，包裹在小标签中。这里有许多新的模板标签，您可以使用许多其他标签来自定义您的模板，以显示您想要的 HTML。以下是一些比较常见的例子:

```
    the_permalink()
    ```

    :帖子的永久超链接的 URL。

```
    the_title()
    ```

    :帖子的标题。

```
    the_time('F jS, Y')
    ```

    :以“2010 年 1 月 1 日”的格式显示文章的日期。要以不同方式设置日期格式，请替换

    ```
    'F jS, Y'
    ```

    用另一个 <ulink url="http://php.net/manual/en/function.date.php">PHP 日期格式字符串。</ulink>

```
    the_author()
    ```

    :为撰写帖子的用户显示存档的名称和链接。

```
    the_content()
    ```

    :插入文章内容。没必要把这个放在里面

    ```
    <p></p>
    ```

    标签，因为这将自动完成。

```
    the_category()
    ```

    :显示文章类别存档的名称和链接。

使你的文章显示与你现有的模板相匹配的最简单的方法是在 WordPress 循环中剪切并粘贴你的模板的示例内容代码。然后将适当的模板标签插入文章 HTML 的每个部分。一旦你包含了你想要的一切，是时候结束循环了

```
<?php endwhile; ?>
```

。一旦处理完所有的帖子，就会运行此操作。理想情况下，它后面应该有一些导航控件:

```
<div class="navigation">  <div class="alignleft"><?php previous_posts_link('&laquo; Previous Entries') ?></div>  <div class="alignright"><?php next_posts_link('Next Entries &raquo;') ?></div></div>
```

每个页面显示一定数量的文章，如 WordPress 设置中所定义的。这些控件将允许您的访问者通过下一个和上一个链接导航回旧帖子。传递给的参数

```
previous_posts_link
```

和

```
next_posts_link
```

(括号中的字符串)用于链接的文本。此时，请注意

```
while
```

结构已经关闭，但是

```
if(have_posts())
```

仍然开放。我需要对此稍加阐述。一个页面可能没有文章可以显示(例如，在你添加任何文章之前的主页，或者一个月没有文章发表的存档页面。)在那些情况下，

```
if(have_posts())
```

将评估为

```
false
```

，所以您刚刚编写的模板代码都不会运行。对于这些情况，您需要提供一些后备内容。所以，我们首先需要定义

```
else
```

条件，然后关闭

```
if
```

用 PHP 的语句

```
endif
```

关键词:

```
<?php else: ?><p>Sorry, there are no posts to display.</p><?php endif; ?>
```

对于本例，我们只是添加了一个段落，告诉没有找到该页面的帖子。你也可以添加一个框或链接到网站的其他部分，以帮助访问者找到他们正在寻找的内容。

## 其他页面

在我们的简单例子中，

```
index.php
```

就是作为网站上每一页的模板。但是，如果你想修改模板的某个方面，只在一个特定的页面(或一组 WordPress)让你很容易做到这一点。你需要做的就是

```
index.php
```

更具体的模板文件。例如，您可以创建一个

```
single.php
```

文件和模板将用于单个帖子页面，而不是

```
index.php
```

文件。同样，在那里

```
category.php
```

(对于类别

```
search.php
```

(用于搜索结果)，

```
home.php
```

(用于主页)和许多其他模板文件，您可以创建这些模板文件来自定义站点的每个独立区域。

## 下一步是什么？

如果您已经完成了整个教程，那么您已经:

*   将 HTML 模板中的内容导入到 WordPress 主题文件中
*   更改了代码中对图像、JavaScript 文件和类似内容的引用
*   将所有的 CSS 文件、JavaScript 文件和图片复制到你的新 WordPress 主题目录中
*   包括一些代码来告诉 WordPress 不同内容的位置
*   增加了一些 WordPress 自动放在一起的菜单和链接
*   开始熟悉 WordPress 循环和 WordPress 主题背后的想法

当然，创建一个 WordPress 主题有比我在这里所能介绍的更多的东西。我希望我已经激起了你想了解更多的兴趣。

如果你喜欢读这篇文章，你会爱上[可学的](https://learnable.com?utm_source=sitepoint&utm_medium=link&utm_campaign=learnablelink)；向大师们学习新技能和技术的地方。会员可以即时访问 SitePoint 的所有电子书和互动在线课程，如[WordPress](https://learnable.com/courses/the-beginners-guide-to-web-design-with-wordpress-120?utm_source=sitepoint&utm_medium=link&utm_campaign=learnablelink)网站设计初学者指南。

对这篇文章的评论已经关闭。有关于 WordPress 的问题吗？为什么不在我们的[论坛](https://www.sitepoint.com/forums/forumdisplay.php?266-CMS-amp-Wordpress?utm_source=sitepoint&utm_medium=link&utm_campaign=forumlink)上问呢？

## 分享这篇文章