# 机器人工厂——第二部分

> 原文：<https://www.sitepoint.com/the-robot-factory-part-two/>

## 在 Sinatra 中异步添加和删除资源

![](img/38628173224c545729d7972ce611aa67.png)

在本教程的第一部分中，我们讲述了如何在数据库中添加和删除机器人。在本教程中，我将展示如何利用 XMLHttpRequest 在后台执行操作，而无需刷新页面。没错——我们要用一些 Ajax 加速应用程序！除非你在过去的五年里一直躲在山洞里，否则你可能听说过[Ajax](http://en.wikipedia.org/wiki/Ajax_(programming))——它基本上是一种在后台发布和检索数据的方法，通常用于更新网页的部分内容，而无需刷新整个页面。

机器人工厂第一部分的代码在添加 Ajax 之前就可以工作，这是一件好事，因为这意味着它可以在任何没有启用 JavaScript 或不支持 XMLHttpRequest 的浏览器上工作(今天的孩子们称之为[渐进增强](http://www.alistapart.com/articles/understandingprogressiveenhancement))。添加 Ajax 基本上是一个 3 步的过程:

1.  阻止实际的请求发生，这样页面就不会重新加载
2.  将新资源的 html 插入到页面上，可能会产生某种神奇的效果
3.  在后台将新资源保存到数据库中

我将使用奇妙的 [RightJS JavaScript 库](http://rightjs.org/)来帮助处理 Ajax。

首先，我们需要在布局中包含必要的 JavaScript 文件:

```
script src="http://cdn.rightjs.org/right.js"
  script src="/application.js"
```

接下来，我们需要一个路由，指定 JavaScript 将被写入同一个文件:

```
get('/application.js') { content_type 'text/javascript' ; render :str, :javascript, :layout => false }
```

最后，我们需要一个地方来放置我们将要添加的自定义 JavaScript。这位于页面底部所有其他视图的下方:

```
@@javascript
// javascript goes here
```

## 使用 Ajax 删除

我们通过添加一些 JavaScript 来做到这一点。我们可以把它写成另一个内嵌视图，放在所有其他视图代码的下面，实际上 JavaScript 并不需要太多，但它确实依赖于一些 RightJS 的魔力。我们将从使删除请求变得更快开始:

```
@@javascript

"form.destroy".onSubmit(function(event) {

   this.parent().fade();

   event.stop();

   this.send();

 });
```

这段 JavaScript 使用了令人敬畏的 UJS 语法，RightJS 使用该语法来查找所有符合 css 规则“form.destroy”的元素(即所有具有“destroy”类的表单，即每个机器人上的删除按钮)。然后，它会向其中动态添加以下事件:

1.  父节点(即机器人本身)将使用 RightJS 内置的漂亮渐变效果逐渐消失。
2.  表单的实际操作将被停止，因此它不会以正常方式发送
3.  这将使用 Ajax 发送表单。

RightJS 的一个很好的优点是，这个函数适用于所有带有 *destroy* 类的表单——甚至是在 JavaScript 文件加载后动态创建的表单，就像我们将在下一节中做的那样。在此之前，我们必须考虑当 Sinatra 异步接收删除请求时会发生什么。目前，我们的处理程序如下所示:

```
delete '/delete/robot/:id' do

 Robot.get(params[:id]).destroy

 redirect to('/')

end
```

这很好，除了如果我们使用 Ajax，我们实际上不想通过重定向来刷新页面。Sinatra 里有一个很棒的方法(嗯，其实是从 [Rack](http://rack.rubyforge.org/doc/classes/Rack/Request.src/M000283.html) 来的)叫做 request.xhr？如果请求是 Ajax 请求，它将返回 **true** ，否则返回 false。当使用 Ajax 时，我们可以用它来停止重定向:

```
delete '/delete/robot/:id' do

 Robot.get(params[:id]).destroy

 redirect to('/') unless request.xhr?

end
```

## 使用 Ajax 添加

现在让我们看看如何使用 Ajax 构建一个机器人。下面的 JavaScript 位于前面的代码块下面:

```
"form.build".onSubmit(function(event) {

 event.stop();

 this.send({

   onSuccess: function(xhr) {

   $('robots').insert(xhr.responseText);

  }

 });  

});
```

这与上面的删除功能类似。它将这一点应用于所有具有“构建”类的表单(即大的“构建机器人”按钮)。它做的第一件事是停止发送表单，然后使用 Ajax 发送它。这个函数还包含一个回调函数，当 Ajax 请求成功时就会发生这个回调函数。它要求将响应文本插入到 id 为“robots”的列表中。基本上，这将在列表的末尾添加一个新的机器人。要做到这一点，我们需要确保机器人被归还。此时，处理程序如下所示:

```
post '/build/robot' do
 robot=Robot.create
 redirect to('/')
end
```

这只是创造了一个新的机器人。如果机器人是使用 Ajax 创建的，我们需要做一些不同的事情，所以我们再次请求。方法:

```
post '/build/robot' do

 robot=Robot.create

 if request.xhr?

   slim :robot, { :layout=> false, :locals => { :robot => robot } }

 else

   redirect to('/')

 end

end
```

在机器人被创建并保存到数据库之后，我们检查请求是否是 Ajax 请求。如果它是我们之前创建的机器人视图的结果(告诉过你它会很有用)。我们包含了一些额外的选项，以确保我们没有在这个代码片段中包含布局(`:layout=>false`)，并且包含了新的 robots 参数(`:locals => { :robot => robot }`)。如果它不是一个 Ajax 请求，那么我们就像以前一样做——重定向回主页，新的机器人将在请求处理时被绘制出来。

## 旋转

剩下要做的唯一一件事就是添加一个很酷的 Ajax spinners，显示后台正在发生的事情。前往[http://www.ajaxload.info/](http://www.ajaxload.info/)制作一个你最喜欢的旋转器设计的 gif。将它保存在与 Sinatra 代码相同目录下的“public”文件夹中，然后对代码进行以下修改。在视图中，将图像添加到索引页面(确保给它一个容易记住的 id，我使用的是‘waiting’):

```
@@index

…

img#waiting src="https://s3.amazonaws.com/daz4126/waiting.gif"

Add some css, so that the spinner is not displayed by default:

@@styles

…

#waiting{display:none;position:absolute;left:200px;top:100px;}
```

然后在 JavaScript 中，告诉 RightJS 微调器的 id:

```
@@javascript

Xhr.Options.spinner = 'waiting';

...
```

就是这样！我们使用 Ajax 调用使界面更加快捷，避免了每次添加和删除机器人时都要重新加载页面。这也意味着数据库检索@robots 数组的频率不会那么高。该页面也有一个更好的感觉，消失的效果为应用程序增加了一些额外的波兰。当然，你可以在其他 JavaScript 框架中很容易地做到这一点，但是我觉得 RightJS 提供了一种非常简洁和直接的处理 Ajax 的方法。我很乐意在评论中听到关于这一点的任何想法，以及你在 web 应用中使用 Ajax 的经验。

你可以在 github 上看到[的完整代码，并且用我的版本](https://gist.github.com/1110005)玩一玩[。](http://robotfactory.heroku.com/)

享受建造机器人的乐趣！

## 分享这篇文章