# 介绍 XUL——网络的最大秘密:第二部分

> 原文：<https://www.sitepoint.com/xul-nets-biggest-secret-2/>

**[上次](https://www.sitepoint.com/blog/)，我们和 XUL 一起熟悉了逛大街的方法。现在，让我们看看搜索和导航…**

…不要忘记[下载本系列](https://www.sitepoint.com/examples/xul/xul_code.zip)的代码档案，如果您还没有下载的话！

##### 拼命寻找

正如上一个例子所展示的，使用 XUL，您可以通过修改浏览器来使导航更快更容易，从而给站点的访问者带来独特的体验。而且，当您开始将 JavaScript 融入这种混合中时，各种强大的新机会就会出现。这里有一个简单的例子，它显示了一个总是可见的搜索框。它还展示了与 XUL 建筑形式的潜力。

```
<?php 

header ( 'Content-type:  

application/vnd.mozilla.xul+xml' ); 

?> 

<?xml version="1.0"?> 

<!-- example2.xul --> 

<?xml-stylesheet href="example2.css" type="text/css"?> 

<window 

     title="Searching Sitepoint" 

     xmlns:html="https://www.w3.org/1999/xhtml" 

     xmlns="http://www.mozilla.org/keymaster/ 

gatekeeper/there.is.only.xul"> 

 <script type="application/x-javascript" src="example2.js"/> 

 <hbox id="searchPane"> 

   <description><html:div id="title">This  

is HTML</html:div></description> 

   <grid flex="1"> 

     <columns> 

       <column/> 

       <column/> 

     </columns> 

     <rows> 

       <row align="end"> 

         <textbox id="searchField"/> 

         <button id="searchButton" label="Search  

Sitepoint" oncommand="search();"/> 

       </row> 

     </rows> 

   </grid> 

 </hbox> 

 <xbox flex="1"> 

  <browser id="sitepointBrowser" flex="1" src=" 

https://www.sitepoint.com/"/> 

 </xbox> 

</window>
```

要了解这一点，请浏览代码归档中的 example2.xul，并尝试在右上角的框中输入“PHP”。

首先要注意的是，我包含了一些 JavaScript:

```
<script type="application/x-javascript" src="example2.js"/>
```

我们一会儿再看剧本。

接下来，我使用了两个`<xbox />`元素。盒子是我们可以放入其他 XUL 元素的容器——它们使您的应用程序布局变得容易。我已经给第一个赋了一个 id，所以我在我的 CSS 文档中标识它。替代 [hbox](http://www.xulplanet.com/references/elemref/ref_hbox.html) 的是 [vbox](http://www.xulplanet.com/references/elemref/ref_vbox.html) ，用于创建 Y 轴上的分割，而不是 X 轴。

我使用了 [description](http://www.xulplanet.com/references/elemref/ref_description.html) 元素，它是文本的通用容器，只是为了证明您可以将 HTML 放在 XUL 文档中；注意，我必须为 HTML 指定名称空间前缀。请注意，并不是所有的 XUL 元素都可以用来显示 HTML——经验法则似乎是，如果元素可以包含文本，它应该能够显示 HTML。

在第一个 hbox 中，我使用了一个[网格](http://www.xulplanet.com/tutorials/xultu/grids.html)，它的作用很像 HTML 中的一个表格，我用它来帮助正确定位元素。这应该是任何有 ASP.NET 经验的人都熟悉的。

然后我使用了两个“表单”元素，[文本框](http://www.xulplanet.com/references/elemref/ref_textbox.html)和[按钮](http://www.xulplanet.com/references/elemref/ref_button.html)。我已经给每一个都分配了一个“id”属性，用于分配 CSS，并用 DOM 定位文本框。“oncommand”属性指定了单击按钮时调用的 JavaScript 函数，很像 HTML 中的“onClick”属性。

最后，注意第二个 hbox 中 browser 元素的 id；我将在 JavaScript 中使用它。

我在这里使用的 CSS 文档如下所示:

```
window 

{ 

  background-color: navy; 

} 

#searchPane 

{ 

  background-color: silver; 

} 

#searchField 

{ 

  width: 300px; 

  height: 25px; 

  font-family: verdana; 

  font-size: 12px; 

  font-weight: bold; 

  color: purple; 

} 

#searchButton 

{ 

  font-family: verdana; 

  font-size: 12px; 

  font-weight: bold; 

  color: navy; 

} 

/* This style is for the HTML div element */ 

#title 

{ 

  font-family: verdana; 

  font-size: 14px; 

  font-weight: bold; 

  color: #ff4500; 

  text-indent: 10px;  

}
```

请注意，CSS 文档适用于 XUL 和 HTML。

现在，JavaScript:

```
// For searching sitepoint 

function search () { 

  var searchField=document.getElementById('searchField'); 

  var sitepointBrowser=document.getElementById('sitepointBrowser'); 

  sitepointBrowser.setAttribute('src', 

    'https://www.sitepoint.com/search/search.php?q=' 

    +searchField.value+'&submit=Search'); 

}
```

我已经使用 DOM 获取了`searchField`的值。我还使用它的 id 属性获取了 browser 元素。在第三行中，我修改了 browser 元素中 src 属性的值，使其指向 SitePoint 的搜索 URL。

没问题！

##### 外卖菜单

如果你曾经尝试过用 [JavaScript](https://www.sitepoint.com/blog/) 实现一个菜单，你会知道应该是相当容易的事情实际上是非常困难的。实现比简单菜单更多的东西，你将面临一场艰苦的战斗。对于 XUL，这是一个不同的故事。

使用 [menubar](http://www.xulplanet.com/tutorials/xultu/menubar.html) 元素，你可以在你的站点上放置一个菜单:

```
<?php  

header ( 'Content-type: application/vnd.mozilla.xul+xml' );  

?>  

<?xml version="1.0"?>  

<!-- example3.xul -->  

<?xml-stylesheet href="example3.css" type="text/css"?>  

<window  

     title="Searching Sitepoint"  

     xmlns:html="https://www.w3.org/1999/xhtml"  

     xmlns="http://www.mozilla.org/keymaster/gatekeeper/  

there.is.only.xul">  

 <script type="application/x-javascript" src="example3.js"/>  

 <hbox id="menuPane">  

   <menubar id="sitepointMenubar" grippyhidden="true">  

     <menu id="clientside-menu" label="Client Side">  

       <menupopup id="clientside-popup">  

         <menuitem label="Client Side Home"  

            oncommand="goTo('https://www.sitepoint.com/cat/81')"/>  

         <menuseparator/>  

         <menuitem label="HTML and XHTML"  

            oncommand="goTo('https://www.sitepoint.com/subcat/88')"/>  

         <menuitem label="JavaScript and DHTML"  

            oncommand="goTo('https://www.sitepoint.com/subcat/89')"/>  

         <menuitem label="CSS"  

            oncommand="goTo('https://www.sitepoint.com/subcat/90')"/>  

         <menuitem label="XML"  

            oncommand="goTo('https://www.sitepoint.com/subcat/91')"/>  

       </menupopup>  

     </menu>  

     <menu id="serverside-menu" label="Server Side">  

       <menupopup id="serverside-popup">  

         <menuitem label="Server Side Home"  

            oncommand="goTo('https://www.sitepoint.com/subcat/82')"/>  

         <menuseparator/>  

         <menuitem label="ASP and .NET"  

            oncommand="goTo('https://www.sitepoint.com/subcat/92')"/>  

         <menuitem label="Perl and CGI"  

            oncommand="goTo('https://www.sitepoint.com/subcat/93')"/>  

         <menuitem label="Java and J2EE"  

            oncommand="goTo('https://www.sitepoint.com/subcat/94')"/>  

         <menu id="phpmysql-menu" label="PHP and MySQL">  

           <menupopup id="phpmysql-popup">  

             <menuitem label="PHP and MySQL Interviews"  

                oncommand="goTo('https://www.sitepoint.com/subcat/97')"/>  

             <menuitem label="PHP and MySQL Tutorials"  

                oncommand="goTo('https://www.sitepoint.com/subcat/98')"/>  

             <menuitem label="PHP and MySQL Reviews"  

                oncommand="goTo('https://www.sitepoint.com/subcat/99')"/>  

           </menupopup>  

         </menu>  

       </menupopup>  

     </menu>  

   </menubar>  

 </hbox>  

 <hbox flex="1">  

  <browser id="sitepointBrowser" flex="1" src="  

https://www.sitepoint.com/"/>  

 </hbox>  

</window>
```

这个剧本比你目前看到的要大一点。让我们仔细看看。

菜单的根元素是[菜单栏](http://www.xulplanet.com/references/elemref/ref_menubar.html)。我已经关掉了“grippy ”,在 Mozilla 中你可以点击它来隐藏菜单和工具栏。

菜单栏下面是一个[菜单元素](http://www.xulplanet.com/references/elemref/ref_menu.html)，我已经给它分配了一个标签，以便访问者阅读。菜单栏下面的每个菜单元素定义一个新的下拉菜单。

对于菜单，我们有一个 [menupopup](http://www.xulplanet.com/references/elemref/ref_menupopup.html) ，它是当你点击菜单时显示的“面板”。

在 menupopup 内部，我们放置了[menuitem](http://www.xulplanet.com/references/elemref/ref_menuitem.html)，其中标签指定了为菜单项显示的文本，oncommand 属性用于触发一个 JavaScript 函数(我们稍后将会看到)。

出现在两个 menupopup 元素下的[menus pager](http://www.xulplanet.com/references/elemref/ref_menuspacer.html)用于在菜单上放置一条水平线。

注意 PHP 菜单吗？我在这里创建了一个子菜单，这样 SitePoint 的每个 PHP 相关类别都可以出现在一个菜单下。

JavaScript 非常简单:

```
// Directs browser element to a different src  

function goTo (url) {  

  var sitepointBrowser=document.getElementById('sitepointBrowser');  

  sitepointBrowser.setAttribute('src',url);  

}
```

和 CSS:

```
window  

{  

  background-color: navy;  

}  

menu  

{  

  background-color: white;  

  font-family: verdana;  

  font-weight: bold;  

  font-size: 12px;  

  color: gray;  

}  

menupopup  

{  

  background-color: silver;  

}  

menuitem  

{  

  font-family: verdana;  

  font-weight: bold;  

  font-size: 12px;  

  color: navy;  

}
```

换句话说，构建菜单从未如此简单！

你可以在 XUL 做很多事情，我可以永远给你演示。所有那些你费尽心机构建到你的网站中的东西，比如树形菜单，“工具提示”，滚动条，进度条等等，用 XUL 都是小菜一碟。

对于任何有构建 GUI 经验的人来说，你会发现 Mozilla 甚至实现了“广播者”和“观察者”作为 XUL 元素，还有一个“命令集”元素，可以用来对事件做出多种响应。哦，我提到覆盖，模板和 RDF 数据源了吗？当你想改变 XUL 窗口部件的行为时，XBL 怎么办？也许下周，在这个系列的最后一期中…

## 分享这篇文章