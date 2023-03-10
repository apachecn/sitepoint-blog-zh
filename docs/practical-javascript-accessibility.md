# 实用的 JavaScript 可访问性

> 原文：<https://www.sitepoint.com/practical-javascript-accessibility/>

这篇文章将向你展示一些你现在可以做的简单事情，让你的 JavaScript 更容易理解。这不是尖端技术，而是我们已经做了多年的东西。这篇文章是对我们的介绍性文章“T2 JavaScript 可访问性 101 ”的扩展

## 三个核心原则

JavaScript 可访问性归结为三个核心原则:

*   所有 JavaScript 功能都必须采用可以被解释为文本的形式。
*   所有 JavaScript 功能都必须可以通过键盘访问。
*   JavaScript 中基于时间的活动应该是用户可控的，除非这会改变它的含义。

让我们来看看这些原则中的每一条，看看我们能做些什么来实现它。

## 文本解释

这一原则的要点是将信息表示为具有**有意义结构**的文本，这可以通过编程来确定。实体元素语义应该是所有交互内容的基础，但是对于辅助技术来说尤其如此。

视力正常的用户并不总是受到语义的影响——菜单之所以是菜单，是因为它看起来像菜单，不管它是由列表还是由`<div>` s 构建的。但是盲人用户只能理解他们的屏幕阅读器能够解释的内容——菜单之所以是菜单，是因为它是结构化链接的层次结构。例如，如果我们正在构建一个选项卡框，它可能看起来像下面的例子。注意各种语义标签是如何使用的。

```
<div class="box">
  <section class="panel">
    <h3 class="tab" tabindex="0">Section 1</h3>
    <p class="content">This is the content for Section 1</p>
  </section>
  <section class="panel">
    <h3 class="tab" tabindex="0">Section 2</h3>
    <p class="content">This is the content for Section 2</p>
  </section>
</div>
```

让信息可以通过程序访问也很重要。这意味着使用标准的 <abbr title="Document Object Model">DOM</abbr> 函数向页面添加新内容，而不是使用`document.write()`或`innerHTML`。

不可否认，这很方便，而且通常比一个节点一个节点地添加新内容要快得多。`innerHTML`的问题是浏览器经常改变标记，所以得到的 DOM 与您指定的不同。在极少数情况下，以这种方式添加的内容根本不会出现在 DOM 中。

要解决这个问题，请通过一个中间的非追加元素添加 HTML，如下所示。要使用这个函数，向它传递一个 HTML 字符串和一个目标元素引用。该函数创建一个虚拟 DOM，然后将其节点附加到目标上。

```
function appendHTML(target, html)
{
  var fragment = document.createElement('div');
  fragment.innerHTML = html;

  while(fragment.hasChildNodes())
  {
    target.appendChild(fragment.firstChild);
  }

  return target;
}
```

## 键盘可访问性

当使用键盘访问交互内容时，坚持使用一组核心键:`Tab`、`Enter`、四个`Arrow Keys`和`Escape`。这些键应该不加修改地使用，即不需要按住`Shift`或其他修饰键。如果您确实需要使用其他键或修饰键击，您应该向用户提供说明。`Alt`尽管如此，最好还是避免组合，因为它们用于本地菜单快捷方式。

大多数键也有一个默认的浏览器动作，有时有必要阻止默认动作，以防止行为冲突。例如，当在下拉菜单中使用`Up-Arrow`和`Down-Arrow`键时，你不会希望它们同时滚动页面。标准的做法是使用`preventDefault()`，如本例所示，取自菜单脚本:

```
menu.addEventListener('keydown', function(e)
{
  if(/^(3[789]|40)$/.test(e.keyCode.toString()))
  {
    switch(e.keyCode)
    {
      case 37:
        //... handle left-arrow
        break;
      case 38:
        //... handle up-arrow
        break;
      case 39:
        //... handle right-arrow
        break;
      case 40:
        //... handle down-arrow
        break;
    }

    e.preventDefault();
  }
}, false);
```

如果`keyCode`与一个箭头键匹配，该函数将根据情况处理该键，然后调用`preventDefault()`。如果按下任何其他键，则忽略该事件，保持默认行为。注意 ***千万不要*** 挡住`Tab`键，否则会困住用户焦点！

注意上面的例子使用了`keydown`而不是`keypress`，因为大多数浏览器不会(也不应该)为控制键触发`keypress`事件。如果按键被按住，`keydown`事件也会持续触发，所以可能会有这样的情况，你更喜欢使用`keyup`,它不会重复，但是不能阻止默认动作。

另一个重要的考虑是确保我们维护一个**逻辑内容顺序**。例如，当使用丰富的工具提示时，它们必须直接插入到触发它们的元素之后，这样你就可以`Tab`到它们，然后屏幕阅读器就可以阅读它们了。例如，一个简单的工具提示可能如下所示:

![A rich tooltip positioned above the linked-term.](img/d849922e4a502acb324da70a87466fa8.png)

位于链接术语上方的丰富工具提示。

您可以看到主工具提示文本周围有方括号，而底部的链接有方括号和一个分隔管道符号。文本也被包裹在`<em>`中，以增加语义强调。禁用 CSS 时，内容如下所示:

![The same tooltip viewed without CSS, showing the explanation as parenthesised text.](img/077434d894c2c0d63f64ca7ab5511506.png)

在没有 CSS 的情况下查看相同的工具提示，以带括号的文本显示解释。

以下是该示例的 HTML:

```
<blockquote>
  <p>
    Assistive technologies can derive information
    from their attributes and text; for example, a
    dynamic menu would be made using links organised
    into nested lists, in which the menu levels are
    denoted by the hierarchy, and by the use of
    <span id="context">
      <a href="http://www.maxdesign.com.au/2006/01/17/about-structural-labels/"
         title="descriptive headings used to indicate the main components of a web page, such as global site navigation, local navigation and footer information">
        structural labels
      </a>
    </span>
    around each top-level link.
  </p>
</blockquote>
```

链接周围的`<span>`提供了插入目标，所以可以直接在链接后面添加工具提示；它还为工具提示的绝对定位提供了相对上下文:

```
#context
{
  position:relative;
}
#context > span.tooltip
{
  position:absolute;
  bottom:1.7em;
  right:0;
}

#context > span.tooltip
{
  width:18em;
  padding:6px 8px;
  white-space:normal;
  border:1px solid #555;
  font:normal normal normal 0.85em/1.4 arial,sans-serif;
  text-align:right;
  background:#ffd;
  box-shadow:1px 2px 3px -1px rgba(0,0,0,0.5);
}
#context > span.tooltip > em
{
  display:block;
  padding:4px 4px 8px 4px;
  text-align:left;
  font-style:normal;
}
```

下面是该示例的 JavaScript:

```
var
infotext,
tooltip = null,
context = document.getElementById('context'),
trigger = context.getElementsByTagName('a').item(0);

trigger.addEventListener('click', function(e)
{
  if(tooltip === null)
  {
    infotext = trigger.getAttribute('title');
    trigger.removeAttribute('title');

    tooltip = document.createElement('span');
    tooltip.className = 'tooltip';

    var info = tooltip.appendChild(document.createElement('em'));
    info.appendChild(document.createTextNode(' (' + infotext + ') '));

    tooltip.appendChild(document.createTextNode('[ '));

    var more = tooltip.appendChild(document.createElement('a'));
    more.setAttribute('href', trigger.getAttribute('href'));
    more.appendChild(document.createTextNode('reference'));

    tooltip.appendChild(document.createTextNode(' | '));

    var google = tooltip.appendChild(document.createElement('a'));
    google.setAttribute('href', 'http://www.google.com/search?q=Structural+Labels');
    google.appendChild(document.createTextNode('search'));

    tooltip.appendChild(document.createTextNode(' ]'));

    tooltip = context.appendChild(tooltip);
  }
  else
  {
    trigger.setAttribute('title', infotext);

    context.removeChild(tooltip);
    tooltip = null;
  }

  e.preventDefault();
}, false);
```

`preventDefault()`在这种情况下用于当点击链接触发(或删除)工具提示时，阻止链接被跟踪。这就是为什么工具提示还包括原始的引用链接，所以与静态标记相比，没有内容损失。

## 控制基于时间的活动

JavaScript 最常见的基于时间的活动是内容动画。确保动画有一个暂停按钮是很重要的，理想情况下，这是一种查看全部内容的方式。例如，滚动新闻滚动条可以这样构建:

```
<div id="ticker">
  <ol>
    <li><a href="...">This is the first news item</a></li>
    <li><a href="...">This is the second news item</a></li>
  </ol>
</div>
<div id="buttons">
  <button id="pause-button" type="button">Pause</button>
  <button id="expand-button" type="button">Expand</button>
</div>
```

这个标记是一个*排序的*列表，因为新闻条目通常是按时间排序的，最新的标题在最上面。该示例中的 CSS 如下所示:

```
#buttons
{
  display:none;
}
#buttons.script-enabled
{
  display:block;
}

#ticker.script-enabled,
#ticker.script-enabled > ol
{
  position:relative;
}

#ticker
{
  white-space:nowrap;
  overflow:hidden;
  margin:5px 0 0 0;
  padding:5px 10px;
  border:2px solid #555;
  background:#f2f2f2;
}
#ticker.script-enabled > ol,
#ticker.script-enabled > ol > li
{
  margin:0;
  padding:0;
  list-style-type:none;
}
#ticker.script-enabled > ol > li
{
  display:inline-block;
  margin-right:20px;
}

#ticker.script-enabled.expanded,
#ticker.script-enabled.expanded > ol
{
  position:static;
}
#ticker.script-enabled.expanded
{
  white-space:normal;
  overflow:hidden;
}
```

而下面的 JavaScript 将这一切集合在一起:

```
var
container = document.getElementById('ticker'),
list = container.getElementsByTagName('ol').item(0),
buttons = document.getElementById('buttons'),
pauser = document.getElementById('pause-button'),
expander = document.getElementById('expand-button');

buttons.className = 'script-enabled';
container.className = 'script-enabled';

var scrollwidth = 0;
var items = list.getElementsByTagName('li');
for(var i = 0; i < items.length; i ++)
{
  scrollwidth += items[i].offsetWidth + 20;
}

var scrollstart = container.offsetWidth;
list.style.left = scrollstart + 'px';

var
timer = null,
moving = false,
scrolloffset = scrollstart;

function pause()
{
  moving = false;
  window.clearInterval(timer);
  timer = null;
}

function resume()
{
  moving = true;
  timer = window.setInterval(function()
  {
    scrolloffset -= 5;
    if(scrolloffset < (0 - scrollwidth))
    {
      scrolloffset = scrollstart;
    }
    list.style.left = scrolloffset + 'px';
  }, 100);
}

pauser.addEventListener('click', function()
{
  if(moving)
  {
    pause();
    pauser.firstChild.nodeValue = 'Resume';
  }
  else
  {
    resume();
    pauser.firstChild.nodeValue = 'Pause';
  }
}, false);

expander.addEventListener('click', function()
{
  pause();
  container.className = 'expanded';
  pauser.parentNode.removeChild(pauser);
  expander.parentNode.removeChild(expander);
}, false);

resume();
```

需要注意的重要一点是，`buttons`容器在默认情况下是隐藏的，只有在添加了`script-enabled`类时才会显示。这是为了在没有 JavaScript 的情况下查看页面时，用户不会看到什么都不做的按钮。

类似地，应用`overflow`和其他将列表转换成水平滚动条的属性的规则，只有在添加了`script-enabled`类时才生效。这是必要的，因为默认情况下，这些样式掩盖了大部分内容，所以我们需要确保除非有脚本，否则不会发生这种情况。

`Pause`按钮停止滚动，以便您可以在自己的时间内阅读每个项目，然后切换到`Resume`按钮，以便您可以重新开始。`Expand`按钮也停止了滚动，但随后添加了一个`expanded`类，它覆盖了`overflow`和其他布局样式。这使得内容变回一个静态的链接列表。

## 结论

这是实用 JavaScript 可访问性的旋风之旅！但是如果有一件事我想让你带走，那就是 **JavaScript 可访问性并不难**。只需要稍微注意一下三个核心原则。下一次我将继续学习更高级的技术，比如漫游`tabindex`、拖放和可访问的 Ajax！

## 分享这篇文章