# jQuery DOM 遍历综述

> 原文：<https://www.sitepoint.com/comprehensive-jquery-dom-traversal/>

![Separate elements of code in hospital drips. A metaphor for DOM traversal.](img/2e1603300764949021dbb2e2143ef550.png)

DOM 遍历意味着一旦选择了 web 页面上的一个或多个元素，就可以相对于最初的选择在页面元素中移动。在此过程中，您可以用新的选择替换原始选择，也可以从中添加和减去元素。

在本文中，我们将研究 jQuery DOM 遍历的可用方法，并了解该库如何为我们提供多种方法，以便根据元素与页面中其他元素的关系轻松选择元素。

## 过滤元件

让我们先来看看如何筛选出更具体的内容。您可以根据许多条件来过滤元素，比如它们相对于其他元素的位置，以及它们是否有特定的类。大多数情况下，最终选择的元素会比开始时少。

以下是不同过滤方法的列表:

*   [eq](http://api.jquery.com/eq/) —该方法将匹配元素的集合减少到位于您指定的索引处的元素。索引是从零开始的。因此，要选择第一个元素，您必须使用`$("selector").eq(0)`。从版本 1.4 开始，您可以提供一个负整数来从末尾而不是开头开始计算元素。

*   [第一个](http://api.jquery.com/first/)和[最后一个](http://api.jquery.com/last/)—`first`方法将只返回匹配元素集中的第一个元素，而`last`将返回匹配元素集中的最后一个元素。这两种方法都不接受任何参数。

*   [slice](http://api.jquery.com/slice/) —如果你正在寻找一个集合中所有元素的索引在给定的范围内，你可以使用`slice()`。该方法接受两个参数。第一个参数指定方法应该开始切片的起始索引，第二个参数指定选择应该结束的索引。第二个参数是可选的，如果省略，将选择索引大于或等于`start`的所有元素。

    参见 [CodePen](http://codepen.io) 上 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )的笔 [eq 和切片方法](http://codepen.io/SitePoint/pen/EZOrNV/)。

*   [filter](http://api.jquery.com/filter/) —该方法会将您的元素集减少到那些匹配选择器或通过您在传递给该方法的函数中设置的标准的元素。下面是这种使用选择器的方法的一个例子:

    ```
    $("li").filter(":even").css( "font-weight", "bold" ); 
    ```

    您也可以使用函数选择相同的元素:

    ```
    $("li")
    .filter(function( index ) {
       return index % 2 === 0;
    })
    .css( "font-weight", "bold" ); 
    ```

    您还可以使用该功能执行更复杂的选择，例如:

    ```
    .filter(function( index ) {
     return $( "span", this ).length >= 2;
    }) 
    ```

    这将只选择至少有两个`span`标签的元素。

    参见 [CodePen](http://codepen.io) 上 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )的笔[过滤方法](http://codepen.io/SitePoint/pen/JEexEK/)。

*   [map](http://api.jquery.com/map/) —您可以使用此方法通过函数传递当前选择中的每个元素，最终创建一个包含返回值的新 jQuery 对象。返回的 jQuery 对象本身包含一个数组，您可以对它使用`get`方法来处理一个基本数组。

    在 [CodePen](http://codepen.io) 上通过 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )查看笔[映射方法](http://codepen.io/SitePoint/pen/JEexJR/)。

## 穿越大教堂

考虑这样一个场景，您知道可以用来访问各种元素的选择器，但是您需要使用所有这些元素的父元素。此外，父类没有所有父类共有的特定类或标签。它们唯一的共同点是它们都是你可以访问的元素的父元素。我不止一次面临类似的情况。

在这种情况下，jQuery 提供了许多有用的方法来访问父母、子女或兄弟姐妹。让我们一个一个地看一下:

*   [children](http://api.jquery.com/children/) —这个方法允许我们获得元素集合中每个元素的子元素。这些孩子可以选择性地被选择器过滤。

*   [find](http://api.jquery.com/find/) —该方法将获得由选择器或元素过滤的匹配元素集中每个元素的所有后代。在这种情况下，传递给`find()`的选择器参数不是可选的。如果想获得所有的后代，可以将通用选择器(`'*'`)作为参数传递给这个方法。

    正如你在演示中看到的，`children()`只给段落的直接子元素加下划线，但是`find()`用一个匹配的选择器给它的所有子元素添加了背景。

    [演示](http://codepen.io/SitePoint/pen/YNRBQx)

*   [parent](http://api.jquery.com/parent/) —该方法将获取当前集合中每个元素的父元素。可以使用选择器选择性地过滤父代。

*   [parents](http://api.jquery.com/parents/) —这个方法将获得集合中每个元素的所有祖先。它还接受一个可选的选择器参数来过滤祖先。`parent()`和`parents()`的区别在于，前者只遍历 DOM 树中的一个层次，而`parents()`则一直遍历到文档的根。

*   [最近的](http://api.jquery.com/closest/) —该方法将通过测试元素本身，然后在 DOM 树中向上遍历，获得与给定选择器匹配的第一个元素。`parents()`和`closest()`有两个显著的区别。`parents()`从元素的父元素开始遍历，`closest()`从元素本身开始遍历。另一个区别是，`closest()`只会遍历 DOM 树直到找到匹配，而`parents()`会继续向上移动直到到达文档的根元素。

    请考虑下面演示中的两行代码:

    ```
    $("i").closest("span").css("background", "yellow");
    $("b").parent().css("color","blue"); 
    ```

    现在请注意，最后一段中的单词“italic”没有任何带有`<span>`标签的祖先。因此，它的背景没有改变。

    类似地，第二行将包围粗体字的`<span>`标记的颜色改为蓝色。现在，在最后一个例子中，整个段落是`<b>`标签的父项，因此它全部变成蓝色。

    [演示](http://codepen.io/SitePoint/pen/JEexyb)

*   [兄弟](http://api.jquery.com/siblings/) —该方法获取匹配元素集中每个元素的兄弟。您可以选择提供一个选择器作为参数，只获取具有匹配选择器的同级。

*   [prev](http://api.jquery.com/prev/) —这个方法将获得集合中每个元素的前一个兄弟元素。如果您提供了选择器，则该方法将只选择与该选择器匹配的元素。

*   [prevAll](http://api.jquery.com/prevAll/) —这个方法将获得我们集合中每个元素的所有前面的兄弟元素。就像其他方法一样，您可以提供一个选择器来过滤返回的元素。

*   [next](http://api.jquery.com/next/) —该方法将只获取匹配元素的下一个同级元素。如果提供了选择器，它将仅在匹配时获取同级。

*   [nextAll](http://api.jquery.com/nextAll/) —这个方法将获得我们的集合中元素的所有兄弟元素。可以通过提供选择器来选择性地过滤兄弟。

    考虑演示中的这两行代码:

    ```
    $("selector").next(".nextall").css("color","orange");  
    $("selector").nextAll(".nextall").css("color","red"); 
    ```

    您将看到列表中没有一个项目是橙色的。这是因为我们的引用项的下一个直接兄弟没有`nextall`类。

    通过 [CodePen](http://codepen.io) 上的 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )查看 jQuery 中的笔 [next、nextAll、previous 和 previousAll。](http://codepen.io/SitePoint/pen/VPVgzz/)

    我想再一次提到，当你给`next()`和`prev()`提供一个选择器时，它们不会寻找所有的下一个和上一个兄弟来找到匹配给定选择器的第一个。它们将只查看紧接在前面和后面的兄弟，如果这些兄弟没有匹配的选择器，将返回一个空的 jQuery 对象。

## 更多与 DOM 遍历相关的方法

在遍历 DOM 时，可能会遇到这样的情况:需要向选择中添加更多与原始集合无关的元素，或者需要恢复到之前的元素集合。jQuery 提供了一些函数，可以用来执行所有这些任务。

*   [add](http://api.jquery.com/add/) —这个方法将创建一个新的 jQuery 对象，它将包含我们添加到现有元素列表中的新元素。请记住，不能保证新元素会按照传递给`add`方法的顺序被添加到现有的集合中。

*   [addBack](http://api.jquery.com/addBack/) — jQuery 维护一个内部堆栈，用于跟踪元素集的变化。调用任何一个遍历方法都会在堆栈上推出一组新的元素。如果你想同时使用先前的和新的元素，你可以使用`addBack`方法。

    对于`addBack`部分下的第一种情况，我们首先选择段落，然后调用`children()`。在第二种情况下，在调用`children()`之后，我们也调用`addBack()`。调用`addBack()`将第二段添加到我们的选择中，然后我们给它加上红色边框。

    通过 [CodePen](http://codepen.io) 上的 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )查看 jQuery 中的笔 [add 和 addBack。](http://codepen.io/SitePoint/pen/RKqvZx/)

*   [end](http://api.jquery.com/end/) —该方法将结束最近的过滤操作，并将您的元素集返回到其先前的状态。当您想要操作与当前元素集相关的一些元素，恢复到原始元素集，然后操作不同的元素集时，它会很有用。

    在下面的演示中，我首先使用`.find("b")`来选择所有的`<b>`标签，然后一旦我将它们的颜色改为绿色，我就调用`.end()`。这个调用将我们返回到之前的选择，我再次选择所有的`<i>`标签。

    [演示](http://codepen.io/SitePoint/pen/OWadjw)

*   [contents](http://api.jquery.com/contents/) —如果你想得到集合中所有元素的所有子元素，包括文本和注释节点，你可以使用`contents`方法。如果`<iframe>`与您的 web 页面在同一个域中，您也可以使用这个方法来获取`<iframe>`的内容。

*   [而非](http://api.jquery.com/not/) —如果您有一大组元素，并且只想要那些*与给定选择器*不匹配的元素的子集，您可以使用`not()`。从版本 1.4 开始，该方法还可以将函数作为参数，根据特定条件测试每个元素。任何符合这些条件的元素都将被排除在筛选集之外。在下面的演示中，我使用`.not(".not-selected")`为所有没有`not-selected`类的元素添加了一个边框。同样，在下一组 div 中，我也已经将所有高度不大于 120px 的元素的颜色设置为紫色。

    见笔[。在 jQuery](http://codepen.io/SitePoint/pen/jyQdLR/) 中 not()by site point([@ site point](http://codepen.io/SitePoint))on[code pen](http://codepen.io)。