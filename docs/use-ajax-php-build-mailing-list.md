# 使用 AJAX 和 PHP 建立你的邮件列表

> 原文：<https://www.sitepoint.com/use-ajax-php-build-mailing-list/>

如果没有感兴趣的观众，一个设计优雅、充满吸引人的内容的网站毫无价值。选择加入邮件列表是联系你的受众并为你的网站带来重复流量的最有效的方法之一。如果访问者喜欢并信任你的网站，他们很可能会信任你，告诉你他们的电子邮件地址——毕竟，他们希望得到新产品、文章或其他相关信息的通知。这是一个与你的网站用户建立关系的好方法，并鼓励他们一次又一次地回到你的网站。

在本文中，我们将学习使用 AJAX 接受邮件列表订阅，而无需刷新页面；有了这种方法，你的注册过程将变得快速而轻松。我们的 AJAX 注册表单也能够接受不使用现代 AJAX 浏览器的访问者的订阅。

本文假设您的邮件列表存储在 MySQL 数据库中，但是只要您有必要的代码将地址添加到您自己的邮件列表中，您应该很容易将本文中的代码应用到您自己的注册过程中。

在我们开始之前，[下载代码](https://www.sitepoint.com/examples/mailinglist/code-archive.zip)，看看我们将在本教程中涵盖的内容。

##### 大局

我们的邮件列表注册系统有三个关键部分:

1.  PHP 页面中的 HTML 表单接收访问者的电子邮件地址。

3.  JavaScript 将监听表单提交，这将触发对具有电子邮件地址的服务器的 AJAX 调用。

5.  服务器上的一个简单的 PHP 页面将接收地址。它将检查电子邮件地址的错误，然后将地址存储在 MySQL 数据库中。成功或错误消息将被发送回 HTML 页面，供用户反馈。

我们还将通过为浏览器中没有启用 JavaScript 的用户编写一个基于 PHP 的注册来确保所有人都可以使用这个注册功能。

为了简化我们系统中的 AJAX 编码，我们将使用流行的 JavaScript 库[原型](http://prototype.conio.net/)来监听表单提交，并处理 AJAX 消息。如果你需要一本关于 Prototype 的入门书，可以看看 Dan Webb 的文章[无痛 JavaScript 使用 Prototype](https://www.sitepoint.com/article/painless-javascript-prototype) 和[JavaScript 库世界杯](https://www.sitepoint.com/article/javascript-library)。

##### 表单收益函数

我们的第一步是构建接收电子邮件地址的表单；然后，我们将连接到 JavaScript 文件来交付功能。该表单如下所示:

```
<form id="addressForm" action="index.php" method="get"> 

  <fieldset> 

    <legend>Join our mailing list!</legend> 

    <p> 

      <input type="text" name="address" id="address" /> 

      <input type="submit" value="Sign Up" /> 

    </p> 

    <p id="response"><?php echo(storeAddress()); ?></p> 

  </fieldset> 

</form>
```

表单本身非常简单。我们只是有一个文本字段来接受用户的电子邮件地址，还有一个提交按钮来触发 AJAX 功能。表单的 ID 非常关键，因为它将允许我们的 JavaScript 监听提交。

请注意表单中包含一个名为`storeAddress`的 PHP 函数输出的段落。这是我们易访问注册表单的核心。当页面第一次加载时，`storeAddress`返回一个单独的不间断空格，这样段落看起来是空的。稍后，当我们的 AJAX 功能被用户提交的电子邮件地址触发时，JavaScript 将在这个段落中插入一条消息，让用户知道发生了什么。或者，如果用户在浏览器中没有启用 JavaScript，表单将被提交，这将导致该页面被再次加载。这个动作将导致`storeAddress`再次被执行。这一次，它将返回一条显示在段落内部的消息。

在页面的顶部，我们将用一个`<script>`标签链接到原型库。我们还将链接到`mailingList.js`，我们接下来会看到:

```
<script type="text/javascript" src="js/prototype.js"></script> 

<script type="text/javascript" src="js/mailingList.js"></script>
```

这种设置的美妙之处在于页面的结构与其功能完全分离，这使得开发和维护变得非常简单。

##### 使用 AJAX 传递地址

在`js`文件夹中，与`prototype.js`文件并排的是一个名为`mailingList.js`的文件。该文件包含驱动特定于该页面的 AJAX 功能的代码。我们在这个文件中的第一项工作是向`form`元素的`submit`事件添加一个事件监听器，这将触发对服务器的 AJAX 调用。然而，我们不能马上这样做——我们需要等到页面被加载，这由窗口对象的`load`事件来表示。

为了添加这些事件监听器，我们将使用 Prototype 的方便的`observe`方法。我们首先为页面的`load`事件添加一个监听器。在这个侦听器中，我们将一个事件侦听器附加到表单的`submit`事件:

```
// Attach handler to window load event 

Event.observe(window, 'load', init, false); 

function init() { 

  // Attach handler to form's submit event 

  Event.observe('addressForm', 'submit', storeAddress); 

}
```

注意，当表单被提交时，我们表单的事件监听器将调用 JavaScript 函数`storeAddress`。这个`storeAddress`函数包含对我们的 PHP 脚本进行 AJAX 调用的 JavaScript。它看起来是这样的:

```
function storeAddress(e) { 

  // Update user interface 

  $('response').innerHTML = 'Adding email address...'; 

  // Prepare query string and send AJAX request 

  var pars = 'address=' + escape($F('address')); 

  var myAjax = new Ajax.Updater('response', 'ajaxServer.php', {method: 'get', parameters: pars}); 

  // Stop form from submitting when JavaScript is enabled 

  Event.stop(e); 

}
```

这个函数非常简单。它首先通过显示“添加电子邮件地址…”消息让用户知道正在发生的事情。接下来，我们从表单中收集信息，为发送 AJAX 请求做准备。注意我们是如何使用 Prototype 的`$F`函数访问文本字段的内容的，当提供给表单元素的 ID 时，该函数获取给定表单元素的值。我们使用 JavaScript 转义函数将用户输入转换为 Unicode 格式；这样，它的 URL 就可以通过`GET`方法交付给 PHP 脚本了。

接下来是这个函数最重要的部分:创建一个新的`Ajax.Updater`对象。当我们创建一个`Ajax.Updater`对象时，我们给它传递一些参数，包括:

1.  要在其中显示服务器响应的元素的 ID

3.  PHP 脚本的 URL

5.  您想要发送到服务器端脚本的数据，以及交付方法(即`POST`或`GET`)

`Ajax.Updater`会将电子邮件地址发送到服务器，并耐心等待响应。当它收到一个响应时，它将显示它来代替我们在方法开始时插入的“添加电子邮件地址…”消息。最后，我们调用`Event.stop`(原型库提供的另一个函数)来阻止表单实际提交到服务器。

##### 建立你的邮件列表数据库

在我们编写 PHP 来存储电子邮件地址之前，我们需要一个地方来存放它们。这个 SQL 将为您构建一个名为`mailinglist`的表:

```
CREATE TABLE `mailinglist` ( 

  `id` INT NOT NULL AUTO_INCREMENT , 

  `email` TEXT NOT NULL , 

  PRIMARY KEY ( `id` ) 

);
```

代码档案中包含一个名为`createTable.php`的 PHP 文件，它将为您创建这个表。然而，在运行这个文件之前，您需要用 MySQL 数据库的详细信息更新`dbConstants.php`文件。我建议您现在更新这个文件，因为我们将在下一节中使用这些常量。

##### 存储电子邮件地址

我们已经做好了将地址发送到服务器的一切准备；现在，PHP 将把地址存储在数据库中，并向 Ajax 对象返回一个字符串来指示成功或失败，从而结束这个过程。我们来看看邮件地址发送到的`ajaxServer.php`文件。

```
<?php 

require_once("inc/storeAddress.php"); 

echo(storeAddress()); 

?>
```

这个非常简单的 PHP 脚本调用一个名为`storeAddress`的函数，并将`storeAddress`返回的消息返回给`Ajax`对象。与`Ajax`对象通信就像打印一个字符串一样简单。

`storeAddress`做的第一件事是将一个名为`$message`的变量初始化为一个不间断的空格。然后，它确保脚本在查询字符串中收到了一个电子邮件地址。如果没有，我们将把`$message`变量集作为一个不间断空间。`$message`将在本功能结束时返回给调用者。

```
function storeAddress() { 

  $message = "&nbsp;"; 

  // Check for an email address in the query string 

  if( !isset($_GET['address']) ){ 

    // No email address provided 

  }
```

一旦我们确定我们有一个可以使用的电子邮件地址，我们就要确保它是一个有效的地址。我们将使用一个正则表达式来查找一些字母数字字符，后跟@符号、更多的字母数字字符、句点和更多的字母数字字符。如果测试失败，我们会将`$message`设置为错误消息，通知用户输入的电子邮件地址无效:

```
 else { 

    // Get email address from the query string 

    $address = $_GET['address']; 

    // Validate Address 

    if(!preg_match("/^[_a-z0-9-]+(.[_a-z0-9-]+)*@ 

    [a-z0-9-]+(.[a-z0-9-]+)*$/i", $address)) { 

      $message = "<strong>Error</strong>: An invalid email address was provided."; 

    }
```

如果地址通过了测试，我们会将它存储在数据库中。为此，我们连接到 MySQL，选择数据库，并运行我们的`INSERT`查询。如果地址存储正确，我们将`$message`设置为成功消息；否则，我们将`$message`设置为错误消息。

```
 else { 

      // Connect to database 

      $con = mysql_connect(DBHOST ,DBUSER, DBPASS); 

      mysql_select_db(DBNAME, $con); 

      // Insert email address into mailinglist table  

      $result = mysql_query("INSERT INTO mailinglist SET email='" . $address . "'"); 

      if(mysql_error()){ 

        $message = "<strong>Error</strong>: There was an error storing your email address."; 

      } 

      else { 

        $message = "Thanks for signing up!"; 

      } 

    }
```

最后，我们将`$message`返回到`ajaxServer.php`:

```
 }   return $message; }
```

现在，加载页面，输入您的电子邮件地址，并提交表单——您应该看到您的电子邮件地址被添加到了`mailinglist`表中，而无需重新加载页面。即使您禁用了 JavaScript，页面的工作方式也非常相似，除了不是使用 JavaScript 将`storeAddress`的输出动态插入页面，而是直接插入页面的 PHP 代码中的 HTML。

AJAX 确实使注册邮件列表变得轻而易举。不需要等待页面重新加载，这会打断访问者的浏览体验，也不需要排除那些禁用 JavaScript 的浏览者。Prototype 使构建 AJAX 功能的过程变得快速而轻松，允许您与站点的粉丝保持密切联系。

## 分享这篇文章