# 使用 SendGrid 处理收到的电子邮件

> 原文：<https://www.sitepoint.com/handle-incoming-email-with-sendgrid/>

如果你使用过 Tumblr 或现已倒闭的 Posterous 等博客服务，你可能会意识到，它们中的许多都允许你绕过它们的网络界面，通过电子邮件向特定的电子邮件地址发送消息来创建帖子。

在本文中，我将介绍如何使用 SendGrid 实现电子邮件到帖子的特性。SendGrid 是一种发送电子邮件的服务——通常是批量发送，但他们也提供了一种不太为人所知的处理新邮件的功能。通过 ping 您选择的 URI，可以将 SendGrid 配置为处理给定域的所有传入消息，通过实现一个简单的 webhook，您可以相应地处理传入邮件。

## 入门指南

我将本文的示例代码基于这个瘦框架框架应用程序。

为了使应用程序更容易调试，我们可以通过将以下内容添加到其`composer.json`文件的`require`部分来实现基于文件的日志记录:

```
"slim/extras": "dev-develop",
```

更新`include/services.php`中框架的实例化，以如下方式配置记录器:

```
$app = new Slim(array(
    'view' => new Twig(),
    'templates.path' => $c['config']['path.templates'],
    'log.writer' => new \Slim\Extras\Log\DateTimeFileWriter(array(
        'path' => dirname($c['config']['path.logs']),
        'name_format' => 'Y-m-d',
        'message_format' => '%label% - %date% - %message%'
    ))
));
```

然后，将示例配置文件复制到`config/config.php`，并设置您自己的配置值(比如您的数据库连接细节)。此外，添加以下行以指定将保存日志文件和上传图像的目录:

```
'path.logs'    => $basedir . 'logs/',
'path.uploads' => $basedir . 'public/uploads/'
```

创建目录，并确保它们都可以被 web 服务器写入。

我们的应用程序将为注册用户提供一个电子邮件别名。通过匹配收件人电子邮件地址中`@`之前的部分，我们可以确定发帖的用户。在现实世界中，您可能希望使别名更加难以猜测，并且可能限制对从特定地址发送的电子邮件的访问(尽管这很容易被欺骗！).

数据库模式定义了两个表来分别保存用户和帖子:

```
CREATE TABLE users (
    id INTEGER NOT NULL AUTO_INCREMENT,
    name VARCHAR(128) NOT NULL ,
    alias VARCHAR(45) NOT NULL ,

    PRIMARY KEY (id) ,
   INDEX alias (alias ASC)
);

CREATE  TABLE posts (
    id INTEGER NOT NULL AUTO_INCREMENT,
    title VARCHAR(255) NOT NULL,
    body TEXT NOT NULL,
    image varchar(255),
    user_id INTEGER NOT NULL,

    PRIMARY KEY (id)
);
```

您还需要一个拥有 [SendGrid](http://sendgrid.com/) 的帐户(对于这个示例应用程序，免费计划应该足够了)。注册后，使用顶部导航导航到开发者页面，然后单击右侧的解析收到的电子邮件。为您的主机名和回叫输入合适的值。

![sendgrid-01](img/4a0192e0b4d3bdbfe29fc5635ae8abf2.png)

当收到给定域的电子邮件时，SendGrid 会向您的 URL 发送一个 POST 请求，其中包括发件人、收件人、正文以及任何文件附件等信息。

最后，您需要为您正在使用的域添加一个 MX 记录。你这样做的方式取决于你的主机提供商，可能会有很大的不同，所以请查阅相关文件。记录需要指向*mx.sendgrid.net*。

## 建立回拨

应用程序应该在您指定的 URL 上响应 POST 请求，例如:

```
$app->post('/endpoints/email', function () use ($app, $c) {
```

如果 SendGrid 尝试“ping”您的端点并得到 4xx 或 5xx 响应，它将排队并重试请求，并持续这样做 3 天。因此，成功的 ping 返回 200 状态非常重要。

当 SendGrid 到达端点时，它会使用收到的电子邮件中的各种信息。这些都记录在[在线 API 参考](http://sendgrid.com/docs/API_Reference/Webhooks/parse.html)中，但是我们特别感兴趣的是:

![sendgrid-02](img/81474e0d9c829c96d86f06f29281b91a.png)

在理想情况下，我们可以直接从“收件人”字段中读取收件人的电子邮件地址，但该字段可以匹配以下任何一个示例(不完全),具体取决于发送方式:

*   `test-user@example.com`
*   `"Test User" <test-user@example.com>`
*   `"Test User" <test-user@example.com>, another-recipient@example.com`
*   `"Test User" <test-user@example.com>; "Another Recipient" <another-recipient@example.com>`

我们需要一个正则表达式来解析可能有多个收件人的内容:

```
$to = $req->post('to');
preg_match_all('/\b[A-Z0-9._%+-]+@(?:[A-Z0-9-]+\.)+[A-Z]+\b/i', $to, $result, PREG_PATTERN_ORDER);

foreach ($result[0] as $toAddress) {
    // handle the from address
}
```

对于每个收件人，我们提取地址的别名部分，并尝试查找符合以下条件的用户:

```
$alias = substr($toAddress, 0, strpos($toAddress, '@'));
$user = $c['db']->users->where('alias = ?', $alias)->fetch();

if ($user) {
    // great, we've got a valid user 
}
```

对于每个收件人，创建帖子可能如下所示:

```
$c['db']->posts()->insert(array(
    'title'   =>  $req->post('subject'),
    'body'    =>  ($req->post('html')) ? $req->post('html') : $req->post('text'),          
    'user_id' =>  $user['id'],
));
```

现在我们有了基本的电子邮件发送功能！当然，这不是很安全，但这超出了本文的范围。

让我们稍微扩展一下应用程序，允许用户将图片作为邮件附件发送到文章中。来自 SendGrid 的 POST 请求包含一个名为`attachments`的参数，该参数包含附加文件的数量。附加文件随请求一起发布，可以像处理从 web 表单上传文件一样进行处理。

```
if ($user) {
    $numAttachments = $req->post('attachments');
    $uploadedFiles = array();

    if ($numAttachments) {
        foreach ($_FILES as $key => $file) {
            $log->info('Saving file to ' . $c['config']['uploads.dir'] . $file['name']);
            move_uploaded_file($file['tmp_name'], $c['config']['uploads.dir'] . $file['name']);
            $uploadedFiles[] = $file['name'];
    }
}
```

为了简单起见，我们只将第一个附加文件的文件名保存到数据库中:

```
$c['db']->posts()->insert(array(
    'title'   => $req->post('subject'),
    'body'    => ($req->post('html')) ? $req->post('html') : $req->post('text'),          
    'user_id' => $user['id'],
    'image'   => (count($uploaded_files)) ? $uploadedFiles[0] : ''
));
```

## 摘要

在本文中，我们看到了接收邮件解析的一个简单用途——一个博客风格应用程序的基本“电子邮件到帖子”类型功能，允许用户通过向一个特殊地址发送电子邮件来创建帖子。

通过注册一个简单的回电，你可以做各种有趣的事情来回复收到的邮件。例如，怎么样:

*   收到电子邮件时发送警报
*   将附件传输到 Dropbox、Copy.com、SkyDrive、S3 等。
*   允许人们通过电子邮件回复来自公告栏、消息系统等的通知。
*   处理退订请求

如果你想到其他有趣的用途，请在评论中告诉我。哦，这篇文章的可下载代码可以在 GitHub 上找到，供你克隆和探索。

## 分享这篇文章