# 用 Sass 对表单元素进行主题化

> 原文：<https://www.sitepoint.com/theming-form-elements-sass/>

毫无疑问，表单输入占据了相当大的一部分网页。由于用户每天都可能会遇到表单控件，所以似乎只需要通过为每个控件提供 Sass 的优势来帮助我们快速地对项目的输入进行主题化。

## 占位符

一个[占位符](http://caniuse.com/#feat=input-placeholder)是对用户的一个提示，告诉用户可以在相应的控件中输入什么信息。当`type`属性的值设置为`text`、`search`、`tel`、`url`或`email`时适用；否则会被忽略。不幸的是，要设计占位符的样式，需要与每个 pseudo 相关联的适当供应商前缀，这样作者就可以覆盖 IE、Firefox、Safari、Chrome 和 Opera 等主要浏览器供应商。

### 混音助手

这个占位符`@mixin`可以在各种上下文中使用，比如单独使用或者与您选择的选择器结合使用。我还冒昧地构建了一个 [Sass map](https://www.sitepoint.com/using-sass-maps) ,其中包含了您可以设计的所有属性。

**index.html**

```
<label for="username">Name</label>
<input type="text" name="username" id="username" placeholder="first, last">
```

**_placeholder-mixin.scss**

这些 mixins 使用的 [`@at-root`](http://www.alwaystwisted.com/articles/2014-03-08-using-sass-33s-at-root-for-piece-of-mind) 指令通过切换放置 Sass 中嵌套调用的上下文来工作，并将声明拉到选择器链的顶层。

**注意**:在这篇文章[发表时，node-sass](https://github.com/sass/node-sass) 由于行`@at-root #{&}#{$pseudo}`的插值问题而无法编译这个占位符 mixin。

```
$pseudo-phprefix: "::-webkit-input-placeholder" "::-moz-placeholder" ":-ms-input-placeholder" "::placeholder";

$ph-styles: (
font-family: sans-serif,
font-size: medium,
font-style: normal,
font-weight: normal,
color: inherit,
letter-spacing : normal,
line-height: normal,
text-align: inherit,
text-decoration: inherit,
padding: 0
);

@mixin placeholder-theme($styles) {
@each $pseudo in $pseudo-phprefix {

@at-root #{&}#{$pseudo} {
@each $key, $value in $styles {
#{$key}: #{$value};
}
}

}
}

@mixin placeholder {
@each $pseudo in $pseudo-phprefix {

@at-root #{&}#{$pseudo} {
@content
}

}
}
```

定义了所需的`@mixin`之后，我们可以将它作为一个独立的语句进行调用，该语句将使用您传递的属性和值对占位符进行通用样式化。第二种方法利用目标选择器来避免对系统中某些不需要的占位符进行全局样式化。

**_placeholder-mixin.scss**

```
@include placeholder { color: red; }

input {
@include placeholder-theme($ph-styles);
}
```

**CSS 输出**

```
::-webkit-input-placeholder {
color: red;
}
::-moz-placeholder {
color: red;
}
:-ms-input-placeholder {
color: red;
}
::placeholder {
color: red;
}
input::-webkit-input-placeholder {
font-family: sans-serif;
font-size: medium;
font-style: normal;
font-weight: normal;
color: inherit;
letter-spacing: normal;
line-height: normal;
text-align: inherit;
text-decoration: inherit;
padding: 0;
}
input::-moz-placeholder {
font-family: sans-serif;
font-size: medium;
font-style: normal;
font-weight: normal;
color: inherit;
letter-spacing: normal;
line-height: normal;
text-align: inherit;
text-decoration: inherit;
padding: 0;
}
input:-ms-input-placeholder {
font-family: sans-serif;
font-size: medium;
font-style: normal;
font-weight: normal;
color: inherit;
letter-spacing: normal;
line-height: normal;
text-align: inherit;
text-decoration: inherit;
padding: 0;
}
input::placeholder {
font-family: sans-serif;
font-size: medium;
font-style: normal;
font-weight: normal;
color: inherit;
letter-spacing: normal;
line-height: normal;
text-align: inherit;
text-decoration: inherit;
padding: 0;
}
```

**注意事项**:避免使用占位符属性代替标签。两者的目的不同，因为标签属性描述了表单元素的角色；也就是说，它表明了预期的信息类型。占位符属性是关于内容应该采用的格式的提示。在有些情况下，占位符属性永远不会显示给用户，所以没有它的表单也必须是可以理解的。

## 跳跃占位符效果

这是一个非常酷的风格和主题效果，但只在 WebKit/Blink 中有反应。一些人也将此称为“浮动标签”模式，当它首次出现时，引起了关于需要这样一种模式的有趣讨论。

通过[码笔](http://codepen.io)上的 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )看到笔[跳跃占位符](http://codepen.io/SitePoint/pen/EaMqLL/)。

**index.html**

```
<label for="phone">Cell Phone</label>
<input type="tel" name="phone" class="jpinput" id="phone" placeholder="(555) 555-5555">
```

神奇之处在于伪选择器`::-webkit-input-placeholder[style*=hidden]`，一旦用户开始输入他/她的信息，它就允许作者设计占位符的状态。我很想找到一个相当于上述选择器的 Mozilla，但是我的搜索一无所获。David Bushell 有一个很棒的演示，我在下面链接了它，它利用了伪有效性选择器，但是当然这个方法也有一些缺点。

**_jp-input.scss**

```
$jpinput-height: 40px;
$jpinput-radius: 4px;
$jpinput-padding: 10px 16px;
$jpinput-bg: #8DAA91;
$jpinput-color: #4F4137;
$jpinput-ph-color: $jpinput-color;
$jpinput-phide-color: $jpinput-bg;

input {
appearance: none;
box-sizing: border-box;
border-radius: $jpinput-radius;
display: inline-block;
outline: 0;
width: 100%;
}

.jpinput {
height: $jpinput-height;
padding: $jpinput-padding;
transition: transform 225ms ease-in-out;
background: $jpinput-bg;
color: $jpinput-color;

@include placeholder {
position: relative;
top: 0;
left: 0;
transition: all 300ms ease-in-out;
color: rgba($jpinput-ph-color, .5);
}
}

.jpinput::-webkit-input-placeholder[style*=hidden] {
transform: translateY(-$jpinput-height);
opacity: 1;
visibility: visible !important;
color: $jpinput-phide-color;
}
```

当然还有其他方法，我邀请你深入研究，以确定利弊。只是让大家知道，在撰写本文时存在的许多选项通常都屈从于 JavaScript 的支持。

*   [FloatLabels.js](http://clubdesign.github.io/floatlabels.js)
*   [鼓室文本输入效果](http://tympanus.net/Development/TextInputEffects/index2.html)
*   [http://mds.is/float-label-pattern](http://mds.is/float-label-pattern)
*   [http://bradfrost.com/blog/post/float-label-pattern](http://bradfrost.com/blog/post/float-label-pattern%5D(http://mds.is/float-label-pattern)
*   [http://dbushell.com/2013/11/08/form-label-design](http://dbushell.com/2013/11/08/form-label-design)
*   Chris Coyier 的浮动标签系列
*   [大卫·布什尔演示](http://codepen.io/dbushell/pen/ruDAb)

## 标签

HTML [标签](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/label)表示界面中一个项目的标题，并且可以与它的相关控件相关联。它也有助于表明什么样的信息是预期的。

### 安全输入

我们可以使用标签作为滑动门，以便当输入的焦点被移开时隐藏输入的信息。这是一种隐藏隐私文本的巧妙方法，如信用卡号或社会保险号。这个例子也利用了我之前分享的 Sass 占位符`@mixin`。如果您对这种模式感兴趣，标签可以放在相关控件的前面、后面或周围。

在 [CodePen](http://codepen.io) 上通过 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )看到笔[安全输入](http://codepen.io/SitePoint/pen/OPqKwV/)。

**index.html**

```
<div class="slabel">
<input id="credit-card" type="text" pattern="[0-9]{13,16}" placeholder="xxxx-xxxx-xxxx-xxxx">
<label for="credit-card">Credit Card</label>
</div>
```

**_slabel-input.scss**

```
$slabel-theme: (
border: 0,
radius: 0,
padding: 0,
font: inherit,
bg: white,
label-bg: white,
label-color: inherit,
error-color: #E82C0C,
placeholder-color: #B9E0D6,
success-color: #5C832F
);
```

Jordano Aragã在 CodePen 网站上为这些极具个性和趣味的标签设计了这些令人惊叹的图案。

## 输入

input 元素用于创建带有表单的交互式控件，并接受来自用户输入的数据。输入通常以文本、电子邮件、数字、收音机、复选框甚至按钮的形式出现。

### 挑选

这里有一种简洁的方式来设计选择输入，而不使用变通方法或肮脏的技术来改变外观。在大多数利用 JavaScript 的解决方案中，原生 select 元素被设置为`display: none`并被转换成一组 div。客户喜欢它，但作者鄙视它。我认为下面的方法对每个人都是一个胜利，直到一个更本地的 CSS 解决方案被实现。它允许作者设计有行为的浏览器，并为没有行为的浏览器提供可靠的后备。

在 [CodePen](http://codepen.io) 上通过 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )看笔[选择选项](http://codepen.io/SitePoint/pen/ZYPgjX/)。

**index.html**

```
<select>
<option>Property Closing*</option>
<option>open</option>
<option>closed</option>
</select>
```

**_ 选择-输入. scss**

```
$select-arrow: 'data:image/svg+xml;base64,PD94bWwgdmVyc2lvbj0iMS4wIiA/PjxzdmcgaGVpZ2h0PSI0OCIgdmlld0JveD0iMCAwIDQ4IDQ4IiB3aWR0aD0iNDgiIHhtbG5zPSJodHRwOi8vd3d3LnczLm9yZy8yMDAwL3N2ZyI+PHBhdGggZD0iTTE0LjgzIDE2LjQybDkuMTcgOS4xNyA5LjE3LTkuMTcgMi44MyAyLjgzLTEyIDEyLTEyLTEyeiIvPjxwYXRoIGQ9Ik0wLS43NWg0OHY0OGgtNDh6IiBmaWxsPSJub25lIi8+PC9zdmc+';
$select-padding: 0;

select {
border-radius: 0;
-webkit-appearance: none; // autoprefixer won't add this and we still need it
appearance: none;
cursor: pointer;
padding: $select-padding;
width: 100%;

@media screen and (-webkit-min-device-pixel-ratio: 0) {
background-image: url(#{$select-arrow});
background-position: right 0 top 50%;
background-repeat: no-repeat;
}
}
```

阅读由 [Lea Verou](http://lea.verou.me/2011/03/custom-select-drop-downs-with-css3) 撰写的讨论这种方法的原始文章，或者查看由 Tympanus 撰写的这些令人惊叹的[精选灵感](http://tympanus.net/Development/SelectInspiration)。

### 收音机

单选按钮表示从项目列表中选择一个项目。这种输入通常用于用户只有一个选项可以选择的情况，而复选框场景允许选择多个选项。

接下来的这个定制无线电是一个基于 Sass 的方法，是对 Mark Otto 在 [WTF，forms？](http://wtfforms.com)这还包括一些更新的定位技术，使用 CSS 变换来实现内圈，而且它的伸缩性非常好，非常简单，可以开始定制。

在 [CodePen](http://codepen.io) 上用 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )看笔[无线电控制](http://codepen.io/SitePoint/pen/QwoeVG/)。

**index.html**

```
<label for="radioa" class="input-control radio">
<input type="radio" id="radioa" name="radio" value="radio-value">
<span class="input-control__indicator"></span> Send Me Stuff!
</label>

<label for="radiob" class="input-control radio">
<input type="radio" id="radiob" name="radio" value="radio-value">
<span class="input-control__indicator"></span> Don't send a friggin' thing.
</label>
```

**_input-radio.scss**

```
$input-radius: 0 !default;
$input-unit: 16px !default; // accepts px, em, rem
$input-spacing: $input-unit * 1.5 !default; // adjust depending on font-family
$input-font: sans-serif !default;

$radio-bg: white !default;
$radio-txt-color: #AACCFF !default;

$radio-checked-custom: (
background: #0081D0
) !default;
$radio-checked: #222233 !default;
$radio-checked-focus: white !default;
```

花点时间看看 Samurai 制作的这些令人敬畏的[动画广播输入](http://codepen.io/simurai/pen/rJwpz)，进一步探索想法和灵感。

## 检验盒

type 属性值为“checkbox”的 input 元素表示可以切换的状态或选项。

### SVG Checkbox

这种方法使用最初由 [Sara Soueidan](http://slides.com/sarasoueidan/building-better-interfaces-with-svg) 演示的 SVG 技术，使用 CSS 中美国作者可用的 `element. This element uses the `stroke-dasharray`和`stroke-dashoffset` SVG 属性创建自定义复选框和 X 形状，以动画显示选择(X)。这个例子建立在我提到的原始演示的基础上，并添加了`:focus`状态和一些其他的好东西来帮助调整和定制这个输入，以满足您的需要。`

 `在 [CodePen](http://codepen.io) 上通过 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )查看 Pen [SVG 复选框](http://codepen.io/SitePoint/pen/BybXqj/)。

**index.html**

```
<input type="checkbox" id="optiona" name="optiona">
<label for="optiona">Click Me
<svg viewBox="0 0 100 100" xmlns="https://www.w3.org/2000/svg">
<path d="M 10 10 L 90 90"></path>
<path d="M 90 10 L 10 90"></path>
</svg>
</label>
```

**_checkbox-input.scss**

```
$font-size: 2em; // adjust accordingly
$ratio: 1; // adjust accordingly
$size: $ratio + em;
$ratio: $ratio;
$gutter: 5px; // adjust accordingly
$stroke-dash: 270;
$stroke: (
thickness: $gutter/3,
style: solid,
color: #fff
);
$mark-ischecked: (
stroke-dashoffset: 0
); // define css properties
$label-ischecked: (); // define css properties
$svg-ischecked: (); // define css properties
```

### 滑块复选框

这是我在 Transformicons 项目中使用的一个类似滑块的复选框。它主要是用 Sass 构建的，但是非常可重用，并且拥有最少的代码来定制和设计。请注意，这种自定义输入模式还允许键盘导航方面的可访问性。一旦用户通过 tab 键将焦点放在输入上，颜色指示将通知用户。一旦这个通知发生，用户就可以用空格键来设置选项。

在 [CodePen](http://codepen.io) 上通过 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )查看 Pen [Transformicons 复选框](http://codepen.io/SitePoint/pen/GgeVwR/)。`