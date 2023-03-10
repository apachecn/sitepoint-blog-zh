# 使用 Node.js 发送电子邮件

> 原文：<https://www.sitepoint.com/sending-email-using-node-js/>

大多数网络应用程序都需要发送奇怪的电子邮件。无论是本质上的[交易](http://blog.mailchimp.com/what-is-transactional-email/)，还是以营销为导向的——例如时事通讯、促销或产品推荐——这都是一个相当普遍的要求。如果你要发送电子邮件，很可能你会想用 HTML 来发送。

实际上发送电子邮件非常简单。也就是说，在大量发送时，你需要考虑各种各样的其他问题，比如处理反弹、跟踪点击以及与垃圾邮件过滤器斗争。

然而，就本文的目的而言，我们更感兴趣的是如何处理 HTML 电子邮件模板，以及由此带来的所有问题。

不幸的是，电子邮件客户端之间的各种限制、怪癖和差异意味着 HTML 电子邮件充满了困难。

然而，有许多关于构建 HTML 电子邮件的好资源，甚至只是在 SitePoint 上。有马西莫的指南[构建你的第一封电子邮件简讯](https://www.sitepoint.com/first-email-newsletter/)，蒂姆的[编码指南](https://www.sitepoint.com/code-html-email-newsletters/)和劳伦的系列[最佳实践](https://www.sitepoint.com/best-practice-email/)。

然而，本文的重点并不在于开发 HTML 电子邮件时所面临的问题，而是一些可以帮助你自动化这个过程的工具。我们将关注两个主要领域:从 Node.js 应用程序发送 HTML 电子邮件，以及使用 Grunt 等工具。

让我们看看几个主要问题和一些解决方案。

## 纯文本版本

虽然电子邮件客户端的 HTML 渲染能力往往远远达不到你可以在浏览器中做的事情，但大多数都支持 HTML-尽管它们可能是初级的。但不是全部。此外，有些人明显更喜欢接收纯文本版本，并会在他们的客户端中阻止 HTML 版本。因此，我们需要发送一个纯文本版本，以及你的所有唱歌，所有跳舞的 HTML 电子邮件。

概括地说，您可以采取两种方法之一——准备一个单独的纯文本模板，或者选择让它从您的 HTML 自动生成。后者是否有效，很可能会归结到你的 HTML 版本的复杂程度和格式，结果可能会不一致。就个人而言，我更喜欢生成一个单独的明文版本，因为它给了我对输出更多的控制，但是如果你更喜欢自动化它，有很多选择。

有一个节点邮件程序(我们很快就会看到)[插件](https://github.com/andris9/nodemailer-html-to-text)可以自动从 HTML 电子邮件中提取文本，它是 [Premailer](http://premailer.dialect.ca/) 执行的众多任务之一，这是我们稍后会看到的另一个工具。

### 内联 CSS

由于许多电子邮件客户端的限制，你应该总是[内联你的 CSS](https://www.sitepoint.com/rules-best-practice-email-design-coding-practices/) 。

我们也不是简单地谈论把你的风格放在你的 HTML 邮件的标签中。相反，CSS 样式需要使用内嵌的`style`属性应用于每个元素。考虑下面的例子:

```
.btn-primary {
  text-decoration: none;
  color: #FFF;
  background-color: #348eda;
  border: solid #348eda;
  border-width: 10px 20px;
  line-height: 2;
  font-weight: bold;
  text-align: center;
  cursor: pointer;
  display: inline-block;
  border-radius: 5px;
  text-transform: capitalize;
}
```

```
<tr>
  <td class="content-block">
    We may need to send you critical information about our service and it is important that we have an accurate email address.
  </td>
</tr>
<tr>
  <td class="content-block">
    <a href="{{ url }}" class="btn-primary">Confirm your email address</a>
  </td>
</tr>
```

下面是相同的 HTML 片段，带有内联的 CSS:

```
<tr style="margin: 0; padding: 0; font-family: Helvetica Neue, Helvetica, Helvetica, Arial, sans-serif; box-sizing: border-box; font-size: 14px;">
  <td class="content-block" style="margin: 0; padding: 0 0 20px; font-family: Helvetica Neue, Helvetica, Helvetica, Arial, sans-serif; box-sizing: border-box; font-size: 14px; vertical-align: top;">
    We may need to send you critical information about our service and it is important that we have an accurate email address.
  </td>
</tr>
<tr style="margin: 0; padding: 0; font-family: Helvetica Neue, Helvetica, Helvetica, Arial, sans-serif; box-sizing: border-box; font-size: 14px;">
  <td class="content-block" style="margin: 0; padding: 0 0 20px; font-family: Helvetica Neue, Helvetica, Helvetica, Arial, sans-serif; box-sizing: border-box; font-size: 14px; vertical-align: top;">
    <a href="{{ url }}" class="btn-primary" style="margin: 0; padding: 0; font-family: Helvetica Neue, Helvetica, Helvetica, Arial, sans-serif; box-sizing: border-box; font-size: 14px; color: #FFF; text-decoration: none; background-color: #348eda; border: solid #348eda; border-width: 10px 20px; line-height: 2; font-weight: bold; text-align: center; cursor: pointer; display: inline-block; border-radius: 5px; text-transform: capitalize;">Confirm your email address</a>
  </td>
</tr>
```

坦率地说，手工做这件事会是一件很麻烦的事情——更不用说让你的 HTML 邮件模板变得几乎不可维护了。这也是一种迫切需要自动化的任务。毫无疑问，我们现在会看到很多解决方案。

## 果汁

Juice 是一个 JavaScript 库，可以自动内联你的 CSS，非常适合 HTML 邮件。只需给它提供一些 HTML 和样式表，它就会把它变成一堆不可维护的东西，就像上面的例子一样。

你可以在 Node.js 应用程序中使用 Juice，使用[模块](https://www.npmjs.org/package/juice2)，使用[咕噜](https://www.npmjs.org/package/grunt-juice-email)，或者使用[大口](https://www.npmjs.org/package/gulp-juice)。

## 咕哝内联 CSS

你也可以使用这个咕哝插件。用法很简单。

安装插件:

```
npm install grunt-inline-css --save-dev
```

注册任务:

```
grunt.loadNpmTasks('grunt-inline-css');
```

最后，对它进行配置，告诉它要处理什么:

```
grunt.initConfig({
  inlinecss: {
    main: {
      options: {
      },
      files: {
        'templates/emails/_inlined/confirm-email.html': 'templates/emails/_raw/confirm-email.html',
        'templates/emails/_inlined/password-reset.html': 'templates/emails/_raw/password-reset.html'
      }
    }
  }
})
```

值得指出的是，在幕后，插件正在使用 Juice。

## 预邮件

[Premailer](http://premailer.dialect.ca/) 是一项用于处理 HTML 电子邮件的在线服务，它执行多项任务:

*   内联您的 CSS
*   将相对路径(如链接)转换为绝对路径
*   根据电子邮件客户端的功能检查 CSS 属性
*   或者，它可以自动为您创建一个替代文本版本

为此使用基于 web 的服务当然很好，但是每次修改时都必须手动复制和粘贴源代码可能会很快变得乏味。幸运的是，还有一个 [API](http://premailer.dialect.ca/api) ，更好的是，[一个包](https://github.com/JedWatson/node-premailer)，它使得从你的节点应用程序使用 API 更加容易。

要安装该软件包，请运行以下命令:

```
npm install premailer-api
```

下面是一个简单的命令行工具示例，它获取一个原始模板，存储为一个名为`in.html`的文件，然后将它发送到 Premailer 进行处理。然后，它将处理后的 HTML 输出到`out.html`，并将纯文本版本输出到`out.txt`:

```
var premailer = require('premailer-api')
  , fs = require('fs');

var template = fs.readFileSync('./in.html', 'utf8');

premailer.prepare(
  {
    html: template 
  }, 
  function(err, email) {  
    fs.writeFileSync('out.html', email.html);
    fs.writeFileSync('out.txt', email.text);
  }
);
```

举例来说，给定以下 HTML:

```
<!-- in.html -->
<html>
  <head>
    <title>My Email</title>
    <style type="text/css">
      a { color: #336699; }
    </style>
  </head>
  <body>
    Styles inlined with 
    <a href="http://premailer.dialect.ca">Premailer</a> via 
    <a href="https://github.com/JedWatson/node-premailer">node-premailer</a>.
  </body>
<html>
```

这将为您提供以下内容:

```
<!-- out.html -->
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.0 Transitional//EN" "https://www.w3.org/TR/REC-html40/loose.dtd">
<html>
<head>
  <title>My Email</title>
  <style type="text/css">
    a { color: #336699; }
  </style>
</head>
<body>
  Styles inlined with 
  <a href="http://premailer.dialect.ca" style="color: #336699;">Premailer</a> via 
  <a href="https://github.com/JedWatson/node-premailer" style="color: #336699;">node-premailer</a>.
</body>
</html>
```

和纯文本版本:

```
// out.txt
Styles inlined with
Premailer ( http://premailer.dialect.ca ) via
node-premailer ( https://github.com/JedWatson/node-premailer ).
```

请注意，除了内联 CSS 之外，Premailer 还转换了链接以适应纯文本。

有许多选项可供您使用——更多详细信息，请查阅 Premailer 文档。

Premailer 有多种使用方式。有一个 [Grunt 插件](https://github.com/dwightjack/grunt-premailer)，一个 [Gulp 插件](https://github.com/justin713/gulp-premailer)，或者你可以按照上面的示例代码运行你自己的命令行工具。

## 其他资产

如果你使用 HTML 格式的电子邮件，它们很可能至少包含一张图片。让你的整封邮件完全基于图片——虽然常见得令人沮丧——是一个巨大的禁忌，但几幅图片将大有助于美化它们。

你可以对你的图像做很多事情。显然，链接的图像需要在外部可用，所以你必须确保你使用的是绝对路径，而不是相对路径。Premailer 是这里的一个选项。

一种常见的方法是将 HTML 电子邮件的图像上传到内容交付网络(CDN)，或亚马逊的 S3 等服务。

[Grunt Email 设计工作流程](https://github.com/leemunroe/grunt-email-design)包集成了 Rackspace 的云文件，或者有一些插件用于[集成 S3](https://github.com/MathieuLoutre/grunt-aws-s3) 。

## 任务执行者

我们已经提到了一些可以在任务运行器中使用的工具，比如 Grunt 或 Gulp。

咕哝/吞咽工作流程示例可能包括:

*   使用 Juice 内联 CSS
*   识别图像，上传到 S3 和纠正他们的路径
*   缩小 HTML
*   预编译车把模板
*   将预编译的模板组合成一个 JST 文件

也许使用 Grunt 处理电子邮件模板最简单的方法是从 [Grunt 电子邮件样板](https://github.com/dwightjack/grunt-email-boilerplate)开始。那里有很多功能——并不是所有的功能你都想用——所以去看看文档，看看它能做什么。自然地，有一些[类似于](https://github.com/drewtempelmeyer/email-boilerplate)的东西。

如果你喜欢 Yeoman，有很多专门用于 HTML 邮件的生成器，比如这个。

## 电子邮件模板库

就 Node.js 解决方案而言，您可能会将[电子邮件模板](https://www.npmjs.org/package/email-templates)库称为 HTML 电子邮件的瑞士军刀。

它负责创建 HTML 电子邮件的几乎整个过程，等等。

该库提供以下功能:

*   支持 ejs、Jade、Swig、车把、徽章和灰尘
*   使用 Less，SASS，Stylus 或 Styl 的 CSS 预处理
*   使用 juice 的 css 内联
*   与 Nodemailer 集成，邮戳
*   支持批量发送

换句话说，它是 Node 的 HTML 电子邮件的厨房水槽。让我们来看看它，以及如何使用它。

## 使用

通过 npm 安装:

```
npm install email-templates
```

接下来，创建一个目录来存放你的模板。在其中，为每种类型的电子邮件创建一个目录。例如，一个目录用于确认电子邮件，另一个用于密码重置指令，以及可能的通用消息包装。

下面是一个目录结构示例:

```
templates
  emails
    confirm-email
    password-reset
    welcome
```

接下来，创建您的模板。至少，你的每一个电子邮件衍生品都需要一个呈现 HTML 版本的模板。它的文件名很重要——它必须被称为`html.ext`，其中`.ext`代表你选择的模板库。因此，如果你使用手柄，例如，你会想把你的文件命名为`html.hbs`、Jade、`html.jade`，等等。注意，您需要确保安装了合适的模板引擎！

很有可能，您还需要创建一个样式表。调用这个`styles.ext`，再次使用扩展告诉库如何处理它。`.css`用于标准 CSS，或者`.scss`或`.less`，如果你想使用预处理程序，依此类推。

如果您喜欢创建自己的纯文本版本，可以创建一个名为`text.ext`的文件。同样的规则也适用于 HTML 模板的扩展，因此它将被命名为类似于`text.hbs`、`text.jade`等。

举例来说，如果您选择的模板语言是 Handlebars，并且您碰巧更喜欢 SCSS，那么您应该如何布置`confirm-email`目录:

```
templates
  emails
    confirm-email
      html.hbs
      text.hbs
      styles.scss
```

接下来，您需要配置您的传输机制。图书馆与[节点邮件](https://github.com/andris9/Nodemailer)和[邮戳](https://github.com/voodootikigod/postmark.js)无缝协作；出于本文的目的，我们将使用 Nodemailer，因为它不需要任何第三方服务，并且是从 Node.ks 发送电子邮件的最流行的选择之一。

在大多数情况下，你可能会想通过 SMTP 使用 Nodemailer，尽管有各种各样的[其他传输](https://github.com/andris9/Nodemailer#available-transports)，从 [Sendmail](https://github.com/andris9/nodemailer-sendmail-transport) 到 [SES](https://github.com/andris9/nodemailer-ses-transport) 和 [Sendgrid](https://github.com/sendgrid/nodemailer-sendgrid-transport) 。

下面是一个使用 SMTP 的示例:

```
var nodemailer = require('nodemailer');
var transport = nodemailer.createTransport(smtpTransport({
  host: 'smtp.yourprovider.org',
  port: 25,
  auth: {
    user: 'username',
    pass: 'password'
  }
}));
```

如果您使用的是使用一些非标准设置的 Gmail，您可以使用以下快捷方式:

```
var nodemailer = require('nodemailer');
var transport = nodemailer.createTransport({
  service: 'gmail',
  auth: {
    user: 'username@gmail.com',
    pass: 'password'
  }
});
```

现在已经配置好了，您可以发送一封电子邮件，如下所示:

```
emailTemplates(templatesDir, function(err, template) {

  if (err) {
    console.log(err);
  } else {

  var locals = {
    email: 'user@example.com',
    url: 'http://acme.com/confirm/xxx-yyy-zzz'
  };

    // Send a single email
    template('confirm-email', locals, function(err, html, text) {
      if (err) {
        console.log(err);
      } else {
        transport.sendMail({
          from: 'Acme Corp <no-reply@acme.com>',
          to: locals.email,
          subject: 'Please confirm your e-mail address',
          html: html,
          text: text
        }, function(err, responseStatus) {
          if (err) {
            console.log(err);
          } else {
            console.log(responseStatus.message);
          }
        });
      }
    });
  }
});
```

### 电子邮件模板库的利与弊

显然，这个库解决了很多 HTML 电子邮件的问题。它也给你很大程度的灵活性。如果你想使用 Jade 和 SASS，你可以——同样的手柄和更少，或 Swig 和手写笔。

不过，有一点——如果你有大量的电子邮件要发送，你可以批量发送，库只会每批处理你的模板一次。然而，下一次你发送一封邮件或一批邮件时，它将再次贯穿整个过程。如果您发送大量电子邮件，您最好创建预编译的模板。

请记住，您需要确保资产的链接和路径是绝对的，还需要优化您的图像或将资产上传到内容交付网络(CDN)。

## 测试你的 HTML 邮件

当然，如果没有测试说明，任何发送 HTML 邮件的指南都是不完整的。

Mailcatcher 是一个非常有用的工具，可以“拦截”从你的应用程序发出的电子邮件。您只需在您的机器上将其作为后台进程运行，配置您的应用程序以使用适当的端口，然后您就可以在您的机器上通过基于 web 的界面查看电子邮件。马修·塞特(Matthew Setter)在 SitePoint 上写了一篇关于它[的全面介绍。](https://www.sitepoint.com/email-debugging-mailcatcher/)

通过比较你的电子邮件在不同客户端的截图，测试你的 HTML 电子邮件也是值得的。

## 结论

HTML 电子邮件可能是一个真正的痛苦，但许多头痛可以通过自动化来缓解。在本文中，我研究了一些选项，既有来自 Node.js 应用程序的，也有使用诸如 Grunt 之类的任务运行器的。希望我已经给了你足够的资源让你开始。环顾四周，看看什么最适合您、您的工作流程和您的应用。如果你知道我没有提到的任何其他有用的工具，请在评论中告诉我。

## 分享这篇文章