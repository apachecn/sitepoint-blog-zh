# 在 Drupal 8 中使用 Ajax 表单

> 原文：<https://www.sitepoint.com/using-ajax-forms-drupal-8/>

在本文中，我将向您展示一种不用编写一行 JavaScript 代码就能使用 Drupal 8 Ajax API 的简洁方法。为此，我们将回到在[上一篇文章](https://www.sitepoint.com/building-drupal-8-module-blocks-forms/)中为 Drupal 8 构建的第一个定制表单，以及*Ajax fy*它的一些行为，以使它对用户更加友好。

![Drupal 8 logo](img/44344cd73dc67de517bb8f8b99424ca0.png)

该表格的更新版本可以在[这个库](https://github.com/upchuk/d8-demo-modules/tree/master/demo)中找到，名称为`DemoForm`(`demo`模块)。我们在本文中写的代码也可以在那里找到，但是是在一个叫做`ajax`的单独的分支中。如果您想继续，我建议您克隆 repo 并在您的开发环境中安装该模块。

## 演示

虽然名不副实，但`DemoForm`对于说明在 Drupal 8 中编写定制表单的基础非常有帮助。它处理验证、配置，并举例说明了表单 API 的一般用法。当然，它专注于基础，并没有什么引人注目的事情发生。

如果您还记得，或者检查一下代码，您会看到表单显示了一个单独的文本字段，负责收集电子邮件地址并保存为配置。表单验证负责确保提交的电子邮件有一个`.com`结尾(这是一个拙劣的尝试，但足以说明表单验证的原理)。因此，当用户提交表单时，他们将一个新的电子邮件地址保存到配置中，并在屏幕上显示一条确认消息。

在本文中，我们将把电子邮件验证逻辑转移到一个 Ajax 回调中，这样在用户输入完电子邮件地址后，验证就会自动触发，并且在不提交表单的情况下打印出一条消息。同样，这种行为并没有什么了不起的，你会经常在各种形式中看到它(通常是为了验证用户名)。但是对于在 Drupal 8 中查看 Ajax 来说，这是一个很好的练习。

## Ajax 表单

我们需要做的第一件事是将电子邮件验证逻辑从通用的`validateForm()`转移到一个只处理这个方面的方法:

```
/**
 * Validates that the email field is correct.
 */
protected function validateEmail(array &$form, FormStateInterface $form_state) {
  if (substr($form_state->getValue('email'), -4) !== '.com') {
    return FALSE;
  }
  return TRUE;
}
```

正如您所注意到的，我们还稍微改变了逻辑，以确保电子邮件地址以`.com`结尾。

然后，我们可以遵从主验证方法的逻辑，以确保我们现有的行为仍然有效:

```
/**
 * {@inheritdoc}
 */
public function validateForm(array &$form, FormStateInterface $form_state) {
  // Validate email.
  if (!$this->validateEmail($form, $form_state)) {
    $form_state->setErrorByName('email', $this->t('This is not a .com email address.'));
  }
}
```

这样，即使我们的表单以某种方式被提交(通过编程或其他方式)，验证仍然会运行。

接下来，我们需要转向表单定义，特别是电子邮件字段，并使它基于用户交互触发 ajax 请求。这将是用户更改字段值并将焦点从其中移开的行为:

```
$form['email'] = array(
  '#type' => 'email',
  '#title' => $this->t('Your .com email address.'),
  '#default_value' => $config->get('demo.email_address'),
  '#ajax' => [
    'callback' => array($this, 'validateEmailAjax'),
    'event' => 'change',
    'progress' => array(
      'type' => 'throbber',
      'message' => t('Verifying email...'),
    ),
  ],
  '#suffix' => '<span class="email-valid-message"></span>'
);
```

我们在这里新做的是将`#ajax`键与一些相关的键一起添加到数组中。此外，我们在 form 元素后添加了一些标记，作为关于电子邮件有效性的短消息的包装。

数组`#ajax`中的回调指向表单类(`validateEmailAjax()`)中的一个方法，而`event`为 jQuery [变更事件](https://api.jquery.com/change/)添加一个绑定到表单元素的 javascript。或者，您也可以指定一个 path 键来代替回调，但是在我们的例子中，这意味着还必须设置一个路由和一个控制器，这似乎是多余的。我们不想要`wrapper`键，因为我们不打算用返回的内容填充一个区域，而是希望细化回调产生的动作。为此，我们将使用 Ajax 命令。

要了解更多这方面的内容，我鼓励你参考 Ajax 的 Ajax API 页面或表单 API 入口。还有一些其他选项可以用来进一步定制表单元素的 Ajax 行为。

现在是时候在我们的表单类中编写回调方法了。它接收`$form`数组和`$form_state`对象作为来自触发 Ajax 请求的表单的参数:

```
/**
 * Ajax callback to validate the email field.
 */
public function validateEmailAjax(array &$form, FormStateInterface $form_state) {
  $valid = $this->validateEmail($form, $form_state);
  $response = new AjaxResponse();
  if ($valid) {
    $css = ['border' => '1px solid green'];
    $message = $this->t('Email ok.');
  }
  else {
    $css = ['border' => '1px solid red'];
    $message = $this->t('Email not valid.');
  }
  $response->addCommand(new CssCommand('#edit-email', $css));
  $response->addCommand(new HtmlCommand('.email-valid-message', $message));
  return $response;
}
```

简单地说，在这个方法中，我们执行验证并返回一个 Ajax 响应，该响应包含多个命令，这些命令根据验证结果而有所不同。使用 [CssCommand](https://api.drupal.org/api/drupal/core%21lib%21Drupal%21Core%21Ajax%21CssCommand.php/class/CssCommand/8) 我们将一些 css 直接应用于电子邮件表单元素，而使用 [HtmlCommand](https://api.drupal.org/api/drupal/core%21lib%21Drupal%21Core%21Ajax%21HtmlCommand.php/class/HtmlCommand/8) 我们替换指定选择器的内容(还记得表单元素的后缀吗？).

这些命令很大程度上映射到 jQuery 函数，因此很容易掌握。您可以在本页的[中找到所有可用命令的列表。因为我们在这个方法中使用了三个新类，我们必须记住*也在顶部使用*它们:](https://api.drupal.org/api/drupal/core%21modules%21system%21core.api.php/group/ajax/8)

```
use Drupal\Core\Ajax\AjaxResponse;
use Drupal\Core\Ajax\CssCommand;
use Drupal\Core\Ajax\HtmlCommand;
```

差不多就是这样。如果您清除缓存并重新加载表单，在 email 字段中键入并移除焦点将触发回调以验证电子邮件地址。您会注意到那里的小图标(可以在定义中更改)和我们定义的短消息。正确的*电子邮件地址应该以绿色突出显示该字段，并打印 OK 消息，相反，红色用于显示相反的消息。*

如果我们在表单元素定义中指定了一个`wrapper`,我们就可以返回一些内容(或者呈现数组),这些内容会被放在那个选择器中。因此，您可以选择返回内容或 Ajax 命令，但我建议大多数情况下选择后者，因为它们提供了更灵活(和一致)的行为。

## 结论

在本文中，我们看到了一个使用 Ajax 改进表单并使其对最终用户更友好的例子。我们写了零行 javascript 代码来完成这个任务。

在我们的例子中，这真的是一个偏好或爱好的问题。但是如果您正在处理一个有 20 个字段的表单，并且像这样在多个字段上进行验证，那么使用 Ajax 确实很有意义。用户不会因为提交表单后发现输入无效而感到烦恼。

尽管在 Drupal 8 中表单是 Ajax 的主要应用领域，但是还有其他一些方法可以在不编写 JavaScript 的情况下利用它。

曾经的好方法是在任何链接上添加`use-ajax`类。每当点击链接时，Drupal 都会向`href`属性中的 URL 发出一个 Ajax 请求。从回调中，您可以返回 Ajax 命令并根据需要执行各种操作。但是请记住，jQuery 和其他核心脚本并不是为匿名用户加载在所有页面上的(因此 Ajax 将优雅地退化为常规的链接行为)。所以，如果你需要这种行为，确保你[包括这些匿名用户的脚本](http://www.webomelette.com/drupal-8-core-javascript-files-anonymous-users)。

## 分享这篇文章