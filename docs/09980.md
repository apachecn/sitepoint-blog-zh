# jQuery 插件

> 原文：<https://www.sitepoint.com/jquery-plugins/>

本文摘自厄尔·卡斯尔丁&克雷格·沙奇的《jQuery:忍者新手》。更多详情见下文。

“嘿，现在一切都准备好了——你能不能回去把第三阶段的菜单放到管理区？你能把上一阶段制作的酷列表添加到前端的列表中吗——并添加你做的滚动效果……你可以复制并粘贴代码，对吗？”

啊，复制/粘贴:我们的好朋友和最坏的敌人。当然，这看起来是一种快速实现功能的方法——但是我们都知道这种代码重用很容易变成我们最糟糕的 JavaScript 噩梦。我们不能让这种情况发生在我们美丽的 jQuery 作品上。

在整本书中，你已经看到 jQuery 插件架构是多么的有用。我们利用了第三方创造的各种方式——从样式化的滚动条，到图像库，到自动完成表单域。好消息是，将您的代码打包成插件以便在您的其他项目中重用是非常容易的——而且，如果您的代码非常特殊，在其他开发人员的项目中也是如此！

### 创建插件

在你有冲动把你的一些代码变成一个插件之前，你的 jQuery 编写生涯只需要很短的时间。没有什么比看到从 jQuery 链中间调用您自己的代码更好的了！最好的一点是，将现有的 jQuery 转换成插件是一个非常简单的过程，并且您可以根据自己的喜好进行定制。

#### 安装

在我们开始之前，我们需要一个插件的想法。一段时间以前，客户提到他想突出显示页面上的所有文本段落，这样当用户将鼠标移动到段落上时，文本将变得不突出显示，以表明它已经被阅读。虽然你会同意这远不是最好的用户界面想法，但演示如何制作一个插件足够简单，无需专注于效果代码本身。

要让插件像普通的 jQuery 动作一样可调用，我们所要做的就是给 jQuery *原型*附加一个函数。在 JavaScript 中，任何对象或内置数据类型的 prototype 属性都可以用新的方法或属性来扩展它。对于我们的插件，我们将使用核心`jQuery`对象本身的原型属性来添加我们的新方法。

最安全的(只有！)方法是为`jQuery`函数创建一个私有作用域。这个 JavaScript 技巧确保了您的插件能够很好地工作，即使在有人将$函数用于非 jQuery 目的的页面上也是如此:

```
(function($) {
  // Shell for your plugin code
})(jQuery);
```

这段代码可以放在脚本中的任何地方，但是标准的做法是将它放在一个名为`jquery.pluginname.js`的单独的 JavaScript 文件中，并像包含任何插件一样包含它。现在您有了一个独立的文件，您可以在将来的项目中轻松地使用它，或者与全世界共享它！

在这个保护壳中，我们可以不受惩罚地使用$ alias。这就是所有的准备工作——所以是时候开始写插件了。首先我们需要给它一个名字，highlightOnce，并把它附加到 jQuery 插件钩子上，`$.fn`:

```
 (function($) {
  // Shell for your plugin code
  $.fn.highlightOnce = function() {
    // Plugin code
  }
})(jQuery);
```

内部`$.fn`是 jQuery.prototype JavaScript 属性的快捷方式——这是放置插件的最佳位置。这是 jQuery 放置动作的地方，所以现在我们已经添加了自定义动作，我们可以调用它，就像它内置在 jQuery 中一样。

在这一点上，我们的代码*看起来*像一个 jQuery 插件，但是它的行为并不像一个 jQuery 插件——还有一个任务要做。如果我们现在要在插件代码中执行一些操作，我们实际上是在一次性处理*整个选择*；例如，如果我们运行`$('p').highlightOnce()`，我们将把每个段落元素作为单个选择来操作。我们需要做的是处理每个元素，一次一个，并返回元素，这样 jQuery 链才能继续。下面是插件的一个相当标准的构造:

```
// Plugin code
return this.each(function() {
  // Do something to each item
});
```

所以你现在有了一个创建简单插件的框架。保存这个大纲，这样你就可以随心所欲地快速创建新的插件了！

#### 添加插件的功能

我们的 highlightOnce 插件已经准备好了，所以让我们给它一份工作。到目前为止，我们添加的所有结构只是脚手架——现在是时候创建一个建筑了！我们可以在插件内部运行的代码类型与我们习惯编写的代码完全相同；我们可以用`$(this)`结构访问当前对象，并执行我们需要的任何 jQuery 或 JavaScript 代码。

我们的插件需要完成的第一个功能是高亮显示每个选中的元素，所以我们将背景设置为亮黄色。接下来，我们需要处理用户鼠标经过元素时的情况，这样我们就可以去掉高亮显示。我们只希望这种情况发生一次，只要元素褪回原来的颜色(别忘了，我们需要 jQuery UI Effects 组件来实现这一点):

```
// Do something to each item
$(this)
  .data('original-color', $(this).css('background-color'))
  .css('background-color', '#fff47f')
  .one('mouseenter', function() {
    $(this).animate({
      'background-color': $(this).data('original-color')
    }, 'fast');
  });
```

恰好有一个 jQuery 动作正好符合我们的需求:one action。从功能上来说，`one`动作与我们之前看到的`bind`动作是相同的，因为它让我们将一个事件处理程序附加到我们的元素上。与 one 的区别在于事件只会运行*一次*，之后事件会自动解除自身绑定。

对于我们的代码，我们将当前背景色保存在元素的`data`存储中，然后将`mouseover`事件绑定到所选的 DOM 元素。当用户将鼠标放在元素上时，我们的代码就会运行，背景颜色就会变回原来的颜色。这样，我们的插件就可以使用了:

```
$('p')
  .hide()
  .highlightOnce()
  .slideDown();
```

这非常令人兴奋:我们的功能被捕获在一个可链接、可重用的插件中，我们把它放在了`hide`和`slideDown`动作之间。看看只需要 11 行代码(其中 6 行是标准的插件脚手架！)，你可以看到把你打算重用的任何功能变成一个插件是值得的！

#### 添加选项

jQuery 插件是产生可重用代码的一种很好的方式，但是要真正有用，我们的插件需要在我们创建它们的上下文之外适用:它们需要*可定制*。我们可以在插件中添加用户指定的选项，当插件投入使用时，这些选项可以用来修改插件的行为。

从插件用户的角度来看，我们很熟悉选项是如何工作的，因为我们已经将选项传递给了我们在整本书中使用的每个插件。选项让我们以微妙和更明显的方式修改插件的功能，这样它就可以在我们能想象到的尽可能多的情况下使用。

有两种类型的插件选项:简单值和对象文字。让我们从更简单的开始，看看它是如何工作的。对于我们的 highlightOnce 插件，硬编码颜色似乎很有限。我们想让开发者选择用他们喜欢的任何颜色来突出他们的元素。让我们把它作为一种选择:

```
$.fn.highlightOnce = function(color) {
  ⋮
  $(this).css('background-color', color  || '#fff47f')
  ⋮
};
```

插件可以用颜色调用，但也可以不带参数调用——在这种情况下，将使用默认值(感谢 JavaScript ||操作符)。让我们用绿色突出显示我们的段落:

```
$('p')
  .hide()
  .highlightOnce('green')
  .slideDown();
```

如果您有一两个总是需要的简单选项，这种方法很好。但是当你的插件开始变得更复杂时，你会有很多设置，你的用户会想要覆盖一些而保留其他的默认值。这就是我们转向更复杂的对象文字符号的地方。

这并不可怕—您已经知道如何定义这种类型的设置对象。我们已经将它们用于`animate`、`css`和大多数 jQuery UI 组件。键/值对象的好处是只需要定义一个参数，用户可以在其中指定多个设置。我们的第一步是为每个选项设置默认值:

```
$.fn.highlightOnce.defaults = {
  color : '#fff47f',
  duration : 'fast'
}; 
```

我们现在将默认值作为一个对象，所以我们需要利用 jQuery `$.extend`函数。这个方便的函数有多种用途，但就我们的目的而言，我们将用它来扩展一个对象，方法是添加另一个对象的所有属性。这样，我们可以用我们的默认设置扩展用户传入的选项:插件已经为每个选项指定了值，如果用户指定了其中一个值，默认设置将被覆盖。完美！让我们看看代码:

```
$.fn.highlightOnce = function(options) {
  options = $.extend($.fn.highlightOnce.defaults, options);

  return this.each( … );
};
```

我们的`options`变量包含了正确的设置——无论它们是由用户定义的，还是由默认对象定义的。现在我们可以使用代码中的设置了:

```
$(this)
  .data('original-color', $(this).css('background-color'))
  .css('background-color', options.color)
  .one('mouseenter', function() {
    $(this).animate({
      'background-color': $(this).data('original-color')
    }, options.duration);
  });
```

作为插件用户，我们可以指定高亮显示的颜色或持续时间——或者接受默认值。在下面的示例中，我们将接受默认颜色，但是将持续时间覆盖为 2，000 毫秒，而不是“快速”:

```
$('p')
  .hide()
  .highlightOnce({duration: 2000})
  .slideDown();
```

#### 添加回拨

您已经看到回调函数和事件是多么有用。整本书中的许多效果和控件都依赖于它们——我们使用的许多插件让我们可以访问回调来定制它们的功能。回调是一种机制，根据插件内部发生的事件，给插件的用户一个运行他们自己代码的地方。一般来说，您会对想要向用户公开什么事件有一个相当好的想法。例如，对于我们的 highlightOnce 插件，我们可能希望在设置效果时、效果结束时以及淡出开始时运行额外的代码。

为了演示，让我们尝试公开一个`setup`事件(它将在附加了`mouseover`处理程序后运行)和一个`complete`事件(它将在最后的`animate`动作结束后运行):

```
$.fn.highlightOnce.defaults = {
  color : '#fff47f',
  duration : 'fast',
  setup : null,
  complete: null
};
```

默认情况下，回调函数不应该做任何事情，所以我们将它们设置为 null。当运行回调的时候，有几种可能的方法。如果我们的回调需要在 jQuery 回调的地方运行，我们可以简单地将用户传入的函数提供给 jQuery 动作。否则，我们需要在适当的位置手动调用该函数:

```
$(this)
  .data('original-color', $(this).css('background-color'))
  .css('background-color', options.color)
  .one('mouseenter', function() {
    $(this).animate(
      {'background-color': $(this).data('original-color')},
      options.duration,
      options.complete
    );
  });

  // Fire the setUp callback
  $.isFunction(options.setup) && options.setup.call(this);
```

上面我们可以看到两种类型的回调。`complete` 回调处理程序很简单:当`animate` 动作完成时效果也完成了，并且`animate` 动作本身接受一个回调函数，所以我们只需传递函数。然而`setup` 处理程序就没这么幸运了——我们必须自己解雇它。我们转向 jQuery 和一些高级 JavaScript 来执行代码。首先，我们检查回调是否是一个带有方便的返回布尔值的`$.isFunction` jQuery 动作的函数:`true` 如果回调是一个函数，则`false` 如果不是。如果是后者(很可能是因为用户保留了默认值，在这种情况下，它仍然是`null`)，那么尝试执行它就没有意义了！

### 更多实用功能

除了`$.isFunction`，jQuery 还提供了以下函数:`$.isArray`(用于测试变量是否为数组)`$.isPlainObject`(用于简单的 JavaScript 对象)`$.isEmptyObject`(用于没有属性的对象)。这些函数为您提供了许多确定 JavaScript 构造的性质和属性的方法。

如果回调*已经定义了*，我们需要运行它。有几种方法可以运行 JavaScript 函数，最简单的方法就是调用它:`options.setup()`。这将运行良好，但问题是它是在`default`对象的范围内被调用的，而不是在事件的目标元素的范围内(正如我们习惯的那样)。因此回调函数将无法确定它正在处理哪个 DOM 元素。为了补救这一点，我们使用 JavaScript 方法`call`。您传递给`call`的第一个参数将覆盖您正在调用的方法中的`this`。在我们的例子中，我们传入`this`，这是我们想要的 DOM 元素。

更正了范围后，我们现在可以在`complete`事件处理程序中使用`$(this)`来滑动元素，一旦效果完成并完成:

```
$('p')
  .hide()
  .highlightOnce({
    color: '#FFA86F',
    complete: function() {
      $(this).slideUp();
    }
  })
  .slideDown();
```

#### jQuery 风格的回调

您可能已经注意到，jQuery 回调似乎比我们插件的命名事件集成得更好。例如，在`hide`动作中，可以将回调函数指定为第一个(也是唯一的)参数，或者可以先包含速度参数，然后再包含回调函数。没有必要像上面那样包含一个键/值对。秘诀是什么？这看起来有点像 JavaScript 黑客。如果您检测到第一个参数是一个函数，而不是一个对象，那么您可以假定只指定了一个回调，所以您将参数移过来。

这里有一个来自 jQuery 核心库的(被删节的)例子，是 Ajax `load`动作的一部分。`params` 参数是可选的，所以如果不提供它，第二个参数被认为是回调:

```
load: function( url, params, callback ){
  // If the second parameter is a function
  if ( jQuery.isFunction( params ) ){
    // We assume that it's the callback
    callback = params;
    params = null;
```

`params` 参数应该是一个填充了各种设置的对象。但是如果我们发现它实际上是一个函数，我们就把这个函数赋给`callback` 变量，并清除`params` 变量。这是一个很酷的技巧，也是让你的插件感觉更像 jQuery 的好方法。

让我们修改我们的`highlightOnce`插件来使用这个回调检测技巧:

```
$.fn.highlightOnce = function(options, callback) {
  if ($.isFunction(options)) {
    callback = options;
    options = null;
  }
  options = $.extend($.fn.highlightOnce.defaults,options);

  return this.each(function() {
    // Do something to each item
    $(this)
      .data('original-color', $(this).css('background-color'))
      .css('background-color', options.color)
      .one('mouseenter', function() {
        $(this).css('background-color', '');
        $.isFunction(callback) && callback();
      });
  });
};
```

**note:**Want more?

在 Earle castle dine&Craig Sharkie 的《jQuery:忍者新手》上查看这本书并在线购买。你也可以下载该书的[代码档案](https://www.sitepoint.com/books/jquery1/code.php)，查看[的更新和勘误表](https://www.sitepoint.com/books/jquery1/errata.php)，或者在 [SitePoint 论坛](https://www.sitepoint.com/forums/)上咨询蓬勃发展的 JavaScript 和 jQuery 开发者社区。

## 分享这篇文章