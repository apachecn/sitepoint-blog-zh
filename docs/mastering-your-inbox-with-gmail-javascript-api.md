# 使用 Gmail JavaScript API 掌控您的收件箱

> 原文：<https://www.sitepoint.com/mastering-your-inbox-with-gmail-javascript-api/>

在本文中，我们将使用 [Gmail RESTful API](https://developers.google.com/gmail/api/) 构建一个基本的 Gmail 收件箱和邮件查看应用。本文的目的是给你一个很好的起点，让你使用这个 API 创建自己的新 JavaScript 应用程序。我们将使用 [jQuery](http://jquery.com) 和 [Bootstrap](http://getbootstrap.com/) 来减轻代码负担，这样我们就可以专注于快速工作，而不用担心跨浏览器的 JavaScript 不一致和基本样式。

一如既往，本文的完整代码可以在我们的 GitHub repo 上找到。

![Gmail Logo](img/5434f87a4e362b1d2b0f64b9100332d7.png)

## 在您的 Google 帐户上启用 Gmail API

首先，我们需要启用 Gmail API 访问来获取我们的 API 凭证。为此，我们需要在我们最喜欢的网络浏览器中访问[谷歌的开发者控制台](https://console.developers.google.com)。从那里，我们需要创建一个项目(或者选择一个现有的项目)并转到 API 部分。在 Google Apps APIs 部分选择“Gmail API ”,然后点击“启用 API”按钮。

现在我们需要创建两组凭证，一组用于 web 应用程序的 OAuth 2.0 客户端 ID，另一组用于创建浏览器 API 密钥。这可以在 Google 开发者控制台的凭证部分通过点击“添加凭证”按钮来完成。

![Add credentials screenshot](img/64ffd24382f032cc2ab8304219650e16.png)

对于浏览器 API 密钥，我们只需填写“名称”字段。然而，对于生产，我建议添加一个 HTTP referrer(这将防止我们的 API 密钥被非授权域滥用)。对于 OAuth 2.0 客户端 ID，我们必须输入至少一个授权的 JavaScript 源。对于本地开发环境，这可能是`http://localhost`或类似的。我们不需要输入授权重定向 URI。

填写完必要的字段后，我们应该能够在凭据部分看到我们的凭据。让此信息在浏览器标签中保持打开状态，以备后用。

## 连接到 Gmail API

尽管 Gmail API 是一个使用 OAuth 2.0 的标准 REST API，但我们建议使用 Google 自己的 JavaScript 库来连接和使用任何 Google 创作的 API。这是因为 Google 已经将身份验证逻辑和所需的依赖项打包到一个包含文件中——对我们来说工作量更少了！

所以，首先，让我们设置我们的 HTML 文件，我们将使用它作为我们的应用程序的基础。为了这个应用程序的目的，我们将在一个 HTML 文件中包含我们所有的代码。在生产环境中，我建议将 HTML、CSS 和 JavaScript 分离到不同的文件中。

```
<!doctype html>
<html>
  <head>
    <title>Gmail API demo</title>
    <meta charset="UTF-8">

    <link rel="stylesheet" href="//maxcdn.bootstrapcdn.com/bootstrap/3.3.5/css/bootstrap.min.css">
    <link rel="stylesheet" href="//maxcdn.bootstrapcdn.com/bootstrap/3.3.5/css/bootstrap-theme.min.css">
    <style> .hidden{ display: none; } </style>
  </head>
  <body>
    <div class="container">
      <h1>Gmail API demo</h1>

      <button id="authorize-button" class="btn btn-primary hidden">Authorize</button>

      <table class="table table-striped table-inbox hidden">
        <thead>
          <tr>
            <th>From</th>
            <th>Subject</th>
            <th>Date/Time</th>
          </tr>
        </thead>
        <tbody></tbody>
      </table>
    </div>

    <script src="//code.jquery.com/jquery-1.11.3.min.js"></script>
    <script src="//maxcdn.bootstrapcdn.com/bootstrap/3.3.5/js/bootstrap.min.js"></script>

    <script type="text/javascript"> var clientId = 'xxxxxxxxxxxx-xxxxxxxxxxxxxxxxxxxxxxx.apps.googleusercontent.com';
      var apiKey = 'xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx';
      var scopes = 'https://www.googleapis.com/auth/gmail.readonly'; </script>

    <script src="https://apis.google.com/js/client.js?onload=handleClientLoad"></script>
  </body>
</html> 
```

就在底部(在结束的`</body>`标签上方)是我们包含 Google 的 JavaScript 客户端库的地方。请注意最后的查询字符串，它包含回调函数，脚本加载后将执行该函数——稍后我们将使用它来初始化我们的应用程序。上面就是我们定义 API 凭证的地方，我们需要从 Google 开发者控制台凭证部分粘贴这些凭证。我们还定义了我们需要用户的哪些权限，这些权限被称为[范围](https://developers.google.com/gmail/api/auth/scopes)。对于此应用程序的目的，我们只需要只读的 Gmail 访问。向用户请求尽可能少的权限是一个好习惯——这让用户放心，我们不会在他们不知道的情况下代表他们发送电子邮件。

除此之外，我们有一个按钮，允许用户授权我们访问他们的 Gmail 帐户，我们还推出了一个表格，一旦我们获取了收件箱数据，就可以保存这些数据。如前所述，我们已经包含了 jQuery 和 Bootstrap 的必要文件。

## 认证用户

现在，我们将为用户提供一种机制来验证我们访问他们的 Gmail 帐户。如上所述，我们需要构建一个名为`handleClientLoad()`的函数，一旦 Google 的 JavaScript 客户端库加载到页面中，这个函数就会被自动调用。然后，这个函数将调用一系列其他函数，最终导致我们获取它们的收件箱。

```
function handleClientLoad() {
  gapi.client.setApiKey(apiKey);
  window.setTimeout(checkAuth, 1);
}

function checkAuth() {
  gapi.auth.authorize({
    client_id: clientId,
    scope: scopes,
    immediate: true
  }, handleAuthResult);
}

function handleAuthClick() {
  gapi.auth.authorize({
    client_id: clientId,
    scope: scopes,
    immediate: false
  }, handleAuthResult);
  return false;
}

function handleAuthResult(authResult) {
  if(authResult && !authResult.error) {
    loadGmailApi();
    $('#authorize-button').remove();
    $('.table-inbox').removeClass("hidden");
  } else {
    $('#authorize-button').removeClass("hidden");
    $('#authorize-button').on('click', function(){
      handleAuthClick();
    });
  }
}

function loadGmailApi() {
  gapi.client.load('gmail', 'v1', displayInbox);
} 
```

我们应该在同一个脚本块中，将这段代码直接插入到设置 API 凭证的位置下方。

总结一下这个函数调用链所经历的过程:

1.  `handleClientLoad()`简单地设置 API 键，并在 1 毫秒后转到`checkAuth()`。
2.  `checkAuth()`检查用户之前是否已经通过谷歌认证了我们的应用。在这里将`immediate`参数设置为`true`意味着如果用户没有通过身份验证，我们不会用登录/权限模式提示用户。然后，我们将认证结果传递给`handleAuthResult()`。
3.  然后做两件事中的一件；如果用户已经通过认证，它将使用`loadGmailApi()`加载 Gmail API，或者它将在用户界面上显示授权按钮，并附加一个点击事件，该事件将触发`handleAuthClick()`
4.  `handleAuthClick()`只是执行与`checkAuth()`相同的认证功能，但会向用户呈现一个登录/许可模式。一旦用户通过验证，之前的`handleAuthResult()`功能将被触发。
5.  一旦这些系列的功能被执行，并且用户已经通过验证，我们应该总是发现自己处于`loadGmailApi()`功能。这只是从 Google 的 JavaScript 客户端库中加载 Gmail API 功能，然后调用我们的`displayInbox()`函数。

**提示**:你可以在这个页面检查(和撤销)哪些应用程序可以访问你的 Gmail 账户:【https://security.google.com/settings/security/permissions】T2。这在测试时会派上用场。

## 获取并显示用户的收件箱

现在我们已经验证了用户，我们可以继续使用我们的`displayInbox()`函数显示他们的一些数据。我们需要使用以下组件来构建该功能:

首先，我们需要从 Gmail 获取邮件列表。为此，我们需要调用 [Users.messages: list 端点](https://developers.google.com/gmail/api/v1/reference/users/messages/list)。出于此应用程序的目的，我们将请求标记为`INBOX`的最后十条消息:

```
function displayInbox() {
  var request = gapi.client.gmail.users.messages.list({
    'userId': 'me',
    'labelIds': 'INBOX',
    'maxResults': 10
  });

  request.execute(function(response) {
    $.each(response.messages, function() {
      var messageRequest = gapi.client.gmail.users.messages.get({
        'userId': 'me',
        'id': this.id
      });

      messageRequest.execute(appendMessageRow);
    });
  });
} 
```

这将返回一个 JSON 对象，其中包含经过身份验证的用户收到的最后十条消息的 id，以及一些我们不需要的其他外围数据。注意，我们可以使用特殊的`me`的`userId`来表示当前认证的用户。与所有使用 Google 库发出的 API 请求一样，请求应该被分配给一个变量，然后应该调用`execute()`函数来实际发出请求。这个函数接受一个回调函数作为它的参数，并允许您指定一个参数来保存响应。

我们实际上在这里发出了两个 API 请求，不幸的是，list API 端点只返回消息 id，而不是实际的消息数据。因此，在回调函数中，我们需要遍历每条消息，并请求更多特定于该消息的数据。为此，我们需要调用[user . messages:get endpoint](https://developers.google.com/gmail/api/v1/reference/users/messages/get)通过它的 id 获取一条消息，并将响应传递给另一个函数`appendMessageRow()`。

现在我们已经得到了消息数据，我们终于准备好修改 DOM 并向用户展示一些东西了！

```
function appendMessageRow(message) {
  $('.table-inbox tbody').append(
    '<tr>\
      <td>'+getHeader(message.payload.headers, 'From')+'</td>\
      <td>'+getHeader(message.payload.headers, 'Subject')+'</td>\
      <td>'+getHeader(message.payload.headers, 'Date')+'</td>\
    </tr>'
  );
} 
```

我们在这里使用 jQuery 的 [append()函数](http://api.jquery.com/append/)将包含消息数据的行追加到我们之前剔除的 HTML 表中。这应该留给我们一个工作的应用程序，可以向用户显示他们收件箱中的最后十条消息！但是如果你不能真正地阅读邮件，它就没有多大用处，对吗？

**注意**:如果你按照本教程学习，你还需要`getHeader()`实用函数让代码在这里工作。你可以在文章的末尾阅读关于效用函数[。](#utility-functions)

## 显示电子邮件消息内容

因为我们已经从我们的`Users.messages: get`请求中获得了消息内容，所以没有必要再发出任何 API 请求来显示这些数据。我们只需要在现有代码中构建一种机制，以便于显示我们之前获取的现有数据。

为此，我们首先需要添加一个启动消息内容查看器的方法。所以我们将修改上面的`appendMessageRow()`代码，添加一个到主题表格单元格的链接。

```
function appendMessageRow(message) {
  $('.table-inbox tbody').append(
    '<tr>\
      <td>'+getHeader(message.payload.headers, 'From')+'</td>\
      <td>\
        <a href="#message-modal-' + message.id +
          '" data-toggle="modal" id="message-link-' + message.id+'">' +
          getHeader(message.payload.headers, 'Subject') +
        '</a>\
      </td>\
      <td>'+getHeader(message.payload.headers, 'Date')+'</td>\
    </tr>'
  );
} 
```

这是利用 [Bootstrap 的模态](http://getbootstrap.com/javascript/#modals)功能，在点击链接时启动一个预定义的模态窗口。因此，现在我们需要对代码进行另一次修改，以便在每次将消息摘要插入表中时，也在 DOM 中构建一个模态窗口容器。所以我们简单地将这个片段添加到我们之前的`append()`片段之下。

```
$('body').append(
  '<div class="modal fade" id="message-modal-' + message.id +
      '" tabindex="-1" role="dialog" aria-labelledby="myModalLabel">\
    <div class="modal-dialog modal-lg">\
      <div class="modal-content">\
        <div class="modal-header">\
          <button type="button"\
                  class="close"\
                  data-dismiss="modal"\
                  aria-label="Close">\
            <span aria-hidden="true">&times;</span></button>\
          <h4 class="modal-title" id="myModalLabel">' +
            getHeader(message.payload.headers, 'Subject') +
          '</h4>\
        </div>\
        <div class="modal-body">\
          <iframe id="message-iframe-'+message.id+'" srcdoc="<p>Loading...</p>">\
          </iframe>\
        </div>\
      </div>\
    </div>\
  </div>'
); 
```

请注意，我们在这里只是剔除了消息内容面板，并且我们是在 iframe 中这样做的。使用 iframe 是因为如果我们简单地将消息内容直接插入到 DOM 中，它可能会以多种方式破坏我们自己的应用程序。任何尝试过 HTML 电子邮件构建的人都会告诉你，残破、陈旧的 HTML 和内嵌、过时的 CSS 是司空见惯的，所以如果我们将代码直接插入 DOM，它可能会严重破坏我们应用程序的美观。

出于几个原因，我们也不想在构建模型时将 HTML 直接插入 iframe。一个是浏览器不兼容，另一个是如果我们在页面加载时在后台获取并呈现 10 个外部 HTML 页面(包括图像),这可能会影响我们应用程序的初始化速度。

现在我们已经有了一个消息细节表和一个带有空白内容面板的模态窗口，所以是时候实现实际的消息内容显示机制了。在同一个函数中，我们需要在主题单元格链接上附加一个点击事件，以便在请求消息模式时呈现 iframe 的内容。

```
$('#message-link-'+message.id).on('click', function(){
  var ifrm = $('#message-iframe-'+message.id)[0].contentWindow.document;
  $('body', ifrm).html(getBody(message.payload));
}); 
```

这只是访问 iframe(已经存在于 DOM 中)并将我们的消息 HTML 注入到它的`<body>`元素中。为了实现这个功能，需要一个小的解决方案。

### 效用函数

现在，你可能会问我们在前面几个代码片段中使用的那些函数是什么；即`getBody()`、`getHeader()`和`getHTMLPart()`。这些是我们定义的实用函数，用于提取使用 [Gmail API 消息资源](https://developers.google.com/gmail/api/v1/reference/users/messages)时的一些细微差别，该资源以不一致的格式返回多部分电子邮件(嵌套部分)，以及 base64 和 UTF-8 编码的邮件正文。(这些函数的完整源代码可以在我们的 [GitHub repo](https://github.com/sitepoint-editors/gmail-api-javascript-example) 上获得)。

## 一点点造型

为了完善我们的应用程序，将这个 CSS 添加到我们的 HTML 页面的`<head>`部分:

```
iframe {
  width: 100%;
  border: 0;
  min-height: 80%;
  height: 600px;
  display: flex;
} 
```

## 结束语

我们现在应该有一个工作应用程序，可以显示最近的消息摘要列表，以及显示一个完整的 HTML 格式的电子邮件。

![Gmail demo app in action](img/49b8f75d7acfc3ff5299323ebbbe207e.png)

显然，这款应用还有很大的改进空间，尤其是:

*   使用 [JavaScript 的日期对象](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Date)更好的日期格式
*   [小胡子](https://github.com/janl/mustache.js)或[把手](http://handlebarsjs.com/) HTML 模板(将 HTML 排除在 JavaScript 之外)
*   消息的正确日期排序(目前这是无序显示的，因为单个消息数据请求是异步的，所以无论哪一个最先返回，都首先进入表中)
*   能够获取超过 10 条消息并对结果进行分页
*   消息的 Ajax 自动更新(带有新消息的浏览器通知)

我还希望在这个应用程序中添加更多功能，很明显接下来的步骤可能是:

*   添加更多的电子邮件功能，如撰写、回复、转发等。(需要进一步的权限请求)
*   将用户界面与 Gmail 已经提供的区别开来

如果你有任何其他改进或建议，请随时在评论中添加。

完整的源代码可以通过我们的 GitHub repo 获得。

## 分享这篇文章