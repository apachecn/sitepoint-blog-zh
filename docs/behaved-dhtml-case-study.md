# 行为良好的 DHTML:案例研究

> 原文：<https://www.sitepoint.com/behaved-dhtml-case-study/>

众所周知，在过去的几年里，DHTML 几乎完全被用于邪恶的目的。用户将这项技术与侵入性广告和容易出错的页面联系在一起，而开发者则将其与浏览器检测和可怕的黑客攻击联系在一起。

这个假设是不幸的。浏览器技术在过去几年取得了巨大的进步。如果做得好，DHTML 可以改善许多网页的用户体验。过去需要的黑客技术现在几乎不存在了。

当使用现代的 DHTML 时，我经常回忆起 Web 开发的旧时光，那时我第一次对这项技术感兴趣。尽管我的初衷是好的，但我的第一批脚本现在代表了当今 DHTML 编码人员应该避免的例子——它们过于依赖特定的浏览器功能，当这些需求没有得到满足时，就会抛出错误或不正确地降级。它们不能很好地与同一页面上的其他脚本一起工作，并且它们有时会依赖其他技术。

当我遇到这样的脚本时，我认为它们表现很差。他们有成为好人的潜力；所有的工具都在那里。他们只是没有做他们应该做的事情。

随着我作为一名 Web 开发人员的成长，我开始意识到行为良好的 DHTML 的价值。我总是可以说服客户，这个脚本在任何浏览器中都可以工作，也可以不工作。他们并不总是欣赏显而易见的一般化，比如现在几乎每个人都支持 DHTML，就像代码在不受支持的情况下降级一样。

我还注意到，在开发这种 DHTML 时，我倾向于一遍又一遍地遵循同样的五步过程。下面是一个使用这个过程创建一个非常基本的 DHTML 脚本的例子。一旦你理解了这些原则，你就可以将这个过程应用到大多数 DHTML 效果中，并得到令人印象深刻的结果。

这里提供的代码示例假设您对 JavaScript、HTML 和 DOM 有一定的了解。然而，任何 Web 开发人员或设计人员都应该能够从熟悉这个过程中获得一些价值。

##### 标签脚本

DHTML 在 Web 上的一个常见用途是创建我称之为动态标签的东西。动态标签用于标记表单字段。但是，标签的文本呈现在表单域内部，而不是在表单域附近(这种情况更常见)。

当表单域受到关注时，标签会消失，以便用户可以键入。如果用户没有键入任何内容，只要用户单击或离开该字段，标签就会恢复。

动态标签节省空间，看起来很清晰，感觉光滑。在适当的情况下，它们可以是对基本表单标签的一个很好的改进。

一个天真的开发人员可能会实现这样一个动态标签脚本:

```
 <input type="text" name="username" value="username"  

  onfocus="if (this.value == 'username') this.value = '';"  

  onblur="if (this.value == '') this.value = 'username';" />
```

[示例 A](https://www.sitepoint.com/examples/wellbehaveddhtml/ExampleA.html) 展示了这种类型的实现。

这是有效的第一步，但仅此而已。像这样的 DHTML 是过去设计糟糕的脚本的一个例子，不应该出现在任何生产网站上。

让我们一个一个来看问题:

1.  依赖于 JavaScript

如果 JavaScript 被禁用，该效果不起作用。在这种情况下，标签实际上仍然会出现，因为它被硬编码到字段的值属性中。但是，当用户聚焦表单时，什么也没有发生。用户体验被严重破坏了——可能比字段旁边有一个普通的文本标签更糟糕。

*   耦合到处理表单的代码

耦合是编程界使用的一个术语，用来表示两个组件的实现紧密联系在一起——通常是一件非常糟糕的事情。耦合意味着当一个组件的代码改变时，另一个组件的代码可能也必须改变。

在我们的例子中，创建我们的效果的 JavaScript 与处理表单的服务器代码紧密耦合。服务器代码必须知道每个表单字段的标签是什么，并且能够将它们从表单提交中过滤出来。这是因为我们将标签的文本放在了每个字段的 value 属性中。如果用户没有在这些字段中的一个(或任何一个)输入任何内容，标签实际上会被提交。

要查看实际操作的例子，只需点击 Submit，无需在 Example A 中输入任何内容。

*   独占绑定到事件处理程序

新手 DHTML 脚本的一个常见问题是它们直接设置元素的事件属性值。您可以通过元素的属性来实现，或者在 JavaScript 中使用属性来实现。直接设置 JavaScript 事件通常不是一个好主意，因为只有一个代码块可以使用每个事件。如果您开始在一个页面上运行多个脚本，各个脚本的事件处理程序可能会相互覆盖。这种类型的 DHTML 更难维护，并且会导致难以调试的错误。

在现代浏览器中，我们可以使用事件侦听器将多个函数绑定到一个特定的事件。除非绝对必要，否则避免旧的事件处理方式。

*   非模块化设计

这个脚本不是模块化设计的。如果我们决定实现另一个动态标签，我们别无选择，只能将当前代码复制并粘贴到该框的事件处理程序中，并更改标签文本显示的各个位置。

如果我们在脚本中发现了一个 bug，或者想要进行修改，我们必须记住对每个标签进行修改。如果我们决定更改标签文本，我们必须在三个地方进行更改。非模块化设计的程序很难维护和开发，因为它们很容易出错。容易出错，难调试。

现在我们已经分析了第一个动态标签脚本中的问题，我们对脚本下一次迭代的目标有了一个很好的想法。简而言之，我们想要一个动态标签脚本:

*   不依赖于 JavaScript
*   不与任何其他组件耦合
*   不专门绑定到任何事件
*   模块化设计

##### 编写行为良好的 DHTML 的 5 个步骤

我们生产动态标签脚本的目标与大多数网页 DHTML 增强的目标没有什么不同。事实上，我写的几乎所有脚本都有相同的目标。

随着时间的推移，我发现几乎任何 DHTML 效果都可以遵循一个简单的过程来确保达到这些目标:

1.  确定效果的基本逻辑结构。
2.  创建一个完整的效果实例。
3.  确定所有用户代理需求。
4.  编写代码以在满足代理要求时转换逻辑结构。
5.  彻底测试每个目标平台。

##### 步骤 1:识别效果的底层逻辑结构

我们的主要目标之一是避免对 JavaScript 的任何依赖。解决这个问题的一个常见但最终有缺陷的方法是尝试检测服务器上“支持的”浏览器。如果支持浏览器，则向其发送代码的动态版本。否则，将发送一个更简单的版本。

问题是，在服务器上明确地检测浏览器的类型和版本几乎是不可能的。即使您可以，您也无法检测 JavaScript 实际上是否为特定用户启用。浏览器只是没有向服务器发送足够的信息来可靠地识别它们自己或它们的配置。

避免 JavaScript 依赖的最好方法是在不需要 DHTML 效果的简单逻辑文档结构上构建 DHTML 效果。如果支持，该效果将在客户端上动态启用。如果没有，用户将看到基本文档。

由于有了 [`label` HTML 元素](https://www.w3.org/TR/1999/REC-html401-19991224/interact/forms.html#h-17.9.1)，我们的动态标签的逻辑结构工作得很好。

label 元素在结构上将表单元素链接到它的文本标签。在大多数可视化浏览器中，使用 label 元素和任何其他元素(或者根本没有元素)之间唯一的触觉差异是，单击标签会将表单聚焦到与该标签相关联的字段上。

然而，此时我们感兴趣的只是为我们的效果构建最符合逻辑的底层结构，所以我们将使用 label 元素。[例 B](https://www.sitepoint.com/examples/wellbehaveddhtml/ExampleB.html) 展示了我们的工作。

显然，这里没有什么新奇的东西，这正是我们想要的。这一步的代码是我们效果的最小公分母视图。理想情况下，无论是在最新版本的 Mozilla 中，还是在手机上，这个文档都应该是有意义的。如果用户的浏览器没有我们的效果所需的功能，或者没有启用这些功能，用户将会看到这个文档。

##### 第二步:创建一个在最佳环境下效果的完整工作示例

一旦你有了合适的逻辑结构，接下来要做的就是修改它来创建一个完整的效果实例。不要担心脚本在这一点上会如何降级，只要让它在假设您需要的每个特性都将可用并打开的情况下工作。

看看我们在第 1 步中的工作，很容易看到我们必须为每个动态标签完成的高级任务，以显示我们的效果:

1.  隐藏常规 HTML 标签元素。
2.  将 JavaScript 函数附加到关联字段的 onfocus 和 onblur 事件，以便在适当的时候显示和隐藏标签。

完成第一项任务的最简单方法是使用如下 CSS 规则:

```
<style type="text/css">  

  label {  

    display:none;  

    }  

</style>
```

如果你不熟悉 CSS，你可以在[SitePoint.com](https://www.sitepoint.com/subcat/90)或者 [W3C](https://www.w3.org/Style/CSS/learning) 这里得到一个快速入门。

像这样一个简单的 CSS 规则的问题是，它将关闭页面上每个标签的显示。当我们想要在一个页面上使用这个规则时，我们必须修改它，这个页面上有我们想要以常规方式显示的标签元素，而没有效果。这根本不是一个非常模块化的设计。

当然，解决方案是给我们想要动态表现的标签一个特殊的类:

```
<style type="text/css">   

  label.dynamic {   

    display:none;   

    }   

</style>
```

第二个任务基本上要求我们遍历页面上的所有标签元素，检查它们是否有正确的类，如果有，就向它们的关联字段添加事件处理程序。我们还应该在字段的属性中保存标签文本的副本，以便于访问，并在这里初始化标签显示。

这需要一些文档对象模型的知识。如果你对细节生疏了，或者从来没有花时间去学习，你可以在 W3C 上温习一下。浏览器厂商通常也有很好的资源(例如[微软](https://web.archive.org/web/20030810221100/http://msdn.microsoft.com:80/workshop/author/dhtml/reference/dhtml_reference_entry.asp)和 [Mozilla](http://www.mozilla.org/docs/dom/) )，尽管这些资源明显偏向于他们自己的实现。

在一个完美的世界中，一旦我们了解了 DOM 是如何工作的，我们就可以使用下面的代码来执行我们的任务。它使用了`getElementsByTagName`和`getElementById`方法，以及`className`属性。这些都在 [DOM 级别 1](https://www.w3.org/TR/1998/REC-DOM-Level-1-19981001/level-one-html.html) 中定义。

这段代码还使用了来自 [DOM Level 2 Events](https://www.w3.org/TR/2000/REC-DOM-Level-2-Events-20001113/events.html) 的`addEventListener`方法。

```
n setupLabels() {   

  // get all the labels on the entire page   

  var objLabels = document.getElementsByTagName("LABEL");   

  var objField;   

  for (var i = 0; i < objLabels.length; i++) {   

    // if the label is supposed to be dynamic...   

    if ("dynamicLabel" == objLabels[i].className) {   

      // get the field associated with it   

      objField = document.getElementById(objLabels[i].htmlFor);   

      // add event handlers to the onfocus and onblur events   

      objField.addEventListener("focus", focusDynamicLabel, false);   

      objField.addEventListener("blur", blurDynamicLabel, false);   

      // save a copy of the label text   

      objField._labelText = objLabels[i].firstChild.nodeValue;   

      // initialize the display of the label   

      objField.value = objField._labelText;   

    }   

  }   

}
```

然而，这些代码不能用于 IE/windows，因为它不完全兼容 DOM 它不支持 DOM Level 2 Events 模块。相反，它支持一个做同样事情的专有接口。由于 IE/windows 拥有如此庞大的用户群——我们希望看到我们的效果——我们在脚本中添加了一个小的黑客来适应它不同的对象模型(注意，更改的行是粗体的):

```
function setupLabels() {   

  // get all the labels on the entire page   

  var objLabels = document.getElementsByTagName("LABEL");   

  var objField;   

  for (var i = 0; i < objLabels.length; i++) {   

    // if the label is supposed to be dynamic...   

    if ("dynamicLabel" == objLabels[i].className) {   

      // get the field associated with it   

      objField = document.getElementById(objLabels[i].htmlFor);   

      // add event handlers to the onfocus and onblur events   

      **addEvent(objField, "focus", focusDynamicLabel);   

      addEvent(objField, "blur", blurDynamicLabel);**   

      // save a copy of the label text   

      objField._labelText = objLabels[i].firstChild.nodeValue;   

      // initialize the display of the label   

      objField.value = objField._labelText;   

    }   

  }   

}   

**function addEvent(objObject, strEventName, fnHandler) {**   

  // DOM-compliant way to add an event listener   

 **if (objObject.addEventListener)   

    objObject.addEventListener(strEventName, fnHandler, false);**   

  // IE/windows way to add an event listener   

 **else if (objObject.attachEvent)   

    objObject.attachEvent("on" + strEventName, fnHandler);   

}**
```

我们可以通过使用相同的实用函数附加到窗口的 onload 事件，让这个脚本在页面加载后运行。

```
addEvent(window, "load", setupLabels);
```

现在我们要做的就是实现`focusDynamicLabel`和`blurDynamicLabel`。这很简单——就像我们第一个动态标签脚本的原始代码一样。唯一的区别是，它应该被一般化，以便相同的函数适用于页面上的每个动态标签。

在完全兼容 DOM 的浏览器中，我们可以使用事件对象的 target 属性(也在 [DOM Level 2 Events](https://www.w3.org/TR/2000/REC-DOM-Level-2-Events-20001113/events.html) 中定义)来获取对触发事件的元素的引用，并操纵它:

```
function focusDynamicLabel(event) {   

  // get the form field that fired this event   

  var elm = event.target;   

  // if it is currently displaying the label...   

  if (elm._labelText == elm.value) {   

    // ... turn it off   

    elm.value = "";   

  }   

}   

function blurDynamicLabel(event) {   

  // get the form field that fired this event   

  var elm = event.target;   

  // if it's empty...   

  if ("" == elm.value) {   

    // ... display the label text   

    elm.value = elm._labelText;   

  }   

}
```

但是，IE/windows 再次实现了这个功能[，与](https://web.archive.org/web/20030806053020/http://msdn.microsoft.com:80/workshop/author/dhtml/reference/objects/obj_event.asp)略有不同，使用属性`srcElement`而不是标准化的`target`，并通过`window.event`而不是将事件对象隐式传递给事件处理函数的标准化方式来提供事件对象。

我们将需要另一个小的黑客和助手功能:

```
function focusDynamicLabel(event) {   

  // get the form field that fired this event   

 **var elm = getEventSrc(event);**   

  // if it is currently displaying the label...   

  if (elm._labelText == elm.value) {   

    // ... turn it off   

    elm.value = "";   

  }   

}   

function blurDynamicLabel(event) {   

  // get the form field that fired this event   

 **var elm = getEventSrc(event);**   

  // if it's empty...   

  if ("" == elm.value) {   

    // ... display the label text   

    elm.value = elm._labelText;   

  }   

}   

**function getEventSrc(e) {**   

  // get a reference to the IE/windows event object   

 **if (!e) e = window.event;**   

  // DOM-compliant name of event source property   

 **if (e.target)   

    return e. target;**   

  // IE/windows name of event source property   

 **else if (e.srcElement)   

    return e.srcElement;   

}**
```

例 C 展示了我们迄今为止的工作。

现在，我们已经实现了原始标签脚本的一个更加专业的版本。它不仅仅绑定到事件处理程序，我们通过将它实现为一系列函数，使脚本更加模块化。正因为如此，这个脚本将更加灵活，更易于维护。

但是 DHTML 和处理表单的代码之间的耦合如何呢？如果我们将表单字段留空并按下提交按钮，“用户名”将被提交到服务器端进程。我们仍然需要解决这个问题。

每个表单都有一个 [`onsubmit`事件](https://www.w3.org/TR/1999/REC-html401-19991224/interact/scripts.html#adef-onsubmit)，该事件在其值被提交给服务器之前被触发。我们只需要遍历页面上的每个表单，并将事件处理程序添加到该事件中。做这件事的一个好地方是在我们的设置函数中:

```
function setupLabels() {    

  // get all the labels on the entire page    

  var objLabels = document.getElementsByTagName("LABEL");    

  var objField;    

  for (var i = 0; i < objLabels.length; i++) {    

    // if the label is supposed to be dynamic...    

    if ("dynamicLabel" == objLabels[i].className) {    

      // get the field associated with it    

      objField = document.getElementById(objLabels[i].htmlFor);    

      // add event handlers to the onfocus and onblur events    

      addEvent(objField, "focus", focusDynamicLabel);    

      addEvent(objField, "blur", blurDynamicLabel);    

      // save a copy of the label text    

      objField._labelText = objLabels[i].firstChild.nodeValue;    

      // initialize the display of the label    

      objField.value = objField._labelText;    

    }    

  }    

  // for each form in the document, handle the onsubmit event with the     

  // resetLabels function    

 **for (var i = 0; i < document.forms.length; i++) {    

    addEvent(document.forms[i], "submit", resetLabels);    

  }**    

}
```

为了实现`resetLabels`函数，我们做了与设置相反的事情:遍历表单中的每个标签，检查它是否是一个动态标签。如果是，并且它显示标签文本，我们将其值重置为空字符串。

```
function resetLabels(event) {    

  var elm = getEventSrc(event);    

  // get all label elements in this form    

  var objLabels = elm.getElementsByTagName("LABEL");    

  var objField;    

  for (var i = 0; i < objLabels.length; i++) {    

    // if the label is dynamic...    

    if ("dynamicLabel" == objLabels[i].className) {    

      // get its associated form field    

      objField = document.getElementById(objLabels[i].htmlFor);    

      // if the field is displaying the label, reset it to empty string    

      if (objField._labelText == objField.value) {    

        objField.value = "";    

      }    

    }    

  }    

}
```

[示例 D](https://www.sitepoint.com/examples/wellbehaveddhtml/ExampleD.html) 显示了我们在步骤 2 结束时的工作。我们已经成功地将原始的结构化文档转换成了我们想要的动态效果。它不再与处理表单的代码耦合，它可以很好地与其他脚本一起工作，并且是模块化的代码。

##### 步骤 3:确定所有用户代理需求

这一步很简单:我们只需浏览第二步中的代码，并确定我们使用的所有对象、特性和其他浏览器需求。我们将使用这些信息创建一个 JavaScript 函数，剔除所有不符合这些要求的浏览器。

在 labels 脚本中，我们使用了许多不同的 DOM 技术，但是我们只需要测试三种技术:

*   `document.getElementById`
*   `window.attachEvent`或者
*   `window.addEventListener`

我们可以用这个简单的函数来实现:

```
function supportsDynamicLabels() {    

  // return true if the browser supports getElementById and a method to     

  // create event listeners    

  return document.getElementById &&     

    (window.attachEvent || window.addEventListener);    

}
```

我们不需要测试更多属性的原因是，我们使用的所有 DOM 函数要么来自 DOM 1 级 HTML，要么来自 DOM 2 级事件。一旦我们看到当前的浏览器支持每个建议中的一个方法，我们就可以假设它实现了该建议的其余部分(至少表面上是这样)。

我们只使用了每个建议的一小部分，所以我们不需要在测试中涉及更多的细节。随着您的脚本变得越来越复杂，您会发现有些浏览器只部分支持某些推荐，您需要测试越来越多的特定功能。

W3C 推荐标准实际上提出了一种方式，让浏览器通过 [`hasFeature`方法](https://www.w3.org/TR/1998/REC-DOM-Level-1-19981001/level-one-core.html#ID-102161490)来表明它支持哪一级的 DOM。具有讽刺意味的是，这种方法并没有得到很好的支持。

DHTML 的现实可能总是包括部分的和错误实现的规范。开发人员有责任确保他们对所需的特性进行了正确的测试。

##### 步骤 4:当代理需求得到满足时，转换逻辑结构。

在特性检查功能之后，接下来要做的事情是编写代码，将结构从您在步骤 1 中编写的逻辑代码转换为步骤 2 中的动态代码。

在每个进行转换的地方，您应该首先检查当前的浏览器是否受支持。这样效果要么完全实现，要么根本不实现。

我们对文档的逻辑结构进行了两处修改，一处是添加了关闭 HTML 标签显示的样式规则，另一处是在窗口的 onload 事件中运行的 setup 函数。如果浏览器不受支持，我们只需防止这两种转换发生。

对于样式规则，我们将更改我们的代码，以便使用 JavaScript 将规则实际写出到文档中。这是我经常使用的一个优雅的解决方案，因为它非常可靠。确保文档结构只在 JavaScript 存在时才改变的最好方法是只使用 JavaScript 来改变文档结构。

我们删除了在步骤 2 中添加的样式表规则，并用以下 JavaScript 替换它:

```
if (supportsDynamicLabels()) {    

  document.writeln('<style type="text/css">');    

  document.writeln('label { display:none; }');    

  document.writeln('</style>');    

} 
```

我们还将 setup 函数移到“if”分支中，因为我们希望它仅在满足我们的要求时运行:

```
if (supportsDynamicLabels()) {    

  document.writeln('<style type="text/css">');    

  document.writeln('label { display:none; }');    

  document.writeln('</style>');    

  **addEvent(window, "load", setupLabels);**    

}
```

[例 E](https://www.sitepoint.com/examples/wellbehaveddhtml/ExampleE.html) 显示完成的效果。

##### 步骤 5:在所有目标平台上彻底测试

不能低估仔细测试 DHTML 效果的重要性。一个简单的事实是，如果你要编写 DHTML，你需要能够在大多数运行它的平台上亲自测试它。

例如，简单的谷歌搜索就会发现 [Windows IE 5+](https://web.archive.org/web/20030814030038/http://msdn.microsoft.com:80/workshop/author/dhtml/reference/methods/getelementbyid.asp) 、 [Gecko](http://www.mozilla.org/docs/dom/domref/dom_doc_ref48.html#1025944) 、 [Safari](https://web.archive.org/web/20030808170149/http://developer.apple.com/internet/safari_faq.html) 似乎都实现了我们需要的功能。

然而，如果你在 Safari 1.0 上运行 Example E，你会注意到一个大问题:效果只运行一次！第一次单击文本框时，标签会正确消失。但是在侠影身上，什么都没发生。文本框保持空白，并且您永远无法将标签放回原处。

原来 Safari 有一个 bug——在下一个文本框被聚焦之前，它不会为一个文本框触发 onblur。在我们的例子中，这意味着如果用户简单地从文本框上跳开或单击，而没有聚焦另一个文本框，我们的标签将不会重新出现。

Safari 与 onblur 的问题是一个无法通过简单的功能检测来测试的实现错误的例子。我们将需要更新我们的特性测试功能，以便专门针对 Safari Web 浏览器进行测试。以下更改将达到目的:

```
function supportsDynamicLabels() {     

  return      

    document.getElementById &&      

    (window.attachEvent || window.addEventListener) &&      

    **null == navigator.appVersion.match(/Safari/d+$/);**     

} 
```

添加的行使用一个[正则表达式](https://web.archive.org/web/20010803002258/http://www.webreference.com/js/column5/regular.html)来测试 navigator 对象的`appVersion`属性，并在当前浏览器不是 Safari 时返回 true。

当测试一个特定的浏览器时，最好在该浏览器的对象模型中测试一个特定的专有属性。例如，IE 有`window.clientInformation`属性，可以用来明确地将其与其他浏览器区分开来。

然而，Safari 似乎不支持任何专有属性。因此，我们必须测试 navigator 对象的`appVersion`属性。您也可以测试`userAgent`属性，但是这不太可靠，因为它可以被一些浏览器的用户修改。

[例子 F](https://www.sitepoint.com/examples/wellbehaveddhtml/ExampleF.html) 展示了我们的最终作品。我们已经成功地将我们第一个表现不佳的动态标签脚本转化成了更好的东西。我们的最终代码是完全模块化的，不依赖于 JavaScript，与其他脚本配合良好，并且不耦合到任何其他组件。

在测试期间，我们发现 Safari 在处理文本框上的焦点和模糊事件时有一个不明显的错误，这使得它无法支持我们的效果。我们期待 Safari 的发布能够修复这个 bug，届时我们可以轻松地更新我们的功能测试功能，只测试有问题的版本。

最重要的是，我们用来达到这一点的五步过程可以很容易地应用于现代网站的任何其他 DHTML 效果。

DHTML 可以用来补充很多网页的 UI，也可以做到不需要它的支持。这种风格的 DHTML 编码不应该与过去表现不佳的脚本相提并论，而应该被认为是专业 Web 开发人员武库中的另一个有价值的工具。

## 分享这篇文章