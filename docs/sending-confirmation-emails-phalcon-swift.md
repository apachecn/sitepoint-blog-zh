# 使用 Phalcon 和 Swift 发送确认电子邮件

> 原文：<https://www.sitepoint.com/sending-confirmation-emails-phalcon-swift/>

今天，发送电子邮件被认为是任何网络应用程序的基本功能。通常，发送电子邮件是为了通知用户网站上发生的某种活动，例如当他注册帐户、更新信息或找到新朋友时。在这个简短的教程中，我将向你展示如何在一个 Phalcon 示例应用程序中通过 Gmail 快速发送电子邮件。你可以从 [GitHub](https://github.com/phpmasterdotcom/sendmail) 下载完整的源代码。我们将建立功能，让我们发送确认电子邮件，并对所述确认电子邮件的点击作出反应。

## 第一步:安装 Phalcon，Phalcon 工具

安装 Phalcon 很容易，只要你按照他们网站上的[说明](http://docs.phalconphp.com/en/latest/reference/install.html)去做。不要忘记安装[费尔康工具](http://docs.phalconphp.com/en/latest/reference/tools.html)！

如果你使用的是 vagger，你也可以按照本教程中的[将整套软件安装到你的盒子中。](https://www.sitepoint.com/provisioning-phalcon-tools-vagrant/)

## 步骤 2:应用程序的初始设置

我们已经成功地建立了开发环境。现在我们开始创建应用程序的框架。打开终端(在 Linux 和 Mac 中)或命令提示符(在 Windows 中为 cmd ),并在保存 web 项目的文件夹中键入以下内容:

```
 phalcon project sendmail
```

这将产生以下文件和文件夹结构:

![sendmail project structure](img/24dd0082fd9dfcb1c3e53563fa0f73c0.png)

试试看。打开你的网络浏览器，输入[http://127 . 0 . 0 . 1/sendmail/](http://127.0.0.1/sendmail/)，或者你给你的项目虚拟主机的地址。如果一切运行正常，如下图所示，继续第 3 步。

![enter image description here](img/c5c6bf7d28fc7d0e8ddaad27ce3ff710.png)

## 步骤 3:初始配置

首先，您需要配置一些东西:

1.  数据库连接(应用程序/配置/配置. php)
2.  模型、视图和控制器目录
3.  基本应用程序 URL
4.  邮件服务器配置

所有配置都存储在`config.php`文件中，如下图所示:

![config.php](img/352cac1cc6cc67ba3fce37d68b72da36.png)

如上图所示输入配置设置，仅用您自己的凭据替换。

关于“邮件”，这里我将驱动程序设置为 smtp。我将主机设置为使用 Gmail 的 SMTP 服务器`smtp.gmail.com`。我通过 SSL 将端口设置为 465，并将`from`设置为我的电子邮件地址，基本上就是我们实际发送电子邮件时使用的电子邮件地址。

## 步骤 4:数据库模式和配置

下面的模式显示了我们将使用的两个表:

```
 CREATE TABLE IF NOT EXISTS `users` (
          `id` int(10) unsigned NOT NULL AUTO_INCREMENT,
          `username` varchar(40) NOT NULL,
          `email` varchar(40) NOT NULL,
          `password` char(60) NOT NULL,
          `mustChangePassword` char(1) DEFAULT NULL,
          `fullName` varchar(50) NOT NULL,
          `active` char(1) DEFAULT NULL,
          PRIMARY KEY (`id`)
        ) ENGINE=InnoDB  DEFAULT CHARSET=utf8 AUTO_INCREMENT=1 ;

    CREATE TABLE IF NOT EXISTS `email_confirmations` (
          `id` int(10) unsigned NOT NULL AUTO_INCREMENT,
          `usersId` int(10) unsigned NOT NULL,
          `code` char(32) NOT NULL,
          `createdAt` int(10) unsigned NOT NULL,
          `modifiedAt` int(10) unsigned DEFAULT NULL,
          `confirmed` char(1) DEFAULT 'N',
          PRIMARY KEY (`id`)
    ) ENGINE=InnoDB  DEFAULT CHARSET=latin1 AUTO_INCREMENT=1 ;
```

我故意省略了如何创建数据库的细节——假设您知道如何做这件事。用您最喜欢的工具创建一个示例数据库，并对其执行上面的代码。

这里有一个关于关系的注释:

*   “用户”模型有许多“email_confirmations”。
*   “电子邮件确认”模型属于“用户”

以后请记住这一点。

## 步骤 5:模型和验证

模型是扩展 Phalcon\Mvc\Model 的类。它必须放在模型目录中。模型文件必须包含单个类；它的类名应该采用 camel 大小写表示法，并且应该与数据库表名相对应。相反，如果您想要一个模型用户和一个数据库表“users ”,您需要使用 [`getSource`方法](http://docs.phalconphp.com/en/latest/reference/models.html)来配置它。

我们现在让 Phalcon 工具为我们生成代码。使用命令行并移动到您的项目目录，执行以下命令:

```
 phalcon model users
    phalcon model email_confirmations
```

让我们为表用户检查示例生成的类`Users`，并相应地升级它:

![enter image description here](img/c38c911356c9ae584052279031a21520.png)

在上面的代码中，我们测试了登录字段是否符合电子邮件规则。此外，我们确保登录是唯一的。注意 return 语句，它检查验证是否返回了任何验证消息。

让我们来看看生成的类`EmailConfirmations`:

![email_confirmation](img/a1a020c0f4d7d9c372b173607f925df7.png)

下面的代码代表了一个非常简单的框架，我将只解释`afterCreate()`和`initialize()`应该是什么样子——其余的代码可以在本文开头链接的库中找到。

1.  在请求期间，`initialize()`方法只被调用一次。在这个方法中，Phalcon 是自动生成一个 hasMany 关系。

```
 public function initialize(){
            $this->belongsTo('usersId', 'Users', 'id', array(
                'alias' => 'user'
            ));
        }
```

第一个参数指示关系中本地模型 EmailConfirmations 的字段；第二个指示被引用模型的名称(用户)，第三个指示被引用模型中的字段名称。`alias`是为这两个模型之间的连接创建另一个名称，因此我们可以通过调用`$emailConfirmationInstance->user`来引用电子邮件确认的用户。

1.  `afterCreate()`方法在帐户创建后向用户发送确认电子邮件。请注意，我们使用抽象的功能来发送电子邮件——电子邮件服务驻留在我们的依赖注入器中，它独立于模型本身来完成这项工作。

```
 public function afterCreate(){
                $this->getDI()->getMail()->send(
                    array($this->user->email => $this->user->username),
                    'Please confirm your email',
                    'confirmation',
                    array( 'confirmUrl' => '/confirm/' . $this->code.'/'. $this->user->email)
                );
            }
```

`afterCreate()`和`beforeValidationOnUpdate()`是事件——参见文档了解更多信息。

## 步骤 6:控制器和库

下面的代码展示了一个非常简单的控制器框架，我将解释`view()`和`create()`动作应该是什么样子，其余的代码可以在源代码中找到。

```
 class SessionController extends ControllerBase{
            public function indexAction() {}
            public function signupAction(){}
            public function loginAction() {}
            public function confirmEmailAction(){}
    }
```

`signupAction()`从表单接收数据并存储到表中，而`confirmEmailAction()`在注册后确认帐户。你可以在 SessionController.php 的[看到完整的文件。](https://github.com/phpmasterdotcom/sendmail/blob/master/app/controllers/SessionController.php)

应用程序的这一部分在[“邮件”组件](https://github.com/phpmasterdotcom/sendmail/blob/master/app/library/Mail.php)中实现。

```
 require_once __DIR__ . '/../../vendor/Swift/swift_required.php';
        class Mail extends Phalcon\Mvc\User\Component
        {

            protected $_transport;

            public function getTemplate($name, $params){}
            public function send($to, $subject, $name, $params){}  
        }
```

在这里，我使用的是 [Swift mail](http://swiftmailer.org/docs/sending.html) 图书馆。通过 composer 安装它，或者就像在链接的 GitHub repo 上一样将它包含在供应商文件夹中(见[此处](https://github.com/phpmasterdotcom/sendmail/tree/master/vendor))。这个类扩展了`Phalcon\Mvc\User\Component`，虽然不是必需的，但它帮助我们通过 DI 容器获取它。现在，我们在服务容器中注册这个类:

```
 <?php
        //app/config/service.php
        //Mail service uses Gmail;
        $di->set('mail', function(){
                return new Mail();
        });
```

函数`getTemplate`定义了电子邮件要使用的模板(详情见源代码)。

```
 public function send($to, $subject, $name, $params)
    {
        //Settings
        $mailSettings = $this->config->mail;
        $template = $this->getTemplate($name, $params);
        // Create the message
        $message = Swift_Message::newInstance()
              ->setSubject($subject)
                              ->setTo($to)
              ->setFrom(array(
                  $mailSettings->fromEmail => $mailSettings->fromName
              ))
              ->setBody($template, 'text/html');
              if (!$this->_transport) {
                $this->_transport = Swift_SmtpTransport::newInstance(
                    $mailSettings->smtp->server,
                    $mailSettings->smtp->port,
                    $mailSettings->smtp->security
                )
                  ->setUsername($mailSettings->smtp->username)
                  ->setPassword($mailSettings->smtp->password);
              }
              // Create the Mailer using your created Transport
            $mailer = Swift_Mailer::newInstance($this->_transport);
            return $mailer->send($message);
        }
```

## 第七步:查看

我使用基于 Twitter 引导库的[脚手架](http://docs.phalconphp.com/en/latest/reference/tools.html)。尽管脚手架是一个临时的解决方案，但这并不意味着它不能看起来漂亮！

请记住，当使用带有 bootstrap 的脚手架时，Phalcon 将只生成带有适当 HTML 类的 HTML 标记——是否包含静态资源(如图像和 CSS)取决于您，因此导航到您的项目根目录，通过添加样式表行来编辑文件 **app\views\index.volt** :

```
 <head>
        <title>Phalcon PHP Framework</title>
        {{stylesheet_link ('http//netdna.bootstrapcdn.com/twitter-bootstrap/2.3.2/css/bootstrap-combined.min.css',false)}} 
        {{stylesheet_link ('css/css.css')}} 
        </head>
        <body>
            {{ content() }}
        </body>
```

这个特殊的例子使用了 Volt 模板引擎。Volt 是一种用 C 语言为 PHP 编写的超快速、设计友好的模板语言。它为您提供了一组助手来以一种简单的方式编写视图。更多信息见[官方文件](http://docs.phalconphp.com/en/latest/reference/volt.html)。

接下来，创建一个注册表单。一个典型的视图将结合使用`Phalcon\Tag`组件，这是一个方便的助手类，帮助创建表单、链接和其他 html 元素:

![form signup](img/b887e7b137d40f9b4c2e380a60be94de.png)

是时候尝试一下了。打开 web 浏览器，访问应用程序，然后转到[应用程序 URL]/注册。

![signup form](img/63bbe75b53cf2fead95a0d7cadb6b239.png)

…然后检查该用户的电子邮件帐户，确认电子邮件已成功发送:

![confirm account](img/84756c7218773bcd831a8373a2562011.png)

完美！我们的电子邮件已发送！点击确认激活账号，就这样！

## 结论

这就是用 Phalcon 和 Gmail 发送电子邮件的全部内容。当然，你可以随心所欲地定制你的电子邮件，让邮件更有吸引力。

更多关于 Phalcon 的信息，请查看[在线文档](http://docs.phalconphp.com/en/latest/index.html)。感谢您的阅读，请在下面留下您的反馈和评论。

## 分享这篇文章