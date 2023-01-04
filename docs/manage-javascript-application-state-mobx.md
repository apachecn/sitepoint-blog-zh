# 如何用 MobX 管理您的 JavaScript 应用程序状态

> 原文：<https://www.sitepoint.com/manage-javascript-application-state-mobx/>

![Mission control watching a rocket to another planet](img/47ecb3b9af997a142a79afe8b644704c.png)

这篇文章由 Michel Weststrate 和 Aaron Boyer 进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！

如果您曾经使用 jQuery 编写过非常简单的应用程序以外的东西，那么您可能会遇到保持 UI 不同部分同步的问题。通常，数据的变化需要反映在多个位置，随着应用程序的增长，您会发现自己被束缚住了。为了抑制这种疯狂，通常使用事件让应用程序的不同部分知道什么时候发生了变化。

那么，现在如何管理应用程序的状态呢？我要大胆地说，你对变革的认同过度了。没错。我甚至不认识你，我要叫你出来。如果你没有超额订阅，那么我敢肯定你工作太努力了。

当然，除非你用的是 MobX

## “国家”到底是什么？

这里有一个人。嘿，那是我！我有一个`firstName`、`lastName`和`age`。
另外，`fullName()`功能可能会在我遇到麻烦的时候出现。

```
var person = {
  firstName: 'Matt',
  lastName: 'Ruby',
  age: 37,
  fullName: function () {
    this.firstName + ' ' + this.lastName;
  }
}; 
```

您将如何通知您的各种输出(视图、服务器、调试日志)对那个人的修改？你什么时候触发这些通知？在 MobX 之前，我会使用 setters 来触发定制的 [jQuery 事件](http://api.jquery.com/category/events/)或 [js 信号](https://millermedeiros.github.io/js-signals/)。这些选项为我提供了很好的服务，但是，我对它们的使用远远没有细化。如果`person`对象的任何部分发生变化，我将触发一个“changed”事件。

假设我有一段显示我名字的视图代码。如果我改变我的年龄，该视图将会更新，因为它与那个`person`的`changed`事件相关联。

```
person.events = {};

person.setData = function (data) {
  $.extend(person, data);
  $(person.events).trigger('changed');
};

$(person.events).on('changed', function () {
  console.log('first name: ' + person.firstName);
});

person.setData({age: 38}); 
```

我们怎样才能控制过度燃烧呢？简单。只需为每个字段设置一个 setter，并为每个更改设置单独的事件。等等——如果你想同时改变`age`和`firstName`,你可能会开始过度开火。您必须创建一种方法来延迟事件的触发，直到两个更改都完成。那听起来像工作，而且我很懒…

## MobX 前来救援

MobX 是由 Michel Weststrate 开发的一个简单、专注、高性能且不引人注目的状态管理库。

从 MobX 文档中:

> 只要对状态做点什么，MobX 就会确保你的应用尊重这些改变。

```
var person = mobx.observable({
  firstName: 'Matt',
  lastName: 'Ruby',
  age: 37,
  fullName: function () {
    this.firstName + ' ' + this.lastName;
  }
}); 
```

注意到区别了吗？是我唯一的改变。
让我们再看看那个`console.log`的例子:

```
mobx.autorun(function () {
  console.log('first name: ' + person.firstName);
});

person.age = 38; // prints nothing
person.lastName = 'RUBY!'; // still nothing
person.firstName = 'Matthew!'; // that one fired 
```

使用`autorun`，MobX 只会观察已经被访问的内容。

如果你认为这很棒，看看这个:

```
mobx.autorun(function () {
  console.log('Full name: ' + person.fullName);
});

person.age = 38; // print's nothing
person.lastName = 'RUBY!'; // Fires
person.firstName = 'Matthew!'; // Also fires 
```

好奇吗？我知道你是。

## 核心 MobX 概念

### 可观察量

```
var log = function(data) {
  $('#output').append('<pre>' +data+ '</pre>');
}

var person = mobx.observable({
  firstName: 'Matt',
  lastName: 'Ruby',
  age: 34
});

log(person.firstName);

person.firstName = 'Mike';
log(person.firstName);

person.firstName = 'Lissy';
log(person.firstName); 
```

[在 CodePen 上运行](http://codepen.io/SitePoint/pen/BLwpBp?editors=0010)

MobX 可观察对象只是对象。在这个例子中，我没有观察到任何东西。这个例子展示了如何将 MobX 应用到现有的代码库中。用`mobx.observable()`或者`mobx.extendObservable()`就可以开始了。

### 自动运行

```
var person = mobx.observable({
  firstName: 'Matt',
  lastName: 'Ruby',
  age: 0
});

mobx.autorun(function () {
  log(person.firstName + ' ' + person.age);
});

// this will print Matt NN 10 times
_.times(10, function () {
  person.age = _.random(40);
});

// this will print nothing
_.times(10, function () {
  person.lastName = _.random(40);
}); 
```

[在 CodePen 上运行](http://codepen.io/SitePoint/pen/dpVNyb?editors=0010)

当你的可观察值改变时，你想做些什么，对吗？请允许我介绍一下`autorun()`，每当被引用的可观察对象发生变化时，它就会触发回调。注意在上面的例子中，当`age`改变时`autorun()`不会触发。

### 计算

```
var person = mobx.observable({
  firstName: 'Matt',
  lastName: 'Ruby',
  age: 0,
  get fullName () {
    return this.firstName + ' ' + this.lastName;
  }
});
log(person.fullName);

person.firstName = 'Mike';
log(person.fullName);

person.firstName = 'Lissy';
log(person.fullName); 
```

[在 CodePen 上运行](http://codepen.io/SitePoint/pen/wzAgKd?editors=0010)

看看这个`fullName`函数，注意它是如何不带参数的？MobX 将自动为您创建一个计算值。这是我最喜欢的 MobX 功能之一。注意到`person.fullName`有什么奇怪的地方吗？再看看。那是一个函数，你不用调用它就能看到结果！通常，你会叫`person.fullName()`而不是`person.fullName`。你刚刚遇到了你的第一个 [JS getter](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/get) 。

乐趣不止于此！MobX 将观察你的计算值的依赖关系的变化，并且当它们发生变化时，只有*运行。如果没有任何变化，将返回一个缓存值。参见下面的案例:*

```
var person = mobx.observable({
  firstName: 'Matt',
  lastName: 'Ruby',
  age: 0,
  get fullName () {
    // Note how this computed value is cached.
    // We only hit this function 3 times.
    log('-- hit fullName --');
    return this.firstName + ' ' + this.lastName;
  }
});

mobx.autorun(function () {
  log(person.fullName + ' ' + person.age);
});

// this will print Matt Ruby NN 10 times
_.times(10, function () {
  person.age = _.random(40);
});

person.firstName = 'Mike';
person.firstName = 'Lissy'; 
```

[在 CodePen 上运行](http://codepen.io/SitePoint/pen/jrGyWY?editors=0010)

在这里你可以看到我已经多次点击了`person.fullName`计算，但是只有当`firstName`或`lastName`被改变时，这个函数才会运行。这是 MobX 可以大大提高应用程序速度的方法之一。

### 更多！

我不会再继续重写 MobX 的精彩文档了。查看文档，了解更多处理和创建可观测量的方法。

## 让 MobX 发挥作用

在我让你厌烦之前，让我们做点东西。

下面是一个简单的非 MobX 的人的例子，每当这个人改变时，它将打印这个人的全名。

参见 [CodePen](http://codepen.io) 上 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )的 Pen [简单 MobX jQuery 示例](http://codepen.io/SitePoint/pen/NRadbw/)。

请注意，即使我们从未更改过名字或姓氏，这个名字也呈现了 10 次。您可以通过许多事件来优化这一点，或者检查某种变化的有效负载。工作量太大了。

下面是使用 MobX 构建的同一个示例:

参见 [CodePen](http://codepen.io) 上 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )的 Pen [简单 MobX jQuery 示例](http://codepen.io/SitePoint/pen/pEWRNK/)。

注意这里没有`events`、`trigger`或`on`。有了 MobX，你就可以处理最新的价值和它已经改变的事实。注意到它只渲染了一次吗？那是因为我没有改变`autorun`在看的任何东西。

让我们构建一些稍微不那么琐碎的东西:

```
// observable person
var person = mobx.observable({
  firstName: 'Matt',
  lastName: 'Ruby',
  age: 37
});

// reduce the person to simple html
var printObject = function(objectToPrint) {
  return _.reduce(objectToPrint, function(result, value, key) {
    result += key + ': ' + value + '<br/>';
    return result;
  }, '');
};

// print out the person anytime there's a change
mobx.autorun(function(){
  $('#person').html(printObject(person));
});

// watch all the input for changes and update the person
// object accordingly.
$('input').on('keyup', function(event) {
  person[event.target.name] = $(this).val();
}); 
```

[在 CodePen 上运行](http://codepen.io/SitePoint/pen/kkGgwQ?editors=1010)

在这里，我们可以编辑整个人对象，并自动观察数据输出。这个例子中有几个弱点，最明显的是输入值与 person 对象不同步。让我们来解决这个问题:

```
mobx.autorun(function(){
  $('#person').html(printObject(person));
  // update the input values
  _.forIn(person, function(value, key) {
    $('input[name="'+key+'"]').val(value);
  });
}); 
```

[在 CodePen 上运行](http://codepen.io/SitePoint/pen/wzAgqA?editors=1010)

我知道，你还有一个抱怨:“Ruby，你渲染过度了！”你说得对。你在这里看到的是为什么许多人选择使用 React。React 允许您轻松地将输出分成可以单独渲染的小组件。

为了完整起见，这里有一个我已经优化过的 jQuery 例子。

我会在真实的应用程序中做这样的事情吗？大概不会。如果我需要这种粒度级别，我会随时使用 React。当我在实际的应用程序中使用 MobX 和 jQuery 时，我使用的`autorun()`足够细粒度，以至于我不会在每次更改时都重新构建整个 DOM。

您已经做到了这一步，所以这里是用 React 和 MobX 构建的同一个示例

## 让我们制作一个幻灯片

你会如何表现幻灯片的状态？
让我们从单个幻灯片工厂开始:

```
var slideModelFactory = function (text, active) {
  // id is not observable
  var slide = {
    id: _.uniqueId('slide_')
  };

  return mobx.extendObservable(slide, {
    // observable fields
    active: active || false,
    imageText: text,
    // computed
    get imageMain() {
      return 'https://placeholdit.imgix.net/~text?txtsize=33&txt=' + slide.imageText + '&w=350&h=150';
    },
    get imageThumb() {
      return 'https://placeholdit.imgix.net/~text?txtsize=22&txt=' + slide.imageText + '&w=400&h=50';
    }
  });
}; 
```

我们应该有一些东西，将汇总我们所有的幻灯片。让我们现在就开始:

```
var slideShowModelFactory = function (slides) {
  return mobx.observable({
    // observable
    slides: _.map(slides, function (slide) {
      return slideModelFactory(slide.text, slide.active);
    }),
    // computed
    get activeSlide() {
      return _.find(this.slides, {
        active: true
      });
    }
  });
}; 
```

幻灯片活了！这更有趣，因为我们有一个可观察的幻灯片数组，允许我们在集合中添加和删除幻灯片，并相应地更新我们的 UI。接下来，我们添加`activeSlide`计算值，它将根据需要保持当前值。

让我们渲染我们的幻灯片。我们还没有准备好 HTML 输出，所以我们只是打印到控制台。

```
var slideShowModel = slideShowModelFactory([
  {
    text: 'Heloo!',
    active: true
  }, {
    text: 'Cool!'
  }, {
    text: 'MobX!'
  }
]);

// this will output our data to the console
mobx.autorun(function () {
  _.forEach(slideShowModel.slides, function(slide) {
    console.log(slide.imageText + ' active: ' + slide.active);
  });
});

// Console outputs:
// Heloo! active: true
// Cool! active: false
// MobX! active: false 
```

酷，我们有几张幻灯片，`autorun`刚刚打印出它们的当前状态。让我们换一两张幻灯片:

```
slideShowModel.slides[1].imageText = 'Super cool!';
// Console outputs:
// Heloo! active: true
// Super cool! active: false
// MobX! active: false 
```

看来我们的`autorun`起作用了。如果你改变了`autorun`正在观看的任何东西，它就会开火。让我们将控制台的输出改为 HTML:

```
var $slideShowContainer = $('#slideShow');
mobx.autorun(function () {
  var html = '<div class="mainImage"><img src="' 
           + slideShowModel.activeSlide.imageMain 
           + '"/></div>';

  html += '<div id="slides">';
  _.forEach(slideShowModel.slides, function (slide) {
    html += '<div class="slide ' + (slide.active ? ' active' : '') 
         + '" data-slide-id="' + slide.id + '">';
    html += '<img src="' + slide.imageThumb + '"/>'
    html += '</div>';
  });
  html += '</div>';
  $slideShowContainer.html(html);
}); 
```

我们现在有了这个幻灯片显示的基础，但是，还没有交互性。你不能点击缩略图并改变主图像。但是，您可以使用控制台轻松更改图像文本和添加幻灯片:

```
// add a new slide
slideShowModel.slides.push(slideModelFactory('TEST'));
// change an existing slide's text
slideShowModel.slides[1].imageText = 'Super cool!'; 
```

让我们创建第一个也是唯一一个动作来设置所选幻灯片。我们必须通过添加以下动作来修改`slideShowModelFactory`:

```
// action
setActiveSlide: mobx.action('set active slide', function (slideId) {
  // deactivate the current slide
  this.activeSlide.active = false;
  // set the next slide as active
  _.find(this.slides, {id: slideId}).active = true;
}) 
```

为什么要用`action`you quest？很棒的问题！MobX 动作不是必需的，正如我在其他关于改变可观察值的例子中所展示的。

行动在几个方面帮助你。首先，MobX 动作都是在事务中运行的。这意味着我们的`autorun`和其他 MobX 的反应，会等到动作完成后才开火。想一想。如果我试图在事务之外停用当前幻灯片并激活下一张幻灯片，会发生什么情况？我们的`autorun`早就开火两次了。第一次运行会很尴尬，因为没有活动的幻灯片可以显示。

除了事务性之外，MobX 动作还可以简化调试。我传递给我的`mobx.action`的第一个可选参数是字符串`'set active slide'`。这个字符串可以用 [MobX 的调试 API](https://mobxjs.github.io/mobx/refguide/spy.html)输出。

所以我们有了我们的动作，让我们用 jQuery 连接它的用法:

```
$slideShowContainer.on('click', '.slide', function () {
  slideShowModel.setActiveSlide($(this).data('slideId'));
}); 
```

就是这样。现在，您可以单击缩略图，活动状态会像您预期的那样传播。这是幻灯片的一个工作示例:

参见 [CodePen](http://codepen.io) 上 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )的 Pen [简单 MobX jQuery 示例](http://codepen.io/SitePoint/pen/EgbBAJ/)。