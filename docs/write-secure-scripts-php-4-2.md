# 用 PHP 4.2 写安全脚本！

> 原文：<https://www.sitepoint.com/write-secure-scripts-php-4-2/>

长久以来，PHP 作为服务器端脚本语言的最大卖点之一就是从表单提交的值会自动被创建为全局变量。从 PHP 4.1 开始，PHP 的开发者推荐了另一种访问提交数据的方法。在 PHP 4.2 中，他们*关闭了*旧的做事方式！正如我将在本文中解释的，这些更改是以安全的名义进行的。我们将一起探索 PHP 处理表单提交和其他数据的新特性，以及如何使用它们来编写更安全的脚本。

##### 这张图怎么了？

考虑下面的 PHP 脚本，该脚本仅在输入正确的用户名和密码时授予对网页的访问权限:

```
<?php 

  // Check the username and password 

  if ($username == 'kevin' and $password == 'secret') 

    $authorized = true; 

?> 

<?php if (!$authorized): ?> 

  <!-- Unauthorized users are prompted for their credentials --> 

  <p>Please enter your username and password:</p> 

  <form action="<?=$PHP_SELF?>" method="POST"> 

    <p>Username: <input type="text" name="username" /><br /> 

       Password: <input type="password" name="password" /><br /> 

       <input type="submit" /></p> 

  </form> 

<?php else: ?> 

  <!-- Super-Secret HTML content goes here --> 

<?php endif; ?>
```

好吧，我敢肯定大约一半的观众只是翻了翻白眼，说“这太愚蠢了——我绝不会犯这样的错误！”但是我保证你们中的很多人会想“嘿，这还不错。我应该写下来！”当然，总有相当困惑的少数人(“什么是 PHP？”).PHP 被设计成一种“简单易用”的脚本语言，初学者可以在几分钟内开始使用；它也应该保护那些初学者不犯类似上面的可怕错误。

需要说明的是，上述脚本的问题在于，您可以在不提供正确的用户名和密码的情况下轻松访问它。只需在浏览器中输入页面地址，并在末尾加上`?authorized=1`。由于 PHP 会自动为提交的每个值创建一个变量——无论是来自表单帖子、URL 查询字符串还是 cookie——这会在脚本中将`$authorized`设置为 1，并将一个未经授权的用户放在上校的秘方前面(向不喜欢吃垃圾食品的读者道歉，他们不会理解这个笑话)。

很容易解决，对吧？只需在脚本顶部默认设置`$authorized`为 false 即可。这里的问题是根本没有必要进行修复！`$authorized`是一个完全在脚本中创建和使用的变量；为什么开发人员要担心保护他或她的每一个变量不被恶意用户提交的值覆盖呢？

##### PHP 4.2 如何改变事物？

从 PHP 4.2 开始，一个全新的 PHP 安装默认关闭了`register_globals`选项，所以 EGPCS 值(EGPCS 是 Environment、Get、Post、Cookies、Server 的缩写——PHP 中所有的外部变量源)不会被创建为全局变量。是的，这个选项仍然可以手动打开，但是 PHP 团队希望你不要这样做。为了遵从他们的意愿，您需要使用另一种方法来获得这些值。

从 PHP 4.1 开始，EGPCS 值现在可以在一组特殊的数组中使用:

*   `$_ENV` —包含系统环境变量
*   `$_GET` —包含查询字符串中的变量，包括来自 GET 表单的变量
*   `$_POST` —包含从发布表单提交的变量
*   `$_COOKIE` —包含所有 cookie 变量
*   `$_SERVER` —包含服务器变量，如`HTTP_USER_AGENT`
*   `$_REQUEST` —包含`$_GET`、`$_POST`和`$_COOKIE`中的所有内容
*   `$_SESSION` —包含所有注册的会话变量

在 PHP 4.1 之前，使用`register_globals`的开发人员关闭了(这也被认为是稍微提升 PHP 性能的好方法)使用像`$HTTP_GET_VARS`这样麻烦的数组来访问这些值。这些新的变量名不仅更短，而且还有一些不错的新特性。

首先，让我们重新编写上一节中被破坏的脚本，以便在 PHP 4.2 下使用(即关闭`register_globals`):

```
<?php  

  $username = $_REQUEST['username'];  

  $password = $_REQUEST['password'];  

  $PHP_SELF = $_SERVER['PHP_SELF'];  

  // Check the username and password  

  if ($username == 'kevin' and $password == 'secret')  

    $authorized = true;  

?>  

<?php if (!$authorized): ?>  

  <!-- Unauthorized users are prompted for their credentials -->  

  <p>Please enter your username and password:</p>  

  <form action="<?=$PHP_SELF?>" method="POST">  

    <p>Username: <input type="text" name="username" /><br />  

       Password: <input type="password" name="password" /><br />  

       <input type="submit" /></p>  

  </form>  

<?php else: ?>  

  <!-- Super-Secret HTML content goes here -->  

<?php endif; ?>
```

如您所见，我只需在脚本顶部添加三行代码:

```
 $username = $_REQUEST['username'];  

  $password = $_REQUEST['password'];  

  $PHP_SELF = $_SERVER'['PHP_SELF'];
```

因为我们期望用户提交用户名和密码，所以我们从`$_REQUEST`数组中获取这些值。使用这个数组允许用户以任意方式传递值:通过 URL 查询字符串(例如，允许用户创建一个书签，为他们自动输入凭据)、通过表单提交或作为 cookie。如果您希望将他们提交凭证的方法限制为表单提交(或者更准确地说，HTTP POST 请求，如果情况危急，可以在没有表单提交的情况下进行模拟)，您可以使用`$_POST`数组来代替:

```
 $username = $_POST['username'];  

  $password = $_POST['password'];  

  $PHP_SELF = $_SERVER['PHP_SELF'];
```

我们还从服务器变量数组中取出常用的`PHP_SELF`变量；像表单变量一样，它不会在禁用`register_globals`的情况下自动创建。因为它在脚本中只使用一次，所以您可能更喜欢在表单代码中直接引用它:

```
 <form action="<?=$_SERVER['PHP_SELF']?>" method="POST">
```

除了“允许”这三个变量之外，脚本没有任何变化。关闭`register_globals`只是迫使开发人员注意来自外部(不可信)来源的数据。

**吹毛求疵者注意:**PHP 的默认`error_reporting`设置仍然是`E_ALL & ~E_NOTICE`，所以如果没有提交“用户名”和“密码”值，试图将它们从`$_REQUEST`或`$_POST`数组中取出不会产生错误消息。如果您在 PHP 设置中使用了更严格的错误检查，您将需要添加更多的代码来检查这些变量是否被首先设置。

##### 但这不意味着更多的打字吗？

是的，在上面这样的简单脚本中，PHP 4.2 中新的做事方式确实需要更多的输入。但是，嘿，往好的方面想——你可以通过按键开始收费了！

不过说真的，PHP 的开发者并不是对你的痛苦完全无动于衷(我有一个同父异母的兄弟，他患有重复性压力损伤)。这些新数组的一个特别之处在于，与所有其他 PHP 变量不同，它们是完全全局的。这对你有什么帮助？让我们稍微扩展一下我们的例子来看看。

为了允许网站上的多个页面要求查看用户名/密码组合，我们将授权码移动到包含文件(`protectme.php`)中，如下所示:

```
<?php /* protectme.php */   

  function authorize_user($authuser, $authpass)   

  {   

    $username = $_POST['username'];   

    $password = $_POST['password'];   

    // Check the username and password   

    if ($username != $authuser or $password != $authpass):   

      ?>   

      <!-- Unauthorized users are prompted for their credentials -->   

      <p>Please enter your username and password:</p>   

      <form action="<?=$_SERVER['PHP_SELF']?>" method="POST">   

        <p>Username: <input type="text" name="username" /><br />   

           Password: <input type="password" name="password" /><br />   

           <input type="submit" /></p>   

      </form>   

      <?php   

      exit();   

    endif;   

  }   

?>
```

现在，我们的受保护页面如下所示:

```
<?php   

  require('protectme.php');   

  authorize_user('kevin','secret');   

?>   

<!-- Super-Secret HTML content goes here -->
```

简单明了，对吧？现在，对于目光敏锐、经验丰富的人来说，这是一个挑战——`authorize_user`函数缺少了什么？

缺少的是在函数内部声明`$_POST`以从全局范围引入它！在 PHP 4.0 中，当`register_globals`打开时，您需要添加一行代码来访问函数中的`$username`和`$password`变量:

```
 function authorize_user($authuser, $authpass)   

  {   

    global $username, $password;   

    ...
```

在 PHP 中，与其他语法相似的语言不同，函数外部的变量在函数内部不会自动可用。您需要用上面演示的`global`行来特别引入它们。

在 PHP 4.0 中关闭了`register_globals`以提高安全性，您将使用`$HTTP_POST_VARS`数组来获取从表单提交的值，但是您仍然必须从全局范围导入该数组:

```
 function authorize_user($authuser, $authpass)   

  {   

    global $HTTP_POST_VARS;   

    $username = $HTTP_POST_VARS['username'];   

    $password = $HTTP_POST_VARS['password'];
```

但是在 PHP 4.1 或更高版本中，特殊的`$_POST`变量(以及上一节中列出的其他特殊变量)总是在所有范围内可用。这就是为什么`$_POST`变量不需要在函数的顶部声明为`global`:

```
 function authorize_user($authuser, $authpass)   

  {   

    $username = $_POST['username'];   

    $password = $_POST['password'];
```

##### 这对会话有什么影响？

特殊的`$_SESSION`数组的引入实际上有助于简化会话代码。不用将全局变量注册为会话变量，然后必须跟踪哪些变量是在什么时候注册的，只需将所有的会话变量称为`$_SESSION['varname']`。

让我们考虑另一个授权示例。这一次，它将使用会话将用户标记为在您的站点上逗留的剩余时间内被授权。首先是 PHP 4.0 版本(启用了`register_globals`):

```
<?php    

  session_start();    

  if ($username == 'kevin' and $password == 'secret')    

  {    

    $authorized = true;    

    session_register('authorized');    

  }    

?>    

<?php if (!$authorized): ?>    

  <!-- Display HTML Form prompting user to log in -->    

<?php else: ?>    

  <!-- Super-secret HTML content goes here -->    

<?php endif; ?>
```

现在，找出安全漏洞。和以前一样，在 URL 的末尾添加`?authorized=1`绕过了安全措施，允许访问页面内容。开发人员可能认为`$authorized`是一个会话变量，而忽略了同样的变量可以通过用户输入轻松设置的事实。

下面是我们添加特殊数组(PHP 4.1)并关闭`register_globals` (PHP 4.2)时脚本的样子:

```
<?php    

  session_start();    

  if ($_POST['username'] == 'kevin' and    

      $_POST['password'] == 'secret')    

    $_SESSION['authorized'] = true;    

?>    

<?php if (!$_SESSION['authorized']): ?>    

  <!-- Display HTML Form prompting user to log in -->    

<?php else: ?>    

  <!-- Super-secret HTML content goes here -->    

<?php endif; ?>
```

看到了吗？直白多了！我们没有将普通变量注册为会话变量，而是直接设置会话变量(在`$_SESSION`数组中)，然后以同样的方式使用它。对于哪些变量是会话变量不再有混淆，您会注意到代码也稍微短了一些！

##### 摘要

在本文中，我解释了 PHP 脚本语言最近变化背后的原因。在 PHP 4.1 中，语言中添加了一组特殊的数组来访问外部数据值。这些数组可用于任何范围，使外部数据访问更加方便。在 PHP 4.2 中，`register_globals`被默认关闭，以鼓励向新数组的迁移，并减少没有经验的开发人员编写不安全的 PHP 脚本的倾向。

## 分享这篇文章