# 控制您的 Javascript

> 原文：<https://www.sitepoint.com/getting-your-javascript-under-control/>

![js_control](img/eb91f77765c6e7e1cdf7158cbfb0e5c9.png)您还记得在 2011 年 RailsConf 上正式宣布的资产管道吗？是的，我觉得这很酷。就像 Rails 中的几乎所有东西一样，有些人喜欢它，有些人讨厌它。无论哪种方式，它都引起了轩然大波，然后在同一时间，DHH 宣布 CoffeeScript 和 SCSS 违约。BOOOM！

老实说，在 2013 年，Rails 3.1 发布近两年后，我们不再考虑它。在开发 Rails 应用程序时，它已经成为我们工作流程的一部分。不需要额外的努力，我们就可以在应用程序的客户端获得性能提升。借助 CoffeeScript，我们正在编写更好的最佳实践 JavaScript，并且不再配置资产打包程序来连接和缩减 JavaScript 和 CSS。它就这样发生了。

我还认为它改变了我们开发应用程序客户端的方式。在过去的一年里，我花在 JavaScript 上的时间可能比以往任何时候都多。仅仅是因为按照我想要的方式建立不再是一件痛苦的事情。我可以让它有条理，更重要的是，现在它真的很容易测试。

现代浏览器中 JavaScript 引擎的指数级改进使得复杂的 JS 框架能够以漂亮、敏捷和响应迅速的 web 应用程序的形式发展和繁荣。

本系列是从使用 Rails 创建丰富的 JS 支持的 UI 的第一线学到的一种经验，重点是如何控制我们的客户端代码，并且永远不要回到过去的“垃圾抽屉”开发时代。

## 以巨大的力量，雅达，雅达，雅达

我这么说是轻率的，但是随着我们应用程序的客户端变得越来越大，越来越复杂，我们真的应该采用我们为 Ruby 代码所采用的相同的实践。

如果试驾你的 JavaScript，请举手。

这些天我仍然希望只看到几手牌。大概一年前我肯定没有试驾过。我有测试，但通常是在代码之后很久才写的。当我写它们的时候，它们是脆弱和难以管理的。然后有一天，我在一堆 jQuery 和各种相关插件中被一个讨厌的 bug 蛰了。我已经过了不可收拾的地步。最人道的做法是把这个可怜的东西放下。

我选择采用某种框架([主干](http://backbonejs.org/))和测试框架([茉莉](http://pivotal.github.com/jasmine/))。即使对于琐碎的任务，我也会创建主干视图或模型，驱动它通过测试并应用。Backbone 之类的东西的美妙之处在于，通过它的**随意使用**的精神，我可以将它应用到所有的用例中，不管场景有多复杂(或不复杂)。我也可以一次切掉一个逻辑块，而不是全力以赴。

我并不是说这绝对是处理新的或现有应用程序的正确方法，但这肯定是正确的方法(T2)。

让我们举一个非常简单、愚蠢的场景来说明。让我想到的是让我爱上 rails 的一个简单、免费的东西，flash。我们希望我们的闪光灯出现一个关闭的“X”控件，如果我们悬停在闪光灯上，它会褪色，单击“X”它会消失，这一切都很像咆哮。我们做的第一件事是写一个测试。

因为我们将使用 Jasmine 作为我们的测试框架，我们将使用 [jasminerice](https://github.com/bradphelan/jasminerice) gem。这消除了我们在处理资产管道时的痛苦。要启动并运行 jasminerice，您只需将宝石放入您的`Gemfile`

```
group :development, :test do
  gem "jasminerice"
end
```

运行生成器`rails g jasminerice:install`，它将在您的`spec`目录中建立一个`javascripts`目录(假设您使用的是 rspec)和一些帮助文件、一个虚拟测试和一些 fixtures。测试主干视图时，夹具非常重要。一会儿你会看到我们如何使用它们。

一旦在应用程序中设置了 jasminerice，删除已经安装的虚拟测试，并创建一个名为`spec/javascripts/fixtures/flash.html`的 fixture，其内容如下

```
<div class="flash"><p>There is a message for the user <span class="close-alert">X</span></p></div>
```

这个 fixture 本质上是我们将在应用程序中使用的标记。它可能略有不同，但基本成分是外层`div`上的类别`flash`和内层`span`上的`close-alert`。**现在**我们有足够的时间来编写测试。

用以下内容创建一个文件`spec/javascripts/flash_spec.js.coffee`。

```
describe "Closing the alert box", ->

  beforeEach ->
    loadFixtures 'flash'

  it 'will close on a click of the cross', ->
    view = new App.Flash()
    $('.close-alert').trigger('click')
    expect(view.$el).not.toBeVisible()
```

在上面的规范中，我们首先将 fixture 加载到 DOM 中，也就是说，我们将它放在未启动的 spec runner 页面上。Jasminerice 包括助手`loadFixtures`,它创建一个容器`jasmine-fixtures`,并在其中附加加载的 fixtures。当规范完成时，它从 DOM 中删除了 fixture，这是一个相当漂亮的助手。

规范的其余部分设置 flash 的一个新实例，触发对 close 元素的单击，并断言 DOM 元素不再可见。

对于那些没有太多主干经验的人来说，`$el`是视图的容器。默认情况下，它是一个普通的老式`div`，但是你可以随意设置。我不会过多地讨论这个问题，但是请注意，您在上面的测试中看到的`$el`属性是一个主干助手，用于传递容器元素的选择器，它相当于 jQuery 中的`$('<your-selector')`。但是我们可能永远不知道视图的`<your-selector>`(动态设置，或者集合中单个视图上的多个元素)，所以最好使用`$el`。

通过将浏览器指向[http://localhost:3000/jasmine](http://localhost:3000/jasmine)来运行该套件。正如所料，我们得到了一个有足够有效的失败的红色套件。如果我们在第一次获得绿色时作弊，创建一个文件`app/assets/javascripts/app.js.coffee`,看起来像这样:

```
window.App = {}

App.Flash = Backbone.View.extend
  el: '.flash'

  events: {
    'click .close-alert': 'closeAlert'
  }

  closeAlert: ->
    this.$el.fadeOut()
```

这里我们为应用程序设置了一个名称空间。从我们在全局名称空间中隔离代码的意义上来说，这是一个很好的实践。它仍然是一个全局的，但是我们将把我们的使用限制在一个已知的合理的名称空间，`App`。

然后，我们通过设置`el`属性并将 click 事件绑定到它的处理程序来扩展主干视图。你必须喜欢 Backbone 的简单性(尤其是以 CoffeeScript 的形式)。我特别喜欢事件在视图领域内的绑定方式`el`,这样就不用在`e.currentTarget`处理程序上添加唯一的 id 了。

再做一次测试，我们还是会红。这是怎么回事？jQuery 坏了吗？当然不是，记住我们正在处理一个事件的测试。`fadeOut`上的 jQuery 默认动画时间是 400 毫秒。我们的测试触发了事件，并且几乎立即运行了预期。让我们使用无知睡眠等效方法来解决这个问题:

```
it 'will close on a click of the cross', ->
    view = new App.Flash()
    $('.close-alert').trigger('click')
    waits 410
    runs ->
      expect(view.$el).not.toBeVisible()

it 'will close on a click of the cross', ->
```

好了，套件现在应该是绿色的，但我们已经注入了 410 毫秒的延迟。我们**知道**那不可能是好事。

## 伟大的斯科特

是时候介绍测试 JavaScript [sinon.js](http://sinonjs.org/) 的最佳方法了。它就像巧克力和花生酱一样。Sinon 是测试 JS 的真正实用带。您可以模仿、窥探、存根和调用匹配器断言。最棒的是，它与 Jasmine 配合得非常好(据我所知，还有所有其他测试框架)。我们将使用 sinon 来弯曲时间本身，并消除使我们的测试套件变慢的讨厌的等待/运行组合。

要在应用程序中包含 Sinon，只需将 sinon.js 下载到新创建的`spec/javascripts/support`目录中。并在规范中包括以下内容:

```
it 'will close on a click of the cross', ->
    clock = sinon.useFakeTimers();
    view = new App.Flash()
    $('.close-alert').trigger('click')
    clock.tick(410)
    expect(view.$el).not.toBeVisible()
```

就这样，我们又有了闪电快速测试套件。说实话，Sinon 真的让我大吃一惊，将时钟刻度设置为 110，你会看到规格失败，因为不透明度仍然处于过渡中期。在我看来，这真是一件美丽的作品。

接下来，我们希望当我们悬停在闪光灯上时，它会稍微变暗。一开始是一个很奇怪的 UI。当我第一次开始使用 Ubuntu 的时候，这些通知确实让我有点恼火，但是我已经习惯了，尤其是当通知覆盖在 UI 的其他部分的时候，这些部分可能包含重要的信息。无论如何，目前我们只是举例说明一个例子。

测试这个非常类似于淡出闪光灯，只是我们要使用一个新的匹配器。

```
it 'will fade slightly when the mouse hovers over it', ->
    clock = sinon.useFakeTimers();
    view = new App.Flash()
    view.$el.trigger('mouseenter')
    clock.tick(110)
    expect(view.$el.css('opacity')).toBe(0.1)
```

使用上面的规范和下面的实现是行不通的。

```
App.Flash = Backbone.View.extend
  el: '.flash'

  events: {
    'click .close-alert': 'closeAlert',
    'mouseenter': 'mouseOver'
  }

  closeAlert: ->
    this.$el.fadeOut()

  mouseOver: ->
    this.$el.fadeTo(100, 0.1)
```

基本上，不透明度值的精度会有一些微小的偏差。因此，我们将使用`toBeCloseTo`匹配器来指定测试值的精度，在这种情况下，0.01 对我来说已经足够了。

```
expect(view.$el.css('opacity')).toBeCloseTo(0.01, 0.1)
```

淡入是微不足道的。然而，需要注意的是，由于我们将渐隐回不透明度值 1，Jasmine 实际上将 css 属性作为字符串读取。为了保持一致性，您可以使用传统的`parseInt`。

```
expect(parseInt(view.$el.css('opacity'))).toBe(1)
```

## 红色、绿色、重构、重构测试

到目前为止，我们已经写了一些非常难看的规范，有很多重复。重复是我最喜欢的代码味道，因为它很容易重构。删除重复内容后，我们得到的规范看起来像这样:

```
describe "flash alert box", ->

  beforeEach ->
    loadFixtures 'flash'
    @view = new App.Flash()
    @clock = sinon.useFakeTimers()

  @fireEvent = (event, element, clockTick=500)->
  element.trigger(event)
  @clock.tick(clockTick)

  it 'will close on a click of the cross', ->
    @fireEvent('click', $('.close-alert'))
    expect(@view.$el).not.toBeVisible()

  it 'will fade slightly when the mouse hovers over the container', ->
    @fireEvent('mouseenter', @view.$el)
    expect(@view.$el.css('opacity')).toBeCloseTo(0.01, 0.1)

  it 'will fade back in when the mouse leaves the container', ->
    @fireEvent('mouseleave', @view.$el)
    expect(parseInt(@view.$el.css('opacity'))).toBe(1)
```

正如你从重构后的测试中所看到的，(就像我们重构 [rspec](https://www.relishapp.com/rspec) 测试一样)我们正在将所有重复的设置转移到`beforeEach`函数中。我们还创建了一个助手`@fireEvent`，它接受事件、DOM 元素和时钟节拍。时钟刻度我已经设置为可选，默认为 500，因为我们使用 sinon 来伪造时钟，我们不必太担心，但它肯定值得记住默认值。

## 包扎

现在我们有一些技巧来测试我们的客户端代码。如果你不认为“为一个简单的 flash 横幅写这么多代码”的话，我会感到非常震惊，在某种程度上，你是正确的。作为开发人员，我们都需要完成工作。但是为什么我们质疑简单 JavaScript 的测试，而不是 rspec shoulda-matchers 之类的东西。如果你质疑它，酷。在一天结束时，我们必须评估和权衡测试给我们带来的价值。

我花了大约 10 分钟的时间来测试和重构这个例子。在过去的一年里，我已经进行了大量的编写“愚蠢”测试的练习，所以花 10 分钟为一个几乎随机出现在我的应用程序中的元素编写一些规范是可以接受的。

在某种程度上，我可能希望 ajax 化表单提交中的 flash，在一个好的主干结构中已经有它将使这种转换更容易。另外，演示代码中有一个 bug。这是一个小问题，但我知道有测试在适当的位置，这将是直截了当的扫平(鼠标进入-关闭-等待-鼠标离开)。

本系列的下一部分将更多地关注测试技术和技巧，以及最终让我们的 JavaScript 处于持续集成(CI)之下。然后，我们将完成(DUN DUN DUN) Internet Explorer 系列，我的意思是测试我们的 JavaScript 跨浏览器/平台。

## 分享这篇文章