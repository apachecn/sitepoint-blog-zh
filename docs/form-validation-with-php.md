# 用 PHP 进行表单验证

> 原文：<https://www.sitepoint.com/form-validation-with-php/>

在本文中，我们将使用 HTML 和 PHP 构建并验证一个小型 web 表单。表单是用 HTML 创建的，表单内容的验证和处理是用 PHP 完成的。这篇文章的目的是教你一些基本的 HTML 表单元素，以及如何在 PHP 脚本中访问它们的数据。

我们将创建的表单包含许多输入:文本字段、单选按钮、多选项选择列表、复选框和提交按钮。将对这些输入进行验证，以确保用户为每个输入都提供了一个值。

如果一个或多个字段为空，表单将再次显示。但是，这一次，空字段旁边将会出现错误字符串“Missing”。如果没有一个字段是空的，那么将以简单的方式显示所提供的值。

你可以在 GitHub 上找到这篇文章[的代码。](https://github.com/sitepoint-editors/basic-form-validation-with-php)

[![PHP & MySQL: Novice to Ninja](img/0c5e27a112706e9dfe4e0f846efcefa0.png)](https://www.sitepoint.com/premium/books/php-mysql-novice-to-ninja-7th-edition/)

## HTML 表单

![The initially loaded form](img/91c2b749f192448183290581b74e06f5.png)

让我们创建 HTML 表单。该表单的目的是让虚拟公司" *The World of Fruit* "对他们的客户进行水果调查。该表单包含三项内容:

*   用户的详细信息(姓名、地址和电子邮件地址)
*   用户的水果消费偏好(他们每天吃的水果量，以及他们最喜欢的水果)
*   如果用户想要免费的小册子

### 文本字段

姓名、地址和电子邮件字段将用如下的`label`和`input`元素编码:

```
<label for="name">Name</label>
  <div>
    <input type="text" name="name" id="name" value="">
  </div>

<label for="address">Address</label>
  <div>
    <input type="text" name="address" id="address" value="">
  </div>

<label for="email">Email</label>
  <div>
    <input type="text" name="email" id="email" value="">
  </div> 
```

HTML 输入元素有几个属性。将`type`设置为`text`将它们定义为接受文本的单行输入字段。`name`属性用于指定字段的名称，并用于在 PHP 处理过程中访问元素。ID 属性将输入与其相关联的标签相关联(通过标签的`for`属性)，这使得表单更易于访问。

### 单选按钮

单选按钮存储用户每天吃多少水果。

```
<label>
  <input type="radio" name="howMany" value="zero"> 0
</label>
<label>
  <input type="radio" name="howMany" value="one"> 1
</label>
<label>
  <input type="radio" name="howMany" value="two"> 2
</label>
<label>
  <input type="radio" name="howMany" value="twoplus"> More than 2
</label> 
```

将`type`设置为`radio`会将输入呈现为单选按钮。请注意每个按钮是如何被赋予相同的名称的。这将单选按钮视为一组，允许用户选择 0、1 或 2。每个按钮的`value`属性都不同，以提供不同的值供用户选择。因为输入在这里被包装在标签中，所以不需要`for`和`id`属性。

### 选择列表

select 元素存储用户选择的最喜欢的水果:

```
<label for="favoriteFruit">My favorite fruit</label>
<div>
  <select name="favoriteFruit[]" id="favoriteFruit" size="4" multiple="">
    <option value="apple">Apple</option>
    <option value="banana">Banana</option>
    <option value="plum">Plum</option>
    <option value="pomegranate">Pomegranate</option>
    <option value="strawberry">Strawberry</option>
    <option value="watermelon">Watermelon</option>
  </select>
</div> 
```

`name`属性是一个数组(由名称后的方括号定义)，因为允许多重选择(由于`multiple`属性的存在)。如果没有`multiple`属性，只有一个选项是可选的。

每个选项元素的`value`属性是将提交给服务器的值，开始和结束`option`标记之间的文本是用户将在选择菜单中看到的值。每个`option`元素必须有一个不同的值。

### 检验盒

```
<label for="brochure">Would you like a brochure?</label>
<div>
  <input type="checkbox" name="brochure" id="brochure" value="Yes">
</div> 
```

复选框元素用于让用户选择他们是否想要一本小册子。如果选中该框，其值将被设置为“是”。否则，它将被有效地设置为“否”。

### 提交按钮

```
<div>
  <input type="submit" name="submit" value="Submit">
</div> 
```

最后但同样重要的是**提交**按钮。它的`value`元素的值显示为按钮的文本。点击它提交表单。

### 表单元素

```
<form method="POST" 
  action="<?php echo htmlspecialchars($_SERVER["PHP_SELF"]);?>"> 
```

接下来是`form`元素。虽然 HTML 表单支持许多属性，但是只需要设置两个属性:`action`和`method`。`method`决定表单内容的提交方式。`action`定义提交表单时将表单内容发送到哪里。

将`action`设置为`POST`意味着表单的内容作为 HTTP 请求主体的一部分发送。这些值可以通过`$_POST`超级全局变量在 PHP 中检索到。替代`POST`的是`GET`，它将表单的值作为 URL 的一部分传递。使用 GET 发送的值可以通过`$_GET`超级全局变量在 PHP 中检索。

方法`POST`和`GET`的主要区别是可见性。网上有很多关于如何选择的文章，但是我的建议是在使用表单时坚持 POST，*除非*你有很好的理由在一个可视的 URL 中传递用户数据。

在上面的例子中，`action`被设置为将`$_SERVER["PHP_SELF"]`的值传递给 PHP 的`htmlspecialchars()`方法的结果，T1 是当前正在执行的脚本的名称。此方法用于将特定的 HTML 字符转换为它们的 HTML 实体名称。比如`>`会转换成`&gt;`。

这做了两件事:

1.  如果用户提交 HTML 标记，它可以防止表单被破坏。
2.  这是一种防范 [XSS(跨站点脚本)攻击](https://owasp.org/www-project-top-ten/2017/A7_2017-Cross-Site_Scripting_(XSS))的手段，攻击者将使用[来试图利用 web 应用程序中的漏洞](http://seancoates.com/blogs/xss-woes)。要了解其他常见的漏洞，请查看[OWASP 十大](https://owasp.org/www-project-top-ten/)。

*注意:在一个实际的应用程序中，你可能不止使用`htmlspecialchars`来净化表单输入，比如像 [laminas-filter](https://docs.laminas.dev/laminas-filter/intro/) 这样的第三方库。*

如果您希望表单回发到生成它的同一个脚本，使用`$_SERVER["PHP_SELF"]`比简单地硬编码一个位置更可取，因为如果您更改了脚本的名称，就不必更新代码。

在我们结束讨论表单之前，使用 HTML 表单时需要记住三件事:

*   所有的表单控件*必须*包含在`form`标签中。
*   文本和表单控件的对齐可以通过多种方式实现。CSS 是这方面的首选，尤其是现在有了新的布局工具，包括 Flexbox 和 Grid。(请不要使用表格进行布局，因为表单元素不是表格数据！)
*   为了获得额外的可访问性，有必要研究一下用于分组表单元素的`fieldset`元素。

## 表单处理

现在您已经用 HTML 定义了表单，让我们来看看处理表单的代码。在此之前，请注意表单可能处于两种状态之一:

1.  第一次加载页面时会加载一个空白表单。
2.  如果表单验证失败，则加载表单。在这种状态下，用户填写的字段将包含提供的值。其他字段将为空，旁边会显示一条错误消息。

### 验证表单内容

当提交表单时，下面的条目将被存储在`$_POST`数组中(或`$_GET`数组，取决于表单的`method`属性)。左侧列中的值取自控件的`name`属性，我还标记了该字段是否是用于验证目的的必填字段。

| 田 | 类型 | 强制或可选 |
| --- | --- | --- |
| 名字 | 单值变量 | 命令的 |
| 地址 | 单值变量 | 命令的 |
| 电子邮件 | 单值变量 | 命令的 |
| 多少 | 单值变量 | 强制–必须选择一个 |
| 喜爱的水果 | 排列 | 强制–必须选择一个 |
| 小册子 | 单值变量 | 可选择的 |

如果用户不遵守这些规则，将会显示一条错误消息。任何已经完成的字段将保持不变，允许用户简单地调整他们的输入并重新提交表单，而不必重新输入数据。

在我们继续之前，我们在本文的代码中执行的验证非常简单。通常，您会执行更复杂的验证，例如:

*   某些字段是否不长于某个长度
*   电子邮件地址是否符合[电子邮件地址规范](https://tools.ietf.org/html/rfc2822)
*   如果开始日期的格式正确并且在某个日期之前或之后

为此，您可能会使用第三方库，如 [laminas-validator](https://docs.laminas.dev/laminas-validator/) 或[Symfony 验证组件](https://symfony.com/doc/current/validation.html)
然而，这超出了本文的范围。

让我们看看验证表单所需的 PHP，它位于页面顶部，表单的 HTML 之前:

```
<?php
$errors = [];
$fields = ['name', 'address', 'email', 'howMany', 'favoriteFruit', 'brochure'];
$optionalFields = ['brochure'];
$values = [];
if ($_SERVER["REQUEST_METHOD"] == "POST") {
    foreach ($fields as $field) {
        if (empty($_POST[$field]) && !in_array($field, $optionalFields)) {
            $errors[] = $field;
        } else {
            $values[$field] = $_POST[$field];
        }
    } 
```

该代码初始化三个数组:

*   `$errors`:最初为空，存储验证失败的表单字段
*   `$fields`:存储所有需要处理的字段名
*   `$optionalFields`:存储可选的字段名称
*   `$values`:最初为空，这是一个关联数组，将包含所提供的字段及其值

之后，它检查用于提交表单的方法是否被设置为`POST`。如果是，它遍历数组中的值并检查`$_POST`超全局是否包含相关的字段名。如果是，并且该字段有值，则该字段及其值存储在`$values`数组中。然而，如果该字段为空，并且不在`$optionalFields`数组中，它将被添加到`$errors`数组中。

在这里的例子中，我们检查的是`$_POST`数组，因为表单的方法被设置为`POST`。如果`method`被设置为`GET`，代码将被更新以检查`$_GET`超全局。

在检查提交的错误后，如果没有记录错误，代码会打印出用户提交的值:

```
 if (empty($errors)) {
        foreach ($fields as $field) {
            if ($field === "favoriteFruit") {
                printf("%s: %s<br />", $field, var_export($_POST[$field], TRUE));
            } else {
                printf("%s: %s<br />", $field, $_POST[$field]);
            }
        };
        exit;
    }
} 
```

运行这段代码后，页面的 HTML 就会呈现出来。假设我们要用用户提供的值再次呈现表单，我们需要稍微更新一下表单。具体来说，需要将一些 PHP 代码合并到每个元素的 HTML 中。

对于输入字段，我们将更新它们以设置它们的值，如下例所示，这将填充 name 字段的值:

```
<input type="text" 
       name="name"
       value="<?php echo htmlspecialchars($values['name']);?>"> 
```

这里使用`htmlspecialchars()`的原因与之前使用`PHP_SELF`的原因相同——防止 XSS 攻击。然后，如果没有提供值，将在输入字段后添加以下 HTML 来显示错误消息:

```
<?php if (in_array('name', $errors)): ?>
  <span class="error">Missing</span>
<?php endif; ?> 
```

![Form submitted but with errors](img/2e698d1d95e38ff785bab30c11b554d8.png)

类`error`的使用提供了一个钩子，用于使用 CSS 对错误消息进行样式化:

```
.error {
  color: #FF0000;
} 
```

### 扩展表单的可用性

稍加努力，您还可以记住用户从单选按钮中做出的选择，并选择:

```
<input type="radio" name="howMany"
 <?php if (isset($howMany) && $howMany == "zero") echo "checked"; ?>
 value="zero"> 0
<input type="radio" name="howMany"
 <?php if (isset($howMany) && $howMany == "one") echo "checked"; ?>
 value="one"> 1
<input type="radio" name="howMany"
 <?php if (isset($howMany) && $howMany == "two") echo "checked"; ?>
 value="two"> 2
<input type="radio" name="howMany"
 <?php if (isset($howMany) && $howMany == "twoplus") echo "checked"; ?>
 value="twoplus"> More than 2 
```

已添加代码来检查与单选按钮关联的变量是否已在验证部分中定义。如果是这样，`checked`将作为元素 HTML 的一部分输出。

现在来谈谈选择菜单。实际上，将代码重组为一个循环比盲目地添加代码来手动检查每个选项更好。该循环可以动态地为选项生成 HTML，并且可以将检查选项是否已被选中的功能合并到其中:

```
<select name="favoriteFruit[]" size="4" multiple="">
<?php
$options = ["apple", "banana", "plum", "pomegranate", "strawberry", "watermelon"];
foreach ($options as $option) {
    printf(
        '<option value="%s" %s>%s</option>',
        $option,
        (in_array($option, $values['favoriteFruit'])) ? "selected" : '', ucfirst($option)
    );
}
?>
</select> 
```

如果你还不熟悉 PHP 循环，你可以阅读“[学习循环](https://www.sitepoint.com/loops)”这篇文章。

注意，这里的区别在于属性`selected`的使用:单选按钮使用`checked`，而`select`选项使用`selected`。这只是我们不得不忍受的那些小矛盾之一。

有一个话题我没有提到，也超出了本文的范围，那就是在验证成功之后，您将如何处理这些数据。有多种可能性，包括将其保存在数据库中或通过电子邮件将数据发送给自己。无论您选择哪一种，您都需要确保:

*   该表单已由用户提交
*   用户输入的数据没有错误

## 摘要

验证是必不可少的，特别是如果您打算将提交的数据保存在数据库或其他形式的持久存储中。验证的时候，记住一句老话 *GIGO(垃圾进，垃圾出)*是值得的，你不会错太多。

在本文中，您学到了四件事:

*   如何创建一个基本的 HTML 表单
*   如何用 PHP 验证表单
*   如何重新显示用户输入
*   如何显示错误消息

有关`$_SERVER`、`$_POST`和`$_GET`的更多信息，请阅读文章“[介绍超级全局变量](https://www.sitepoint.com/introducin-superglobals/)”，当然还有[PHP 文档](http://php.net/manual/en/language.variables.superglobals.php)。

## 分享这篇文章