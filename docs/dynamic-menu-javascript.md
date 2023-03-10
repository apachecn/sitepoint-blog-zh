# 在 JavaScript 文章中构建动态菜单

> 原文：<https://www.sitepoint.com/dynamic-menu-javascript/>

这里，我们不是在谈论 DHTML 下拉菜单。这里，我们讨论的是一个使用 Javascript 的简单导航菜单。它标识活动页面，并相应地以不同于其他链接的方式显示该链接，从而方便用户导航。

这里我们将雇用一个单身。将用于导航菜单下所有页面的 js 文件。所以您不需要更新所有的页面:您只需要更新一个 javascript 库文件(。js 文件)。

例如，考虑这三个导航链接:

![1012_image1](img/a700faced7acc8dec203f63c780c180a.png)
链路 1 激活。

![1012_image2](img/0e568e3e0e2a20717086c5f47c1e3439.png)
链路 2 激活。

![1012_image3](img/c0f49e7641a1bb0a5f2131adf94f592b.png)
链路 3 激活。

如您所见，这些是导航菜单如何根据活动链接出现在不同页面的截图。这些菜单由单个 javascript 文件生成。

通过为菜单定义一个类(比如说`.links`)并为`.links:active`分配不同的属性，可以只使用层叠样式表(CSS)来创建这样一个菜单。所以你可以用一个 CSS 文件来创建菜单。

虽然这很完美，但是在这里使用 Javascript 还是有好处的。正如您在上面的照片中看到的，活动链接的末尾有一个“β”字符。这只是为活动链接创建的，因此是动态创建的。使用 JavaScript，您可以使用任何字符或图标图像，使链接看起来非常活跃。

##### 履行

要实现这种类型的菜单，首先你需要一个 CSS 文件，比如 links_style.css，来定义链接的样式。接下来是 Javascript 文件 nav.js。

在 nav.js 文件中，首先我们声明三个数组。一个名为“`links`”的数组包含链接的名称。另一个数组叫做“`links_text`”，由链接的文本组成(例如 Link 1)。前者将用于与该文件进行比较。使用链接文件中的标题命名链接数组中的各个元素，不包括扩展名(例如，如果第一个链接将您带到 index.htm，则将其命名为 index)。第三个数组名为“`links_url`”，包含用户链接到的 URL。

现在，我们想要的是将数组链接中的每个元素与当前页面的不带扩展名的文件名进行比较。然后，我们将相应地为链接分配一种样式，以及您决定添加到链接文本中的任何额外文本或图像，以突出显示活动页面。

设`loc`为比较字符串。`loc`可以发现为:

`var loc=String(this.location);
loc=loc.split("/");
loc=loc[loc.length-1].split(".");
loc=loc[loc.length-2];`

因此，如果当前页面是 https://www.sitepoint.com/index.htm，那么`loc`将被索引。现在我们将它与数组链接中的每个元素进行比较。这个数组的第一个元素和 loc 是相等的，因此我们用下面的方式写这个链接。

`document.write('<table class="explorer_active"
onmouseover="this.className='explorer_active';return true"
onmouseout="this.className='explorer_active';return true"
onmousedown="this.className='explorer_active';return true"
onclick="location.href='' + links_url[i] + ''"><tr><td>
<a href="' + links_url[i] + '" class="menu">'
+ links_text[i] + ' <b>Â»</b></a></td></tr></table>');`

由于`links`数组中的其他元素不等于`loc`，它们将被写成:

`document.write('<table class="explorer"
onmouseover="this.className='explorer_over';return true"
onmouseout="this.className='explorer';return true"
onmousedown="this.className='explorer_down';return true"
onclick="location.href='' + links_url[i] + ''"><tr><td>
<a href="' + links_url[i] + '" class="menu">'
+ links_text[i] + '</a></td></tr></table>');`

这就是你的动态菜单！

您必须在所有页面中放置的代码是:

`<script language="javascript" src="nav.js"></script>`

将这段代码放在页面上需要导航菜单出现的任何地方。

您需要的另一个代码是

`<link rel="stylesheet" href="links_style.css" />`

它必须放在 HTML 页面的`<head>`部分。

因此，清单 1 的 nav.js 是:

`/* The link details */
var links = new Array ("link1", "link2", "link3");
var links_text = new Array ("Link 1", "Link 2", "Link 3");
var links_url = new Array ("link1.htm", "link2.htm",
"link3.htm");`

 `/*解析位置*/
var loc = String(this . location)；
loc = loc . split("/")；
loc=loc[loc.length-1]。拆分(“.”);
loc = loc[loc . length-2]；

/*菜单生成函数*/
函数 dyn _ Menu _ gen()
{
for(var I = 0；I<links . length；i++)
{
if(loc = = links[I])
{
document . write('<table class = " explorer _ active "
onmouseover = " this . class name = " explorer _ active "；return true "
onmouseout = " this . class name = ' explorer _ active '；return true "
onmousedown = " this . class name = ' explorer _ active '；return true "
onclick = " location . href = ' '+links _ URL[I]+' ' "><tr><TD>
<a href = " '+links _ URL[I]+' " class = " menu ">'
+links _ text[I]+'<b></b></a></TD>
}
else
{
document . write(<table class = " explorer " onmouseover = " this。
class name = " explorer _ over "；return true " onmouseout = " this . class name =
' explorer "；return true " onmousedown = " this . class name =
' explorer _ down "；return true " onclick = " location . href = ' '
+links _ URL[I]+' ' "><tr><TD><a href = " '+links _ URL[I]+
' " class = " menu ">'
+links _ text[I]+'</a></TD></tr><
}
document . write(<table cellspacing = " 0 " cell padding = " 0 "
bgcolor = " # FFFFFF "><tr><TD></TD></tr></table>')；
}
}` 

`/* Generate the menu */
dyn_menu_gen();`

清单 2，links_style.css 是:

`.explorer{font-family:verdana,arial,helvetica; font-size:8pt;
font-weight:normal; text-decoration:none; color:#000000;
background:#B5D0FF; cursor:hand; width:150px;
height:30px; border:1 solid #A6C0ED}
.explorer_over{font-family:verdana,arial,helvetica; font-size:8pt;
font-weight:normal; text-decoration:none; color:#000000;
background:#A7C0EB; cursor:hand; width:150px;
height:30px; border-right:1 solid #5C6980; border-bottom:1
solid #5C6980; border-left:1 solid #B8D3FF; border-top:1
solid #B8D3FF}
.explorer_down{font-family:verdana,arial,
helvetica; font-size:8pt; font-weight:normal;
text-decoration:none; color:#000000; background:#A7C0EB;
cursor:hand; width:150px; height:30px; border-left:1
solid #5C6980; border-top:1 solid #5C6980; border-right:1
solid #B8D3FF; border-bottom:1 solid #B8D3FF}
.explorer_active{font-family:verdana,arial,helvetica; font-size:8pt;
font-weight:normal; text-decoration:none; color:#000000;
background:#FFFFFF; cursor:hand; width:150px;
height:30px}
.menu{font-family:verdana,arial,helvetica; font-size:8pt;
font-weight:normal; text-decoration:none; color:#000000}`

我希望这个脚本能够简化您的 Web 页面的动态 JavaScript 导航的创建！

## 分享这篇文章