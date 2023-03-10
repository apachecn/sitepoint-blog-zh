# JavaScript:就是不验证！

> 原文：<https://www.sitepoint.com/javascript-just-not-validation/>

术语“JavaScript 验证”在描述帮助用户正确填写表单的过程时有些误导。事实上，后端代码执行验证——JavaScript 只是提供帮助。但是当确认和帮助不是来自同一个来源时，它们除了迷惑人们之外什么也没做。

Web 表单是 HTML 变得具有交互性的手段——web 应用程序通过表单接收来自最终用户的信息。有时候，这种信息流可以是悠闲的，放松的，不受限制的。在其他时候，必须检查信息流，以确保 web 表单数据的格式正确，特别是像电子邮件地址、送货地址和信用卡号这样的信息。我们都知道好数据的关键是后端验证，使用 JavaScript 的前端验证可以帮助用户获得正确的数据，而不必等待与服务器的往返。开发人员已经将 JavaScript 功能与他们的后端验证结合起来。然而，大多数人使用不同的技术，将这些形式的验证视为相同的任务。

严格地说，JavaScript 验证不是验证——它是输入辅助。任何人都可以绕过 JavaScript 这是辅助设备，不是大门。它只是帮助你的用户有信心点击那个大的提交按钮。

与后端验证分开构建的 JavaScript 输入辅助并不理想。最坏的情况是，这两部分功能的工作方式不同，在客户端有效的功能在服务器端无效。在最好的情况下，它们工作得很好——最初。但是，由于验证逻辑位于多个位置，当需要更改时，只有一个逻辑得到更新的可能性越来越大，此时我们会得到不一致的结果。

最后，后端验证是*必需的*。JavaScript 助手是*尼斯*。

##### 代码时间

考虑到这些，我创建了 Y-Validator，后端使用 PHP5。有一个[测试版本可供](http://lapse.nerdvana.org.au/code/ydemo/)使用，如果你喜欢的话，你可以[下载代码跟着](http://lapse.nerdvana.org.au/code/ydemo/ydemo.zip)走。

在 PHP 中，我们通过实例化`yValidate`对象来指定验证:

```
<?php 

  require('y-validate.php'); 

  $v = new yValidate(); 

  $v->add('name','+NAME','namefeedback','Required, valid names are letters, numbers and '-()?'');?>
```

`y-validate.php`库包含了`yValidate`类。我们首先通过`add()`函数向对象提供需要验证的各种输入:

*   第一个参数是字段的 ID 或`Name`；单选按钮和复选框使用 Name(而`[]`用于复选框，以便 PHP 接收一个值数组)，而所有其他字段使用 ID。
*   第二个参数是验证类型，在私有对象变量`regValidations`中定义，它用正则表达式映射参数字符串以进行验证。对于必填字段，它以`+`为前缀。
*   第三个参数指定放置反馈的标签的 ID。
*   第四个参数是消息文本，告诉用户预期的内容。

从这一点来说，验证提交的代码就是通过`$v->validateAll($_POST)`传递`$_POST`变量。它使用在`$v->add(...)`中定义的映射来验证输入，验证时返回 true 或 false，如果适用的话，还会返回一组错误。那是全部缝合的后端！

```
<script type="text/javascript" src="y-validate/js/y-validate.js"></script> 

<script type="text/javascript"> 

  <?php echo $v->buildJS(); ?> 

  <?php $v->jsValidations(); ?> 

</script>
```

当我们构建前端来捕获输入时，我们包括前端脚本`y-validate.js`。这创建了一个类似于上面 PHP 脚本的类。为了链接这两个验证，我们调用 PHP 对象的两个方法。首先，`$v->buildJS`将 JavaScript 验证类实例化为一个对象，并将验证类型检查附加到字段和提交按钮上，它们通过`$v->add(...)`附加到 PHP 对象上。其次，`jsValidations`准备`window.onload`将 PHP 对象中的同名+正则表达式关系附加到表单中的字段。

重要的是，`jsValidations`方法确保后端的验证成为前端的辅助检查。更新后端，前端也更新，这意味着验证不能不同步。

```
<label for="name">Your name <?php echo $v->fieldFeedback('name','namefeedback',$namefeedback);?></label> 

<input type="text" name="name" id="name" value="<?php if (isset($name)) echo $name;?>" />
```

如果出现输入问题，后端和前端代码都需要能够通知用户。自然，字段本身有标签，当输入需要纠正时，这是给用户反馈的最佳位置。`fieldFeedback`需要三个参数:

1.  第一个是适合反馈的领域。

3.  第二个是为了捕捉反馈而创建的一个`<em>`的 ID；这映射到`$v->add(...)`的第三个参数。

5.  第三个参数是在`$v->add(...)`的第四个参数中定义的初始提示。

因此，除了`$v->add(...)`中设置要使用的验证的第二个参数之外，两者之间的参数很好地映射在一起。

使用一个位置作为反馈意味着无论 JavaScript 是否启用，用户都有一个地方可以寻求帮助。因为验证和输入辅助功能是从同一个对象构建的，所以验证和输入辅助的反馈文本是相同的。此外，为了突出前端验证的辅助性质，反馈消息在任何交互发生之前被放入反馈字段。这将预先提示用户期望的格式，让他们第一次就得到正确的格式。

一旦页面加载完毕，`window.onload`事件就会触发，并将 JavaScript 输入帮助链接到所有字段和提交按钮；这是在`$v->jsValidations`设置的。当用户对字段进行更改时，由`window.onload`附加的 JavaScript 事件就会触发，使用配置的正则表达式检查值。

##### 已经在外面了

我在我的库中使用了 PHP(见下文)，但是其他语言和框架也有类似的想法:

*   PHPCake 有一个名为 JS 验证器的扩展[；它依赖 AJAX 进行后端验证。](http://bakery.cakephp.org/articles/view/js-vaildator)
*   Ruby on Rails 使用 AJAX 将验证直接移植到后端验证。
*   目前正在对 [CodeIgniter](http://codeigniter.com/forums/viewthread/50993) 进行工作。
*   Python 有一个聪明的家伙，他在我开始开发这个库的同时发布了一个库。

除了 Python 解决方案，这个概念的大多数验证器都依赖 AJAX 将字段发送回服务器进行验证。由于验证需求通常不会在页面请求和用户输入数据的几秒钟内发生变化，这对于每个字段的验证来说是一个额外的开销。此外，这些脚本没有挂钩到帮助用户的最佳方法——首先告诉他们期望什么。通过将验证的英文描述附加到每个字段，并作为有用的提示首先提示用户输入什么，我们帮助用户第一次就得到正确的信息。有多少次你看着一张表格，咆哮着“为什么我开始的时候你不告诉我？”

##### 结论

使用一个中央对象来管理验证和输入辅助任务，即使禁用了 JavaScript，也能创建一致的最终用户体验。

*   我们有一个地方来更新增强功能(JavaScript 辅助)和所需的功能(后端验证)。因此，这两者不能脱节，我们可以确保用户的无缝体验。
*   我们有一个地方可以找到增强，所以对于我们代码的未来维护者来说，找到在代码中实现各种验证和帮助的点就不会那么困惑了。
*   该准则还提倡行为与表现的分离。程序员编写验证程序，设计师实现反馈的呈现。
*   用户感觉有更多的控制权，对点击大的提交按钮有信心。

不要忘记:PHP-Validate 和一个示例表单的代码可以从下载处获得。

## 分享这篇文章