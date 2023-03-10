# Ajax 和屏幕阅读器:什么时候可以工作？

> 原文：<https://www.sitepoint.com/ajax-screenreaders-work/>

在过去的几个月里，我们都听到了很多关于 AJAX 的讨论，随之而来的是大量的文章、技巧、演示和实用 API，旨在探索各种可能性，并尝试实现最佳实践技术。但是，对于所有的兴奋和炒作，关于 AJAX 和可访问性的话题仍然说得很少。

Google 确实取得了一些成果，特别是 standards-schmandards 上的文章“AJAX 和可访问性”,该文章谈到了确保应用程序在没有 JavaScript 的情况下也能工作，并且还提出了使用警告对话框向屏幕阅读器传递信息的想法；但是从文章的语气来看，很明显作者只是在猜测这种方法是否可行(正如我们稍后将看到的，它可能不可行)。西蒙·威廉森在 SitePoint 博客中谈到了这个话题，但是他只从 JavaScript 支持的角度谈到了可访问性。

更复杂和微妙的问题出现在支持 JavaScript 的设备上，但这些设备可能仍然不能与您的应用程序交互。基于浏览器的屏幕阅读器是这样的:它们是支持脚本的浏览器，但是它们实际的 JavaScript 支持与浏览器本身的支持完全不同。

WebAIM 上的文章[“AJAX 应用程序的可访问性(第 1 部分)”解决了这一点，解释了如果要访问动态界面变化，应用程序必须主动通知用户发生了变化，然后允许直接访问新内容。这篇文章到此为止——到目前为止，它没有说这实际上是如何做到的。它自信地在第 2 部分承诺解决方案，但是承诺是容易的！](http://webaim.org/techniques/ajax/)

无论我们看哪里，从 AccessifyForum 的讨论到像 T2 的 Derek Featherstone 和 T4 的 Peter Paul-Koch 的博客，我们都同意我们需要更多的信息。

这就是我写这篇文章的原因:展示我收集的一些数据和分析，看看是否能得出有用的结论。

##### 一点背景…

在过去的几个月(以及更早)里，我一直在研究主流的屏幕阅读器和其他辅助设备如何响应 JavaScript:它们生成或响应什么类型的事件，以及在什么情况下。这项研究是在 [Access Matters](http://www.access-matters.com/index/index-of-javascript-tests/) 进行的，由鲍勃·伊斯顿、德里克·费瑟斯通、迈克·斯坦豪斯和我协调。

除此之外，我为我最近出版的书[JavaScript 选集](https://www.sitepoint.com/premium/library)做了大量的初步研究。这项研究旨在发现辅助设备如何响应定期或异步更新 DOM 的脚本，例如滚动新闻滚动条中的项目，或者对 XMLHttpRequest 的响应。

我们发现屏幕阅读器中的脚本支持非常不稳定和不完整——然而这还不是最大的问题！有很多方法可以创建可用的挂钩(例如，我们测试的所有屏幕阅读器都会在链接和表单控件上生成点击事件)，但真正的症结是:屏幕阅读器用户如何知道内容已经更改？

视力正常的用户可以随意访问网页，因为他或她可以看到网页的不同部分；如果有什么变化，我们可以在视觉上引起用户的注意。但是盲人没有这样的机会。他们处理页面的方式是线性的，所以如果页面的一部分在他们当前关注的位置之前或之后发生了变化，用户不会注意到这一点，即使他们遇到了，也不会意识到这一点。

屏幕阅读器不会宣布 DOM 的动态变化——这些变化只是在后台发生——所以任何给定的变化都很可能不会被注意到，除非我们以某种方式通知用户。

这是一个价值 64000 美元的问题:我们如何做到这一点？要回答这个问题，我们需要尝试一些不同的策略，然后看看(或者说，听听)结果！

##### 测试

在我们开始之前，您可能希望[下载所有这些测试](https://www.sitepoint.com/examples/ajax/ajax-tests.zip)的档案，这样您就可以参考它们或者自己运行测试。

***第一次测试***

第一个测试只是更新触发器元素正下方的一段文本。以下是核心 HTML:

```
<p> 

    <a href="./" id="trigger">This link is the trigger.</a> 

</p> 

<p id="response"> 

    This paragraph will update with the response. 

</p> 

<p> 

    This is some text that comes after the response, 

    to check continuity. 

</p>
```

这是 JavaScript:

```
window.onload = function() 

{ 

  var trigger = document.getElementById('trigger'); 

  var response = document.getElementById('response'); 

  trigger.onclick = function() 

  { 

    var request = null; 

    if(typeof window.XMLHttpRequest != 'undefined') 

    { 

      request = new XMLHttpRequest(); 

    } 

    else if(typeof window.ActiveXObject != 'undefined') 

    { 

      try { request = new ActiveXObject('Microsoft.XMLHTTP'); } 

      catch(err) { request = null; } 

    } 

    if(request != null) 

    { 

      request.onreadystatechange = function() 

      { 

        if (request.readyState == 4  

            && /^(200|304)$/.test(request.status.toString())) 

        { 

          response.innerHTML = request.responseText; 

        } 

      } 

      request.open('GET', 'test.php?msg=Hello+World', true); 

      request.send(null); 

    } 

    return false; 

  }; 

};
```

“test.php”脚本只是为请求的`responseText`输出一条消息；可能是任何事:

```
<?php 

echo "And here's the response - " . $_GET['msg']; 

?>
```

为了执行测试，我们使用键盘导航到触发器链接，并按 Enter 来激活该链接。所有的设备都应该激活这个功能，但是它们之后的反应可能会有很大的不同。

**第一次测试的结果**

所有设备都会触发该功能，并且大多数会更新响应段落，但是没有设备会自动读取它(如预期的那样)。这项测试只是用来确保内容更新得到普遍认可，但不幸的是，它并没有得到普遍认可:Windows Eyes 直到触发链接模糊后才会更新其语音输出，如果我们只是让读者继续阅读，这种情况就不会发生。因此，根据用户的交互，他们可能根本听不到更新的消息。

不过，这是一个不错的开始，也许我们的 Windows 眼睛问题是这个例子所特有的。我们在这里寻找的不仅仅是一个更新——我们想要一种自动说出响应的方式，而不需要进一步的用户干预；让我们坚持那个意图。

***第二次测试***

第二个测试与第一个几乎相同，但是这一次我们将采取额外的步骤，将`document.location`设置为响应段落的片段标识符(ID)(使其成为页面内目标)。下面是对`onreadystatechange`函数的补充(以粗体显示):

```
request.onreadystatechange = function() 

{ 

  if (request.readyState == 4 

      && /^(200|304)$/.test(request.status.toString())) 

  { 

    response.innerHTML = request.responseText; 

    document.location = '#response'; 

  } 

}
```

**第二次测试的结果**

这些结果相当复杂:

*   在主页阅读器 3.02 中，响应文本会自动读出，但阅读器不会就此停止:它会继续阅读页面的其余部分。如果响应元素在页面的最末端，这将是一个可行的选择。
*   在主页阅读器 3.04(注意，是一个更新的版本)中，位置设置不再正确工作。读者跳回到页面的顶部，而不是响应段落(我也用 location.replace 试过，看看是否会有所不同，但没有)。
*   在 Hal 6.5 和 Connect Outloud 2.0 中，阅读器宣布一个新的页面加载，但是在响应之后开始读取元素，完全错过了响应。
*   在 JAWS 5.0 和 6.2 中，代码不起作用，有时根本不做任何响应；其他时候，它会再次重新读取触发器链接文本或顶级标题；偶尔，它的行为方式与 Hal 和 Connect Outloud 相同。
*   在 Windows Eyes 5.0 中，内容会更新！但除此之外，它的行为方式看起来就像 Home Page Reader 3.02:它宣布一个新的页面加载，然后开始读取(包括)响应元素。但这种行为并不像它看起来那样:该设备只有这样工作，因为 Windows Eyes 会在加载你以前访问过的页面时记住你以前的位置，而且由于响应是在触发后直接出现的，所以这是你接下来会听到的东西。如果不是这种情况，它将简单地读取直接在触发器之后的内容。
*   Windows Eyes 5.5 (beta)的行为方式与 Hal 和 Connect Outloud 完全相同。

这里有一种模棱两可的模式，几个设备都做同样的事情，跳过响应段落，从后面出现的元素开始。我想到 HTML 可能是一个因素，所以我把它改成这样:

```
<p> 

    <a name="response" id="response" href="#"> 

        This link will update with the response.</a> 

</p>
```

而且，使用相同的位置设置，第二次测试的结果确实发生了变化。尽管我们没有使用链接的 href，但它的加入使锚成为一个可聚焦的元素(而一个段落或一个没有 href 的锚则不是)，这似乎使它在某些设备上更可靠地工作。

**修改后的第二次测试结果**

两个版本的主页阅读器的行为都和以前一样，并通过 Connect Outloud 加入，现在的行为类似于 HPR 3.02(它可以工作，但继续阅读)。两个版本的 Windows Eyes 现在的行为与 5.5 之前一样(它们在响应后开始读取元素)。但是在 JAWS 和 Hal 中，代码工作得很好——说出了响应文本，但没有进一步发生什么(尽管 JAWS 也可能在说出响应文本之前先重新读取页面的顶级标题)。

***第三次考验***

在第三个测试中，一旦响应链接的文本被更新，我们将用响应链接上的编程式 focus()调用替换位置设置。新的 HTML 如下所示:

```
<p> 

    <a href="./" id="response">  

        This link will update with the response.</a> 

</p>
```

同样，只需要对原来的`onreadystatechange`功能做一个小的修改(变化以粗体显示):

```
request.onreadystatechange = function() 

{ 

  if (request.readyState == 4 

      && /^(200|304)$/.test(request.status.toString())) 

  { 

    response.innerHTML = request.responseText; 

    response.focus(); 

  } 

}
```

**第三次测试的结果**

这段代码在 JAWS 5.0 和 Connect Outloud 之外的任何设备上都不起作用(奇怪的是，它在 JAWS 6.2 中不起作用，鉴于它在早期版本中取得了成功)。在大多数设备中无法工作意味着什么都不会发生；然而，在 JAWS 6.2 中，触发器链接将再次被说出，而 Windows Eyes 继续完全按照修改后的第二个测试的方式运行(在响应后开始从元素中读取)。

***第四次考验***

第四个测试完全取消了响应元素，而是在警告对话框中显示响应文本。HTML 只是触发链接，而 onreadystatechange 函数简化为:

```
request.onreadystatechange = function() 

{ 

  if (request.readyState == 4 

      && /^(200|304)$/.test(request.status.toString())) 

  { 

      alert(request.responseText); 

  } 

}
```

**第四次测试的结果**

这对每个人来说都应该是安全的，但令人惊讶的是，事实并非如此:Windows Eyes 5.0 并不总是说出对话文本。有时候，它只是宣布对话框，并没有告诉你对话框说了什么！

***第五次考验***

对于第五个测试，我们将继续形成元素。首先，我们将尝试更新和聚焦一个文本字段:

```
<form action=""> 

  <div> 

    <input type="text" id="response" size="50" 

        value="This field will update with the response"> 

  </div> 

</form>
```

下面是适用的`onreadystatechange`函数:

```
request.onreadystatechange = function() 

{ 

  if (request.readyState == 4 

      && /^(200|304)$/.test(request.status.toString())) 

  { 

    response.value = request.responseText; 

    response.focus(); 

  } 

}
```

**第五次测试的结果**

这个测试在 Home Page Reader 或 Hal 中不起作用(尽管有典型的视觉反应，但什么也没发生)。它在 JAWS 6.2 中也失败了，与第三个测试一样，它再次重复触发链接，并且也可能重新宣布顶级标题。

这段代码在 Windows Eyes 中也失败了，它的行为就像第三次测试一样(即它在响应后开始读取元素)。这段代码唯一适用的阅读器是 JAWS 5.0 和 Connect Outloud，尽管它们也在说出编辑框的值之前说“edit”来宣布编辑框。

***第六次考验***

在第六次测试中，我们将做几乎相同的事情。但是，这一次，我们不是聚焦元素，而是以编程方式选择它的文本:

```
request.onreadystatechange = function() 

{ 

  if (request.readyState == 4 

      && /^(200|304)$/.test(request.status.toString())) 

  { 

    response.value = request.responseText; 

    if (typeof response.createTextRange != 'undefined') 

    { 

      var range = response.createTextRange(); 

      range.select(); 

    } 

    else if (typeof response.setSelectionRange != 'undefined') 

    { 

      response.setSelectionRange(0, response.value.length); 

    } 

  } 

}
```

**第六次测试的结果**

这里的成功和失败模式与之前的测试相同。
在第七个也是最后一个测试中，我们将为响应元素使用一个按钮:

```
<form action=""> 

  <div> 

    <button type="button" 

      id="response">This button will update with the response 

    </button> 

  </div> 

</form>
```

然后，我们将更改按钮文本并聚焦它，就像我们对第五个测试所做的那样:

```
request.onreadystatechange = function() 

{ 

  if (request.readyState == 4 

      && /^(200|304)$/.test(request.status.toString())) 

  { 

    response.firstChild.nodeValue = request.responseText; 

    response.focus(); 

  } 

}
```

**第七次测试的结果**

这个测试也产生了与第五和第六个测试相同的结果，但是有一个小的和预期的变化，JAWS 5.0 和 Connect Outloud(它在其中工作)通过在文本后说“button”而不是在文本前说“edit”来宣布响应小部件。

##### 结论

似乎没有任何可靠的方法来通知屏幕阅读器 DOM 中的更新。有一些零碎的方法适用于一个或多个设备，但没有一个整体的方法或组合可以覆盖所有设备，因为即使是最简单的警报在 Windows Eyes 中也可能无法正常工作。作为开发人员，这对我们意味着什么——是不是意味着我们应该停止使用 AJAX 技术？

***是吗？***

让我们面对现实吧，大量的 AJAX 应用程序(我敢说“大多数”吗？)使用这种方法本身，并不真正从中受益——他们也可以使用传统的帖子和回复。

我甚至想更进一步，呼吁从根本上重新评估我们在这里的优先事项。我们正在谈论的是让动态客户端界面在屏幕阅读器中有效地工作，但也许这从来都不是重点。真正的目的不是让应用程序本身在屏幕阅读器中有效地工作吗？

交互只是细节，也许我们真正在做的是把我们自己的欲望和偏好投射到与他们不相关的用户身上。也许动态客户端界面对屏幕阅读器用户没有任何好处；也许对他们来说真正有用的是发挥阅读器最初为之建立的任务:单独的页面请求和 HTTP 的交互。这些正是屏幕阅读器要处理的交互类型。

***没有？***

也许我们应该让使用屏幕阅读器的人关掉 JavaScript，直到技术能够胜任任务。或者，我们应该在应用程序的开始添加用户偏好，这样用户就可以预先选择界面。如果我们可以确信一个屏幕阅读器用户根本没有 JavaScript，那么我们可以为他们设计非脚本化的功能，像为任何非脚本用户一样，回到 POST/response 范例。

但是不可否认的是，某些类型的 AJAX 应用程序只能以这种方式工作——在某些情况下，不可能提供不依赖于 JavaScript 的真正等效的替代方案。(Gmail 是一个典型的例子:它提供了一个无脚本版本，但它远不如脚本版本功能完善。)也许我们应该看看屏幕阅读器供应商自己，因为他们可能会通过提供必要的挂钩和反馈来帮助用户访问远程脚本，从而对日益流行的远程脚本做出响应。

IBM 目前正在与 GW Micro(Windows Eyes 的制造商)和 Mozilla 基金会合作，引入“角色”和“状态”(由元素属性定义)，它们可以[传达关于元素](http://www.mozilla.org/access/dhtml/)的性质和状态的信息。理论上，这完全解决了问题，并且意味着任何适当的元素都可以传达所有必要的信息:它自己的含义、它的行为角色以及它的当前状态。

但尽管这些都是非常令人兴奋的发展，但这并不是我们现在真正可以使用的东西，因为它不是向后兼容的:它除了 Internet Explorer 或 Firefox 之外，根本不提供任何功能，除了 Firefox 1.5 加 Windows Eyes 5.5 之外，它只为设备组合提供非常有限的功能。

***所以？***

我不得不得出这样的结论，除非能够找到一种方法来通知屏幕阅读器更新的内容，否则 AJAX 技术不能被认为是可访问的，并且在没有预先向用户提供真正等效的非脚本替代方案的情况下，不应该在生产站点上使用。

然而，我坦率而愉快地承认，我只分析了有限数量的测试——我已经涵盖了我能想到的角度，但我确信还有很多想法，我们只需要其中一个就能成功！

所以，如果你认为我太容易放弃，请把这看作是一次战斗的召唤:让我们找到让它成功的方法！

## 分享这篇文章