# 用 jQuery 开发密码强度插件

> 原文：<https://www.sitepoint.com/developing-password-strength-plugin-jquery/>

密码是最常用的身份验证机制。即使许多网站转向多步身份验证，密码在保护您的帐户方面仍然起着关键作用。一个强密码对帐户安全至关重要，这篇文章将教你如何开发一个插件来测量密码强度。我们将使用 [zxcvbn](https://github.com/lowe/zxcvbn) 库和 jQuery 为网站构建一个全功能的密码强度测量仪。

## 介绍

通常，大多数使用是非技术性的，并且对身份验证的工作原理了解有限。即使对于技术用户来说，也很难弄清楚哪些密码是强密码，哪些是容易被破解的。例如，简单地增加密码长度并不一定会增加破解难度。

密码强度计计算用户输入密码的强度。通常，它显示为强度等级，如非常弱、弱、好、强等。每次用户输入时都会更新计量器，因此计算应该作为`onkeypress`或`onkeyup`事件处理程序进行。当用户试图创建新帐户时，弱密码可以被检测到并被拒绝，从而提高整个站点的安全性。

## 密码强度计算

应该有明确定义的计算密码强度的标准。大多数开发人员倾向于对密码强度指示器使用手动验证标准。这是加强密码的一个很好的指导方针，但它可能不是最准确的技术，因为它没有考虑破解密码所需的时间。相反，大多数手动验证依赖于以下条件:

*   字符数。
*   大写字母、数字和符号的使用。
*   黑名单上的词。

这不是对密码强度的真实估计，因为通过这些验证生成的一些密码很容易被破解。因此，我们需要一个精确的测量系统来定义密码强度。

## zxcvbn

在本教程中，我们计划使用 [zxcvbn](https://github.com/lowe/zxcvbn) 实现一个密码强度计。zxcvbn 被认为是一个现实的密码强度计，它利用常见英语单词、重复模式、序列和常见英语名称的数据集来计算强度。也可以让我们在计算实力的时候把一些词列入黑名单。该函数提供的结果比手动强度计算规则精确得多。理解 zxcvbn 如何使用数学函数计算强度超出了本教程的范围。想了解 zxcvbn 内部逻辑的人可以看看 [Dropbox 科技博客](https://tech.dropbox.com/2012/04/zxcvbn-realistic-password-strength-estimation/)的文章。

考虑下面的代码，它使用 zxcvbn 函数。第一个参数是必需的，用于保存要评估的密码。在这种情况下，密码是“password”第二个参数是可选的，可以包含黑名单的值数组。一般来说，我们不希望密码与用户名相似。所以我们必须至少使用用户名作为黑名单值来获得强密码。

```
<script type="text/javascript" >
  var blackList = ["admin"];
  var strength  =  zxcvbn("password", blackList);
</script>
```

zxcvbn 函数返回包含以下属性的对象。

*   **strength . entropy**–以比特为单位测量。
*   **strength . crack _ time**–这是预计的开裂时间。
*   **strength . crack _ time _ display**–这也用于以用户友好的方式提供开裂时间，使用月、年、世纪等。
*   **strength . score**–这是我们将在密码强度计中使用的实际强度。范围从 0 到 4，其中 0 代表弱密码，4 代表强密码。无论你的密码有多复杂，都不会超过四个。
*   **strength . match _ sequence**–这提供了用于计算强度的模式列表。这是一个有多个对象的对象，包含像蛮力，字典等模式。您可以通过调用`strength`对象上的`console.log`来检查这一点。
*   **strength . calculation _ time**–这是计算给定密码强度所花费的时间。一般来说，这将是几毫秒。

有许多参数可以用于密码强度，但我们不希望密码验证太复杂。因此，一般来说，我们将只使用结果的`score`参数。如有必要，我们可以结合使用`crack_time_display`或`crack_time`和`score`来加强密码。

## 构建密码强度表

我们将使用 jQuery 构建一个可重用的密码强度测量仪，并将使用 [jQuery 插件样板](https://github.com/jquery-boilerplate/jquery-boilerplate/)来维护一个标准结构。根据您的喜好，仪表可以有多种开发方式。一些插件提供实际的密码计量器小部件，而一些插件提供密码强度消息，以便用户可以将其插入他们喜欢的任何地方。我们将重点讨论后者，因为它增加了设计密码计量器的灵活性。以下是我们血糖仪的要求:

*   我们需要基本的验证，如检查空值以及密码和确认密码值是否相等。
*   我们可能需要自定义验证，如最少字符，阻止某些字符等。
*   使用预定义的级别报告实际强度，如非常弱、弱、中等、强等。
*   我们可以使用破解时间来实现每个指定级别内的强度。

### 实现力量计插件

首先，我们必须从 GitHub 获取一份 jQuery 插件样板文件。下载完成后，将文件放在项目文件夹中，并将其重命名为`jquery.password.strength.js`。接下来，从 GitHub 下载 [zxcvbn](https://github.com/lowe/zxcvbn) 。将这些文件包含在项目文件中，如下面的代码所示。

```
<html>
  <head>
    <title>jQuery Password Strength Checker</title>
    <script src="https://ajax.googleapis.com/ajax/libs/jquery/2.0.0/jquery.min.js"></script>
    <script type="text/javascript" src="zxcvbn.js"></script>
    <script type="text/javascript" src="jquery.password.strength.js"></script>
  </head>
  <body>
  </body>
</html>
```

现在打开`jquery.password.strength.js`文件。我假设您熟悉 jQuery 插件样板文件。首先，我们必须更新插件名称和默认选项部分，如下面的代码所示。在这里，我们将默认插件名称改为`PasswordStrengthManager`，并用多个插件特定选项替换默认选项。我们有两个选项来指定密码和确认密码字段的值。我们还有一个数组来定义黑名单中的单词。接下来，我们有两个选项叫做`minChars`和`maxChars`，用于实现定制验证。最后，我们有一个名为`advancedStrength`的选项来启用或禁用使用破解时间来计算强度。

```
var pluginName = "PasswordStrengthManager",
    defaults = {
      password: "",
      confirm_pass : "",
      blackList : [],
      minChars : "",
      maxChars : "",
      advancedStrength : false
    };
```

接下来，我们可以进入插件构造函数，如下面的代码所示。在这里，我们有默认的样板代码，除了我们用来保存由 password meter 生成的消息的信息设置。一旦插件被用在任何 HTML 元素上，这个函数就会被调用。

```
function Plugin(element, options) {
  this.element = element;
  this.settings = $.extend({}, defaults, options);
  this._defaults = defaults;
  this._name = pluginName;
  this.init();
  this.info = "";
}
```

插件的`init`函数用于初始化插件特定代码并启动进程。我们来看看修改后的`init`函数(如下图)。首先，我们执行强制验证，比如检查空密码和两个密码字段是否相等。`this.settings`将包含传递给插件初始化器的默认值或自定义值。密码计量器的信息存储在`this.info`中。最后，我们通过初始化将消息设置为密码计量元素。

```
init: function() {
  if ("" == this.settings.password || "" == this.settings.confirm_pass) {
    this.info = "Password fields cannot be empty";
  } else if (this.settings.password != this.settings.confirm_pass) {
    this.info = "Passwords doesn't match";
  }

  $(this.element).html(this.info);
},
```

接下来，我们进入定制验证器部分。一些网站喜欢自己的限制，如密码的字符范围或阻止某些字符。我们将为`minChars`和`maxChars`实现两个验证器。我们已经在默认设置中定义了`minChars`和`maxChars`。这些函数只有在我们在初始化时传递一个值的情况下才会执行。让我们为这些验证器定义函数:

```
minChars: function() {
  if (this.settings.password.length < this.settings.minChars) {
    this.info = "Password should have at least " + this.settings.minChars + " characters";
    return false;
  } else {
    return true;
  }
},

maxChars: function() {
  if (this.settings.password.length > this.settings.maxChars) {
    this.info = "Password should have maximum of " + this.settings.maxChars + " characters";
    return false;
  } else {
    return true;
  }
},
```

这两个函数检查给定密码的最小和最大字符长度，并返回一个布尔值。接下来，我们需要一种方法来调用这些手动验证。因此我们创建了一个名为`customValidators`的函数，如下面的代码所示。

```
customValidators: function() {
  var err = 0;

  if (this.settings.minChars != "") {
    if (!this.minChars()) {
      err++;
    }
  }

  if (this.settings.maxChars != "") {
    if (!this.maxChars()) {
      err++;
    }
  }

  return err;
}
```

这里，我们执行所有的定制验证器，并增加变量`err`的值。如您所见，`if`条件只有在设置的值不为空时才会被执行。因此，如果我们在初始化时不传递参数，这些验证器就不会被执行。最后，调用这个函数作为`init`函数的第一行来获取定制验证器的错误。

### 计算 zxcvbn 强度

这是这个插件的最后一部分，我们使用 zxcvbn 计算实际的密码强度。用以下代码更新`init`函数。

```
init: function() {
  var errors = this.customValidators();

  if ("" == this.settings.password || "" == this.settings.confirm_pass) {
    this.info = "Password fields cannot be empty";
  } else if (this.settings.password != this.settings.confirm_pass) {
    this.info = "Passwords doesn't match";
  } else if (errors == 0) {
    var strength = zxcvbn(this.settings.password, this.settings.blackList);

    switch (strength.score) {
      case 0:
        this.info = "Very Weak";
        break;
      case 1:
        this.info = "Very Weak";
        break;
      case 2:
        this.info = "Weak";
        break;
      case 3:
        this.info = "Medium";
        break;
      case 4:
        if (this.settings.advancedStrength) {
          var crackTime = String(strength.crack_time_display);

          if (crackTime.indexOf("years") !=-1) {
            this.info = "Very Strong";
          } else if (crackTime.indexOf("centuries") !=-1) {
            this.info = "Perfect";
          }
        } else {
          this.info = "Strong";
        }

        break;
    }
  }

  $(this.element).html(this.info);
},
```

当自定义验证器检测到零错误时，我们进入实际的强度计算。我们可以将初始化过程中定义的密码和黑名单中的单词的值传递给 zxcvbn 函数。它将生成具有本文前面提到的属性的结果。

我们使用一个`switch`语句来过滤从 0 到 4 的分值，以提供不同的强度级别。您可以根据自己的喜好更改级别。前四关，我刚刚考虑了实力`score`。此外，我们可以使用`crack_time_display`属性来进一步定义子级别。在这个插件里，我只用过力量分四的。您也可以将它用于其他乐谱。

`crack_time_display`值以用户友好的方式生成破解密码所需的时间。因此，我们使用`crack_time_display`值将级别分为两个子级别。如果用户决定不启用高级力量，将使用名为`Strong`的普通等级。您可以随意使用这些属性来创建高级密码计量器。

最后，我们必须修改样板代码的最后一部分，如下所示，以启用多个初始化，因为我们必须在每个键事件上执行该函数。

```
$.fn[pluginName] = function (options) {
  this.each(function() {
    $.data(this, "plugin_" + pluginName, new Plugin(this, options));
  });
  return this;
};
```

现在我们已经完成了插件的实现。让我们通过下面的代码来看看它是如何工作的。

```
<script type="text/javascript" >
  $(function() {
    $("#pass").keyup(function() {
      initializeStrengthMeter();
    });

    $("#confirm_pass").keyup(function() {
      initializeStrengthMeter();
    });
  });

  function initializeStrengthMeter() {
    $("#pass_meter").PasswordStrengthManager({
      password: $("#pass").val(),
      confirm_pass : $("#confirm_pass").val(),
      minChars : "5",
      maxChars : "15",
      blackList : ["efewf"],
      advancedStrength : true
    });
  }
</script>
```

我们使用一个名为`initializeStrengthMeter`的公共函数来初始化插件。这个函数将在用于密码计数器的元素上调用。因此，定义一个空的`div`元素，并将元素的`id`赋给插件初始化器。这里，我们在插件中定义了所有的属性。您可以根据需要添加或删除它们。最后，我们必须在密码和确认密码字段的`keyup`事件上调用这个函数来启用密码计数器。

## 结论

在本教程中，我们确定了普通密码强度计的缺点，并使用 zxcvbn 进行实际的强度计算。我们开发了一个 jQuery 插件来处理密码验证的常见用例。你可以在这里观看本教程[的演示，并下载](http://www.innovativephp.com/demo/jquery_password_meter)[源代码](http://www.innovativephp.com/demo/jquery_password_meter.zip)。你可以根据自己的需求随意扩展插件。

## 分享这篇文章