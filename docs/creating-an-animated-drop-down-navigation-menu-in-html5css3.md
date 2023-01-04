# 在 HTML5/CSS3 和 Webkit 中创建动画下拉导航菜单

> 原文：<https://www.sitepoint.com/creating-an-animated-drop-down-navigation-menu-in-html5css3/>

这是为想要创建下拉菜单的初级和中级 HTML5 和 CSS3 开发者准备的教程。我们正在创建的菜单只用 CSS 来制作动画……看不到 JavaScript！

本教程的演示结果可以在这里找到。

我们将使用 nav `body`设置我们的 HTML，并提供一个 id“top”。下一步将创建几个 href 为“#”的列表，并给链接一个“toplink”类。在每个列表中，我们将嵌套一个无序列表，然后为每个导航链接嵌套三个列表项，包括它的 href。

```
<nav id="top">
  <ul>
    <li> <a class="toplink" href="#">Item One</a>
      <ul>
        <li><a href="#">Drop Menu 1</a></li>
        <li><a href="#">Drop Menu 2</a></li>
        <li><a href="#">Drop Menu 3</a></li>
      </ul>
    </li>
    <li><a class="toplink" href="#">Item Two</a>
      <ul>
        <li><a href="#">Drop Menu 1</a></li>
        <li><a href="#">Drop Menu 2</a></li>
        <li><a href="#">Drop Menu 3</a></li>
      </ul>
    </li>
    <li><a class="toplink" href="#">Item Three</a>
      <ul>
        <li><a href="#">Drop Menu 1</a></li>
        <li><a href="#">Drop Menu 2</a></li>
        <li><a href="#">Drop Menu 3</a></li>
      </ul>
    </li>
    <li><a class="toplink" href="#">Item Four</a>
      <ul>
        <li><a href="#">Drop Menu 1</a></li>
        <li><a href="#">Drop Menu 2</a></li>
        <li><a href="#">Drop Menu 3</a></li>
      </ul>
    </li>
  </ul>
</nav>
```

以上是导航的全部内容，但是您可以根据自己的需要随意编辑文本和链接。

## 在 CSS 上

使用我们在 HTML 中声明的 id，我们将用一些颜色构造我们的导航栏，当然还有下拉动画(我也包含了一些悬停颜色)。

首先，我们将为导航框(#top)设置 z 索引(用于堆叠)、宽度、行高、填充和背景颜色:

```
#top { 
  z-index: 2; 
  width: 200px; 
  line-height: 2em; 
  background: #222222; 
  padding: 30px; 
} 
```

然后，我们将获得导航列表和列表链接，并给它们颜色和一些填充，使它们位于自己的框中:

```
 #top li li, #top li li a { 
  height:0px; 
  margin-top: -10px; 
  text-align: center; 
  color: rgba(255,255,255,.0); 
  text-decoration: none; 
  -webkit-transition:all .7s ease-in-out;
 } 
```

接下来，我们将为下拉菜单添加一些填充、圆角边框和悬停颜色变化效果:

```
 #top li:hover li { 
  height:40px; 
  margin-top:5px; 
  margin-bottom: 5px; 
  background: #333333; 
  border-radius: 20px; 
} 

#top li li:first-of-type { 
  padding-top: 15px; 
} 

#top li:hover li a { 
  color: rgba(255,102,0,.8); 
} 

#top li li a:hover { 
  color:#ffffff; 
} 
```

最后，我们将从页面上的所有列表中删除项目符号，并给链接一个漂亮的橙色，并删除它们的下划线:

```
 li { 
  list-style: none; 
} 

a { 
  color: orange; 
  text-decoration: none; 
} 
```

一旦你完成了教程，你就可以随心所欲地设计导航了！这是一个基本的例子，你可以操纵和重新设计它来适应你的网站。

如果你喜欢读这篇文章，你会喜欢[可学的](https://learnable.com/)；向大师们学习新技能和技术的地方。会员可以即时访问 SitePoint 的所有电子书和交互式在线课程，如真实世界的[html 5&CSS3](https://learnable.com/courses/html5-css3-for-the-real-world-1484)。

对这篇文章的评论已经关闭。对 HTML 有疑问？为什么不在我们的[论坛](https://www.sitepoint.com/forums/forumdisplay.php?52-(X)HTML?utm_source=sitepoint&utm_medium=link&utm_campaign=forumlink)上问呢？

## 分享这篇文章