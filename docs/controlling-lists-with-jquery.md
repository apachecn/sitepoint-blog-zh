# 用 jQuery 控制列表

> 原文：<https://www.sitepoint.com/controlling-lists-with-jquery/>

本文摘自厄尔·卡斯尔丁&克雷格·沙奇的《jQuery:忍者新手》。更多详情见下文。

列表是后基于表格的网络布局时代真正的无名英雄。随着设计者从专制的表格单元的约束中解放出来，他们开始寻找其他(语义正确的)方法来重新创建常见的用户界面元素，如菜单、导航面板、标签云等等。

一次又一次，当冗余的布局从底层数据中剥离出来时，留下的只是一个列表！

星象跟踪仪！site 已经有了大量的列表:它们构成了我们标签、折叠、菜单、图片库等等的基础——但是我们还可以做更多的事情来扩充和增强这个简单的列表。

**jQuery UI 可选项目**

用户生成内容的海洋对我们的客户来说是一个难题。数以千计的标签从网站用户那里涌入——但现在法律部门表示，作为经理，他必须手动批准每一个标签，以避免最近令人讨厌的诉讼重演。

因为网站采用了不受约束的标签系统，所以列表中会有大量重复的标签——对于当前的系统，这意味着大量的额外管理。客户真正想要的是一种轻松查看标签、选择标签(以及任何重复的标签)并点击按钮批准或拒绝标签的方式。

我们的攻击计划是将 jQuery UI 的可选行为添加到我们的列表中。使元素可选使用户能够套索元素的任何子元素来选择它们:如果单击一个元素，然后拖动到后续元素上，它们会高亮显示。然后，您可以按照自己认为合适的方式处理选择。完美的管理无聊的列表！我们要创建的行为如下图所示。

![Selectable List Items](img/82e1358dc959a1fe0518c3df5ae8429b.png)

除了套索之外，可选行为还允许您使用 **Ctrl** 键向列表中添加非连续的项目(就像您在大多数桌面应用程序中可以做的那样)，甚至可以使用键盘导航选择。

**让你的用户参与进来**

虽然能够在列表上点击和拖动选项是非常酷和非常有用的，但是只有你的用户知道它，它才是酷和有用的！以这种方式进行选择是一种不标准的网络交互形式，所以你需要向用户提供指导，教他们如何使用你的新功能。

让我们来看看标记。服务器抛出一长串标签，这是我们选择器网格的良好基础。我们还将提供几个按钮，允许用户批准或拒绝他们选择的标签，以及一个重置选择的按钮:

```
<ul id="tags">

  <li>bad singer</li>

  <li>old</li>

  <li>plastic surgery</li>

  <li>broke</li>

  ⋮

</ul>

<button id="approve">Approve</button>

<button id="reject">Reject</button>

<button id="clear">Clear</button>
```

一个又大又长的列表有点吓人，所以我们将使用一些基本的 CSS 将列表制作成网格，并将每个标签转换成一个小盒子。网格准备就绪后，我们必须将 jQuery UI 库添加到页面中。现在是时候告诉标签列表变得可选了:

```
$("#tags").selectable();
```

打开你的浏览器，检查一下。嗯……真的发生了什么事吗？是的，它有，但它是看不见的！`selectable`方法的工作原理是将`class`属性添加到选中的项目中，除非我们将样式分配给这些类，否则我们将看不到任何事情发生。如果您在选择列表项时用 Firebug 检查它们，您会看到发生了变化。让我们尝试一下选定元素的样式:

```
#tags .ui-selecting {

  background: #FEFF9F;

}

#tags .ui-selected {

  background-color:#eEeF8F;

}
```

当用户在选择元素的过程中应用`ui-selecting class`,当他们停止时添加`ui-selected class`。如果你现在试一下，你会发现你可以套住一些方块。这是一种非常自然的交互——这正是您希望从页面组件中得到的。您也可以在按住 **Ctrl** 键的同时点击选择单个项目。

我们要做的下一个任务是帮助复制标签。在标签系统中，每个术语的标签数量非常重要——因此，我们将编写一些代码来选择任何与用户选择相匹配的标签，而不仅仅是删除重复的标签。例如，如果他们点击“A-lister”，所有的“A-lister”标签都将高亮显示。

我们需要知道我们可以从 jQuery UI 组件挂接哪些事件。查阅[、](http://docs.jquery.com/UI/Selectable)文档，我们发现可以捕捉到`start`、`stop`、`selecting`、`unselecting`、`selected`和`unselected`事件。我们可以捕获`selecting`事件——并在用户移动鼠标时删除重复的事件——但这可能会有点混乱。我们将坚持使用 stop 事件，它在用户完成选择后立即触发:

```
$('#tags').selectable({

  stop: function() {

     // The user stopped selecting!

  }

});
```

现在我们可以开始寻找重复的标签了。我们的一般方法是列出用户选择的所有标签，然后在更大的标签列表中搜索这些标签的任何副本:

```
var names = $.map($('.ui-selected, this'), function(element, i) {

  return $(element).text();

});

$('li', this)

  .filter(function() {

    if ($.inArray($(this).text(), names) != -1) {

      return true;

    } else {

      return false;

    };

  })

  .addClass('ui-selected');
```

为了找到重复项，我们已经调用了各种新的 jQuery 特性，所以请抓紧时间！

这其中第一个最奇怪:`$('.ui-selected', this)`。这看起来像一个常规的 jQuery 选择器，但是还有第二个参数。事实证明，jQuery 选择器的完整定义实际上是`$(expression, context)`——我们只是省略了第二个参数。`context`定义了 jQuery 应该在哪里寻找选择器；默认情况下，它会查看页面上的任何地方——但是通过将无序列表指定为上下文，表达式将被限制在列表中的元素。

`$.map` **和** `$.inArray`

接下来，我们使用几个 jQuery 实用程序方法:`$.map`和`$.inArray`来处理列表项。jQuery 提供的实用方法主要用于处理 JavaScript 数组——这就是我们在这里所做的。首先我们创建一个名为`names`的数组，我们使用`$.map`方法填充它。

`$.map`方法允许您获取数组中的每个元素，以某种方式处理它，并将结果作为一个新数组返回。当你想以同样的方式变换每个元素时，你可以使用它。我们希望将 jQuery 选择转换成一个简单的标记文本列表——因此我们传递选择，并定义一个匿名函数来返回每个元素的文本。嘿，很快:一个标签文本数组！

接下来，我们像以前一样使用上下文技巧来检索所有列表项元素，并根据它们是否重复来过滤它们。我们的`filter`函数使用`$.inArray`实用程序方法，该方法搜索一个数组(但只搜索普通的 JavaScript 数组——不幸的是，不搜索 jQuery 选择)中的指定值。给定一个数组和一个搜索词，比如`$.inArray(value, array)`，它将返回数组中值的索引。有益的是，如果数组中的值是*而不是*，它将返回`-1`。记住`filter`期望我们返回`true`或`false`——所以我们只是检查`$.inArray`是否返回`-1`，并根据情况返回`true`或`false`。以这种方式使用`filter`允许我们在标签文本数组中搜索每个列表项的文本——如果在那里，那就是重复的，所以我们将它返回给过滤器进行选择。

**访问数据**

既然我们可以做出选择，我们如何使用它们呢？jQuery UI 可选组件使用类名，因此我们也将使用类名。要获取所选值的列表，我们只需搜索任何带有`ui-selected class`的项目:

```
$('#approve').click(function() {

  $('#tags')

    .find('.ui-selected')

    .addClass('approve')

    .removeClass('ui-selected reject');

});

$('#reject').click(function() {

  $('#tags')

    .find('.ui-selected')

    .addClass('reject')

    .removeClass('ui-selected approve');

});

$('#clear').click(function() {

  $('#tags')

    .find('li')

    .removeClass('ui-selected approve reject');

  $('#approved').val('');

});
```

我们只是在用户点击我们的按钮时添加了一个`approve`或`reject class`——也一定要删除`ui-selected class`,因为我们希望批准的标签与选择的标签有所不同。

但是如果我们想，比如说，把这些信息发送给服务器呢？也许将批准的标签列表存储在一个隐藏的表单字段中会比较好，这样服务器就可以访问它进行处理。让我们更新`#approve` click 处理程序来遍历已批准的项目，并以简单的管道分隔格式将每个项目的索引附加到一个隐藏字段:

```
$('#approve').click(function() {

  var approvedItems = "";

  $('#tags')

    .find('.ui-selected')

    .addClass('approve')

    .removeClass('ui-selected reject')

    .each(function() {

      approvedItems += $(this).index() + "|";

    });

  $('#approved').val(approvedItems);

});
```

我们还将在`#clear`按钮点击处理程序中添加一行来清除输入的值:

```
$('#approved').val('');
```

感谢`index`方法，我们现在知道列表中的哪些项目已经被批准。index 会告诉你一个条目在它的父元素中的位置。我们的控件在易用性方面令人印象深刻。jQuery UI `selectable`行为在幕后做了大量工作，允许列表可选——但最终结果是一个自然的组件，这正是我们想要的。

**排序列表**

标签系统得到控制后，是时候看看散布在管理部分的其他一些列表了。这些列表中的许多是由服务器按照它们被输入到系统中的顺序来填充的。这有利于查看新内容，但不利于查找特定项目。我们的客户要求我们在管理部分的所有列表中建立一些排序功能，所以他可以单击一个按钮，让列表按字母顺序升序或降序排序。

我们将要处理的标记是一个由链接组成的简单无序列表:

```
<ul class="sortable">

<li><a href="#">Beau Dandy</a></li>

  <li><a href="#">Glendatronix</a></li>

  <li><a href="#">BMX Spandex Corporation</a></li>

  <li><a href="#">Maxwell Zilog</a></li>

  <li><a href="#">Computadors</a>

</ul>
```

jQuery 对象缺乏任何内置的排序功能。毕竟，这是有道理的；一个选择可能包括位于页面不同部分的不同种类的元素，因此不可能以一致的方式对它们进行排序。因此，为了对 jQuery 选择进行排序，我们需要依靠一些 JavaScript 数组方法。jQuery 选择实际上不是数组，但它们是“类似数组的”，它们允许我们对它们使用 JavaScript `sort`函数。

我们将尝试构建一个可重用的列表排序小部件。我们称之为`SORTER`，我们将调用`SORTER.sort(list)`对列表进行升序排序，调用`SORTER.sort(list, 'desc')`进行降序排序。我们假设传入的选择器将匹配有序或无序列表，但是让我们看看我们是否能做到这一点:

```
var SORTER = {};

SORTER.sort = function(which, dir) {

  SORTER.dir = (dir == "desc") ? -1 : 1;

  $(which).each(function() {

    // Find the list items and sort them

    var sorted = $(this).find("> li").sort(function(a, b) {

      return $(a).text().toLowerCase() > $(b).text().toLowerCase() ?

        SORTER.dir : -SORTER.dir;

    });

    $(this).append(sorted);

  });

};
```

这段代码看起来很短，因为它碰巧做了很多事情！首先，我们检查`desc`是否作为`dir`参数传入，并相应地设置`SORTER.dir`变量。我们需要做的就是获取所有一级子列表元素，并对它们进行排序。我们只想要第一级项目；如果我们获取更高的级别，它们将被排序并被拖到父级别。因为调用`sort`会将我们的选择还原为原始 JavaScript，所以我们需要在`$()`中重新包装它们，以便能够调用 jQuery `text`方法并比较它们的值。我们还将值转换成小写——这使得排序不区分大小写。

**`sort`**功能****

 **`sort`函数是普通的老式 JavaScript:它根据传递给它的函数的结果对数组进行排序。会检查数组的内容，并将它们成对传递给你的函数。如果你的函数返回`1`，`sort`将会交换物品并首先放置第二个。如果你的函数返回`-1`，JavaScript 会把第一项放在最前面。最后，如果您的函数返回`0`，`sort`将认为两个项目相等，不会进行排序。

我们做了一点小魔术，让我们使用相同的函数进行升序和降序排序:我们已经将我们的`SORTER.dir`变量设置为`-1`或`1`，这取决于方向。然后在`sort`比较函数中，我们做进一步的计算:如果`a`小于`b`，我们返回`-SORTER.dir`。如果方向以`-1`的形式传入，我们将其处理为`-(-1)`，也就是`1`——因此，如果我们尝试降序排序，返回值将被交换。

一旦我们对项目进行了排序，我们就可以按照正确的顺序将它们重新插入到列表中。记住，`append`函数首先删除元素——所以它删除项目并把它附加到正确的位置。

为了进行测试，我们将在 HTML 中添加一些按钮，并从它们的`click`事件处理程序中调用`SORTER.sort`:

```
$('#ascending').click(function() {

  SORTER.sort('.sortable');

});

$('#descending').click(function() {

  SORTER.sort('.sortable', 'desc');

});
```

**操纵选择框列表**

在这里，我们将检查`select`元素，尤其是那些带有`multiple="multiple"`的元素(即，显示为可选择项目列表的选择框)。

**交换列表元素**

星象跟踪仪！客户要求我们改进管理功能，将名人分配到 A-list。当前的功能由两个 select 元素组成:一个包含 A-list 名人，另一个包含系统中的所有其他名人。但是受欢迎的世界是极其变化无常的——今天的一线明星明天可能会成为无名小卒。因此，客户希望能够轻松地在每个列表之间交换名人。我们将在界面中添加一些控件让他这样做，如下所示:

![List Boxes with Controls](img/044f407a5946dae0e855d0e46d63a0d7.png)

这是我们正在处理的 HTML，由两个`select`元素和几个用于执行各种操作的按钮组成:

```
<select id="candidates" multiple="multiple" size="8">

  <option value="142">Beau Dandy</option>

  ⋮

</select>

<select multiple="multiple" size="8">

  <option value="232">Johnny Stardust</option>

  ⋮

</select>

<div>

  <input type="button" value="&gt;" />

  <input type="button" value="&lt;" />

  ⋮

</div>
```

如上所述，客户端希望能够将选定的项目从一个列表交换到另一个列表。我们将创建一个包含我们将构建的所有功能的`SWAPLIST`对象。我们需要使用`select`元素时，可以随时重复使用:

```
var SWAPLIST = {};

SWAPLIST.swap = function(from, to) {

  $(from)

    .find(':selected')

    .appendTo(to);

}
```

我们已经定义了一个`swap`函数，它接受针对两个列表的选择器字符串:一个源列表和一个目的列表。我们要做的第一个任务是获取当前选中的任何项目。我们可以使用带有`:selected`表单过滤器的`find`动作来做到这一点。这个过滤器将返回任何设置了属性`selected`的表单元素。然后我们可以用`appendTo`将选择移动到目的地列表。轻松点。一旦我们定义了这个功能，我们就可以通过从适当的点击处理程序中调用我们的交换方法来将它应用于任何两个列表:

```
$('#swapLeft').click(function() {

  SWAPLIST.swap('#candidates', '#a-listers');

});

$('#swapRight').click(function() {

  SWAPLIST.swap('#a-listers', '#candidates');

});
```

现在选中的物品可以随意来回交换！让我们给我们的`SWAPLIST`对象添加更多的功能。交换所有的 T2 元素怎么样？这就更简单了:

```
SWAPLIST.swapAll = function(from,to) {

  $(from)

    .children()

    .appendTo(to);

}
```

我们只是将所有的子元素(而不仅仅是选中的元素)添加到目标列表的底部——整个列表从源列表跳转到目标列表。

**反转选择**

下一个客户请求是添加一个反转当前选择的按钮，使他的员工在处理大量选择时更容易。单击此链接时，目标列表中所有当前选定的项目都会被取消选定，反之亦然。让我们在`SWAPLIST`对象中创建一个函数来完成这个任务:

```
SWAPLIST.invert = function(list) {

  $(list)

    .children()

    .attr('selected', function(i, selected) {

      return !selected;

    });

}
```

我们所要做的就是检索每个列表项并交换它的`selected`属性。我们使用`attr`函数将列表项设置为`!$(this).attr('selected')`。JavaScript NOT ( `!`)运算符(感叹号)反转布尔值，所以如果值是`true`，它就变成`false`，如果值是`false`，它就变成`true`！

**用函数参数**调用 `attr`

 **这是一个很棒的技巧:我们已经使用了`attr`动作——但它不是我们习惯的版本。以前我们使用`attr(key, value)`动作将属性设置为静态值，但是`attr`也让我们传入一个函数来确定值。该函数将被传递两个参数:元素的索引及其当前值。函数的返回值成为属性的新值。对于我们的`invert`方法，我们返回元素当前选择值的相反值——因此每个元素都被切换。我们可以用一堆命令来做这种动态处理:`text`、`html`、`val`、`addClass`、`wrap` …等等！

**搜索列表**

在不得不听客户没完没了地抱怨找到他想要选择的名人有多难之后，你决定免费赠送一个小东西:一个快速搜索功能，让他键入一些字符并自动选择任何匹配的元素:

```
SWAPLIST.search = function(list, search) {

  $(list)

    .children()

    .attr('selected', '')

    .filter(function() {

      if (search == '') {

        return false;

      }

      return $(this)

        .text()

        .toLowerCase()

        .indexOf(search) > - 1

    })

    .attr('selected', 'selected');

}
```

这是怎么回事？首先，我们抓取所有列表项，然后清除任何先前的选择(通过将`selected`设置为空字符串)。接下来，我们使用`filter`动作来查找我们正在搜索的任何元素。

`filter`动作接受一个函数作为参数，并对选择中的每个 jQuery 对象运行该函数。如果函数返回`true`，元素将保留在选择中。但是如果函数返回`false`——它就从选择中消失了，不会受到进一步处理的影响。

为了找到我们关心的元素，我们检查它们包含的文本中是否有我们正在寻找的文本。为了做到这一点，我们使用了文本动作，它给出了一个字符串。我们将其转换为小写(这样搜索就不区分大小写了)，并检查源文本是否位于元素字符串中。JavaScript `indexOf`方法将找到一个字符串在另一个字符串中的位置；例如，`"hello".indexOf('ll');`将返回`2`(和往常一样，索引从`0`开始)。如果没有找到子串，`indexOf`将返回`-1`，这就是我们在这里要检查的。

在运行`filter`函数之后，jQuery 选择中剩下的任何元素都必须包含我们正在寻找的关键字——所以我们再次使用`attr`方法来选择它们。

为了使用我们创建的搜索方法，我们可以将它附加到一个`click`处理程序——这样用户键入一个单词，然后单击一个搜索按钮。更好的方法是给`input`本身附加一个 keyup 处理程序，这样它就可以在您输入时选择:

```
$('#search').keyup(function() {

  SWAPLIST.search("#a-listers, #candidates", $(this).val());

});
```

**note:**Want more?

在 Earle castle dine&Craig Sharkie 的《jQuery:忍者新手》上查看这本书并在线购买。你也可以下载该书的[代码档案](https://www.sitepoint.com/books/jquery1/code.php)，查看[的更新和勘误表](https://www.sitepoint.com/books/jquery1/errata.php)，或者在 [SitePoint 论坛](https://www.sitepoint.com/forums/)上咨询蓬勃发展的 JavaScript 和 jQuery 开发者社区。**** 

## ****分享这篇文章****