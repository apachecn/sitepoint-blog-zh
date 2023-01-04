# 在 ColdFusion 中构建动态菜单

> 原文：<https://www.sitepoint.com/dynamic-menu-coldfusion/>

**静态内容是*所以*昨天。网站，无论是内部的还是外部的，都是为了保持最新。**

为此，在最近的一个内部项目中，我创建了一个新的内部网站点，并选择在 ColdFusion 中开发一个动态填充的菜单。我这样做是为了在每次添加新内容时，管理链接不需要我们打开 HTML 文件。这使得更新菜单对我和内容所有者来说少了很多麻烦。

当 HTML/JavaScript 菜单可以相对容易地编写和维护时，为什么要在 ColdFusion 中费力地构建动态菜单呢？当然，我们可以用 HTML 和 JavaScript 构建菜单，只需根据需要修改代码，但这有什么意思呢？更重要的是，项目维护不是我想要的。我不想永远被束缚在这个项目上；我所在的团队太小了，而且有太多的新项目，不能花时间维护旧项目。

最终用户和我想出了一个易于管理的菜单，并且只占用了网站很少的空间。空间是一个问题的原因是网站有几个嵌入的部分，每个部分有几个嵌入的部分，有…你知道我在说什么。

##### 在代码之前…

我们将从设计数据库开始——在我们知道数据库是如何设计的之前，我们无法编写访问数据库的代码。我们动态菜单的数据库很简单。为了完成我们的菜单，我们只需要两个表——没有存储过程或任何其他东西(尽管存储过程是管理菜单中数据的最简单的方法)，所以您可以使用任何您喜欢的数据库系统。

![1224_image1](img/236257f7acde3771d50aa834613dcc43.png)
图像 1

图 1 显示了我们将用于菜单的数据库表。`MENU_ITEMS`表是所有实际的菜单树项目所在的地方。`Name` ( `menu_item_name`)和`HREF` ( `menu_item_url`)与主键(`menu_item_id`)一起存储。表`MENU_ITEMS_XREF`保存了树本身的结构。`menu_item_id`和`child_id`都是指`MENU_ITEMS`表中的`menu_item_id`。出现在`child_id`列中的 ID 表示该 ID 将嵌套在`menu_item_id`列中的项目下。图 2 说明了这个概念。

![1224_image2](img/a9d297b2961ea1aaa1a94b777dbbc767.png)
图像二

`MENU_ITEMS_XREF`表格中的最后一列是`sort_order`字段。最初，我只是让生成菜单的菜单查询处理排序(按字母顺序)。然而，难道你不知道吗——业务部门有其他想法。所以现在有了一个`sort_order`字段，允许内容所有者将更重要或更受欢迎的主题放在页面顶部。

数据库到此为止。我们来编码吧！

##### 代码

我们用来创建菜单的代码非常容易理解。基本上，我们将创建一个自定义的 ColdFusion 标记，该标记在数据库结构上递归，并在此过程中构建适当的菜单结构。我们将分解`create_menu.cfm`的片段，你可以[在这里](https://www.sitepoint.com/examples/cfmenu/create_menu.zip)下载，并讨论每一部分。最终结果将是一个完全去神秘化的、动态构建的树形菜单。

A 节中的代码定义了一些我们需要作为缺省值的初始参数。你可能需要也可能不需要这些，这取决于你的站点是否有子文件夹之类的页面。我们的站点做到了，所以我们需要一种方法从文件夹树到图片目录。

*A 段*

```
<CFPARAM DEFAULT="1" NAME="ATTRIBUTES.Level"> 

<CFPARAM DEFAULT="" NAME="ATTRIBUTES.divID"> 

<CFIF ATTRIBUTES.Level EQ 1> 

    <CFSET VARIABLES.RootLevel = ""> 

<CFELSEIF ATTRIBUTES.Level EQ 2>  

    <CFSET VARIABLES.RootLevel = "../"> 

<CFELSEIF ATTRIBUTES.Level EQ 3>  

    <CFSET VARIABLES.RootLevel = "../../"> 

</CFIF>
```

下面的代码部分都组合成了一个 JavaScript 块，但是为了更容易解释，我把它分成了更小、更容易理解的部分。这个 JavaScript 将处理菜单，折叠和展开适当的分支，并根据需要更改这些分支的图像状态。

*B 部分*

```
<CFOUTPUT>   

 <script type="text/javascript">   

    function ShowMenu(n)  

    {  

      var menu, arrowImg;  

      menu = document.getElementById("d" + n);
```

*C 部分*

```
 // Determine if the menu is currently showing.  

      if (menu.style.display == 'block')  

      {  

        // If it is showing, hide the menu and update the twisty image.  

        menu.style.display = 'none';  

        arrowImg = document.images['i' + n];  

        arrowImg.src = "#VARIABLES.RootLevel#images/right_arrow.gif";  

      }
```

C 部分处理点击已经展开的菜单分支的事件。这样做会触发分支折叠。菜单不一定要这样运行，但是为什么要求用户选择不同的级别来折叠一个部分呢？相反，我们简单地获取用户点击的部分，并查看它是否被展开；如果是的话，我们就把它折叠起来。如果没有展开，请向右移动到 d 部分。

*D 部分*

```
 else  

      {  

        // Hide all layers first.  

        var divs = document.getElementsByTagName("div");  

        for (var i = 0; i < divs.length; i++)  

         if (divs[i].id.indexOf("d0") >= 0)  

            divs[i].style.display = 'none';  

        // Reset the images.  

        for (var j = 0; j < document.images.length; j++)  

          if (document.images[j].src.indexOf("down_arrow") > 0)  

            document.images[j].src = "#VARIABLES.RootLevel#images/right_arrow.gif";  

        // Show the menus and update their twisty images.  

        i = 2;  

        while (n.length >= i)  

        {  

          menu = document.getElementById("d" + n.substring(0, i));  

          arrowImg = document.images["i" + n.substring(0, i)];  

          menu.style.display = "block";  

          arrowImg.src = "#VARIABLES.RootLevel#images/down_arrow.gif";  

          i += 2;  

        }  

      }  

    }  

  </script>  

</CFOUTPUT>
```

D 部分相当长——它是 JavaScript 函数`ShowMenu()`的剩余部分。如果用户选择的分支尚未展开，那么第一步就是折叠所有分支。我们基本上每次都想从一份新的菜单开始。为了实现这一点，我们循环遍历图像标签的结构(全部以系列命名)，重新设置每个图像。我们在菜单结构本身上重复这个过程(组成菜单的每个`<div>`标签都像图片一样被连续命名，所以遍历整个菜单非常容易)。

一旦整个菜单被折叠，我们必须展开选择的菜单分支。由于每个分支都有自己的子分支，JavaScript 不仅要展开主分支，而且如果用户单击子分支，该分支也必须展开(见图 3)。一旦适当的分支被展开，相应的图像需要被改变。我们的菜单使用了两个图像:一个指向右边的蓝色箭头和一个指向下面的金色箭头。这些为用户提供了额外的视觉提示。

![1224_image3](img/aa964a2635e5de7a7c7ddbce38dd59ca.png)
图像 3

一旦调整了图像，我们就完成了—就 JavaScript 而言，也就是说。我们首先介绍了 JavaScript，尽管我们的自定义标记的 ColdFusion 部分将首先执行，因为我想在我们介绍设置之前建立代码流以及它应该如何工作。JavaScript 是这个菜单的工作马，所以了解那里发生了什么以及一切是如何工作的非常重要。现在，是时候面对 CF 了！

##### 该设置

此菜单是 ColdFusion 自定义标签的产品。只要需要菜单出现的地方(记住这个菜单是垂直的)，只需包含标签。要在页面上设置菜单，需要两行代码。示例 1 显示了`<body>`标记以及实际的定制标记调用。body 标签中的 ColdFusion 代码扩展了菜单，以反映用户在站点中的位置。如果没有这个逻辑，默认情况下，菜单会在加载时折叠。

*样品 1*

```
<body <CFIF IsDefined('URL.Div')>   

onload="ShowMenu('<CFOUTPUT>#URL.Div#</CFOUTPUT>')"</CFIF>>   

<CFINCLUDE TEMPLATE="modules/menu/create_menu.cfm">
```

我们不在标准的`<CF_>`语法中调用定制标签；相反，我们使用`<CFINCLUDE>`来调用它。标签被设计成在构建树的时候调用自己，所以我们只需要包含代码。

现在，回到自定义标记—我们将继续使用 ColdFusion 代码。E 部分建立我们的变量并查询数据库。你会注意到这个查询实际上是一个变量的查询。你可以用你最舒服的方式来执行这个部分。我选择将我的初始查询存储为应用程序范围的变量，因为菜单数据不会经常改变。此外，因为菜单位于网站的每个页面上，所以我想节省我的数据库的额外流量。E 部分中的最后一个查询创建了应用程序变量。

*E 部分*

```
<CFPARAM DEFAULT="0" NAME="ATTRIBUTES.MenuID">   

<CFPARAM DEFAULT="" NAME="ATTRIBUTES.DivID">   

<CFPARAM DEFAULT="0" NAME="VARIABLES.LoopCount">   

<CFPARAM DEFAULT="0" NAME="VARIABLES.IncreNum">   

<CFLOCK SCOPE="APPLICATION" TIMEOUT="10" TYPE="READONLY">   

    <CFSET VARIABLES.qGrabMenu = APPLICATION.qGrabMenu>   

    <CFSET VARIABLES.DataSource = APPLICATION.DataSource>   

</CFLOCK>   

<CFQUERY DBTYPE="query" NAME="qGetMenuElement">   

    SELECT *    

    FROM VARIABLES.qGrabMenu   

    WHERE Menu_item_ID = #ATTRIBUTES.MenuID#   

    ORDER BY sort_order   

</CFQUERY>   

<CFQUERY DATASOURCE="#APPLICATION.DataSource#" NAME="APPLICATION.qGrabMenu" CACHEDWITHIN="#APPLICATION.QueryTimeOut#">   

SELECT    

  menu_item_id,   

        (SELECT menu_item_name FROM MENU_ITEMS WHERE MENU_ITEMS.menu_item_id = menu_items_xref.menu_item_id) AS Parent,   

  (SELECT menu_item_url FROM MENU_ITEMS WHERE MENU_ITEMS.menu_item_id = menu_items_xref.menu_item_id) AS Parentlink,   

  child_id,   

        (SELECT menu_item_name FROM MENU_ITEMS WHERE MENU_ITEMS.menu_item_id = menu_items_xref.child_id) AS Child,   

  (SELECT menu_item_url FROM MENU_ITEMS WHERE MENU_ITEMS.menu_item_id = menu_items_xref.child_id) AS Childlink,   

    sort_order   

    FROM menu_items_xref   

</CFQUERY>
```

F 部分完善了我们的代码；这是我们定制标签的最后一块。
使用递归，我们能够用一个定制标签构建并填充菜单树。`Create_menu.cfm`使用递归调用自己作为自定义标签，以便根据需要从主根菜单中构建子分支。当需要一个子分支时，代码调用`<CF_create_menu>`，为新的分支处理数据。如果一个现有的子分支需要额外的子分支，那么代码将被再次调用，并递归另一个级别(或所需的多个级别)来创建分支。每一关完成后，上一关继续。在所有递归运行之后，我们只剩下了菜单树。

*F 部分*

```
<!--- Build menu --->    

<CFLOOP QUERY="qGetMenuElement">   

    <!--- Increment the loop counter --->   

    <CFSET VARIABLES.LoopCount = VARIABLES.LoopCount + 1>   

    <CFIF (qGetMenuElement.childlink NEQ '')>   

        <!--- Last element on a branch --->   

        <CFOUTPUT>   

        <a href="#VARIABLES.RootLevel##childlink#" class="menu"><img src="#VARIABLES.RootLevel#images/transparent.gif" width="9" height="9" border="0" hspace="4">#child#<!--- -#ATTRIBUTES.divID# ---></a><CFIF ATTRIBUTES.divID NEQ ""><CFIF FindNoCase(qGetMenuElement.ChildLink, CGI.PATH_INFO)><script type="text/javascript">ShowMenu('#ATTRIBUTES.divID#');</script></CFIF></CFIF>   

        </CFOUTPUT>   

        <!--- build out branches --->   

    <CFELSE>    

        <CFOUTPUT>   

        <CFSET VARIABLES.IncreNum = "0" & VARIABLES.LoopCount>   

        <CFSET VARIABLES.Num = ATTRIBUTES.DivID & VARIABLES.IncreNum>    

            <a href="##" class="menu" onclick="ShowMenu('#VARIABLES.Num#'); this.blur(); return false"><img src="#VARIABLES.RootLevel#images/right_arrow.gif" name="i#VARIABLES.Num#" width="9" height="9" border="0" hspace="4 /">#child#<!--- -#ATTRIBUTES.divID# ---></a>   

            <div id="d#VARIABLES.Num#" class="expand">   

                <CF_create_menu menuID = "#child_id#" divID = "#VARIABLES.Num#" level = "#ATTRIBUTES.Level#">   

            </div>   

        </CFOUTPUT>   

    </CFIF>     

</CFLOOP>   

<!--- Reset the loop counter --->   

<CFSET VARIABLES.LoopCount = 0>
```

这就是全部了！我们使用数据库和 ColdFusion 创建了一个动态可折叠的树形菜单。

## 分享这篇文章