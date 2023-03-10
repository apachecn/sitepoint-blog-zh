# 5 个时髦的 WordPress 片段来定制你的评论

> 原文：<https://www.sitepoint.com/5-funky-wordpress-snippets-to-customize-your-comments/>

首先，这句话是“有一个应用程序”。现在，流行的观点是“有一个插件可以解决这个问题”——这并不总是一件好事。你为 WordPress 评论区设想的许多定制可以很容易地在你的主题文件中实现，所以你可以得到你想要的完美的评论区，而不用用更多的插件来膨胀你的站点。

不是程序员？你不必如此。下面，你会发现简单的代码和清晰的定制说明。如果你有问题，看看如何改正它们的建议。请注意，这是为 Twenty Eleven 主题编写的，因此如果您的网站基于另一个主题，一些文件名可能会不同。

## 显示评论/引用次数

大多数模板的评论部分以一条关于有多少评论的消息开始。但是，如果你还想在帖子的其他地方显示评论，比如顶部，该怎么办呢？这个代码片段允许您在单个页面循环中的任何地方放置评论记录和消息，并定制返回哪个消息以获得零个、一个和多个响应。

**模板文件:**

content-single.php

**原始代码:**

```
<?php comments_number( 'no responses', 'one response', '% responses' ); ?>.
```

**如何使用:**

原始代码显示了最基本的消息，包括零条、一条和多条消息。下面的例子展示了用一个简短的消息(“加入讨论！”)在动态文本之前。简单地切换出你想说的原始代码信息。也许你想更健谈，如下所示:

```
<p> Join the discussion! <?php comments_number( 'You get to have the first comment!', 'One comment so far - what can you add?', '% people have commented - add your two cents!' ); ?> </p> 
```

**示例输出:**

没有评论:“你有第一个评论！”

一条评论:“到目前为止只有一条评论，你还有什么要补充的？”

多条评论:“已有 3 人发表评论，请补充您的意见！”

**温馨提示:**

*   如果代码破坏了您的页面，请检查您是否不小心在字符串文本中添加了撇号。如果是这样，使用下面的 HTML 代码来显示撇号:`&rsquo;`
*   基于这些知识，您可以轻松地调整注释部分显示文本的方式。只需打开您的 comments.php 文件，搜索当前显示的文本(默认为 TwentyEleven 以“One thought on…”开始，并用您自己的文本替换字符串。

**了解有关此代码的更多信息:**

[http://codex . WordPress . org/Function _ Reference/comments _ number](http://codex.wordpress.org/Function_Reference/comments_number)

## 自定义您的默认头像

许多博客访问者没有在 Gravatar 上注册，所以他们的头像会显示你的默认设置。如果你的评论区需要比无处不在的神秘人多一点个性，为什么不为你的网站创建一个独特的呢？

**模板文件:**

functions.php

**原始代码:**

```
add_filter( 'avatar_defaults', 'new_default_avatar' ); function new_default_avatar ( $avatar_defaults ) { $new_avatar_url = get_bloginfo( 'template_directory' ) .img/new_default_avatar.png'; $avatar_defaults[$new_avatar_url] = 'My Custom Avatar'; return $avatar_defaults; }
```

**如何使用:**

创建 60px 60px 的图片，并上传到你的主题中的图片文件夹。

在代码中，将图像路径和文件名img/new_default_avatar.png ')替换为您自己的路径和文件名。如果你想命名你的默认头像，你可以用你选择的名字替换“你的新默认头像”。此名称只会显示在您的设置页面中，我们将转到该页面。

在您的仪表板上，前往“设置”>“讨论”,在那里您会看到您的新自定义头像。选择它，点击保存，并享受！

**温馨提示:**

*   如果您必须将图像放在主模板目录之外的某个地方，或者由于某种原因它不能正确链接，请尝试用指向该图像的完整(http://和 all) URL 替换`get_bloginfo( 'template_directory' ) .img/new_default_avatar.png';`。
*   如果你想用 CSS 样式化头像，打开你的样式表并为:`.commentlist .avatar {}`添加一个样式

**了解有关此代码的更多信息:**

[http://codex . WordPress . org/How _ to _ Use _ Gravatars _ in _ WordPress](http://codex.wordpress.org/How_to_Use_Gravatars_in_WordPress)

## 从联系人表单中删除 URL 或其他字段

大多数联系人表单中的默认字段是姓名、电子邮件、网站和评论。虽然所有这些字段都有很好的理由存在，但有些人更愿意忽略某些字段，最著名的是网站字段，垃圾邮件发送者经常试图利用这一点。

这个对你的 functions.php 文件的简单修改将会在一瞬间处理掉那些不需要的字段！

**模板文件:**

functions.php

**原始代码:**

```
add_filter('comment_form_default_fields', 'remove_url'); function remove_url($val) { $val['url'] = ''; return $val; }
```

**如何使用:**

这段代码，按原样粘贴，将删除网站字段。其他字段可以通过将代码中出现的所有三个地方的“url”替换为“email”或“author”(名称)来删除。

**了解有关此代码的更多信息:**

[http://codex.wordpress.org/Function_Reference/comment_form](http://codex.wordpress.org/Function_Reference/comment_form)

## 防止你的博客自己发出声音

从链接到你文章的网站上获得引用通告是件好事。每次你链接到自己的网站时都被 ping，这可不好。在您使用那个插件之前，尝试将这个简单的代码片段添加到您的函数中。

**模板文件:**

functions.php

**原始代码:**

```
function disable_self_ping( &$links ) { foreach ( $links as $l => $link ) if ( 0 === strpos( $link, get_option( 'home' ) ) ) unset($links[$l]); } add_action( 'pre_ping', 'disable_self_ping' );
```

**如何使用:**

只需将代码粘贴到 functions.php 文件的末尾。没有比这更简单的了！

**了解有关此代码的更多信息:**

[http://wp-snippets.com/disable-self-trackbacks/](http://wp-snippets.com/disable-self-trackbacks/)

## 更改“留下回复”文本

如果“留下回复”对你来说听起来有点冷漠和不近人情，你可以考虑用下面的代码来改变它:

**模板文件:**

comments.php

**原始代码:**

```
<?php comment_form(array('title_reply'=>'Put in Your Two Cents')); ?>
```

**如何使用:**

在你的主题文件夹的 comments.php 文件中，搜索靠近底部的`<?php comment_form(); ?>`行。用上面的原始代码替换它。

现在，只需将“投入你的两美分”改为你想在那里显示的任何文本。

**了解有关此代码的更多信息:**

[http://wp-snippets.com/disable-self-trackbacks/](http://wp-snippets.com/disable-self-trackbacks/)

从这些片段中获得乐趣；希望他们给你的评论区带来额外的活力！

## 分享这篇文章