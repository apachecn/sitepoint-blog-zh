# 理解 WordPress 中的“循环”

> 原文：<https://www.sitepoint.com/understanding-loop-wordpress/>

![Loop](img/81935e4b5d4ebd5e97029807ed972d36.png)

*本文是与 [SiteGround](https://www.siteground.com/sitepoint-recommended?afcode=97a975da3502771c04e59cbae092b1dd&campaign=the-loop) 合作创作的系列文章的一部分。感谢您对使 SitePoint 成为可能的合作伙伴的支持。*

当讨论 WordPress，特别是主题的开发(或者在现有的主题中创建新的页面模板)时，你不可避免地会碰到“循环”。这个循环是一个框架，在这个框架中，WordPress 为用户访问的任何给定页面构建内容，无论是静态主页还是展示最近帖子的博客视图，或者介于两者之间的任何内容。这听起来可能有点复杂，但实际上，这只是一个循环机制。

最简单地说，循环是一种循环结构，就像编程中的任何其他结构一样。它遍历所有站点内容的列表，循环浏览你的文章或页面，并从它们获取请求的内容。在最复杂的情况下，您可以多次运行循环，只获取某些类别中的某些项目，不在某些类别中的项目，在某个日期范围内发布的项目，或者具有其他特定标识信息的项目。

WordPress 主题中的每个页面模板都可能包含这个循环。这是模板从存储在数据库中的页面和文章中搜索和获取内容的一种方式。让我们来看看一些细节:

## 循环的一个基本例子

```
<?php 
if ( have_posts() ) {
    while ( have_posts() ) {
        the_post(); 
        // Post Content here
    }
}
?> 
```

你可以在上面的例子中看到，这是一个非常简单的设置。整个事情从一个条件开始，`have_posts`检查以确保实际上有任何要查找的帖子。然后循环发生了—*当*仍然有帖子时(再次使用`have_posts`),它遍历下一个帖子并调用`the_post`——引用当前正在遍历的那个。

## 特定查询

如果你的需求比简单地返回你网站上的每一个帖子更重要，你需要限制你的查询。这就是 [WP_Query](https://codex.wordpress.org/Class_Reference/WP_Query) 发挥作用的地方。

### 按类别过滤

在下面的例子中，我们将查询 id 为 4 的类别中的文章。然后，您可以看到循环本身内容的示例。在这里，我们检查类别 ID 为 4 的文章，然后，在`.post` div 中，我们显示文章的标题(链接到文章)、文章的日期、文章的内容和文章的元数据。

```
<!-- Query for posts which are in category 4 -->
<?php $query = new WP_Query( array( 'cat' => 4 ) ); ?>

<!-- Begin The Loop -->
<?php if ( $query->have_posts() ) {
      while ( $query->have_posts() ) {
        $query->the_post(); ?>
        <div class="post">
            <!-- Display the Title as a link to the Post's permalink. -->
            <h2><a href="<?php the_permalink() ?>" rel="bookmark" title="Permanent Link to <?php the_title_attribute(); ?>"><?php the_title(); ?></a></h2>

            <!-- Display the date (November 16th, 2009 format) and a link to other posts by this posts author. -->
            <?php the_time( 'F jS, Y' ); ?> by <?php the_author_posts_link(); ?>

                         <!-- Display the post content -->
            <div class="entry">
                  <?php the_content(); ?>
            </div>

                        <!-- Display the post metadata -->
            <p class="postmetadata"><?php _e( 'Posted in' ); ?> <?php the_category( ', ' ); ?></p>
        </div> 
    }
} 
```

使用`WP_Query`可以产生一些非常可定制的结果。你可以只包含一个或几个类别的帖子，或者包含除了之外的所有*的帖子。您可以搜索并返回包含关键字的帖子，或通过 ID 定位帖子，使用`post_type`仅显示页面数据，等等。如果你想更深入地了解`WP_Query`，看看 [WP_Query](https://codex.wordpress.org/Class_Reference/WP_Query) 文档。*

> 提示:你可以通过几种方式获得一个类别的 ID 号。一个简单的方法是在你的 WP-Admin 中打开“文章”,然后是“类别”。右键单击列表中所需的类别名称，并保存 URL。然后将它粘贴到文本编辑器或记事本中的某个地方，看一看。例如，它可能看起来像这样:`http://example.com/wp-admin/term.php?taxonomy=category&tag_ID=4&post_type=post&wp_http_referer=%2Fwp-admin%2Fedit-tags.php%3Ftaxonomy%3Dcategory`–您正在寻找的是`tag_ID`号！

### 按自定义字段值过滤

WordPress 中可用的自定义字段非常有用，有时你可能还需要根据这些字段中设置的值来过滤文章。这是一个简单的任务，但是对于 WordPress 开发新手(或者那些没有深入研究定制模板或循环的人)来说，这是值得一试的，因为他们意识到这一切是多么的可定制。假设您有一个自定义字段`department`，并且您正在查找值为`marketing`的帖子:

```
$query = new WP_Query( array('meta_key' => 'department', 'meta_value' => 'marketing') ); 
```

## 理解“循环”

理解循环的最好方法就是使用它。使用它来获取您为主题创建的模板中的内容，或者您在现有主题的现有模板中自定义的代码中的内容。当你想做一些事情时，过滤某些标准，再次运行循环——检查 Codex 中的[循环文档](https://codex.wordpress.org/The_Loop),或者在 Google 上搜索想法。天空是无限的！

* * *

如果你已经建立了你的模板并且解决了所有的循环，你正在寻找一个托管你的 WordPress 站点的地方，看看我们的合作伙伴， [SiteGround](https://www.siteground.com/sitepoint-recommended?afcode=97a975da3502771c04e59cbae092b1dd&campaign=the-loop) 。他们有负担得起的 WordPress 主机，一键安装，暂存环境，等等！

## 分享这篇文章