# SitePoint 代码挑战:CSS 下拉菜单

> 原文：<https://www.sitepoint.com/sitepoint-code-challenge-css-drop-down-menus/>

在我们的 [SitePoint 论坛](https://www.sitepoint.com/forums/ "SitePoint forums")快速代码挑战系列的下一步，我们将从深奥和可爱(棋盘、旋转骰子)转向更具体和实用的东西:下拉菜单。你不必走很远就能找到一个——在 SitePoint 主页上就有一个。

这里有一份与**相关的** HTML:

```
<!DOCTYPE html>
<head>
  <style type="text/css">
    /* Put your css here */
  </style>
</head>
<body>
<div>
  <ul>
    <li><a href="http://www.sitepoint.com" >Home</a></li>
    <li><a href="https://www.sitepoint.com/forums/">Forums</a>
      <ul>
        <li><a href="https://www.sitepoint.com/forums/forumdisplay.php?240-Web-Content" >Web Content</a></li>
        <li><a href="https://www.sitepoint.com/forums/forumdisplay.php?40-Design-Your-Site" >Design Your Site</a></li>
        <li><a href="https://www.sitepoint.com/forums/forumdisplay.php?4-Program-Your-Site" >Program Your Site</a></li>
        <li><a href="https://www.sitepoint.com/forums/forumdisplay.php?95-Host-Your-Site" >Host Your Site</a></li>
        <li><a href="https://www.sitepoint.com/forums/forumdisplay.php?45-Manage-Your-Site" >Manage Your Site</a></li>
        <li><a href="https://www.sitepoint.com/forums/forumdisplay.php?47-Community-Center" >Community Center</a></li>
      </ul>
    </li>
    <li><a style="cursor:default;">Resources</a>
      <ul>
        <li><a href="https://www.sitepoint.com/newsletter/#subscription-info" >Newsletters</a></li>
        <li><a href="https://www.sitepoint.com/category/podcast/" >Podcast</a></li>
        <li><a href="http://reference.sitepoint.com/css" >CSS Reference</a></li>
        <li><a href="http://reference.sitepoint.com/html" >HTML Reference</a></li>
        <li><a href="http://reference.sitepoint.com/javascript" >Javascript Reference</a></li>
        <li><a href="https://www.sitepoint.com/tools/" >Tools</a></li>
        <li><a href="https://www.sitepoint.com/tag/html5-dev-center/" >HTML5 Dev Center</a></li>
        <li><a href="https://www.sitepoint.com/web-hosting-reviews/" >Hosting Reviews</a></li>
      </ul>
    </li>
    <li><a href="http://dealfuel.com/?utm_source=sitepoint&utm_medium=menu&utm_campaign=sitepoint" target="_blank">Deals</a></li>
    <li><a href="http://products.sitepoint.com/" >Bookstore</a>
      <ul>
        <li><a href="https://www.sitepoint.com/books-matrix/" >Find the right book</a></li>
        <li><a href="http://products.sitepoint.com/" >New Releases</a></li>
        <li><a href="http://products.sitepoint.com/development" >Web Development Books</a></li>
        <li><a href="http://products.sitepoint.com/design" >Web Design Books</a></li>
        <li><a href="http://products.sitepoint.com/business" >Business & Marketing Books</a></li>
        <li><a href="https://www.sitepoint.com/for-educators/" >Books for Educators</a></li>
        <li><a href="https://www.sitepoint.com/frequently-asked-questions/" >Frequently Asked Questions</a></li>
        <li><a href="https://www.sitepoint.com/shipping/" >Shipping Rates & Times</a></li>
      </ul>
    </li>
    <li><a href="http://www.sitepointmarket.com" >Market</a></li>
    <li><a href="https://learnable.com/sitepoint" >Courses</a></li>
    <li><a href="http://www.sitepoint.com" >Sitepoint Network</a>
      <ul>
        <li><a href="https://www.sitepoint.com/" >JSPro</a></li>
        <li><a href="http://www.sitepoint.com" >BuildMobile</a></li>
        <li><a href="http://www.sitepoint.com" >DesignFestival</a></li>
        <li><a href="http://www.sitepoint.com" >RubySource</a></li>
        <li><a href="http://www.sitepoint.com" >CloudSpring</a></li>
        <li><a href="http://www.sitepoint.com" >PHPMaster</a></li>
        <li><a href="https://learnable.com/" >Learnable</a></li>
        <li><a href="http://www.sitepointmarket.com" >SitePoint Market</a></li>
      </ul>
    </li>
  </ul>
</div>
</body>
</html>
```

SitePoint 使用一个名为 Superfish 的 jQuery 插件来确保向后兼容旧浏览器，甚至是 IE 6。在这个练习中，我们将只使用创建这个效果所需的 CSS，因为现代浏览器不需要任何 JavaScript 就可以实现它。

**初学者挑战**
仅在 CSS 中复制 SitePoint 主页下拉菜单。您需要使用 li:hover 属性、相对位置和绝对位置。不要太在意颜色、字体和间距，这里的目标是掌握和理解下拉菜单的效果。

**高级挑战**
使用 CSS 媒体查询将菜单变成手机的手风琴。请记住，手机会将对元素的第一次触摸记录为:悬停。

**专家挑战**
使用 CSS 转场，让菜单折叠或淡入、淡出，或者任何你喜欢的方式。您可能需要修改 HTML 以获得更精细的效果。

玩得开心！

你可以在这里找到答案。

## 分享这篇文章