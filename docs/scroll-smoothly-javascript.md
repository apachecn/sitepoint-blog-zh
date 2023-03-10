# 用 JavaScript 让内部链接平滑滚动

> 原文：<https://www.sitepoint.com/scroll-smoothly-javascript/>

当用户在一个很长的文档中导航时，当他们点击一个链接跳转到同一文档中的另一个位置时，他们经常会感到困惑或不知所措。

它们是在同一页上，还是在不同的一页上？他们应该从这里滚动更多吗？这是怎么回事？

这个问题的答案是让用户在文档中滚动到链接的位置，[，就像这个](http://www.kryogenix.org/code/browser/smoothscroll/)。在本教程中，我们将使用少量的 JavaScript 来确保文档内部的链接将用户滚动到他们的目的地，而不是直接跳到那里让用户困惑。

##### 查找内部链接

首先，我们需要识别文档中的所有链接，然后找出哪些是内部链接。获得所有链接的列表很容易:

```
 var allLinks = document.getElementsByTagName('a');
```

我们需要浏览这个列表，并找出哪些链接是内部链接。内部链接中会有一个散列符号(#)，它会指向我们当前正在查看的文档。有用的 location 对象告诉我们正在查看的文档的 URL，所以试试这个:

```
 for (var i=0;i<allLinks.length;i++) { 

  var lnk = allLinks[i]; 

    if ((lnk.href && lnk.href.indexOf('#') != -1) &&  

        ( (lnk.pathname == location.pathname) || 

    ('/'+lnk.pathname == location.pathname) ) &&  

        (lnk.search == location.search)) { 

           DO SOMETHING WITH THE LINK HERE 

    } 

  }
```

这里，for 循环遍历文档中的链接列表，我们检查三件事:

1.  **Does the link contain a hash?**
    We check this using the link’s href property, and the `indexOf()` function to find the location of one string in another.
2.  **Is the link the same as the current location?**
    Links (and the location object) have a pathname attribute. The pathname of the URL https://www.sitepoint.com/about/who/mharbottle.php is /about/who/mharbottle.php in some browsers, and about/who/mharbottle.php in others (note the presence or absence of the first slash). We must check for both.
3.  查询字符串与当前位置相同吗？
    query string 之后出现的是什么？在 url 中；如果你的网站是数据库驱动的，这显然很重要。JavaScript 在包含查询字符串的位置和链接上定义了一个搜索属性。

如果这些问题都是真的，那么我们知道这个链接是一个内部链接，我们可以设置它滚动到它的目的地。

##### 卷轴，不要跳！

现在我们已经确定了一个内部链接，我们想让它在被点击时滚动。为此，我们需要在链接上附加一个 onclick 事件处理程序。在过去，当 Web 开发人员很大胆的时候，许多人认为(嗯，我也这样认为)事件处理程序是在 HTML 中的链接上设置的:

```
<a href="https://www.sitepoint.com/" onclick="myEventHandler()">
```

但这并不是事实。相反，您应该将事件侦听器附加到链接对象。W3C 指定了一个标准的方法来实现这一点，Internet Explorer 也是如此；Scott Andrew 有效地提供了一个函数来处理这两种情况:

```
function ss_addEvent(elm, evType, fn, useCapture) 

// addEvent and removeEvent 

// cross-browser event handling for IE5+,  NS6 and Mozilla 

// By Scott Andrew 

{ 

  if (elm.addEventListener){ 

    elm.addEventListener(evType, fn, useCapture); 

    return true; 

  } else if (elm.attachEvent){ 

    var r = elm.attachEvent("on"+evType, fn); 

    return r; 

  } 

}
```

因此，在链接的循环中，我们调用这个脚本将一个平滑滚动函数附加到内部链接上:

```
ss_addEvent(lnk,'click',smoothScroll);
```

##### 如何滚动

当然，我们实际上也必须有一个`smoothScroll()`函数。这是一个复杂的方面，因为它都是关于寻找一个对象在页面上的位置，不同的浏览器以不同的方式实现。不可思议的 Andrew Clover 已经写了一篇关于如何跨浏览器找到这个位置的摘要，我们将在这里广泛使用这个解决方案。

首先，我们的`smoothScroll`函数是一个事件处理程序，因此，当它被调用时(即当用户点击我们的一个内部链接时)，我们需要检索被点击的链接。Netscape 类的浏览器向每个处理程序传递一个事件对象；Internet Explorer 将这些细节存储在全局`window.event`对象中。

```
 if (window.event) {  

    target = window.event.srcElement;  

  } else if (e) {  

    target = e.target;  

  } else return;
```

这段代码以跨浏览器的方式将单击的链接设置为目标。…嗯，差不多了。Mozilla 有时会将链接中的文本节点作为点击项传递给你。我们需要检查 target 是否是一个文本节点(即它的`nodeType`是否为 3)，如果是，就取它的父节点。

```
if (target.nodeType == 3) { target = target.parentNode; }
```

作为一个偏执狂，我们还会检查我们得到的是不是 A 标记，以防我们遗漏了什么:

```
if (target.nodeName.toLowerCase() != 'a') return;
```

现在，我们需要找到目的地:对应于我们点击的链接中散列之后部分的`<a name>`标签。链接有一个 hash 属性，它包含#和在 URL 中它后面出现的部分，所以现在让我们遍历文档中的所有链接，并检查它们的 name 属性是否等于所单击链接的 hash 部分:

```
 // First strip off the hash (first character)  

  anchor = target.hash.substr(1);  

  // Now loop all A tags until we find one with that name  

  var allLinks = document.getElementsByTagName('a');  

  var destinationLink = null;  

  for (var i=0;i<allLinks.length;i++) {  

    var lnk = allLinks[i];  

    if (lnk.name && (lnk.name == anchor)) {  

      destinationLink = lnk;  

      break;  

    }  

  }  

  // If we didn't find a destination, give up and let the browser do  

  // its thing  

  if (!destinationLink) return true;
```

我们知道我们点击了什么，以及它指向什么。现在我们需要知道的是我们在文档中的位置，以及我们的目的地是哪里。这就是安迪·克洛弗的笔记的无价之处。首先，我们找到目标链接的位置:

```
 var destx = destinationLink.offsetLeft;   

  var desty = destinationLink.offsetTop;  

  var thisNode = destinationLink;  

  while (thisNode.offsetParent &&   

        (thisNode.offsetParent != document.body)) {  

    thisNode = thisNode.offsetParent;  

    destx += thisNode.offsetLeft;  

    desty += thisNode.offsetTop;  

  }
```

注意，按照 IE 的要求，我们通过`offsetParents`循环，直到到达文档主体。接下来，找出我们目前所处的位置:

```
function ss_getCurrentYPos() {  

  if (document.body && document.body.scrollTop)  

    return document.body.scrollTop;  

  if (document.documentElement && document.documentElement.scrollTop)  

    return document.documentElement.scrollTop;  

  if (window.pageYOffset)  

    return window.pageYOffset;  

  return 0;  

}
```

IE5 和 5.5 将当前位置存储在`document.body.scrollTop`中，IE6 存储在`document.documentElement.scrollTop`中，网景类浏览器存储在`window.pageYOffset`中。唷！

我们实际处理滚动的方式是使用`setInterval()`；这个非常有用的函数设置了一个重复的定时器来触发我们选择的函数。在这种情况下，我们将让函数将浏览器的位置向目的地移动一步；`setInterval()`会反复调用我们的函数，当到达目的地时，我们会取消定时器。

首先，使用`clearInterval()`关闭任何当前正在运行的定时器:

```
 clearInterval(ss_INTERVAL);
```

```
ss_INTERVAL is a global variable in which we will later store the ouput of setInterval(). Next, work out how big each step should be:

```
 ss_stepsize = parseInt((desty-cypos)/ss_STEPS);
```

```
ss_STEPS is defined in the script to be the number of steps we take from target to destination. Our "scroll one step" function is called ss_scrollWindow and takes three parameters: 
	*   滚动多少*   目的地位置*   目标链接本身
我们需要在一个字符串中构造对此的调用，并将该字符串传递给`setInterval`，以及我们希望调用重复的频率:

```
 ss_INTERVAL = setInterval('ss_scrollWindow('+ss_stepsize+','+desty+',"'+anchor+'")',10);
```

注意我们是如何构建一个调用`ss_scrollWindow()`的字符串，而不是直接调用`ss_scrollWindow()`——这是关于`setInterval()`最令人困惑的事情之一。
一旦我们做到了这一点，我们必须停止浏览器采取正常的路线，服从链接并直接跳转到目的地。同样，这种情况在不同的浏览器中会有所不同。要停止浏览器在 Internet Explorer 中正常处理该事件，请使用:

```
 if (window.event) {  

    window.event.cancelBubble = true;  

    window.event.returnValue = false;  

  }
```

注意对`window.event`的检查，以确保我们正在使用 IE。
要在 Netscape 类的浏览器中执行同样的操作，请使用以下代码:

```
 if (e && e.preventDefault && e.stopPropagation) {  

    e.preventDefault();  

    e.stopPropagation();  

  }
```

滚动一步
最后一件事:我们实际上是如何滚动的？这里的关键函数是`window.scrollTo()`，向它传递一个 X 和 Y 位置；然后浏览器将窗口滚动到那个位置。一个小问题是你不能一直滚动到底部。如果您传入的 Y 位置小于距文档底部一个窗口的高度，浏览器将只向下滚动到它能向下滚动的程度-显然，如果到页面底部的距离小于窗口的高度，它就不能直接向下滚动到链接。
现在，我们需要检查一下。最好的方法是查看滚动前后的位置是否相同:

```
function ss_scrollWindow(scramount,dest,anchor) {  

  wascypos = ss_getCurrentYPos();  

  isAbove = (wascypos < dest);  

  window.scrollTo(0,wascypos + scramount);  

  iscypos = ss_getCurrentYPos();  

  isAboveNow = (iscypos < dest);  

  if ((isAbove != isAboveNow) || (wascypos == iscypos)) {  

    // if we've just scrolled past the destination, or  

    // we haven't moved from the last scroll (i.e., we're at the  

    // bottom of the page) then scroll exactly to the link  

    window.scrollTo(0,dest);  

    // cancel the repeating timer  

    clearInterval(ss_INTERVAL);  

    // and jump to the link directly so the URL's right  

    location.hash = anchor;  

  }  

}
```

请注意，因为我们以特定的整数增量滚动，这一步可能会带我们越过目的地。因此，我们检查我们是否在滚动前后的链接之上；如果这两个位置不同，我们已经滚动过链接，这样，我们就完成了。如果我们完成了，我们取消计时器并设置页面的 URL(通过设置一点 location 对象),这样看起来好像浏览器已经处理了链接。

##### 产生效果

将这种效果应用到页面的最简单方法是将代码放入名为 smoothscroll.js 的文件中，并使用以下代码将该文件包含在页面中:

```
<script src="smoothscroll.js" type="text/javascript"></script>
```

这种方法遵循不引人注目的 DHTML 原则，使每个人都容易使用。为了让解决方案工作，脚本需要由某个东西来运行；我们将第一步中的代码(遍历链接以找到内部链接)放入函数`ss_fixAllLinks()`，并使用 Scott Andrew 的函数将其绑定到窗口的 onload 事件:

```
ss_addEvent(window,"load",ss_fixAllLinks);
```

整个代码如下所示:

```
function ss_fixAllLinks() {   

  // Get a list of all links in the page   

  var allLinks = document.getElementsByTagName('a');   

  // Walk through the list   

  for (var i=0;i<allLinks.length;i++) {   

    var lnk = allLinks[i];   

    if ((lnk.href && lnk.href.indexOf('#') != -1) &&    

        ( (lnk.pathname == location.pathname) ||   

    ('/'+lnk.pathname == location.pathname) ) &&    

        (lnk.search == location.search)) {   

      // If the link is internal to the page (begins in #)   

      // then attach the smoothScroll function as an onclick   

      // event handler   

      ss_addEvent(lnk,'click',smoothScroll);   

    }   

  }   

}   

function smoothScroll(e) {   

  // This is an event handler; get the clicked on element,   

  // in a cross-browser fashion   

  if (window.event) {   

    target = window.event.srcElement;   

  } else if (e) {   

    target = e.target;   

  } else return;   

  // Make sure that the target is an element, not a text node   

  // within an element   

  if (target.nodeType == 3) {   

    target = target.parentNode;   

  }   

  // Paranoia; check this is an A tag   

  if (target.nodeName.toLowerCase() != 'a') return;   

  // Find the <a name> tag corresponding to this href   

  // First strip off the hash (first character)   

  anchor = target.hash.substr(1);   

  // Now loop all A tags until we find one with that name   

  var allLinks = document.getElementsByTagName('a');   

  var destinationLink = null;   

  for (var i=0;i<allLinks.length;i++) {   

    var lnk = allLinks[i];   

    if (lnk.name && (lnk.name == anchor)) {   

      destinationLink = lnk;   

      break;   

    }   

  }   

  // If we didn't find a destination, give up and let the browser do   

  // its thing   

  if (!destinationLink) return true;   

  // Find the destination's position   

  var destx = destinationLink.offsetLeft;    

  var desty = destinationLink.offsetTop;   

  var thisNode = destinationLink;   

  while (thisNode.offsetParent &&    

        (thisNode.offsetParent != document.body)) {   

    thisNode = thisNode.offsetParent;   

    destx += thisNode.offsetLeft;   

    desty += thisNode.offsetTop;   

  }   

  // Stop any current scrolling   

  clearInterval(ss_INTERVAL);   

  cypos = ss_getCurrentYPos();   

  ss_stepsize = parseInt((desty-cypos)/ss_STEPS);   

  ss_INTERVAL = setInterval('ss_scrollWindow('+ss_stepsize+','+desty+',"'+anchor+'")',10);   

  // And stop the actual click happening   

  if (window.event) {   

    window.event.cancelBubble = true;   

    window.event.returnValue = false;   

  }   

  if (e && e.preventDefault && e.stopPropagation) {   

    e.preventDefault();   

    e.stopPropagation();   

  }   

}   

function ss_scrollWindow(scramount,dest,anchor) {   

  wascypos = ss_getCurrentYPos();   

  isAbove = (wascypos < dest);   

  window.scrollTo(0,wascypos + scramount);   

  iscypos = ss_getCurrentYPos();   

  isAboveNow = (iscypos < dest);   

  if ((isAbove != isAboveNow) || (wascypos == iscypos)) {   

    // if we've just scrolled past the destination, or   

    // we haven't moved from the last scroll (i.e., we're at the   

    // bottom of the page) then scroll exactly to the link   

    window.scrollTo(0,dest);   

    // cancel the repeating timer   

    clearInterval(ss_INTERVAL);   

    // and jump to the link directly so the URL's right   

    location.hash = anchor;   

  }   

}   

function ss_getCurrentYPos() {   

  if (document.body && document.body.scrollTop)   

    return document.body.scrollTop;   

  if (document.documentElement && document.documentElement.scrollTop)   

    return document.documentElement.scrollTop;   

  if (window.pageYOffset)   

    return window.pageYOffset;   

  return 0;   

}   

function ss_addEvent(elm, evType, fn, useCapture)   

// addEvent and removeEvent   

// cross-browser event handling for IE5+,  NS6 and Mozilla   

// By Scott Andrew   

{   

  if (elm.addEventListener){   

    elm.addEventListener(evType, fn, useCapture);   

    return true;   

  } else if (elm.attachEvent){   

    var r = elm.attachEvent("on"+evType, fn);   

    return r;   

  }   

}    

var ss_INTERVAL;   

var ss_STEPS = 25;   

ss_addEvent(window,"load",ss_fixAllLinks);
```

##### 包扎

您的文档内部链接将滚动到它们的目的地，使您的用户能够记住浏览器在文档中的位置，以及他们离起点有多远。代码已经过测试，可以在 Mozilla、IE 和 Opera 中运行；它在 Konqueror 中不起作用，并且被认为在其他浏览器中也不起作用。

## 分享这篇文章

```

```