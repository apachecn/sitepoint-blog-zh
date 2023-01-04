# 用不引人注目的 JavaScript 装点大厅

> 原文：<https://www.sitepoint.com/unobtrusive-javascript/>

这是一个快乐的季节，也是一个令人兴奋的 JavaScript 开发者的时代。随着 Web 2.0 热潮的到来，新一代 JavaScript 开发人员诞生了。现代的 JavaScript 程序员非常认真地对待他们的工作，并把自己算作“真正的”程序员。JavaScript 程序员武器库中的一个关键组件是不引人注目的 JavaScript 方法——即网页的行为应该与其结构保持分离。不引人注目的 JavaScript 的想法源于 web 标准运动，该运动主张 Web 页面应该分为三层——结构层(HTML)、表示层(CSS)和行为层(JavaScript)——并且每一层都应该增强前一层。

传统上，大多数(如果不是全部的话)基于事件的 JavaScript 都是以事件处理程序属性的形式直接写入网页的标记中，例如`onclick`、`onfocus`、`onload`、`onmouseover`和`onmouseout`。此外，所有动态生成的标记都采用就地`document.write`语句的形式。但是这些都不符合不引人注目的 JavaScript 原则。

正如礼物不是圣诞节的全部，JavaScript 也不是网页的全部。一个页面应该是功能性的，不需要任何脚本，而是依赖于它。与标记密不可分的 JavaScript 函数调用和指令就产生了这种依赖性。它们还降低了文档 HTML 的可移植性，随着网站页面数量的增加，文档越来越难以维护。但最糟糕的是，他们会让你上圣诞老人的淘气名单——没人想去那里！

##### 买礼物

不引人注目的 JavaScript 规定脚本应该理想地驻留在一个单独的文档中，并通过 HTML `id`和`class`属性挂钩到一个网页。同样，所有动态生成的标记都应该在使用专门的 DOM 方法构建之后插入 DOM。这样，如果在添加 JavaScript 之前页面就已经可以使用了，那么行为层就变成了对文档的增强，而不是依赖——有点像蛋糕上的糖衣，或者圣诞节的礼物。

现在我们并不是生活在一个理想的世界里。有时，我们发现自己在处理没有被很好地记录或维护的多代项目。其他时候，我们的任务(因此也是预算)并不包括对我们被要求修改的现有代码的完全修补或优化。Web 页面并不总是轻量级的，网络也不总是快速的，而且，在团队开发环境中，开发人员并不总是能够完全控制页面的所有组件。记住这一点，让我们看看当条件不是最佳时，实现不引人注目的 JavaScript 的一个讨厌的副作用。

##### 从前门把礼物带回家

有两种方法可以将 JavaScript 加载到 HTML 文档中。传统的方法是在文档的头部放置一个`<script>`标签，并使用窗口对象的`onload`事件触发您的函数。我们称之为“前端加载”，因为脚本是在文档的`<head>`中页面内容之前加载的，在 DOM 构建之前。前端装载不是一个好主意，因为它容易受到时间问题的影响。例如，浏览器下载、解析并执行网页源代码中遇到的 JavaScript，因此文档的`<head>`中的任何 JavaScript 都会延迟页面的呈现，直到该过程完成。更重要的是，一旦完成并呈现页面，绑定到窗口对象的`onload`事件的函数可能不会马上被触发。这是因为只有当浏览器下载完所有页面依赖项时，才会触发该事件，包括当今网页上常见的价值数百千字节的图像和其他媒体。

前端加载会导致一种不良的效果，即访问者在一段时间内看到一个完整的、没有 JavaScript 的页面，在这段时间内他或她可以点击任何东西。因此，举例来说，如果一个锚点要触发一个模态弹出窗口(CSS 类固醇上的一个`div`伪装成一个弹出窗口),它不会在加载期间这样做，因为设置模态行为所需的 JavaScript 还没有执行，因为窗口对象的`onload`事件还没有触发。相反，一旦锚被点击，浏览器会将用户发送到锚的`href`属性中的 URI。最终的结果将是页面不能像预期的那样运行。当然，锚中有一个有效的 URI 仍然可以让访问者继续使用网站，但是这不是我们想要的或者想要的效果。

这是一个前置的、不引人注目的脚本的样子:

```
front-load.html 

<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN" "https://www.w3.org/TR/html4/strict.dtd"> 

<html> 

  <head> 

    <title>Welcome</title> 

    <script> 

      function initSignin() { 

        var signin = document.getElementById("signin"); 

        signin.onclick = function () { 

          /* 

           * Sign-in code that creates a modal 

           *  popup goes here. 

           */ 

          alert('Pretend this is a modal popup...'); 

          return false; // Stop the anchor's default behavior 

        }; 

      } 

      window.onload = initSignin; 

    </script> 

    <link rel="stylesheet" type="text/css" media="all" href="style.css"> 

  </head> 

  <body> 

    <p class="xmas"> 

      <a href="/signin/" id="signin">Sign in</a> 

    </p> 

    <!-- 700 kilobytes worth of media goes here --> 

  </body> 

</html>
```

您会注意到，我们的`initSignin`函数的执行被推迟到页面内容加载之后。在`initSignin`函数中，我们通过将值 false 返回给锚来停止“登录”锚的默认行为。然而，浏览器不会触发窗口对象的 onload 事件，直到它下载了 700 千字节的媒体。因此，在获得这些文件之前，`initSignin`不会运行，我们的链接行为也不会被覆盖。

##### 从后门偷偷把礼物带进来

第二种——也是最理想的——将 JavaScript 加载到 HTML 文档中的方法是将所有的`<script>`标签放在文档的最末尾，就在结束的`</body>`标签之前。这让我们可以确定 DOM 已经准备好被执行了，因为代码是在所有的`<body>`的 HTML 被加载到 DOM 之后被加载的。这样做消除了对`window`对象的`onload`事件处理程序的需求。它还极大地减少了页面呈现和 JavaScript 执行之间的等待时间，因为它的执行不依赖于仅在完成所有文档依赖项下载时触发的事件。在这种情况下，弹出链接的代码会执行得更快，并且可能在访问者考虑点击“登录”链接之前就已经存在了。

这是一个后加载的不引人注目的脚本的样子:

```
back-load.html  

<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN" "https://www.w3.org/TR/html4/strict.dtd">  

<html>  

  <head>  

    <title>Welcome</title>  

    <link rel="stylesheet" type="text/css" media="all" href="style.css">  

  </head>  

  <body>  

    <p class="xmas">  

      <a href="/signin/" id="signin">Sign in</a>  

    </p>  

    <!-- 700 kilobytes worth of media goes here -->  

    <script>  

      var signin = document.getElementById("signin");  

      signin.onclick = function () {  

        /*  

         * Sign-in code that creates a modal  

         *  popup goes here.  

         */  

        alert('Pretend this is a modal popup...');  

        return false; // Stop the anchor's default behavior  

      };  

    </script>  

  </body>  

</html>
```

请注意，在我们的链接和代码之间有大约 700 千字节的媒体，但这无关紧要，因为浏览器不像 JavaScript 那样顺序加载媒体。因此，它会发出一些媒体请求，但即使在操作正在进行时，它也会执行 JavaScript。

也就是说，即使是后装，也可能存在问题。

##### 把礼物藏起来，直到分发的时候

可能您的页面有很多 JavaScript 需要处理，或者托管您的脚本的服务器出现了短暂的延迟。即使你正在重新加载你的脚本，这样的情况可能会阻止它们立即生效。这可能会导致奇怪的行为，比如前面提到的链接没有被及时覆盖，或者甚至是改变布局的问题。当您通过脚本修改 DOM 时，会出现后一个问题——例如，如果您添加将导致应用 CSS 规则的类名，将元素插入 DOM，或者调整现有元素的位置或尺寸。如果 JavaScript 代码运行得稍微晚一点，并且对 DOM 的更改发生在初始呈现之后，结果将是元素在页面上移动，或者更糟的是，文本在被函数的缓慢执行隐藏之前会短暂出现。

处理这种不可避免的情况的一种技术是在呈现之前隐藏受影响的内容。这意味着按照下面的方式编写一个 CSS 规则:

```
.modal {  

    visibility: hidden;  

}
```

我们给页面中所有应该触发模态弹出窗口的锚点赋予类名`modal`。然后，我们在函数中编写一行代码，覆盖锚点的默认行为，这样一旦完成工作，它就将锚点的可见性设置为 visible，如下所示:

```
el.style.visibility = "visible";
```

然而，我们需要小心，不要在解决其他问题的同时制造新的问题。通过将页面中带有模态类名的所有链接的可见性设置为 hidden，我们冒着将没有 JavaScript 可用的人拒之门外的风险。这种风险之所以存在，是因为负责隐藏链接的机制是 CSS，而负责显示链接的机制是 JavaScript。通过跨越两个分离层，我们假设“每个拥有 CSS 的人也拥有 JavaScript”，但事实并非总是如此。所以，我们需要做的是使用 JavaScript 创建模态样式规则。这样，如果 JavaScript 不可用，就不会创建规则，也不会隐藏链接。在这种情况下，后加载不是一个好主意，因为我们希望该规则尽快可用。如果我们使用这种技术，下面是我们的“登录”示例页面的外观:

```
hide-content.html  

<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN" "https://www.w3.org/TR/html4/strict.dtd">  

<html>  

  <head>  

    <title>Welcome</title>  

    <link rel="stylesheet" type="text/css" media="all" href="style.css">  

    <script>  

      document.write('<style type="text/css">.modal {visibility: hidden;}</style>');  

    </script>  

  </head>  

  <body>  

    <p class="xmas">  

      <a href="/signin/" id="signin" class="modal">Sign in</a>  

    </p>  

    <!-- 700 kilobytes worth of media goes here -->  

    <script>  

      var signin = document.getElementById("signin");  

      signin.onclick = function () {  

        /*  

         * Sign-in code that creates a modal  

         *  popup goes here.  

         */  

        alert('Pretend this is a modal popup...');  

        return false; // Stop the anchor's default behavior  

      };  

      signin.style.visibility = "visible";  

    </script>  

  </body>  

</html>
```

你会注意到我使用了`document.write`来创建模态样式规则。虽然我从不提倡使用`document.write`，但这是我准备破例的一个地方。这个例子使用了一个`<style>`块，但是我通常在真实网站上使用的是一个外部 CSS 文档，其中包含了所有没有 JavaScript 就无法撤销的规则——比如`visibility: hidden`。编写用`document.write`调用 CSS 文档的`<link>`标签是一个简单的单行解决方案，可以确保浏览器在处理`<head>`内容的同时调用该文件(如果 JavaScript 可用的话)。

您还会注意到，我添加了一行代码，在我将一个函数分配给它的`onclick`事件处理程序之后，该代码会重置锚点的可见性。换句话说，现在我确信锚会按照我想要的方式运行，我可以重新打开它。

显示和隐藏内容的方式有很多种，每种方式在特定的上下文中都是有效的。在这种情况下，我选择使用`visibility: hidden`,因为它在隐藏元素时保留了元素的尺寸。例如，如果我使用`display: none`，锚通常占据的空间会塌陷，打开它会导致文档的布局稍微移动。隐藏和显示内容的另一个技巧是将元素的位置设置为`absolute`，将其左值设置为`-3000px`，将其从屏幕的左边缘发送出去。将它带回来很简单，只需将它的位置设置为 relative 或 static，或者给它一个 left 值，将它带回到页面的可视区域。

##### 包装礼物

所以我们重新加载了 JavaScript 并隐藏了代码影响的内容，但是仅仅将它弹出到屏幕上并不是很优雅，而且它也没有给访问者任何关于有内容的提示。这有点像圣诞礼物:直到分发的时候，你才把它们打开放在你的衣柜里。你把它们包起来，把它们留在外面，这样人们就知道他们会有所收获。这同样适用于您正在处理但保持隐藏的内容。表示某事即将发生的最常见方式是使用动画图形作为视觉提示。

让我们为我们的“登录”锚添加一个加载器:

```
loader.css  

.modal {  

    background: url(loading.gif) no-repeat center left;  

}  

.modal a {  

    visibility: hidden;  

}  

loader.html  

<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN" "https://www.w3.org/TR/html4/strict.dtd">  

<html>  

  <head>  

    <title>Welcome</title>  

    <link rel="stylesheet" type="text/css" media="all" href="style.css">  

    <script>  

      document.write('<link rel="stylesheet" type="text/css" href="loader.css">');  

    </script>  

  </head>  

  <body>  

    <div class="xmas">  

      <p class="modal">  

        <a href="/signin/" id="signin">Sign in</a>  

      </p>  

      <!-- 700 kilobytes worth of media goes here -->  

      <script src="loader.js"></script>  

    </div>  

  </body>  

</html>  

loader.js  

var signin = document.getElementById("signin");  

signin.onclick = function () {  

  /*  

   * Sign-in code that creates a modal  

   *  popup goes here.  

   */  

  alert('Pretend this is a modal popup...');  

  return false; // Stop the anchor's default behavior  

};  

signin.style.visibility = "visible";  

signin.parentNode.style.background = "none";
```

我在这里做的第一件事是创建单独的 CSS 和 JS 文件，因为我们的示例已经增长，将 CSS 和 JavaScript 放在单独的文件中总是更好。我添加了一个新的 CSS 背景规则，向我们的“登录”锚的父元素添加一个加载器图形。所以当锚点被隐藏时，它的父元素会显示一个旋转的图形，表示某个东西会暂时占据这个空间。我还将模态类名移到了锚的父元素，因为我们需要它来保存我们的加载图形。最后，我在我们的代码块中增加了一条指令；一旦我们的`onclick`赋值操作完成，它就删除加载器图形。

由于这个例子很小，您不太可能经历足够长的延迟来查看加载器图形。出于这个原因，我把一个模拟两秒钟延迟的例子放在一起[，这样你就可以看到加载器在运行。](https://www.sitepoint.com/examples/unobtrusivejs/05-loader-lag.html)

##### 包装其他所有东西

这种技术不仅限于文本内容；我们还可以给图像添加加载器。然而，我们将设置一个事件处理程序来检测浏览器何时完成图像下载，而不是手动触发从加载器到内容的切换。我们将通过它的 onload 事件处理程序来实现这一点。一旦事件被浏览器触发，我们的代码将处理切换。

在这个例子中，我们将做一点不同的事情，以便我们可以探索不同的实现可能性。在前面的例子中，我们直接通过 JavaScript 操作元素的样式对象。这种方法并不总是合适的，因为设计者可能希望通过 CSS 更直接地控制元素的不同状态。对于这个例子，我们将定义一个加载类名，它将被分配给正在加载的元素。一旦加载完成，我们要做的就是删除类名。

让我们从标记开始:

```
loader-img.html   

<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN" "https://www.w3.org/TR/html4/strict.dtd">   

<html>   

  <head>   

    <title>Welcome</title>   

    <link rel="stylesheet" type="text/css" href="loader-img.css">   

    <link rel="stylesheet" type="text/css" media="all" href="style.css">   

    <script>   

      document.write('<link rel="stylesheet" type="text/css" href="loader-img-js.css">');   

    </script>   

  </head>   

  <body>   

    <ul id="thumbnails">   

      <li class="loading"><img src="img1.jpg"></li>   

      <li class="loading"><img src="img2.jpg"></li>   

      <li class="loading"><img src="img3.jpg"></li>   

      <li class="loading"><img src="img4.jpg"></li>   

      <li class="loading"><img src="img5.jpg"></li>   

      <li class="loading"><img src="img6.jpg"></li>   

      <li class="loading"><img src="img7.jpg"></li>   

      <li class="loading"><img src="img8.jpg"></li>   

      <li class="loading"><img src="img9.jpg"></li>   

      <li class="loading"><img src="img10.jpg"></li>   

      <li class="loading"><img src="img11.jpg"></li>   

      <li class="loading"><img src="img12.jpg"></li>   

      <li class="loading"><img src="img13.jpg"></li>   

      <li class="loading"><img src="img14.jpg"></li>   

      <li class="loading"><img src="img15.jpg"></li>   

    </ul>   

    <script src="loader-img.js"></script>   

    <p class="caption"><strong>Image Credit:</strong> <a href="http://www.sxc.hu/profile/danzo08/">Daniel Wildman</a></p>   

  </body>   

</html>
```

我们这里有一个简单的图片列表。每个列表项都被赋予类名 loading，因为我们知道在 DOM 创建时，图像还没有被下载。

我们还包含了两个 CSS 文件:一个包含基本的布局规则，另一个通过 JavaScript document.write 语句链接，它隐藏了 JavaScript 稍后会显示的内容:

```
loader-img.css   

#thumbnails {   

    list-style-type: none;   

    width: 375px;   

}   

#thumbnails li {   

    width: 125px;   

    height: 125px;   

    float: left;   

}   

loader-img-js.css   

#thumbnails li.loading {   

    background: url(loader-big.gif) no-repeat center center;   

}   

#thumbnails li.loading img {   

    visibility: hidden;   

}
```

最后，也是最重要的，下面是为我们的每个图像实现一个加载器的脚本:

```
loader-img.js   

var thumbs = document.getElementById("thumbnails");   

if (thumbs) {   

  var imgs = thumbs.getElementsByTagName("img");   

  if (imgs.length > 0) {   

    for (var i = 0; imgs[i]; i = i + 1) {   

      var img = imgs[i];   

      var newImg = img.cloneNode(false);   

      img.parentNode.insertBefore(newImg, img);   

      newImg.onload = function () {   

        var li = this.parentNode;   

        li.className = li.className.replace("loading", "");   

      };   

      newImg.src = img.src;   

      img.parentNode.removeChild(img);   

    }   

  }   

}
```

这里，我们抓取缩略图周围的容器元素，以及其中的所有图像。现在，通常我们只是循环遍历这些图像，并给它们分配一个`onload`事件处理程序。不幸的是，Firefox 不会对已经在缓存中的图像触发`onload`事件，所以我们的脚本在后续的页面访问中不起作用。为了解决这个问题，我们简单地克隆映像，并用它的克隆替换原始映像。将新克隆的图像插入到文档中的行为确保了它的 onload 事件将被触发。

另一点需要注意的是，Internet Explorer 和 Opera 要求在`src`属性之前分配`onload`事件处理程序。否则，他们不会炒`onload`事件。当事件被触发时，脚本将从图像的父元素中移除类名加载。这反过来会导致列表项丢失其旋转背景图像，并且图像丢失隐藏它的`visibility: hidden;`声明。在我看来，类名操作是切换元素样式的最优雅的方式，因为它将所有的表示信息保存在一个专用于该任务的单独文件中。它还允许未来通过修改 CSS 对样式规则进行更改，而不需要我们打开 JavaScript 文件。

如果这个例子对你来说运行得太快，我已经把另一个模拟随机延迟的例子放在一起，所以你可以想象这个例子在一个较慢的连接上是什么样子。

##### 后色氨酸时代的冒险

我们已经探索了一些安全处理不引人注目的 JavaScript 计时问题的方法。我们还研究了如何通过将加载器作为隐藏内容的占位符来处理时间问题。我希望这篇教程鼓励了你创造性地、不引人注目地思考(别忘了[下载完整的代码](https://www.sitepoint.com/examples/unobtrusivejs/code.zip))。现在，利用你在这里学到的知识，创建一个不引人注目的、可优雅降解的、令人愉快的网站。嗬！嗬！嗬！

## 分享这篇文章