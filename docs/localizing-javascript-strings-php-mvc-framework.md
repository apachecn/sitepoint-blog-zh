# 在 PHP MVC 框架中本地化 JavaScript 字符串

> 原文：<https://www.sitepoint.com/localizing-javascript-strings-php-mvc-framework/>

今天，我将向您展示如何在 PHP 框架内有效地本地化 Javascript 字符串。你可以在这里下载本教程的一个工作实现。

实际上有几种方法可以在 PHP 环境中本地化 Javascript 字符串。从技术上讲，可以复制您的 Javascript 文件，以目标语言命名，并在用户每次选择新语言时加载所需的版本。但这确实代表了一种很难称之为良好实践的方法，尽管它会“起作用”。

使用这种方法的主要缺点是，每次需要修改 JavaScript 代码时，都必须对每种语言进行修改。如果不给你带来额外的不必要的工作，这只会容易出错。

也有可能通过嵌入在 JavaScript 代码中的 PHP 变量直接调用文字字符串，但是根据您的框架架构，这并不总是一个可用的选项。

因此，我将向您展示一种方法，这种方法肯定有效，而且易于维护。

记住，你可以在这里下载一个工作示例。

所以让我们开始…

在本教程的示例中，我设置了一个按钮来触发一个名为`trigger_msg()`的 Javascript 函数:

```
echo '&lt;input type="button" value="'.$t_launch_message.'" class="trigger" onclick="return trigger_msg();" /&gt;';
```

`trigger_msg()`函数位于/public/JS/main.js 中:

```
function trigger_msg(){
    return alert(translate(LOCALIZATION.here_is_how_it_works));
    }
```

–我们调用/languages/translate.js 中的`translate()`函数，并作为参数传递包含在名为 LOCALIZATION 的数组中的所需元素的名称。
–我们使用语法 translate(name _ of _ language _ array . name _ of _ element，extra_parameter1，extra_paramater2 等)，因此仅使用逗号分隔额外的参数。
–如果用引号括起来，参数当然可以是文字

在我们深入研究`translate()`函数之前，下面是在/languages/current _ language/JS/current _ language . JS 中找到的本地化数组的样子:

```
var LOCALIZATION = {
    here_is_how_it_works :  'Voici comment cela fonctionne.\nAppuyez sur le bouton suivant afin de voir comment Ã§a se passe avec des paramÃ¨tres.',
    who_does_not_know_are_and_that_the_sky_is :  'Qui ne sait pas que %s x %s font %s,\net que le ciel est %s?',
    blue : 'bleu'
  };
```

在我们的数组元素定义中，你可以看到使用了“%s”，这是我们用来保存额外参数的表达式。我们稍后会谈到这一点。
注意，你可以插入 HTML 风格标签，例如< b >等。，在数组元素定义中，如果你使用一个自定义对话框，它会工作得很好。

现在是时候关注我们的`translate()`功能了:

```
(function () {

    if (!window.translate){

      window.translate = function(){
        var html = [ ]; 
        var arguments = arguments;
        var string = arguments[0];

        var objIndex = 0;
        var reg = /%s/;
        var parts = [ ];

        for ( var m = reg.exec(string); m; m = reg.exec(string) ) {  
          // m[0][0] gives undefined in IE
          parts.push(string.substr(0, m[0][0] === "%" ? m.index : m.index));
          parts.push("%s");
          string = string.substr( m.index+m[0].length );
        }
        parts.push(string);

        for (var i = 0; i &lt; parts.length; ++i){
            var part = parts[i];
            if (part &amp;&amp; part == "%s"){
              var object = arguments[++objIndex];
              if (object == undefined) {
                html.push("%s");
              }else{
                html.push(object);
              };
            }else{
              html.push(part);
            }            
        }

        return html.join('');
      }
    };
  })();
```

下面这个函数构成了我们 JavaScript 本地化方案的核心。

基本上，在名为`string`的变量中，我们存储从`trigger_msg()`函数捕获的参数，在我们的第一个 for 循环中解析它们，使用名为`reg`的变量中保存的正则表达式过滤它们，并将结果部分放入名为`parts[]`的数组中。然后我们将这些部分重新组合成一个名为`html[]`的数组，我们的函数将返回这个数组。

名为`reg`的变量包含一个简单的正则表达式“/%s/”，%s 是我们选择用来定义参数的语法，如上所述。

/public/JS/main.js 中的`trigger_msg_with_params()`函数展示了在本地化字符串时如何使用参数。事实上，在系统中，有时需要翻译的字符串可能包含依赖于用户输入的值，这个函数非常方便，因为它不需要重复使用我们的代码:

```
function trigger_msg_with_params(){
    var param1 = 5382;
    var param2 = 9408;
    var param3 = param1 * param2;
    var param4 = translate(LOCALIZATION.blue);

    return alert(translate(LOCALIZATION.who_does_not_know_are_and_that_the_sky_is, param1, param2, param3, param4));
    }
```

你可以看到每个定义的参数，比如`var param1`，也可能是传递给函数调用的参数。定义的参数也可以是对`translate()`函数的实际调用。所有这些，再次证明是非常有用的。

这就是全部了。这个系统代表了一种跨 PHP 框架翻译 JavaScript 字符串的有效而可靠的方法，并允许大量的灵活性。

非常欢迎您留下您的评论或问题。请继续关注更多教程。

## 分享这篇文章