# 替换单选按钮而不替换单选按钮

> 原文：<https://www.sitepoint.com/replacing-radio-buttons-without-replacing-radio-buttons/>

表单元素！它们很难设计，不是吗？用一些自定义标记和我们自己设计的 CSS 来完全替换它们是很诱人的。问题是，由`div`和`span`组成的老鼠窝将缺少使标准`type="radio"`输入可访问的语义和行为品质。

```
<div class="radio-label">
  <div class="radio-input" data-checked="false" data-value="accessible"></div>
  accessibility?
</div>
```

这只是一段孤独的文字，上面写着“可访问性？”悲剧，真的。为了让这个*甚至开始*再次正确工作，我们需要添加各种补救性的 WAI-ARIA 语义。用铁娘子不朽的话来说，“*我能玩疯吗？*

```
<div class="radio-label" id="accessible-radio">
  <div class="radio-input" data-checked="false" data-value="accessible" 
       aria-labelledby="accessible-radio" role="checkbox" aria-checked="false">
  </div>
  accessibility?
</div>
```

我们的例子仍然是*百分之百*不可访问的，因为我们还没有聚集标准`type="radio"`建立的所有常规行为和键绑定。这将需要`tabindex`属性和大量 JavaScript 您知道是什么吗？我甚至不打算开始走那条路。

我*所做的*可以作为[代码笔演示](http://codepen.io/SitePoint/pen/qhCba/)使用，接下来是技术的解释。

在 [CodePen](http://codepen.io) 上看到用 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )代替单选按钮的笔[。](http://codepen.io/SitePoint/pen/qhCba/)