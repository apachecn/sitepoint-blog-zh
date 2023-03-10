# 将电子邮件发送到 Laravel 应用程序

> 原文：<https://www.sitepoint.com/piping-emails-laravel-application/>

## 介绍

在项目管理或支持管理工具中，您会经常看到这种情况:您可以回复一封电子邮件，它会自动显示在 web 应用程序中。不知何故，这些工具能够将这些电子邮件信息直接输入到他们的系统中。

在本文中，我们将看看如何通过管道将电子邮件发送到我们的 Laravel 4 应用程序。为此，我们从一个新的 Laravel 4 项目开始，通过 Composer 安装，如这里的[所示](http://laravel.com/docs/quick)。

```
composer create-project laravel/laravel your-project-name --prefer-dist
```

### 创建 Artisan 命令

为了能够将电子邮件发送到我们的应用程序中，我们必须通过命令行将电子邮件发送到我们的应用程序中。幸运的是，Laravel 有一个名为 Artisan 的命令行工具，它能够执行多项任务。要查看 Artisan 可以运行的所有任务的列表，您可以在项目的根目录中运行`php artisan list`。

在本例中，我们希望它执行一个非常具体的任务:接受一封原始电子邮件，并在我们的应用程序中使用它。不幸的是，这不是 Artisan 能够处理的基本功能之一。我们可以很容易地用一个新命令来扩展它:`php artisan email:parse`。然后，我们将启动 Artisan 并执行某个任务，在本例中，这个任务称为`email:parse`。

我们的第一步是创建这个命令。可以通过 Artisan 自己的用于创建新命令的命令来创建新命令。只需在项目的根目录下运行以下命令:

```
php artisan command:make EmailParserCommand
```

如果一切顺利，现在您将在目录`app/commands`中找到一个名为`EmailParserCommand.php`的文件。在您最喜欢的编辑器中打开它，看看`$name`和`$description`属性。我们可以把它定制成我们想要的任何东西。通过给它一个明确的名称和描述，该命令将很好地列在 artisan 命令列表中。

我把它改成这样，比如:

```
/**
 * The console command name.
 *
 * @var string
 */  protected $name =  'email:parse';  /**
 * The console command description.
 *
 * @var string
 */  protected $description =  'Parses an incoming email.';
```

### 注册命令

当我们在项目的根目录下运行`php artisan email:parse`时，您将会收到一条消息，提示该命令尚未注册。我们的下一步是确保这个命令在 Artisan 中注册。让我们打开文件`app/start/artisan.php`，并在文件末尾添加`Artisan::add(new EmailParserCommand);`来注册我们新创建的命令。我们现在可以再次运行 list 命令来查看我们的`email:parse`命令列表。请注意，这里显示了您刚刚填写的名称和描述。

### 检索原始电子邮件

每当通过 Artisan 调用该命令时，它总是调用`fire`方法。首先，我们必须在这里添加我们对电子邮件的解析。该电子邮件目前位于我们的 IO 流中，我们可以从`php://stdin`中检索到它。我们打开这个 IO 流，然后收集小部分的电子邮件，直到我们读完整个流。

```
/**
 * Execute the console command.
 *
 * @return void
 */  public  function fire()  {  // read from stdin $fd = fopen("php://stdin",  "r"); $rawEmail =  "";  while  (!feof($fd))  { $rawEmail .= fread($fd,  1024);  } fclose($fd);  }
```

发送给我们 Artisan 命令的电子邮件现在驻留在`$rawEmail`变量中。它是整个电子邮件，包含标题、正文和任何附件。

### 解析电子邮件

我们现在有我们的原始电子邮件，但我更喜欢有多个部分的电子邮件。我想检索邮件的标题，如主题和正文。我们可以编写自己的代码来拆分所有这些部分，但是有人已经创建了一个可以在我们的应用程序中使用的包。这个软件包能够将我们的整个电子邮件分成逻辑部分。将下面一行添加到您的`composer.json`文件中，并运行`composer update`

```
"messaged/php-mime-mail-parser":  "dev-master"
```

现在我们需要确保我们可以在我们的命令中实际使用这个包，所以我们再次打开我们的`app/command/EmailParserCommand.php`,我们将这些行添加到顶部:

```
use  MimeMailParser\Parser;
```

现在，我们可以将原始电子邮件解析成不同的部分。将以下代码行添加到 fire 方法的末尾。

```
$parser =  new  Parser(); $parser->setText($rawEmail); $to = $parser->getHeader('to'); $from = $parser->getHeader('from'); $subject = $parser->getHeader('subject'); $text = $parser->getMessageBody('text');
```

我们首先创建一个新的解析器。接下来，我们将原始邮件设置为解析器的文本，最后，我们调用各种不同的方法从邮件头或正文中获取数据。

你现在可以很容易地将你的电子邮件存储在数据库中。例如，如果您有一个电子邮件实体，您可以像这样将电子邮件保存到您的数据库:

```
$email =  new  Email; $email->to = $parser->getHeader('to'); $email->from  = $parser->getHeader('from'); $email->subject = $parser->getHeader('subject'); $email->text = $parser->getMessageBody('text'); $email->save();
```

### 使用附件

你甚至可能想在你的服务器上存储电子邮件的附件。电子邮件解析器类可以处理任何可用的附件。首先，将下面几行再次添加到`app/command/EmailParserCommand.php`类的顶部。

```
use  Illuminate\Filesystem\Filesystem;  use  MimeMailParser\Attachment;
```

现在我们必须用一些处理附件的代码来扩展我们的 fire 方法:

```
$attachments = $parser->getAttachments(); $filesystem =  new  Filesystem;  foreach  ($attachments as $attachment)  { $filesystem->put(public_path()  .  '/uploads/'  . $attachment->getFilename(), $attachment->getContent());  }
```

让我们看看这部分实际上是做什么的。第一行从电子邮件中检索附件。`$attachments`变量是一个附件对象数组。接下来，我们确保创建了一个新的文件系统对象，它将处理服务器上文件的保存。然后我们开始遍历所有附件。我们调用 FileSystem 对象的`put`方法，它接受文件的路径和内容。在这种情况下，我们希望将文件添加到`public/uploads`目录，并使用附件实际拥有的文件名。第二个参数是实际文件的内容。

就是这样！您的文件现在存储在`public/uploads`中。只要确保您的邮件服务器可以通过设置正确的权限将文件添加到该目录中。

### 配置我们的邮件服务器

到目前为止，我们准备了整个应用程序来检索、分割和保存我们的电子邮件。然而，如果你不知道如何给你新创建的 Artisan 命令发送电子邮件，这些代码是没有用的。

根据你使用的工具或邮件服务器的不同，下面你会找到不同的方法将你的邮件发送到你的应用程序。例如，我想将`support@peternijssen.nl`转发给我的应用程序，它位于`/var/www/supportcenter`中。
注意，在你将在下面看到的实际命令中，我每次都添加了`--env=local`，以确保 Artisan 像在开发机器上一样运行。如果你在生产，当然可以去掉这部分。

#### CPanel

如果您正在使用 CPanel，您可以点击`forwarders`上的常规菜单。添加一个新的转发器，并定义您希望将哪个地址转发到您的应用程序。点击高级设置并选择`pipe to program`选项。
在输入字段中，您可以插入下面一行:

```
/usr/bin/php -q /var/www/supportcenter/artisan --env=local email:parse
```

请注意，CPanel 使用相对于您的主目录的路径。

#### 进出口银行

如果在 [Exim](http://www.exim.org/) 上，打开文件`/etc/valiases/peternijssen.nl`。
确保该文件中存在以下行:

```
support@peternijssen.nl:  “|  /usr/bin/php -q /var/www/supportcenter/artisan --env=local email:parse”
```

运行`newaliases`来重建别名数据库。

#### 后缀

在 Postfix 上，在继续之前，确保以下行存在并且在您的`/etc/postfix/main.cf`文件中未被注释:

```
alias_maps = hash:/etc/aliases
alias_database = hash:/etc/aliases 
```

如果您必须更改文件，运行`service postfix reload`来重新加载 postfix

我们现在可以创建一个新的别名，它将通过管道传输到我们的应用程序。
打开`/etc/aliases`并添加以下行:

```
support@peternijssen.nl:  "| /usr/bin/php -q /var/www/supportcenter/artisan --env=local email:parse"
```

运行`newaliases`来重建别名数据库。

#### 发送邮件

使用 Sendmail，您应该首先在`/etc/aliases`文件中创建一个别名:

```
support:  "| /usr/bin/php -q /var/www/supportcenter/artisan --env=local email:parse"
```

运行`newaliases`来重建别名数据库。接下来，确保 Artisan 文件是`chmod`到 755，所以它是可执行的。

最后，将 artisan 文件和 php 本身符号链接到/etc/smrsh

```
ln -s /usr/bin/php /etc/smrsh/php
ln -s /var/www/supportcenter/artisan /etc/smrsh/pipe.php
```

#### QMail

根据您的安装，您必须确保存在以下文件:

```
/usr/local/qmail/mailnames/peternijssen.nl/support/.qmail
```

或者:

```
/usr/local/qmail/mailnames/peternijssen.nl/.qmail-support
```

打开任一文件，添加以下行作为内容:

```
support@peternijssen.nl:  "| /usr/bin/php -q /var/www/supportcenter/artisan --env=local email:parse"
```

## 结论

任何有命令行工具的框架都可以处理你的邮件。这里提供的代码只是一个基本的设置。根据您的项目，您可能希望只允许某些电子邮件地址向您的应用程序发送电子邮件。确保你已经在像 postfix 这样的工具中过滤了邮件，然后再把邮件发送到你的应用程序中。

如果您想使用某种票证系统，您可以很容易地尝试从电子邮件主题中提取支持票证 id，并基于此对电子邮件执行多种不同的操作。

密切关注邮件服务器的日志文件。当实际管道出现故障时，它会给你一些提示，告诉你如何解决。

## 分享这篇文章