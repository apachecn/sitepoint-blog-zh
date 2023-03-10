# HTML5 表单:输入类型(第 2 部分)

> 原文：<https://www.sitepoint.com/html5-forms-input-types-part-2/>

以下是我们的书的摘录，由 Alexis Goldstein、Louis Lazaris 和 Estelle Weyl 撰写的《真实世界的 CSS3，第二版。世界各地的商店都有出售，或者你可以在这里买到电子书。

你可以在这里找到 [Part 1。](https://www.sitepoint.com/html5-forms-input-types/)

### 数字

`number`类型(`type="number"`)提供输入数字的输入。通常，这是一个微调框，您可以在其中输入一个数字，或者单击本机浏览器微调 UI 中的上/下箭头来选择一个数字。

让我们将数量字段改为使用`number`输入类型:

```
<label for="quantity">I would like to receive <input type="number" 
↵min="1" name="quantity" id="quantity"> copies of 
<cite>The HTML5 ↵ Herald</cite></label>
```

图 4.8 显示了这在 Opera 中的样子。

![](img/0a01f52c6763c4969a40ce23dd42dec8.png)

图 4.8。歌剧中看到的数字输入

在许多触摸屏设备上，关注一个`number`输入类型会调出一个数字触摸板(而不是一个完整的键盘)。

`number`输入具有`min`、
、`step`属性，用于指定允许的最小值、最大值和增量值。如果省略`step`，则默认为`1`。
如果您想允许浮点值，您必须指定一个浮点步长，如 0.1 或关键字`any`以允许任何值。请注意，有些浏览器会将受限数字的数字表单字段的宽度最小化。例如，`min="0" max="10" step="1"`不需要像`step="any"`一样宽，用户可以输入圆周率的完整值。

#### 警告:小心使用`number`

有时候你可能认为你想使用
`number`，而实际上另一种输入类型是
更合适。例如，
街道地址应该是一个数字，这似乎是有道理的。但是想想看:你会想要一直点击微调框直到 34154 吗？更重要的是，许多街道号码都有非数字部分:想想
24 或 36B，它们都不支持`number`
输入类型。

此外，帐号可能是字母和数字的混合，或者有破折号。如果你知道你的数字的模式，使用`pattern`属性。请记住，如果范围很大或者数字可能包含非数字字符并且该字段是必填的，则不要使用`number`。如果该字段是可选的，您可能还是想使用`number`或`tel`来提示数字或电话键盘作为触摸屏设备的默认设置。

### 范围

`range`输入类型(`type="range"`)显示一个滑块控件。与数字类型一样，它允许使用`min`、`max`和`step`属性。根据规范，`number`和`range`的区别在于数字的精确值对于`range`来说并不重要。对于需要不精确数字的输入，这是理想的选择；例如，客户满意度调查要求客户对他们接受的服务的各个方面进行评分。

让我们将注册表单改为使用`range`输入类型。我们将创建一个字段，要求用户对他们的 HTML5 知识进行评分，分值范围为 1 到 10:

```
<label for="rating">On a scale of 1 to 10, my knowledge of HTML5
↵is:</label> 
<input name="rating" type="range" min="1" max="10" step="1">
```

图 4.9 显示了这种输入类型在 Safari 中的样子。在这种情况下，不需要`step`属性，因为它默认为`1`。一个负值的`step`将打破这个范围，使拇指在 Firefox 中不可移动。

![](img/357c4f9716f6177cd8545edd27f370ad.png)

图 4.9。Safari 中的范围输入类型

范围的默认值是滑块的中点，换句话说，是最小值和最大值的中间值。包含带有相关联的`datalist`的`list`属性能够沿着显示建议值位置的滑块路径创建小缺口。

如果指定的最大值小于最小值，规范允许反向滑块(值从右到左)；但是，目前没有浏览器支持这一点。此外，该规范允许使用两个拇指来包含`multiple`属性。也没有浏览器支持这个。

从 Firefox 23、Android 4.2 和 Internet Explorer 10 开始，所有浏览器都支持`range`。`list`目前只有 Chrome 20+，Opera 和 Internet Explorer 10+支持`range`输入类型。

### 颜色；色彩；色调

`color`输入类型(`type="color"`)为用户提供了一个颜色选择器——或者至少它在一些浏览器中提供了颜色选择器，包括 BlackBerry 10、Firefox 29+、Safari 8+ for desktop、Chrome、Opera 和 Android 4.4。iOS 8 和 Internet Explorer 11 的 WebKit 还不支持`color`输入类型。拾色器返回小写十六进制 RGB 颜色值，如`#ff3300`，默认值为`#000000`(黑色)。

如果您想要使用颜色输入，请提供占位符文本，指示需要十六进制 RGB 颜色格式，并使用`pattern`属性将输入限制为仅有效的十六进制颜色值。

我们在表单中不使用颜色，但是如果我们使用了，它看起来会有点像这样:

```
<label for="clr">Color: </label> 
<input id="clr" name="clr" type="color" placeholder="#ffffff" 
↵pattern="#(?:[0-9A-Fa-f]{6})">
```

产生的颜色选择器如图 4.10 所示。单击 Other…按钮会弹出一个全色轮，允许用户选择任何十六进制颜色值。如果您想要其他颜色，使用带有相关联的`datalist`的`list`属性来定义您想要作为单独选项推荐的每种颜色。目前只有 Blink 浏览器支持这一功能。

![](img/b049fc6d1ea9c782bbf70582a085f932.png)

图 4.10。Chrome 的颜色输入类型的颜色选择器控件

### 日期和时间

有新的日期和时间输入类型，其中一些包括在 HTML5 规范中，还有一些仍然列在 HTML 生活标准和 HTML5.1 草案中，可能有风险。`date`和`time`输入类型被纳入了 HTML5 W3C 推荐标准，而`datetime`、`datetime-local`、`month`和`week`有被放弃的风险。所有日期和时间输入都接受根据 [ISO 8601 标准格式化的数据。](http://en.wikipedia.org/wiki/ISO_8601)

以下是可用的日期和时间输入类型:

*   `date`:包含日期(年、月、日)，不包含时间；比如 2004-06-24。
*   `time`:用军用格式表示一天中的时间(24 小时制)；例如，22:00 而不是晚上 10:00
*   `month`:只包含年和月；比如 2012-12 年。
*   `week`:覆盖年、周数(从 1 到 52)；比如 2011-W01 或者 2012-W52。
*   `datetime`:包括日期和时间，中间用“T”隔开，后面用“Z”表示 UTC(协调世界时)或用+或–字符指定的时区；例如，“2011-03-17T10:45-5:00”表示 UTC 负五小时时区(东部标准时间)2011 年 3 月 17 日上午 10:45。该值已从规范中删除，然后又重新添加进来。它目前没有支持。
*   `datetime-local`:与 datetime 相同，只是省略了时区；主要区别在于，支持日期和时间的浏览器支持 datetime-local，而 datetime 不支持。

这些类型中最常用的是`date`。规范要求浏览器显示日期控件。在撰写本文时，用于 iOS、Chrome 20+和 Opera 的 WebKit 为这些值中的大部分提供了日历控件。Internet Explorer 11、Safari for desktop 和 Firefox 37 仍然没有。

让我们将订阅开始日期字段更改为使用`date`输入类型:

```
<label for="startdate">Please start my subscription on:</label>
<input type="date" min="1904-03-17" max="1904-05-17" 
↵id="startdate" name="startdate" required aria-required="true" 
↵placeholder="1904-03-17">
```

现在，当我们在 Opera、Chrome 或 iOS WebKit 中查看表单时，我们将拥有一个日历控件，如图 4.11 所示。不幸的是，目前还不能用 CSS 样式化。

![](img/91d392bd65072781c39af05b00bc8919.png)

图 4.11。日历控件

对于`month`和`week`类型，浏览器显示与日期输入类型类似的 UI，但是只允许用户选择完整的月份或周。在这些情况下，无法选择个别日期；相反，点击一天会选择整月或整周。虽然这些浏览器支持`datetime-local`，但不支持`datetime`。`datetime`已弃用。`month`、`week`和
、`datetime-local`也有危险，但还没有
落到同样的命运。Chrome 在 26 版失去了对`datetime`的支持，15 版失去了对 Opera 的支持，iOS7 失去了对 Safari 的支持。不要使用`datetime`，因为支持应该被否决，使用`date`和`time`作为两个独立的输入类型。

我们建议在使用`date`输入类型时包括最小值和最大值。与`number`和`range`一样，这是通过`min`和`max`属性完成的。

我们之前添加到开始日期字段中的`placeholder`属性在支持 datepicker 接口的浏览器中是多余的，但是在 IE、Safari 和 Firefox 的用户实现日期和时间控件之前，让它留在原处是有意义的。在所有浏览器都支持新输入类型的 UI 之前，占位符是向用户暗示那些字段中需要什么类型的数据的好方法。请记住，在不支持的浏览器中，它们看起来只是普通的文本字段。

#### 提示:动态日期

在我们的例子中，我们将`min`和`max`值硬编码到 HTML 中。如果您希望最小日期是当前日期之后的一天——这对于报纸订阅的开始日期是有意义的——这就需要每天更新 HTML。处理它的最好方法是在服务器端动态生成最小和最大允许日期。一点点 PHP 可以走很长的路:

```
<?php function daysFromNow($days){ 
  $added = ($days * 24 * 3600) + time(); 
  return date("Y-m-d", $added); 
} 
?>
```

在我们的标记中，我们有静态日期，现在我们用上面的函数动态地创建它们:

```
<li> 
  <label for="startdate">Please start my subscription on: 
↵</label> 
  <input type="date" min="<?php 
echo(daysFromNow(1)); ?>" 
↵max="<?php echo(daysFromNow(91)); ?>" 
id="startdate" 
↵name="startdate" required aria-required="true"
↵placeholder="<?php echo(daysFromNow(1)); ?>"> 
</li>
```

这样，用户只能输入在表单上下文中有意义的日期。

您还可以在这些输入类型中包含`step`属性。例如，`date`上的`step="7"`将限制用户每周只能选择一天:特定的工作日取决于`min`，如果有一天在场，或者如果没有人在场，则是一周中的当天。在`time`输入中，`step`属性必须用秒来表示，因此在`time`输入类型上添加`step="900"`将导致输入以 15 分钟为增量步进。

## 分享这篇文章