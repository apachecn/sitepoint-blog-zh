# 用 jQuery 创建选择性加入的 Monster 克隆

> 原文：<https://www.sitepoint.com/creating-opt-monster-clone-jquery/>

毫无疑问，建立电子邮件列表最有效的方法是创建一个当访问者偶然发现你的网站时出现的模型。然后，这种模式将包含一个他们无法忽视的选择加入表单(通常会有一个令人信服的贿赂来交出他们的电子邮件，就像某种免费下载)。这种方法并非没有争议，但是:

1.  它们仍然非常有效。
2.  抱怨来自直言不讳的少数人。

为了创建这些模态，大多数人使用第三方软件，如 SumoMe 的 [Opt-in Monster](http://optinmonster.com) 、 [LeadPages](http://leadpages.net) 或 [List Builder](http://sumome.com/app/list-builder) 插件。虽然这些应用程序很方便，但它们并不总是最佳选择，正如我们将在本教程中讨论的，它们很容易被 jQuery 替换。

以下是方法。

## 步骤 1:安装 jQuery

首先，下载 jQuery 并将其嵌入到网页中。为了节省一点时间，可以随意嵌入 jQuery 的外部副本:

```
<script src="//code.jquery.com/jquery-1.11.0.min.js"></script>
```

接下来，我们需要向页面添加两个不同的 jQuery 插件:

第一个插件是 jQuery 模态插件。这是我们可以用来创建用户访问我们的页面后出现的模式框。将插件添加到项目中时，请确保下载所有文件:

*   jquery.modal.min.js
*   jquery.modal.css
*   close.png
*   spinner.gif

您可以使用其他模态插件(或自定义模态插件)遵循本教程，但我发现 jQuery 模态插件是最简单的选择。

第二个插件是 jQuery Cookie 插件。这就是我们将要使用的，这样当用户点击模式上的“关闭”按钮时，他们在接下来的三十天内不会再看到该模式。这意味着:

1.  每个人都应该至少看一次模态。
2.  回头客不会每次访问都看到模态。

依靠 cookies 不是一个简单的方法，但是已经足够好了。

在设置好 jQuery 本身以及这些插件之后，您应该有一个类似如下的 HTML 文件:

```
<html>
    <head>
        <title>jQuery Demo</title>
        <link rel="stylesheet" href="css/jquery.modal.css">
        <script src="js/jquery.min.js"></script>
        <script src="js/jquery.modal.min.js"></script>
        <script src="js/jquery.cookie.min.js"></script>
    </head>
    <body>
    </body>
</html>
```

## 步骤 2:创建一个模型

在`body`标签之间，写下以下内容:

```
<div id="opt-in" style="display:none;">
    <form>
        <input type="email" placeholder="Your email goes here...">
        <input type="submit" value="Free Instant Access!">
    </form>
</div>
```

这是我们的模态。它是当用户访问页面时出现的框。以目前的形式，它看起来不会那么不可思议，但这没关系。然而，有几件事值得一提:

1.  我们的模态需要一个惟一的 ID 来引用。在本例中，我给了这个模态一个 ID“opt-in”。
2.  我们已经将 display 属性设置为“none ”,所以模态不会出现在主界面中。它只会在被召唤时出现。

在这段代码下面，就在结束的`body`标记之前，编写一个函数，它将在页面加载后执行:

```
<script type="text/javascript">
$(window).load(function() {
    // code that'll run when the page loads
}
</script>
```

然后，为了在页面加载时显示模态，我们可以使用 jQuery 模态插件提供的`modal`函数:

```
<script type="text/javascript">
$(window).load(function() {
    // make the modal appear
    $('#opt-in').modal();
});
</script>
```

保存文件并刷新浏览器中的页面后，模式应该会出现:

![Example Modal](img/f96d24d55e4e2895f7335d0ceab459c0.png)

但是我们不希望模态立即出现。如果用户访问页面后有轻微的延迟是最好的。为了实现这一点，我们可以编写一个`setTimeout`函数，就像这样:

```
setTimeout(function() {
    // this code will execute after 7 seconds
}, 7000);
```

第一个参数是我们要执行的函数，第二个参数是延迟(以毫秒为单位)。

当与模态结合时，代码将类似于:

```
$(window).load(function() {
    // delay by 7 seconds
    setTimeout(function(){
        // make the modal appear
        $('#opt-in').modal();
    }, 7000);
});
```

## 第三步:管理 Cookies

当用户点击模态的“关闭”按钮时，我们不希望他们在 30 天内再次看到模态。这使得模态远没有那么烦人。

为了实现这一点，让它在用户点击“关闭”按钮时执行一个功能。这个按钮可以被`.close-modal`类引用，这个函数可以放在`load`函数的底部:

```
$('.close-modal').click(function(){
    console.log("Modal closed.");
});
```

因此代码应该类似于:

```
$(window).load(function() {
    // delay by 7 seconds
    setTimeout(function(){
        // make the modal appear
        $('#opt-in').modal();
    }, 7000);
    // when the "Close" button is clicked
    $('.close-modal').click(function(){
        console.log("Modal closed.");
    });
});
```

然后在这个新函数中，我们将创建一个 cookie:

```
$('.close-modal').click(function(){
    // create a cookie
    $.cookie('hideTheModal', 'true');
});
```

这里，cookie 被命名为`hideTheModal`，它包含一个值`true`。我们还可以通过一个`expires`选项来定义 cookie 应该持续多长时间:

```
$('.close-modal').click(function(){
    // create a cookie
    $.cookie('hideTheModal', 'true', { expires: 30 });
});
```

有一点很重要的是要知道，谷歌浏览器不支持本地文件的 cookies。这意味着您需要通过 Safari 或 Firefox 等浏览器来测试这种基于 cookie 的功能。

有了这个 cookie，我们现在可以编写以下条件:

```
var hideTheModal = $.cookie('hideTheModal');
if(hideTheModal == null){
    // modal appears
} else {
    // modal doesn't appear
}
```

因此，在上下文中，代码应该类似于:

```
$(window).load(function() {
    var hideTheModal = $.cookie('hideTheModal');
    // if the cookie hasn't been set...
    if(hideTheModal == null){
        // delay by 7 seconds
        setTimeout(function(){
            // make the modal appear
            $('#opt-in').modal();
        }, 7000);
        // when the "Close" button is clicked
        $('.close-modal').click(function(){
            // set the cookie
            $.cookie('hideTheModal', 'true', { expires: 30 });
        });
    }
});
```

接下来就是“仅仅”设计一个更好看的模型，并对不同的设计进行对比测试，看看它们如何影响选择加入率。但是当然，那些精确的主题超出了本教程的范围。

## 结论

正如我们所见，用 jQuery 创建一个选择加入模型并不困难。您是否采用这种方法将取决于具体情况——在许多情况下，使用预制软件更有意义——但特别是当我构建小型或静态网站时，我很欣赏这种轻量级和无限可定制的方法。

## 分享这篇文章