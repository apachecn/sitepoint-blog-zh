# 如何整理你的菜单

> 原文：<https://www.sitepoint.com/wordpress-menu-html-tidy/>

我喜欢 WordPress。我也喜欢干净的语义 HTML。不幸的是，一些标准的 WordPress 主题函数返回的代码有点混乱。对我来说，罪魁祸首是 [wp_list_pages()](http://codex.wordpress.org/Function_Reference/wp_list_pages) 和更新的[WP _ nav _ menu()](http://codex.wordpress.org/Function_Reference/wp_nav_menu)；两者都返回页面链接的无序列表，通常用于页面菜单和站点地图，

```
 <?php wp_nav_menu(array( 'depth'=>2 )); ?> 
```

对于默认安装的主页、关于和联系人页面，代码产生了这个令人讨厌的 HTML:

```
 <div class="menu">
<ul>
<li >
<a href="http://mysite.com/" title="Home">Home</a>
</li>
<li class="page_item page-item-2 current_page_ancestor current_page_parent">
<a href="http://mysite.com/about" title="About">About</a>
<ul class='children'>
<li class="page_item page-item-5 current_page_item">
<a href="http://mysite.com/about/contact-us" title="Contact us">Contact us</a>
</li>
</ul>
</li>
</ul>
</div> 
```

代码是有效的，但它包含我们通常不需要的项目:

*   严格来说，外部的`div`不是必需的。我更喜欢给`ul`一个 ID，比如“navigation ”,或者使用 HTML5 `nav`元素。
*   当我们的链接包含相同的文本时，我们不需要标题属性。
*   我们的 CSS 或者 JavaScript 需要“page_item”和“page-item-N”类的钩子吗？
*   子链接列表的“children”类不是必需的——我们可以使用一个选择器来设置它们的样式，比如“nav ul ul li”
*   current_page_ancestor 和 current_page_parent 类的意思是一样的，但是我更喜欢一个更短的名字，比如“open”
*   同样，我希望将 current_page_item 重命名为“active”
*   我们需要完整的页面 URL 吗——我们可以使用更短的绝对地址，比如/、/about 和/contact？

有几种方法来整理 HTML，但最简单的解决方案是使用正则表达式替换字符串。

**注意:**WordPress 3 Walker 对象
在 WordPress 3.0 中，一个自定义的 Walker 对象可以作为参数传递给 wp_nav_menu()。该对象提供代码，为每个页面链接输出您自己的自定义 HTML。虽然这在某些情况下很有用，但你可能需要外部 HTML 的 regexs，代码不一定会更短，而且它在 WordPress 2.x 和更低版本中不工作。

下面的 PHP 代码将一个更整洁的 HTML 菜单输出到两个级别(主菜单和子菜单)。在大多数情况下，它应该替换主题的 header.php 文件中对 wp_nav_menu()或 wp_list_pages()的调用:

```
 echo preg_replace(array(
    '/t/', // remove tabs
    '/'.str_replace('//','//', get_bloginfo('url')).'/i', // remove full URL
    '/current_page_items*/i',
    '/current_page_ancestors*/i',
    '/current_page_parents*/i',
    '/page_items+/i',
    '/page-item-d+s*/i',
    '/childrens*/i',
    '/s*class=["']["']/i', // empty classes
    '/s*title="[^"]+"/i', // all titles
    '/s+>/i',
    '/div>/i' // change div to nav
  ),
  array(
    '',
    '',
    'active',
    'open',
    '',
    '',
    '',
    '',
    '',
    '',
    '>',
    'nav>'
  ),
  wp_nav_menu(array( 'menu_class'=>'', 'depth'=>2, 'echo'=>false ))
); 
```

如果你使用的是 WordPress 之前的版本，把倒数第二行“wp_nav_menu(…)”替换成:

```
"<nav><ul>n"
  . wp_list_pages('depth=2&title_li=&sort_column=menu_order&echo=0')
  . "</ul></nav>" 
```

我们得到的 HTML 更加干净，减少了 50%以上。较长的菜单可以节省更多的钱。

```
 <nav>
<ul>
<li><a href="/">Home</a></li>
<li class="open">
<a href="/about">About</a>
<ul><li class="active"><a href="/about/contact-us">Contact us</a></li></ul>
</li>
</ul>
</nav> 
```

请注意，正则表达式很强大，但很危险。如果您使用更深的页面深度，或者有一个名为“children”或“page_item”的页面，您可能需要更改代码

现在没有借口了——去整理你的 WordPress HTML 吧！

## 分享这篇文章