# 使用 Gmail JavaScript API 发送电子邮件

> 原文：<https://www.sitepoint.com/sending-emails-gmail-javascript-api/>

*这篇文章由[西蒙·科灵顿](https://www.sitepoint.com/author/scodrington)进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

在[之前的一篇文章](https://www.sitepoint.com/mastering-your-inbox-with-gmail-javascript-api/)中，我展示了如何使用 [Gmail JavaScript API](https://developers.google.com/gmail/api/) 构建一个基本的 Gmail 收件箱和邮件查看应用。今天，我将使用 Gmail 强大的后端来增强该应用程序的电子邮件发送功能。我们最终将拥有一个可定制的应用程序，它可以显示最近的消息，允许编写新消息，并允许我们回复特定的消息。

没有必要阅读上一篇文章来理解这篇文章(尽管它会让你对主题有更深的理解)。与以往一样，本文的完整源代码可以在[我们的 GitHub repo](https://github.com/sitepoint-editors/gmail-api-javascript-example) 上找到(在文件夹`02 - Sending mail`中)。

## 在您的 Google 帐户上启用 Gmail API

第一步是在你的谷歌账户上设置 Gmail API。这将允许我们创建利用 Gmail 功能的应用程序。自从我们上次查看 Gmail API 以来，Google 已经改变了 API 管理控制台的用户界面，所以这里有一个关于如何创建必要的 Gmail API 凭证的快速更新。

导航到 [Google 开发者控制台](https://console.developers.google.com)并创建一个项目。点击*创建*将把我们带到新项目仪表板界面。从这里我们需要弹出汉堡导航菜单并选择 *API 管理器*，然后在左侧边栏中我们需要选择*凭证*，然后点击下一页的*新凭证*按钮。

![New credentials button](img/c0ef5710a3d2045dd0915983504d3324.png)

现在我们需要创建两组凭证:一个浏览器 API 密钥和一个 OAuth 客户端 ID。

对于浏览器 API 键，选择 *API 键*，然后选择*浏览器键*。在接下来的页面中，我们只需填写*姓名*字段。然而，对于生产，我建议添加一个 HTTP referrer(这将防止我们的 API 密钥被非授权域滥用)。点击*创建*，Google 会生成一个 API 密匙。

对于 OAuth 客户端 ID，再次点击*新凭证*并选择 *OAuth 客户端 ID* 。选择 *Web 应用*作为应用类型，并输入至少一个授权的 JavaScript 源。对于本地开发环境，这可能是 http://localhost 或类似的。我们不需要输入授权重定向 URI。点击*创建*将生成客户端 ID 和客户端密码。

一旦我们填写了必要的字段，我们应该能够在*凭据*部分看到我们的凭据。在浏览器选项卡中保持此信息打开，以便进行下一步。

## 初始设置

### 拿一份代码

既然已经设置了 API 凭证，我们应该检查现有演示应用程序的[源代码。](https://github.com/sitepoint-editors/gmail-api-javascript-example)

```
git clone git@github.com:sitepoint-editors/gmail-api-javascript-example.git 
```

文件夹`01 - Basic client`包含前一篇文章中的代码。这是我们感兴趣的。

### 输入我们的凭据

我们应该在`index.html`中输入我们的凭证:

```
var clientId = 'xxxxxxxxxxxx-xxxxxxxxxxxxxxxxxxxxxxx.apps.googleusercontent.com';
var apiKey = 'xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx'; 
```

### 调整身份验证范围

最后，我们应该调整一下[认证范围](https://developers.google.com/gmail/api/auth/scopes)。以前，我们要求对用户的 Gmail 帐户进行只读访问。但是，发送电子邮件需要进一步的权限。如下修改`index.html`中的 scopes 变量定义(注意 scopes 变量是一个空格分隔的字符串):

```
var scopes =
  'https://www.googleapis.com/auth/gmail.readonly '+
  'https://www.googleapis.com/auth/gmail.send'; 
```

正如在上一篇文章中提到的，当处理其他人的数据时，请求最低限度的权限总是最佳做法——尤其是像他们的电子邮件帐户这样敏感的东西。这两个范围是我们这个应用程序所需要的。奇怪的是，有一个类似命名的作用域(`compose`)，它允许比我们需要的更多的访问。

### 测试它的工作情况

导航到 http://localhost/Gmail-API-JavaScript-example(或放置了`index.html`文件的任何地方)。如果事情按计划进行，应用程序应该向我们请求授权。一旦获得授权，我们应该会看到类似这样的内容:

![Gmail demo app in action](img/49b8f75d7acfc3ff5299323ebbbe207e.png)

## 发送电子邮件

现在我们已经获得了所需的权限，可以开始第一步了——调整 UI 以添加一个撰写按钮。这个按钮将被放置在界面的右上角(在这个例子中，`pull-right` Boostrap 类有助于定位)。

![Compose button](img/4d669d6d960e4cf67691d477dbad27f7.png)

```
<a href="#compose-modal"
   data-toggle="modal"
   id="compose-button"
   class="btn btn-primary pull-right hidden">Compose</a> 
```

默认情况下,“撰写”按钮不显示在界面上。这是为了使它只出现在用户已经验证。为了启用这个功能，我们需要从元素中删除`hidden`类，同时从显示 inbox 的表中删除`hidden`类。这意味着我们应该修改我们的`handleAuthResult()`函数，在`loadGmailApi()`调用之后添加以下内容:

```
$('#compose-button').removeClass("hidden"); 
```

Compose 按钮将简单地打开一个模态，我们也将把它直接添加到 DOM 中。

```
<div class="modal fade" id="compose-modal" tabindex="-1" role="dialog">
  <div class="modal-dialog modal-lg">
    <div class="modal-content">
      <div class="modal-header">
        <button type="button" class="close" data-dismiss="modal" aria-label="Close">
          <span aria-hidden="true">&times;</span>
        </button>
        <h4 class="modal-title">Compose</h4>
      </div>
      <form onsubmit="return sendEmail();">
        <div class="modal-body">
          <div class="form-group">
            <input type="email" class="form-control" id="compose-to" placeholder="To" required />
          </div>

          <div class="form-group">
            <input type="text" class="form-control" id="compose-subject" placeholder="Subject" required />
          </div>

          <div class="form-group">
            <textarea class="form-control" id="compose-message" placeholder="Message" rows="10" required></textarea>
          </div>
        </div>
        <div class="modal-footer">
          <button type="button" class="btn btn-default" data-dismiss="modal">Close</button>
          <button type="submit" id="send-button" class="btn btn-primary">Send</button>
        </div>
      </form>
    </div>
  </div>
</div> 
```

结果应该是这样的:

![Compose modal](img/5f73dc0a5a6c13f67da07bb1369bdbdd.png)

这是一个标准的引导式表单，在提交时调用`sendEmail()`函数。

```
function sendEmail()
{
  $('#send-button').addClass('disabled');

  sendMessage(
    {
      'To': $('#compose-to').val(),
      'Subject': $('#compose-subject').val()
    },
    $('#compose-message').val(),
    composeTidy
  );

  return false;
} 
```

当调用`sendEmail()`时，我们做的第一件事是禁用发送按钮。每当通过 Ajax 执行提交逻辑时，禁用表单提交功能是很重要的，因为这可以防止用户在请求过程中再次点击按钮。接下来，我们从我们的 compose 表单中获取值，并将所有内容交给`sendMessage()`。最后我们返回`false`。当通过 Ajax 处理表单时，从`onsubmit`函数返回`false`是很重要的——它阻止表单提交和重新加载页面。

```
function sendMessage(headers_obj, message, callback)
{
  var email = '';

  for(var header in headers_obj)
    email += header += ": "+headers_obj[header]+"\r\n";

  email += "\r\n" + message;

  var sendRequest = gapi.client.gmail.users.messages.send({
    'userId': 'me',
    'resource': {
      'raw': window.btoa(email).replace(/\+/g, '-').replace(/\//g, '_')
    }
  });

  return sendRequest.execute(callback);
} 
```

这个功能是我们与 Gmail API 交互的地方。它接受电子邮件标题、电子邮件正文和回调函数的对象。

我们首先构建 [RFC 5322](https://tools.ietf.org/html/rfc5322) 电子邮件消息(包括标题)。[文章建议](http://wesmorgan.blogspot.co.uk/2012/07/understanding-email-headers-part-ii.html)RFC 5322 规范要求消息的`Date`和`From`报头有效。然而，我发现在使用 Gmail API 时，这些标题并不是必需的，因为 Gmail 会自动为我们添加这些标题。Gmail API 还添加了自己的`Message-Id`。

一旦我们准备好电子邮件，我们就可以将它发送给 Gmail API，特别是发送给[用户。这里需要注意的非常重要的一点是，**我们必须在名为`resource`的对象中指定电子邮件消息，而不是名为`message`** 的对象。](https://developers.google.com/gmail/api/v1/reference/users/messages/send)[谷歌记录的 JavaScript 示例](https://developers.google.com/gmail/api/v1/reference/users/messages/send#examples)指出该对象应该被命名为`message`——这是不正确的，也是行不通的。请注意，电子邮件消息需要进行 base-64 编码，为此我们使用了 [window.btoa()](https://developer.mozilla.org/en-US/docs/Web/API/WindowBase64/btoa) 。还要注意 Google 的 base-64 实现与`window.btoa()`和`window.atob()`提供的不同——所以我们需要在编码后进行一些字符替换。具体来说，我们必须用`-`替换`+`，用`_`替换`/`。

最后，我们将执行请求，传递回调函数。

```
function composeTidy()
{
  $('#compose-modal').modal('hide');

  $('#compose-to').val('');
  $('#compose-subject').val('');
  $('#compose-message').val('');

  $('#send-button').removeClass('disabled');
} 
```

`composeTidy()`回调函数非常基础。它只是隐藏撰写模式，清除输入字段，然后重新启用发送按钮。

## 回复电子邮件

现在我们可以阅读电子邮件和撰写新邮件，下一个合乎逻辑的步骤是实现回复电子邮件。

和以前一样，我们做的第一件事是修改 UI 来提供对这个新功能的访问。因此，我们将向之前实现的消息视图模式添加一个模式页脚。

![Reply button](img/81584ee01287b3ef9f4998ddf0aa8a92.png)

```
var reply_to = (getHeader(message.payload.headers, 'Reply-to') !== '' ?
  getHeader(message.payload.headers, 'Reply-to') :
  getHeader(message.payload.headers, 'From')).replace(/\"/g, '&quot;');

var reply_subject = 'Re: '+getHeader(message.payload.headers, 'Subject').replace(/\"/g, '&quot;');

$('body').append(
  ...
  '<div class="modal-footer">\
    <button type="button" class="btn btn-default" data-dismiss="modal">Close</button>\
    <button type="button" class="btn btn-primary reply-button" data-dismiss="modal" data-toggle="modal" data-target="#reply-modal"\
    onclick="fillInReply(\
      \''+reply_to+'\', \
      \''+reply_subject+'\', \
      \''+getHeader(message.payload.headers, 'Message-ID')+'\'\
      );"\
    >Reply</button>\
  </div>'
  ...
); 
```

页脚提供了一个回复按钮，它将所有必需的细节(收件人、主题、消息 ID)传递给一个新的回复模式，然后打开这个新模式。`to`参数需要特别注意，所以它是在标记之前定义的。我们应该总是尝试为`to`参数使用`Reply-To`头，但是如果没有提供，那么`From`头就足够了。我们还需要将任何双引号编码为 HTML 实体，以防止我们自己的标记被破坏。`subject`参数需要同样的双引号转义，以及一个“Re:”前缀。

```
function fillInReply(to, subject, message_id)
{
  $('#reply-to').val(to);
  $('#reply-subject').val(subject);
  $('#reply-message-id').val(message_id);
} 
```

将字段传递给回复模态的`fillInReply()`函数非常简单。它只是将给出的数据直接传递到新的回复模式输入字段中。

![Reply modal](img/7aa72a060aa0665c850c9f7ac19b1287.png)

```
<div class="modal fade" id="reply-modal" tabindex="-1" role="dialog">
  <div class="modal-dialog modal-lg">
    <div class="modal-content">
      <div class="modal-header">
        <button type="button" class="close" data-dismiss="modal" aria-label="Close">
          <span aria-hidden="true">&times;</span>
        </button>
        <h4 class="modal-title">Reply</h4>
      </div>
      <form onsubmit="return sendReply();">
        <input type="hidden" id="reply-message-id" />

        <div class="modal-body">
          <div class="form-group">
            <input type="text" class="form-control" id="reply-to" disabled />
          </div>

          <div class="form-group">
            <input type="text" class="form-control disabled" id="reply-subject" disabled />
          </div>

          <div class="form-group">
            <textarea class="form-control" id="reply-message" placeholder="Message" rows="10" required></textarea>
          </div>
        </div>
        <div class="modal-footer">
          <button type="button" class="btn btn-default" data-dismiss="modal">Close</button>
          <button type="submit" id="reply-button" class="btn btn-primary">Send</button>
        </div>
      </form>
    </div>
  </div>
</div> 
```

回复模式与撰写模式非常相似。主要区别在于存储消息 ID 的隐藏字段。这是在电子邮件客户端中正确发送电子邮件所必需的——将主题与前缀“Re:”匹配是不够的。我们还将禁用*至*和*主题*字段，因为此时不应更改它们，它们只是为了提供上下文才可见。一旦提交了回复模态表单，就会调用`sendReply()`函数。

```
function sendReply()
{
  $('#reply-button').addClass('disabled');

  sendMessage(
    {
      'To': $('#reply-to').val(),
      'Subject': $('#reply-subject').val(),
      'In-Reply-To': $('#reply-message-id').val()
    },
    $('#reply-message').val(),
    replyTidy
  );

  return false;
} 
```

`sendReply()`功能与`sendEmail()`基本相同，除了我们现在通过`In-Reply-To`头，它允许电子邮件客户端正确地进行对话。 [Google 文档](https://developers.google.com/gmail/api/guides/sending#sending_messages)声明还需要提供`References`头，但是在我们的测试中，没有它也能工作。一旦回复被发送，就会触发`replyTidy()`回调。

```
function replyTidy()
{
  $('#reply-modal').modal('hide');

  $('#reply-message').val('');

  $('#reply-button').removeClass('disabled');
} 
```

同样，这与我们之前的`composeTidy()`回调基本相同。然而这次没有必要清除*主题*和*到*输入字段，因为我们的`fillInReply()`函数总是会覆盖它们。

## 结束语

我们现在应该有一个工作的应用程序，可以显示最近的消息，允许编写新消息，并允许我们回复特定的消息。

如果你有兴趣更进一步，这款应用还有很大的改进空间；

*   交错授权请求，这样用户只需同意`readonly`范围就可以阅读他们的收件箱。然后，一旦用户点击撰写或回复，就会提示另一个针对`send`范围的授权请求。
*   撰写模式*到*字段应该从`type="email"`更改，以便用户可以输入姓名和电子邮件地址的组合(例如`Jamie Shields <jamie@somewhere.com>`)。
*   撰写模式*到*字段应根据用户的联系人列表自动完成，并允许用户直接从列表中选择收件人。

添加新功能的空间也很大。我希望在未来看到的一些事情包括:

*   添加电子邮件转发功能
*   给电子邮件添加抄送和密件抄送邮件头
*   能够查看电子邮件附件的完整标题集
*   能够发送 HTML 电子邮件(用所见即所得编辑器编写)

如果你有任何其他改进或建议，请随时在评论中添加。

别忘了，完整的源代码可以通过我们的 [GitHub repo](https://github.com/sitepoint-editors/gmail-api-javascript-example) 获得。

## 分享这篇文章