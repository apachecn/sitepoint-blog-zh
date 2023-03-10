# 快速提示:用 CSS 和 JS 进行单字符转换

> 原文：<https://www.sitepoint.com/how-to-perform-single-character-transforms-with-css-and-js/>

如果你正在为你的下一个项目寻找一个有趣的效果，那么今天的快速提示应该会让你感兴趣。我们将会看到，在一个句子中，各个字符的变换。我们的代码示例使用 jQuery 浏览您选择的句子，并在每个字母上触发一个动画。但是，所有的动画都是通过 CSS 动画而不是 jQuery 本身来运行的。

![enter image description here](img/6ac9cba3acc560e9c7edfbcfc237189b.png "bounce_animation.gif")

## 入门指南

要启动并运行它，我们需要 jQuery。一旦我们对 jQuery 进行了排序，我们就可以在`document.ready()`函数中添加 JavaScript 函数。

```
jQuery(document).ready(function() {
    // Further code to go in here
});
```

我们只需要基本的标记就可以了。在我们的 HTML 文件中，我们添加任何我们想要的文本，然后添加一个基本按钮后面的`.sentence`类。

```
<h1 class="sentence title">Fancy Slide In Text</h1>
<h3 class="sentence subtitle">Embrance the fanciness!</h3>
<div class="button">Click to Animate</div>
```

## 准备你的句子

我们的例子寻找所有具有类`.sentence`的元素。一旦我们有了这些条目，我们循环遍历它们的`text`值的每个字符并创建我们的子串，从我们的句子中一次提取一个字符。每个句子都用一个`<span>`包起来，这样以后就可以单独制作动画了。

我们还测试一个字符是否为空(例如，它是否包含空白，如`" "`)。我们忽略这些，因为我们只对真实角色的动画感兴趣。

一旦我们完成了所有的字符，我们就将新的内容返回到我们的句子中，用包装在`<span>`标签中的字符替换我们的标准纯文本。

```
// Go through a sentence, wrap its characters with spans
function setUpCharacters() {
  var $sentences = $('.sentence');

  // Run for each sentence
  $sentences.each(function() {
    var $sentence = $(this);
    var newContent = '';

    // Go through all characters of the sentence
    for (i = 0; i < $sentence.text().length; i++) {
      var substring = $sentence.text().substr(i, 1);

      // If we have a character, wrap it
      if (substring != " ") {
        newContent += '<span>' + substring + '</span>';
      } else {
        newContent += substring;
      } 
    }

    // Replace content
    $sentence.html(newContent); 
  });
}

setUpCharacters();
```

## 触发您的动画

这是有趣的部分。这是我们遍历所有的`.sentence`元素和`<span>`子元素并激活它们的地方。这个函数本身并不做任何动画，它所做的只是给元素添加一个`.active`类。一旦我们有了这个活动状态，我们就可以应用 CSS 动画来实现我们梦想中的任何新奇的东西！

```
// Go through a sentence and trigger activate state
function triggerCharacters() {
  var sentenceCounter = 0;
  var sentenceDelay = 600;

  $('.sentence').each(function() {
    var $sentence = $(this);

    // Trigger for each sentence
    setTimeout(function() {
      var $spans = $sentence.find('span');
      var spanCounter = 0;
      var spanDelay = 75;

      // Loop through all spans and activate
      $spans.each(function() {
        var $span = $(this);

        // Trigger a timeout so each span is offset
        setTimeout(function() {
          $span.toggleClass('active');
        }, (spanCounter * spanDelay));

        spanCounter++; 
      });
    }, (sentenceCounter * sentenceDelay));

    sentenceCounter++;
  });
}

// For our example, trigger character animations on button click
$('.button').on('click', function() {
  triggerCharacters();
});
```

让我们来分析一下这里到底发生了什么:

*   在我们的效果中，一个句子会在最初的延迟后淡入，以给出交错的外观。我们将为每个句子设置 600 毫秒的初始延迟。这由我们的外部`setTimeout()`函数使用。
*   我们找到并遍历所有的`.sentence`元素，并获取它们的`<span>`子元素。我们指定了一个新的延迟，这一次是针对单个 span 字符的触发。这将允许每个跨度单独激活。
*   内部的`setTimeout()`函数将触发，根据需要执行每个`<span>`。
*   对于我们的例子，我们想在按下按钮时触发这个效果。为此，我们寻找我们的`.button`元素并相应地触发我们的`triggerCharacters()`函数。您可以很容易地将它绑定到另一个元素，或者在页面加载时，由您决定。

## 动画本身

我们将基本样式应用于句子的每个跨度，然后仅当我们使用`.active`类有一个跨度时才应用动画。

```
.sentence span {
  opacity: 0;
  position: relative;
  display: inline-block;
}
.sentence span.active {
  animation: bounceUp 600ms ease 0ms 1 normal both;
}
```

在这里，我们对每个`<span>`元素应用了一个漂亮的 CSS 动画。我们所做的是将元素的位置向上、向下平移，最后回到原始位置。

```
/* Bounce top in */
@keyframes bounceUp {
  0% {
    transform: translate3d(0px, 0px, 0px);
    opacity: 0;
  }
  50% {
    transform: translate3d(0px, -50px, 0px);
    opacity: 0.7;
  }
  80% {
    transform: translate3d(0px, 20px, 0px);
    opacity: 1;
  }
  100% {
    transform: translate3d(0px, 0px, 0px);
    opacity: 1;
  }
}
```

当按下按钮时，第一句话将被激活，其所有连续的`<span>`元素将被激活。在短暂的延迟之后，随后的`.sentence`元素也会被激活，它们的`<span>`子元素也会随之激活。

## 我们的动画在行动

这里有一个代码笔，展示了如何让这个工作。你可以随意看看它来创造你自己的酷效果和动画！

在 [CodePen](http://codepen.io) 上通过 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )看到笔[动画化一串](http://codepen.io/SitePoint/pen/zBOvPB/)中的单个字符。

## 可访问性考虑

因为我们有效地将句子分割成字符，所以屏幕阅读器可能很难阅读完整的单词。为了避免这种情况，我们可以使用元素上的`aria-label`属性告诉屏幕阅读器我们的原始句子是什么。然后我们在我们的每个`<span>`标签上使用 [`aria-hidden="true"`](https://www.w3.org/TR/wai-aria/states_and_properties#aria-hidden) 来对屏幕阅读器隐藏那些单独的元素。这样，屏幕阅读器应该仍然能够读出我们的动画句子，并且应该忽略我们单独的`<span>`元素。

受 JavaScript 影响后，我们的最终标记看起来像这样:

```
<h1 class="sentence title" aria-label="Fancy Slide In Text">
  <span class="active" aria-hidden="true">F</span>
  <span class="active" aria-hidden="true">a</span>
  <span class="active" aria-hidden="true">n</span>
  <span class="active" aria-hidden="true">c</span>
  <span class="active" aria-hidden="true">y</span> 
  <span class="active" aria-hidden="true">S</span>
  <span class="active" aria-hidden="true">l</span>
  <span class="active" aria-hidden="true">i</span>
  <span class="active" aria-hidden="true">d</span>
  <span class="active" aria-hidden="true">e</span> 
  <span class="active" aria-hidden="true">I</span>
  <span class="active" aria-hidden="true">n</span> 
  <span class="active" aria-hidden="true">T</span>
  <span class="active" aria-hidden="true">e</span>
  <span class="active" aria-hidden="true">x</span>
  <span class="active" aria-hidden="true">t</span>
</h1>
```

## 分享这篇文章