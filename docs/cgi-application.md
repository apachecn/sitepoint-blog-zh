# CGI::Application:一个简单的、可扩展的 Web 框架

> 原文：<https://www.sitepoint.com/cgi-application/>

上次，我们介绍了将 HTML 而不是 Perl 代码放在单独的模板中的概念。除了提供一种简单的方法来维护站点的外观和感觉，这种方法还从我们的程序中去除了许多不必要的噪音。作为开发人员，我们的任务是考虑手头的业务需求；担心用户界面可能会分散注意力。

下一步是考虑如何在代码中增加结构和可维护性。当我们编写简单的应用程序时，我们可能有一个从第一行运行到最后一行的脚本，但是最终，我们想要一个结构化的方法来构建我们的应用程序，鼓励可重用性和可维护性。基本上，我们想要一个定义良好的结构。

许多 Web 应用程序都有“运行模式”的概念，尽管它们可能不使用这个术语。示例运行模式可能是:

*   创造用户
*   编辑用户
*   列出 usr

运行模式通常编码在页面参数中，并且经常会看到复杂的逻辑，如下面的伪代码所示:

```
if (run_mode  eq 'create_user') { 

    perform create user code 

} elsif (run mode eq 'edit_user') { 

    perform edit user code  

} elsif (run mode eq 'list_users') { 

    perform list code 

} else { 

    error - invalid run mode 

}
```

CGI::Application 旨在为可重用代码提供一个框架，该框架基于运行模式概念，但以一种避免大量意大利面条式代码的方式。因为它是面向对象的(OO ),所以很容易扩展和增强，稍后我们会看到一些插件，它们会使你的工作变得更加容易。但是如果你不是面向对象专家，不要害怕:如果你不想的话，你不需要大量使用面向对象技术。

CGI::Application 鼓励你采用模型-视图-控制器(MVC)框架。在 Web 开发领域，MVC 已经成为工程应用程序的一个流行概念，尽管它的根源早于 Web。模型是您正在工作的实际业务领域。它包括应用程序理解和执行的逻辑和规则。视图只是用户界面，默认情况下 CGI::Application 支持 HTML::Template，尽管可以使用任何模板解决方案。最后，控制器在模型和视图之间进行协调。它接受输入，将其传递给模型组件，并在适当的时候调用视图组件。

##### 示例应用程序

既然我们已经了解了这些术语，是时候发现实现一个简单的应用程序真的很容易了。例如，假设我们需要维护应用程序中的用户，我们需要记录以下详细信息:

*   `username` —用户的登录名
*   `first_name`
*   `last_name`
*   `email`
*   `password`
*   `receive_newsletter` —表示用户是否希望接收时事通讯的标志

我们预先知道我们将需要以下运行模式:

*   `display_user_form` —无论我们是创建新用户还是编辑现有用户，这都是必需的
*   `maintain_user` —从表单中获取输入，并创建或更新用户信息
*   `list_users` —当我们想要编辑一个用户时，我们需要列出当前可以选择的用户

我们的应用程序的框架如下所示:

```
 1  package MyApp::User; 

  2  use strict; 

  3  use base 'CGI::Application'; 

  4  use CGI::Application::Plugin::DBH        (qw/dbh_config dbh/); 

  5  use CGI::Application::Plugin::ConfigAuto (qw/cfg cfg_file/); 

  6  use Data::Dumper; 

  7 

  8  # Define our run modes 

  9  sub setup { 

 10      my $self = shift; 

 11      $self->start_mode('display_user_form'); 

 12      $self->error_mode('error'); 

 13      $self->mode_param('rm'); 

 14      $self->run_modes( 

 15          'display_user_form' => 'display_user_form', 

 16          'maintain_user'     => 'maintain_user', 

 17          'list_users'        => 'list_users', 

 18      ); 

 19  } 

 20 

 21  # Execute the following before we execute the requested run mode 

 22  sub cgiapp_prerun { 

 23  } 

 24 

 25  # Process any fatal errors 

 26  sub error { 

 27      my $self  = shift; 

 28      my $error = shift; 

 29      return "There has been an error: $error"; 

 30  } 

 31 

 32  # Display the form for creating/maintaining users 

 33  sub display_user_form { 

 34  } 

 35 

 36  # Process the submitted form 

 37  sub maintain_user { 

 38  } 

 39 

 40  # List users for editing 

 41  sub list_users { 

 42  } 

 43 

 44  # Rules for validating the submitted form parameters 

 45  sub _user_profile { 

 46  } 

 47 

 48  # We don't want to store plain text passwords.... 

 49  sub _encrypt_password { 

 50  } 

 51 

 52  # Create a new user record in the database 

 53  sub _create_user { 

 54  } 

 55 

 56  # Update an existing user record in the database 

 57  sub _update_user { 

 58  } 

 59 

 60  # Retrieve the list of users from the database 

 61  sub _get_users { 

 62  } 

 63 

 64  # Retrieve the record for a given user_id 

 65  sub _retrieve_user_details { 

 66  } 

 67  1;
```

第 1 行:我们的应用程序在一个包中实现，我们将其命名为`MyApp::User`。Perl 在包中实现它的类。

第 2 行:这个 pragma 打开严格检查。它捕捉脚本语言中经常犯的错误，例如通过强迫开发人员在使用变量之前声明变量而在变量名中犯了一个错别字(`$banana`与`$bananana`不同)。还有其他优势，我们没有时间在这里讨论。您应该总是使用这个 pragma。

第 3 行:这是我们继承 CGI::Application 所有特性的地方。对于许多应用程序来说，这几乎和您需要知道的 OO 一样多。

第 4 行:这个插件处理配置文件。

第 5 行:这个插件为标准的 Perl 数据库接口提供了一个 API。

第 8-18 行:这是我们设置运行模式的地方。

第 10 行:如果没有提供运行模式，使用`display_user_form`作为默认值。

第 11 行:我们可以指定发生致命错误时调用的方法。

第 12 行:查询参数`rm`将包含运行模式名称。

第 13-17 行:我们将每个运行模式名称映射到一个方法，当运行模式被请求时，该方法将被调用。请注意，运行模式名称不必与方法名称相同，但我倾向于保持它们相同，以避免混淆。

第 21-22 行:CGI::Application 将在调用任何运行模式之前调用`cgiapp_prerun`方法。如果您需要设置任何应用程序配置或数据库连接，这将非常有用。

第 25-29 行:处理任何致命错误。在这种情况下，错误处理程序不会做太多事情—被捕获的错误只是显示在浏览器中。然而，在您的生产代码中，您可能会将消息记录到文件或数据库中，并且可能会向系统管理员发送电子邮件。用户会收到一条简单的信息，说:“我们为技术问题道歉。”您不希望显示失败的 SQL 语句，或者任何会暴露应用程序内部工作方式的内容。

第 32-41 行:将被调用的运行模式方法的存根(本质上是我们 MVC 的控制器组件)。我们以后会充实其中的一些。

第 44-45 行:这是我们放置验证代码的地方。按照惯例，我们在任何被认为是私有的方法前加一个下划线。

第 48-65 行:这些行代表了我们 MVC 的模型组件。我们可以把它们放在一个单独的包中，但是在这个应用程序中把所有东西放在一起是有意义的。

我们需要一个实例脚本 user.cgi 来调用这个包:

```
 1  #!c:/perl/bin/perl -T 

  2  use strict; 

  3  use CGI::Carp qw(fatalsToBrowser); 

  4  use MyApp::User; 

  5  my $webapp = MyApp::User->new(tmpl_path => '/path/to/templates/'); 

  6  $webapp->run();
```

就是这样:我们的 CGI 脚本只有 6 行长！简单解释一下:

第 1 行:让 Web 服务器知道我们的 Perl 解释器的位置。

第 3 行:这对调试很有用，因为它在浏览器中显示任何 CGI 脚本错误(其他错误将被我们包的错误运行模式捕获)。您应该从产品代码中删除它。

第 4 行:使用我们的新包装。

第 5 行:基于我们的包创建一个新对象。我们传递模板所在目录的名称。

第 6 行:运行应用程序。

Perl 在`@INC`数组中定义的目录中寻找它的包。换句话说，解释器将在任何一个`@INC`目录中搜索一个名为 MyApp/User.pm 的文件。或者，如果这些目录都不合适，您可以在运行时用`use lib`以编程方式添加到`@INC`:

```
 1  #!c:/perl/bin/perl -T 

  2  use lib 'path/to/my/libs'; 

  3  use strict; 

  4  use CGI::Carp qw(fatalsToBrowser); 

  5  use MyApp::User; 

  6  my $webapp = MyApp::User->new(tmpl_path => '/path/to/templates/'); 

  7  $webapp->run();
```

补充说明:CGI::Application 的众多优点之一是主应用程序是在一个包中实现的，而不是在 CGI 脚本中实现的。这意味着可以用 mod_perl 实例脚本替换引导 CGI 实例脚本。对于那些不知道的人来说，mod_perl 是一个 Apache 模块，它将 perl 解释器编译成 Apache 内核。这样做有很多好处，其中之一就是它让你的代码快得令人眼花缭乱。你可以在 http://perl.apache.org 找到更多关于 mod_perl 的信息。

##### 显示用户表单

***创建模板***

如前所述，默认情况下支持 HTML::Template，因此我们的表单将非常简单:

```
 1  <!doctype html public "-//W3C//DTD HTML 4.0 Transitional//EN">  

  2  <html>  

  3  <head>  

  4  <title>Maintain Users</title>  

  5  </head>  

  6  <body>  

  7  <tmpl_include menu.tmpl><br><br>  

  8  <tmpl_var name=message>  

  9  <tmpl_if some_errors>There are errors in your form</tmpl_if>  

 10  <form action="" method="post">  

 11    <table>  

 12      <tr>  

 13        <td>Username<tmpl_unless user_id>*</tmpl_unless></td>  

 14        <td><tmpl_unless user_id>  

 15                        <input type="Text" name="username" size="15"  

 16                            value="<tmpl_var name=username>">  

 17                    <tmpl_var name=err_username>  

 18                    <tmpl_else>  

 19                        <tmpl_var name=username>  

 20                    </tmpl_unless>  

 21            </td>  

 22      </tr>  

 23      <tr>  

 24        <td>First Name*</td>  

 25        <td><input type="Text" name="first_name" size="15"  

 26                value="<tmpl_var name=first_name>">  

 27                <tmpl_var name=err_first_name></td>  

 28      </tr>  

 29      <tr>  

 30        <td>Last Name*</td>  

 31        <td><input type="Text" name="last_name" size="15"  

 32                value="<tmpl_var name=last_name>">  

 33                <tmpl_var name=err_last_name></td>  

 34      </tr>  

 35      <tr>  

 36        <td>Email</td>  

 37        <td><input type="Text" name="email" size="15"  

 38                value="<tmpl_var name=email>">  

 39                <tmpl_var name=err_email></td>  

 40      </tr>  

 41      <tr>  

 42        <td>Password<tmpl_unless user_id>*</tmpl_unless></td>  

 43        <td><input type="Password" name="password" size="15"  

 44                value="<tmpl_var name=password>">  

 45                <tmpl_var name=err_password> <tmpl_var name=err_user_id></td>  

 46      </tr>  

 47      <tr>  

 48        <td>Repeat Password<tmpl_unless user_id>*</tmpl_unless></td>  

 49        <td><input type="Password" name="password2" size="15"  

 50                value="<tmpl_var name=password2>">  

 51                <tmpl_var name=err_password2></td>  

 52      </tr>  

 53      <tr>  

 54        <td>Receive Newsletter</td>  

 55        <td><input type="Checkbox" name="receive_newsletter" value="y"  

 56                <tmpl_var name=checked>></td>  

 57      </tr>  

 58      <tr>  

 59        <td colspan="2"><input type="Submit" value="Save"></td>  

 60      </tr>  

 61    </table>  

 62    <input type="Hidden" name="user_id" value="<tmpl_var name=user_id>">  

 63    <input type="Hidden" name="rm" value="maintain_user">  

 64  </form>  

 65  </body>  

 66  </html>
```

如果你以前没有遇到过 HTML::Template，看看[以前的文章](https://www.sitepoint.com/introducing-html-template/)。标准 HTML 和我们的模板之间唯一的区别是输入标签的 value 属性的占位符(例如`username`)和字段错误消息(例如`err_username`)。当我们创建新用户时，占位符将为空，并在运行时填充适当的详细信息。

兴趣点包括:

*   第 7 行:我们包括一个简单的菜单，其中有“创建用户”和“编辑用户”选项。
*   第 14-21 行:如果我们正在创建一个新用户，`user_id`将为 0 (false ),我们应该为用户名显示一个输入标签。如果我们修改一个用户，我们只需显示用户名。我们还用星号表示必填字段。当我们编辑用户时，其中一些字段不是强制的，所以如果`user_id`不为 0，我们隐藏星号。
*   第 63 行:当我们提交表单时，CGI::Application 将调用由`"rm"`参数标识的运行模式。

***创建我们的第一个运行模式方法***

`display_user_form()`方法相当简单:

```
 1  sub display_user_form {  

  2      my $self = shift;  

  3      my $errs = shift;  

  4      my $q    = $self->query;  

  5  

  6      my $user_id  = $q->param('user_id') || 0;  

  7      # If we have a user id, then get the user's details  

  8      $self->_retrieve_user_details($user_id) if ($user_id);  

  9  

 10      # Process the template  

 11      my $template = $self->load_tmpl('user_form.tmpl');  

 12  

 13      # Populate the template  

 14      foreach my $form_val qw(username first_name last_name email message) {  

 15          $template->param($form_val, $self->param($form_val) || '');  

 16      }  

 17  

 18      $template->param('checked', 'checked')  

 19          if ($self->param('receive_newsletter') eq 'y');  

 20  

 21      $template->param('user_id', $user_id);  

 22      $template->param($errs) if $errs;  

 23  

 24      #Display the form  

 25      return $template->output();  

 26  }
```

第 3 行:我们现在可以忽略这一点。当我们希望显示我们的验证错误时使用它，我将在后面讨论。

第 4 行:默认情况下，CGI::Application 在后台使用 CGI 模块，所有的查询参数都可供查询方法使用。我们将它们赋给`$q`纯粹是为了方便，因为我们只需要键入`$q->param`来检索任何参数。

第 6 行:从提交的表单中获取`user_id`。如果没有(即我们正在创建一个新用户)，将`id`设置为零。

第 8 行:如果我们正在更新一个用户(`user_id`不为零)，那么检索用户记录。

第 11 行:加载模板。

第 14-16 行:CGI::Application 允许您使用`param`方法保存应用程序范围的参数。我们在第 8 行调用的方法中保存了用户的详细信息，在这里我们将这些值传递给模板。因为 HTTP 是无状态协议，所以应用程序参数将不能被后续请求访问。如果需要持久数据，应该将值保存在会话变量中。

第 18-22 行:设置剩余的模板参数。第 22 行设置了验证表单时可能返回的任何错误消息。验证将在后面讨论。

第 25 行:CGI::Application 为我们处理所有的输出。我们从不发出“print”命令，也不直接输出 HTTP 头。如果您希望修改标题，您可以使用`header_type`和`header_prop`方法。

***处理表单数据***

让我们考虑一个普通版本的`maintain_user`方法。

```
 1  sub maintain_user {  

  2      my $self = shift;  

  3      my $q    = $self->query;  

  4  

  5      if ($q->param('user_id') == 0) {  

  6          $self->_create_user();  

  7      } else {  

  8          $self->_update_user();  

  9      }  

 10  

 11      $self->param('message', 'User saved');  

 12      $self->display_user_form();  

 13  }
```

在这种运行模式下，如果提交的`user_id`为 0，则意味着我们将创建一个新用户，否则我们将更新一个现有用户。我们有另外两个方法，`_create_user`和`_update_user`，它们将完成实际的数据库相关工作。但最终，我们会希望根据一组规则来验证数据:

*   用户名、名字和姓氏是必填项
*   密码必须匹配
*   除非用户选择接收时事通讯，否则电子邮件不是强制性的
*   电子邮件地址必须是电子邮件格式
*   用户名必须至少有 6 个字母字符，最多 10 个字母字符

验证表单可能是一项单调乏味的任务。幸运的是，我们可以使用一个插件来帮我们完成繁重的工作。

##### 介绍 CGI::Application::Plugin::ValidateRM

CGI::Application::Plugin::ValidateRM 是许多其他模块的集合:Data::FormValidator 使用表示为 Perl 数据结构的配置文件来验证用户输入；HTML::FillInForm 通过解析 HTML 本身用数据填充 HTML 表单。

验证数据最简单的方法是创建一个返回验证配置文件的方法:

```
 1  sub _user_profile {   

  2      my $self = shift;   

  3      return {   

  4          required          => [qw(username first_name last_name user_id)],   

  5          optional          => [qw(email receive_newsletter)],   

  6          dependencies      => {receive_newsletter => ['email']},   

  7          dependency_groups => {password_group => [qw/password password2/]},   

  8          filters           => ['trim'],   

  9          constraints       => {   

 10              email    => 'email',   

 11              username => qr/^w{6,10}$/,   

 12              password => [   

 13              {   

 14                  constraint => qr/^w{6,10}$/   

 15              },   

 16              {   

 17                  name       => 'password_mismatch',   

 18                  constraint => sub {   

 19                      my ($password, $password2) = @_;   

 20                      return ($password eq $password2);   

 21                  },   

 22                  params => [qw(password password2)]   

 23              }   

 24              ]   

 25          },   

 26          msgs => {   

 27              any_errors  => 'some_errors',   

 28              prefix      => 'err_',   

 29              constraints => {'password_mismatch' =>    

                                     "Passwords don't match"}   

 30          }   

 31      };   

 32  }
```

第 2 行:如果你没有使用任何对象属性，这可能不是必需的。在这个例子中，我们没有，但是如果我们需要扩展验证例程来包含数据库访问，那么我们将需要访问数据库句柄。我们稍后将讨论数据库访问。

第 3 行:我们返回数据验证数据结构。注意，我们可以在调用方法的散列引用中定义它，但是我们将控制器方法与模型方法分开。如果你非常严格，你可以把它们放在不同的包里，但是我没有发现这在实践中特别有用。

第 3 行:必填字段列表。

第 4 行:可选字段列表。

第 5 行:电子邮件字段是可选的。但是，如果用户点击“接收简讯”选项，则需要电子邮件地址。

第 6 行:对于依赖组，如果输入了一个值，则所有其他值都是必需的。

第 7 行:我通常会修剪前导和尾随空格，因为我的应用程序很少需要这样做。如果空格对你来说很重要的话，就不要写了。

第 9-25 行:这是一个更复杂的约束列表。

第 9 行:ValidateRM 插件附带了一组常见的格式约束，其中之一是检查电子邮件地址。引用文档:“[约束]检查电子邮件是否看起来像电子邮件地址。这将检查输入是否包含一个@和一个二级域名。对地址部分的检查相当宽松。

第 10 行:我们可以使用自己的正则表达式。我们说用户名必须包含最少六个字母数字字符，最多十个字符。因为我试图说明验证插件的有用性，所以我避免使用更复杂的表达方式，以免转移我想表达的观点。您可能决定“123456”不应该是一个有效的用户名，在这种情况下，您可能希望将 regex 改为`"qr/^[a-z]w{2,5}$/i"`，这样第一个字符必须是一个字母。

第 12-25 行:每个字段可以有多个约束。第一个简单地将相同的验证规则应用于我们用于用户名的密码。

第 16-23 行:这一部分说明了三个概念:

1.  您可以命名您的约束。我们稍后在指定自定义错误消息时会用到这一点。

3.  一个约束可以由一个匿名的子程序来执行。如果您的规则比一个简单的模式更复杂，您将需要这样做。

5.  `params`数组提供将被传递给子程序的字段。

第 26-30 行:定义我们将返回的消息。

第 27 行:如果遇到任何错误，那么`some_errors`模板变量将被设置。

第 28 行:每当我们遇到字段错误时，我们会将错误消息传递给名为`err_<original_variable_name>`的模板变量。

第 29 行:我们为`"password_mismatch"`约束提供了一个定制的错误消息。

任何未通过验证的表单都会重新显示相应的消息。默认情况下，在未提供任何值的必填字段旁边将显示“缺失”的错误消息，当提供的数据不符合我们的规则时，将显示“无效”。您可以更改这些默认值或使用自定义错误消息。Data::FormValidator 的文档提供了许多示例；如果您有任何问题，请查看电子邮件列表。

一旦我们建立了概要文件，我们需要修改我们的`maintain_user`方法来使用它:

```
 1  sub maintain_user {   

  2      my $self = shift;   

  3      my $q    = $self->query;   

  4   

 **5      use CGI::Application::Plugin::ValidateRM (qw/check_rm/);   

  6      my ($results, $err_page) =   

  7          $self->check_rm('display_user_form', '_user_profile');   

  8      return $err_page if $err_page;**   

  9   

 10      if ($q->param('user_id') == 0) {   

 11          $self->_create_user();   

 12      } else {   

 13          $self->_update_user();   

 14      }   

 15   

 16      $self->param('message', 'User saved');   

 17      $self->display_user_form();   

 18  }
```

新代码位于第 5-8 行。我们为`check_rm`方法提供了显示表单方法的名称和配置文件方法的名称。

现在，只要有输入错误，用户就会在每个不正确的字段旁边看到一条消息，并且表单值会被重新填充。我们所要做的就是在散列引用中定义规则。

***用户输入安全***

Perl 的一个很大程度上不为人知的特性是污点模式:它假设所有用户输入都是潜在的恶意输入，被污染的输入可能不会在访问系统环境的代码中使用。虽然污点模式显然很有用，但我们可以在验证例程中提供额外的数据检查层。正如我们的验证配置文件所示，很容易添加一个正则表达式来确保输入不会试图注入可能在脚本攻击中使用的 HTML 或 JavaScript。

当从头开始编码时，这样的检查可能相当费力，但是这个 ValidateRM 插件大大减轻了负担。

***附加检查***

出于讨论的目的，我保持了验证配置文件的简单，但是您可能希望包括一些附加的规则:

*   创建记录时，用户名是必需的。更新现有用户时不需要。
*   更新用户时，密码字段是可选的。如果留空，则保留原始密码。
*   我们不应该被允许创建一个已经存在的用户名。

如果您不想自己编写这些检查，您可以在本教程附带的[代码档案中找到它们。](https://www.sitepoint.com/examples/cgiapp/cgiapp.zip)

##### 应用程序配置和数据库连接

对于我们的应用程序来说，另外两个插件需要进一步研究。首先，大多数应用程序需要某种配置。例如，我们的应用程序需要知道数据库登录的详细信息，这样我们就可以维护用户信息。其次，我们需要连接到数据库。

CGI::Application::Plugin::Config Auto 使用 Config::Auto 框架。我们可以将配置信息放在一个单独的文件中，我们的实例脚本将细节传递给我们的应用程序包。CGI::应用::插件::DBH 提供方便的 DBI 访问。它的一个优点是它是延迟加载的，这意味着我们的应用程序直到我们需要时才真正连接到数据库。这特别有用，因为我们的一些运行模式不需要数据库访问。

首先，创建配置文件 myapp.cfg:

```
$cfg{user}            = 'myusername';         

$cfg{password}        = 'mypassword';     

$cfg{dsn}             = 'dbi:driver:mydb';    

$cfg{template_path}   = 'templates/';    

%cfg;
```

接下来，我们需要对实例脚本做一些小的修改:

```
 1  #!c:/perl/bin/perl -T    

  2  use lib 'path/to/my/libs';    

  3  use strict;    

  4  use CGI::Carp qw(fatalsToBrowser);    

  5  use MyApp::User;    

 **6  my $webapp = MyApp::User->new();    

  7  $webapp->cfg_file('myapp.cfg');**    

  8  $webapp->run();
```

我们已经从第 6 行删除了模板路径，并添加了第 7 行。接下来，我们充实一下:

```
 1  sub cgiapp_prerun {    

  2      my $self = shift;    

  3      $self->dbh_config($self->cfg('dsn'), $self->cfg('user'),    

  4          $self->cfg('password'));    

  5      $self->tmpl_path($self->cfg('template_path'));    

  6  }
```

现在可以通过对象属性`$self->dbh`访问数据库句柄。因此，我们将在标准应用程序中编写以下 DBI 代码:

```
$sth = $dbh->prepare("select column from table");
```

相反，我们写道:

```
$sth = $self->dbh->prepare("select column from table");
```

现在我们可以编写代码来创建我们的用户:

```
 1  sub _encrypt_password {    

  2      my $password = shift;    

  3      my @chars    = ('.', '/', 0 .. 9, 'A' .. 'Z', 'a' .. 'z');    

  4      my $salt     = $chars[rand @chars] . $chars[rand @chars];    

  5      return crypt($password, $salt);    

  6  }    

  7    

  8  sub _create_user {    

  9      my $self = shift;    

 10      my $q    = $self->query;    

 11    

 12      my $encrypted_password = _encrypt_password($q->param('password'));    

 13    

 14      my $sql = "insert into app_user (username, password, first_name, last_name,    

 15          email, receive_newsletter) values (?, ?, ?, ?, ?, ?)";    

 16    

 17      $self->dbh->{PrintError} = 0;    

 18      $self->dbh->{RaiseError} = 1;    

 19      $self->dbh->do($sql, undef, ($q->param('username'), $encrypted_password,    

 20          $q->param('first_name'), $q->param('last_name'), $q->param('email'),    

 21          $q->param('receive_newsletter')));    

 22  }
```

第 1-6 行:加密密码——我们不会以纯文本的形式存储它。这种方法也叫`from _update_user()`。

第 8-22 行:在数据库中创建用户。

第 17 行:我们关闭对标准输出错误的打印，因为这将导致 CGI::Application 失败。记住，CGI::Application 为我们处理所有的输出，包括生成 HTTP 头，所以我们不想干涉它。我们可以在`cgiapp_prerun`方法中设置这个属性，但是这需要我们首先连接到数据库，除非我们特别需要访问数据，否则我们不想这样做。

第 18 行:每当遇到 SQL 错误时，我们都希望应用程序终止，在这种情况下，CGI::Application 的信号处理程序将捕获错误，并将其发送给设置中定义的 error 方法。

第 19-21 行:插入数据。

其余运行模式的代码在本教程附带的 zip 文件中。

##### 扩展 CGI::应用程序

比方说，我们所有的包都要求用户登录后才能做任何事情。我们不想在我们编写的每个包中实现这些功能。因此，我们可以编写一个基类 MyApp::Base，作为 CGI::Application 的子类，而不是直接从 CGI::Application 继承。所有其他包都将从 MyApp::Base 继承。也就是说，我们将替换:

```
use base 'CGI::Application';
```

使用:

```
use base 'MyApp::Base';
```

然后，在 MyApp::Base 的`cgiapp_prerun`方法中，我们可以检查用户是否登录，如果没有，就将他们定向到登录页面。因为这个预运行方法是在执行请求的运行模式之前自动调用的，所以它是集中这种逻辑的理想位置。

提示:CGI::Application::Plugin:Session 应该能帮到你。它为 CGI::Session 提供了一个很好的接口，使得创建和读取会话变量变得简单，就像:

```
$self->session->param('logged-in', 'y');
```

并且:

```
return if ($self->session->param('logged-in') eq 'y');
```

##### 摘要

我希望 CGI::Application 的好处是显而易见的，但是让我们总结一下其中的一些好处:

1.  CGI::应用程序为我们调用运行模式。这消除了开发人员在自己实现运行模式逻辑时经常编写的大量繁琐代码。

3.  我们的代码更易于维护。我们可以把模板交给我们的 HTML 设计人员，而我们只需要担心应用程序的逻辑。记住 MVC:数据库方法和验证概要充当我们的模型，运行模式是控制器，模板是我们的视图。一些开发人员可能会稍微改变或重新分配这些组件的职责——可能在多个包中。但本质上，我们已经有了一个强大而简单的模型来开发我们的应用程序。

5.  该框架是可扩展的。我们可以扩展或覆盖任何我们喜欢的方法来满足我们的需要。

7.  有越来越多的插件，包括从日志到流文件的一切。这些插件经常被用作其他模块的包装器，但是它们为我们做了很多繁重的工作。

9.  我们不局限于 CGI 脚本。因为我们几乎所有的代码都是在包中实现的，所以我们可以使用 mod_perl 实例脚本来代替 CGI 的等价物。这样，我们就获得了直接访问 Apache 内部的所有好处，以及超快的代码和持久的数据库连接。

11.  有一个活跃而有益的用户社区。你可以[注册电子邮件列表](mailto:cgiapp-subscribe@lists.erlbaum.net)，或者[搜索存档](http://www.mail-archive.com/cgiapp@lists.erlbaum.net/)。还有一个[相当不错的维基](http://www.cgi-app.org)。

最后，我想指出一些勇敢的开发者已经将 CGI::Application 移植到了其他语言中。PHP 版本已经推出，作者在他的博客中为 T2 提供了一个简单的第一手案例研究。同样，任何 Python 程序员都应该访问 http://thraxil.org/code/cgi_app/的。

## 分享这篇文章