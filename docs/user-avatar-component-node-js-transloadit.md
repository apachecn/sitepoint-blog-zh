# 用 Node.js & TransloadIt 构建用户头像组件

> 原文：<https://www.sitepoint.com/user-avatar-component-node-js-transloadit/>

在这个系列的第一部分，我们看了一下[transload it](https://transloadit.com/)——一个专门处理图像、视频和音频的文件处理服务。如果您还没有阅读，我强烈建议您现在就阅读，因为它涵盖了许多背景概念，您需要理解这些概念才能理解这一部分。

但是，推理、背景和理论已经讲得够多了——让我们通过一个实际例子来说明如何在自己的应用程序中使用该服务来处理一些图像。

出于本教程的目的，我们将为任意应用程序实现一个用户资料照片功能。我们将使用 TransloadIt 来完成以下任务:

1.  拦截文件上传，不上传到你的应用程序，而是上传到他们的服务器。
2.  执行服务器端文件检查，以确保它符合某些标准，例如它是否确实是一个图像。
3.  为上传的图像创建许多不同的衍生物，例如大小，例如各种大小的缩略图以及用于用户个人资料页面的“中等”和“大”版本。
4.  将衍生产品转移到亚马逊 S3 桶中。
5.  在我们的应用程序中显示新上传图像的缩略图。
6.  使用从 TransloadIt 返回的信息让我们的应用程序知道在哪里可以找到结果图像，这样我们就可以在用户记录中存储对它们的引用。

第一步是构建一些包含汇编指令的模板。

## 模板入门

模板包含 JSON 格式的汇编指令。启动您最喜欢的文本编辑器，开始一些 JSON:

```
{

}
```

…让我们开始吧。

### 过滤文件

首先，我们将添加一个步骤，利用 [/file/filter robot](https://transloadit.com/docs/conversion-robots/#file-filter) 来检查上传文件的 MIME 类型，以确保它是一个图像。将以下内容添加到您的空 JSON 文档中:

```
"steps":
  "files": {
    "robot": "/file/filter",
    "accepts": [
      [
        "${file.mime}",
        "regex",
        "image"
      ]
    ],
    "error_on_decline": true
  },
```

我们来分析一下。

我们从关键字`files`标识的步骤开始。你可以随便怎么称呼它，但是`files`在大多数情况下是有意义的。

接下来，我们告诉 TransloadIt 使用`/file/filter/`机器人，它用于对传入的文件执行一些检查。在这种情况下，我们告诉它我们想接受什么；我们要求它提取文件的 MIME 类型，并对其运行正则表达式(`image`)。

> 在 TransloadIt 指令中，变量用美元符号和花括号`${}`表示。指定正则表达式时，只需指定它的主体。

如果我们设置的测试失败了，`error_on_decline`参数确保抛出一个错误，而不是继续下一步。

换句话说，我们告诉 TransloadIt 拒绝任何不是图像的文件。

## 添加调整大小步骤

现在让我们再创建三个步骤，每个步骤都做相同的事情——创建传入图像的导数(即特定大小)。

我们可以随意命名这些步骤，所以我们将使用为这些衍生工具提供一些上下文的名称— `medium`、`large`和`thumbnail`。

让我们来定义这些步骤中的第一步:

```
"medium": {
  "use": ":original",
  "robot": "/image/resize",
  "width": 300,
  "height": 200,
  "resize_strategy": "fit"
},
```

这里我们定义了一个名为`medium`的步骤，它利用了`/image/resize`机器人。这需要许多参数，其中许多是可选的，这里的记录了这些参数[。](https://transloadit.com/docs/conversion-robots#image-resize)

`use`参数告诉它调整原始文件的大小。

在本例中，我们提供了所需的尺寸——300 x 200 像素——并指定了调整大小的策略。这里的记录了可用的调整大小策略[，但本质上`fit`确保图像被调整大小以适应指定的尺寸，同时保持纵横比。](https://transloadit.com/docs/conversion-robots#resize-strategies)

`large`步骤实际上是相同的:

```
"large": {
  "use": ":original",
  "robot": "/image/resize",
  "width": 480,
  "height": 320,
  "resize_strategy": "fit"
},
```

然后是`thumbnail`步:

```
"thumbnail": {
  "use": ":original",
  "robot": "/image/resize",
  "width": 80,
  "height": 80,
  "resize_strategy": "crop"
},
```

这一次我们使用`crop`策略来确保我们最终得到的图像是完美的正方形。

> 为了使这个过程更有效，没有理由不设置`use`参数来告诉 TransloadIt 将缩略图基于已经处理过的大型或中型版本。

在这个阶段，我们已经确保我们正在处理一个图像，并且我们已经对它进行了三次大小调整，以创建三个独立的图像导数。接下来，我们将告诉 TransloadIt 如何处理新创建的衍生品。

## 出口

如前所述，Transloadit 不会长期存储我们的文件——托管不是该服务的全部内容——所以我们需要将文件转移到更永久的地方。

我们将使用`/s3/store`文件导出机器人将文件上传到亚马逊 S3 存储桶。

下面是您可能如何配置该步骤:

```
"export": {
  "use": [
    "medium",
    "large",
    "thumbnail"
  ],
  "robot": "/s3/store",
  "path": "users/profiles/${fields.username}_${previous_step.name}.${file.ext}",
  "key": "YOUR-S3-AUTH-KEY",
  "secret": "YOUR-S3-AUTH-SECRET",
  "bucket": "YOUR-BUCKET-NAME"
}
```

> 您需要将您的 S3 凭据和存储桶名称替换为您自己的凭据和存储桶名称。

这比我们之前的步骤稍微复杂一些，所以让我们来分解一下。

`use`参数告诉机器人对我们调整过大小的每张图片运行这一步，因此每次上传都会在 S3 上生成三个文件。如您所见，`medium`、`large`和`thumbnail`与我们前面三个步骤的标识符相匹配。

然后，我们使用`path`配置值指定用于存储结果文件的键——S3 的路径术语。这一点与 bucket 的完全限定域名结合在一起，后来成为生成的衍生映像的 URI。

在上面的例子中，我们使用了以下模式:

```
users/profiles/${fields.username}_${previous_step.name}.${file.ext}
```

这种模式首先在路径前面加上前缀`users/profiles/`，然后使用一个名为`username`的隐藏表单字段的值，我们将很快定义这个字段。然后它把这个和定义上一步的键连接起来，这个键就是我们的导数的名字。最后，它添加原始文件的扩展名，可通过变量`${file.ext}`访问。

这相当拗口，所以也许最好用一个例子来说明。给定用户名`bob`，该模式将产生以下三个路径:

```
users/profiles/bob_medium.jpg
users/profiles/bob_large.jpg
users/profiles/bob_thumbnail.jpg
```

你可以采用各种各样的命名策略，通过删减和改变你可用的变量。例如，考虑以下模式:

```
users/profiles/${fields.username}${file.meta.width}x${file.meta.width}.${file.ext}
```

这通过连接用户名、结果文件的宽度和高度以及最后文件的扩展名来动态构造文件名。这将导致类似这样的结果:

```
users/profiles/bob480x320.jpg
```

> 请注意，如果图像小于导数的目标尺寸，这些值将反映最终图像，而不是配置的尺寸。

要简单地使用原始文件名:

```
${file.name}
```

为了确保唯一性，以下变量提供了唯一的 32 个字符的前缀:

```
${unique_prefix}
```

关于可用变量的完整列表，请参考文件中关于[装配变量](https://transloadit.com/docs#assembly-variables)的章节。

## 上传模板

将所有这些步骤放在一起，我们的组装说明——它构成了我们的模板——如下所示:

```
{
  "steps": {
    "files": {
      "robot": "/file/filter",
      "accepts": [
        [
          "${file.mime}",
          "regex",
          "image"
        ]
      ],
      "error_on_decline": true
    },
    "medium": {
      "use": ":original",
      "robot": "/image/resize",
      "width": 300,
      "height": 200,
      "resize_strategy": "fit"
    },
    "large": {
      "use": ":original",
      "robot": "/image/resize",
      "width": 480,
      "height": 320,
      "resize_strategy": "fit"
    },
    "thumbnail": {
      "use": ":original",
      "robot": "/image/resize",
      "width": 80,
      "height": 80,
      "resize_strategy": "crop"
    },
    "export": {
      "use": [
        "medium",
        "large",
        "thumbnail"
      ],
      "robot": "/s3/store",
      "path": "users/profiles/${fields.username}_${previous_step.name}.${file.ext}",
      "key": "YOUR-S3-AUTH-KEY",
      "secret": "YOUR-S3-AUTH-SECRET",
      "bucket": "YOUR-BUCKET-NAME"
    }
  }
}
```

在适当的地方输入您自己的 S3 凭证，然后我们准备好将模板上传到 TransloadIt。

> 你可以在本教程附带的示例代码库中找到上面的 JSON，在一个名为`template.json`的文件中。

如果您还没有使用 TransloadIt 创建帐户，您需要[现在](https://transloadit.com/)创建。

您需要登录；然后转到您的仪表板(我的帐户)。在左边栏的**集成**下，选择**模板**。然后点击右上角的**新建**按钮。

您将被要求提供一个名称来标识您的模板——类似于`user_avatars`的东西应该就可以了。然后粘贴上面的 JSON(也可以在本文附带的库的根目录下找到)——确保您已经用自己的值替换了虚拟的 S3 值——并点击**保存**。

> 如果您更愿意使用替代存储机制，如 FTP 或 Rackspace 云文件，您可以在这里找到相关文档——只需相应地修改最后一步。

您将被带回到主**模板**文件，并且您将注意到新创建的模板已经被分配了一个散列作为惟一的 ID。把这个记下来，因为你以后会需要它。

完成这些后，让我们看看如何在应用程序中使用 TransloadIt。

## 示例应用程序

你可以在 Github 上找到本教程[的示例应用程序。](https://github.com/sitepoint-editors/transloadit-example-app-node-express)

为了运行它，您需要确保安装了以下先决条件:

*   节点. js
*   npm
*   MongoDB
*   凉亭

> 流浪用户将在存储库中找到一个`Vagrantfile`，用于创建一个包含所有列出的依赖项的 VM。

本质上，这是一个简单的 [Express](https://www.npmjs.com/package/express) 应用程序。为了简洁起见，我们在这里不涉及许多内容:

*   它使用[配置](https://www.npmjs.com/package/config)模块将应用程序的配置保存在一个`.yaml`文件中。
*   它使用[mongose](http://mongoosejs.com/)和 MongoDB 来定义用户模型。
*   它使用[护照](https://www.npmjs.com/package/passport)和[本地策略](https://github.com/jaredhanson/passport-local)来提供简单的认证机制。
*   它提供了安全散列密码的中间件。
*   它包括一些简单的中间件，将某些路由限制为只允许经过身份验证的用户使用。
*   它使用把手和[把手-布局](https://www.npmjs.com/package/handlebars-layouts)包来处理模板。

首先，克隆应用程序，然后安装依赖项:

```
npm install
bower install
```

应用程序中有几个元素值得简单介绍一下。

下面是`User`模型的模式定义:

```
var userSchema = mongoose.Schema({
  username : { type: String, required: true, unique: true },
  email    : { type: String, required: true, unique: true },
  password : { type: String, required: true },
  avatar   : { type: mongoose.Schema.Types.Mixed, required: false }
});
```

注意我们是如何包含类型为`Mixed`的`avatar`字段的。这将允许我们将头像指定为哈希，例如:

```
user.avatar = {
  thumbnail : 'http://your.bucket.name.aws.amazon.com/user/profile/bob_thumbnail.jpg',
  medium    : 'http://your.bucket.name.aws.amazon.com/user/profile/bob_medium.jpg',
  large     : 'http://your.bucket.name.aws.amazon.com/user/profile/bob_large.jpg'
};
```

现在基本结构已经就绪，让我们看看 TransloadIt 的 jQuery 插件。

## jQuery 插件

在客户端与 TransloadIt 集成的最简单方法是使用官方的 [jQuery 插件](https://transloadit.com/docs#jquery-plugin)，尽管还有其他选择，我们将在本文后面看到。

该插件的最新版本可通过以下 URL 获得:

```
https://assets.transloadit.com/js/jquery.transloadit2-latest.js
```

最小集成包括以下步骤:

*   您将插件绑定到表单
*   该插件“劫持”表单提交，将文件直接发送到 Transloadit
*   该插件会一直等待，直到文件上传并处理完毕
*   Transloadit 返回一个带有结果的 JSON 对象，其中还包括新生成文件的 URL
*   它创建一个隐藏的 textarea 元素，包含来自 Transloadit 的 JSON
*   表单被提交到您的应用程序

下面是一个初始化插件的非常简单的例子，告诉它使用一个模板:

```
$(function() {
  $('#upload-form').transloadit({
    wait: true,
    params: {
      auth: {
        key: 'YOUR-AUTH-KEY'
      },
      template_id: 'YOUR-TEMPLATE-ID'
    }
  });
});
```

然而，正如我们在上一部分中提到的，在客户端代码中公开您的身份验证凭据是一个坏主意。相反，我们将使用签名。

## 签名

签名是使用身份验证令牌的更安全的替代方法，尽管它们需要一些服务器端的工作。

本质上，使用签名要求你不用发送一堆指令来从你的客户端应用程序进行传输，而是对指令进行编码，并使用 HMAC 算法和你的私有认证密钥对它们进行加密。作为结果，生成一个临时令牌(即签名),该临时令牌限于该特定的指令组合。因为这是暂时的，所以如果令牌遭到破坏，它将很快变得无用。

您不必担心自己生成签名的细节，因为我们可以使用第三方库来处理这个过程。如果你使用的是 Node.js，[官方 SDK](https://github.com/transloadit/node-sdk) 会帮你搞定。

要安装库，请执行以下操作:

```
npm install transloadit --save
```

您将需要您的认证密钥和认证秘密，您可以从 TransloadIt 站点上的 **API 凭证**部分获得。把它们放在`config\default.yaml`的相关部分。

> 您需要通过重命名或将`RENAME_THIS_TO_default.yaml`复制到`default.yaml`来创建默认配置文件。

现在创建一个`TransloaditClient`类的实例，为它提供您的身份验证细节:

```
var TransloaditClient =   require('transloadit');
var transloadit       =   new TransloaditClient({
      authKey     : config.transloadit.auth_key,
      authSecret  : config.transloadit.auth_secret
    });
```

接下来，定义要采取的操作的参数。这可以是一组汇编指令的形式:

```
var params = {
  steps: {
    // ...
  }
};
```

或者，在我们的例子中，我们只需提供模板的 ID:

```
var params = {
  template_id: 'YOUR-TEMPLATE-ID'
};
```

要创建签名:

```
var sig = transloadit.calcSignature(params);
```

这将产生一个散列，其中包含一个签名——某种访问令牌——以及调用服务所需的参数。所以我们的`sig`对象看起来像这样:

```
{
  signature: "fec703ccbe36b942c90d17f64b71268ed4f5f512",
  params: {
    template_id: 'YOUR-TEMPLATE-ID',
    auth: {
    	key: 'idfj0gfd9igj9dfjgifd8gfdj9gfdgf',
    	expires: '2015-06-25T10:05:35.502Z'
    }
  }
}
```

为了将它传递给我们的 Handlebars 模板，以便我们的 JavaScript 可以使用它，我们需要创建一个非常简单的助手:

```
app.engine('.hbs', exphbs(
  {
    extname: '.hbs',
    defaultLayout: 'default',
    helpers : {
      json : function(context) {
        return JSON.stringify(context);
      }
    }
  }
));
```

现在让我们一起来定义`account`路线，它将包括我们的头像上传表单:

```
// The account page
app.get('/account', ensureAuthenticated, function(req, res){

  // Require the TransloadIt client
  var TransloaditClient = require('transloadit');

  // Create an instance of the client
  var transloadit       =   new TransloaditClient({
    authKey     : config.transloadit.auth_key,
    authSecret  : config.transloadit.auth_secret
  });

  // Build the Transloadit parameters...
  var params = {
    template_id 	: 	config.transloadit.template_id
  };

  // ...and generate the signature
  var sig = transloadit.calcSignature(params);  

  return res.render('account', {
    user: req.user,
    sig : sig
  });
});
```

然后，在对应的模板(`views/account.hbs`)中，我们先来看一些非常简单的 HTML:

```
<h2>Hello, {{ user.username }}</h2>

{{# if user.avatar }}
<img src="{{ user.avatar.thumbnail }}" id="avatar">
{{else}}
<img src="/avatar.png" id="avatar">
{{/if}}

<form method="POST" action="/avatar" id="avatar-form">
  <input type="file" name="image" id="avatar-upload">
  <input type="hidden" name="username" value="{{user.username}}">
</form>
```

> 请注意，我们包括了一个包含用户名的隐藏字段。我们将把它和我们的请求一起发送给 TransloadIt，这样它就可以用在我们的模板中。

现在添加 JavaScript，从使用我们的`json` Handlebars 助手初始化一些变量开始:

```
var sig = {{{ json sig }}};
```

现在我们将 TransloadIt 插件绑定到上传表单:

```
$(function() {
  $('#avatar-form').transloadit({
    wait: true,
    params: JSON.parse(sig.params),
    signature: sig.signature,
    fields: true,
    triggerUploadOnFileSelection: true,
    autoSubmit: false,
    onSuccess: function(assembly) {
      $('img#avatar').attr('src', assembly.results.thumbnail[0].url + '?' + (new Date()).getTime() );
      var derivatives = {
        thumbnail : assembly.results.thumbnail[0].url,
        medium : assembly.results.medium[0].url,
        large : assembly.results.large[0].url
      };
      $.ajax({
        type: 'post',
        url: '/avatar',
        data: derivatives,
        success: function(resp){
          console.log(resp);
        }
      })
    }
  });
});
```

这比我们之前看到的最小集成初始化更复杂，所以让我们一次看一点。

我们从`sig`变量中获取参数和签名，该变量在服务器上生成，然后编码为 JSON。因为`params`部分是嵌套的，我们使用`JSON.parse()`将其转换回一个对象，TransloadIt 将从中提取相关参数。

在插件初始化中，`wait`被设置为`true`，这意味着我们要等到两个文件都被上传*和*并被处理。

> 使用[汇编通知](https://transloadit.com/docs/#assembly-notifications)——您可以在稍后的高级用法一节中读到——意味着您不必等待文件被处理，在这种情况下，您可以将`wait`设置为`false`。

`fields`被设置为`true`来告诉插件我们希望在发送文件进行处理时包含附加信息；在我们的例子中，这是一个名为`username`的隐藏表单字段，我们用认证用户的用户名填充它。

`triggerUploadOnFileSelection`用于在用户选择文件后立即将文件发送到 Transloadit，而不是在提交表单时发送。`autoSubmit`防止它在 Transloadit 返回结果后提交表单，因为我们将自己手动完成。

当数据从 Transloadit 返回时，`onSuccess`回调被触发，这给了我们一个`assembly`中数据的散列。

`assembly`对象包含一个`results`属性，该属性又包含我们每个“步骤”的属性。这些包含一个文件对象数组。因为我们只上传一个文件，所以它们是包含一个元素的数组。每个文件对象都包含许多属性，包括原始文件名、元信息、来自 Transloadit 的唯一 id 和其他信息。要查看全部信息，您可能希望将其登录到控制台并查看一下。然而，我们真正感兴趣的是`url`属性，它包含在 S3 上生成的图像的 URL。

> 或者，您可能希望使用`ssl_url`属性，它与`url`相同，但在 HTTPS 之上。

我们只是通过相应的派生名称提取三个 URL，然后创建三个派生名称及其相应 URL 的散列。

为了向用户提供视觉反馈，我们还获取缩略图的 URL，并修改页面上的头像以显示新上传的图像。

最后，我们使用 Ajax 将数据悄悄地发送回我们的应用程序。

下面是获取这些数据的`avatar`路线:

```
// Ajax callback for setting the avatar
app.post('/avatar', ensureAuthenticated, function(req, res){
  req.user.avatar = req.body
  req.user.save(function(err) {
    if(err) {
      return res.send('error');
    }
    return res.send('ok');
  });
});
```

> 在生产中，您可能希望对此进行净化和验证。

如您所见，我们获取派生图像及其 URL 的散列，从`req.user`获取当前已验证的用户，将`avatar`属性设置为所提供的散列，然后更新用户模型。

这只是一种可能的方法。为了获得更快的反馈，你可能希望使用插件的`onResult`回调函数在缩略图生成后立即获取缩略图，而不是等待所有三个衍生工具。与其从客户端代码使用 Ajax 调用来通知服务器，不如使用[程序集通知](https://transloadit.com/docs#notifications)特性，它提供了在后台运行程序集的额外好处，而不是在客户端暂停执行。参考[插件文档](https://transloadit.com/docs#jquery-plugin)以获得全部选项。

这就结束了我们的基本应用。别忘了，所有的源代码——包括认证机制——都在 Github 上。

## 高级用法

在我们结束之前，让我们简单地看一下 TransloadIt 的几个更高级的方面。

### 其他客户端选项

您不必使用提供的 jQuery 插件。在文档的[社区项目](https://transloadit.com/docs#community-projects)部分，你会发现许多备选方案，包括一个用于[引导](https://github.com/pamelafox/bootstrap-transloadit-plugin)的插件，一个用于[拖拽](https://github.com/tim-kos/transloadit-drag-and-drop)的插件，一个[角度](https://github.com/kera-inc/ng-transloadit)插件或支持普通老式 [XHR](https://github.com/tim-kos/transloadit_xhr) 的插件，等等。

XHR 的那个也许值得你更详细地看一看。这是一个基本的解决方案，提供了足够的灵活性，同时要求您提供自己的反馈，例如某种上传指示。还值得注意的是，一旦上传了文件，它会以 1000 毫秒的间隔轮询服务器，以确定何时完成了组装。

### 通知

当文件准备好时，您可以使用通知来 ping 您的应用程序，而不是让用户等待他们的上传被处理。使用这种方法，用户只需要等到上传完成。

从消费者的角度来看，通知很容易实现；只需在组装说明中包含一个`notify_url`，例如:

```
{
  auth       : { ... },
  steps      : { ... },
  notify_url : "http://example.com/webhooks/incoming/transloadit"
}
```

当您的 URL 被 transload it ping 时，提供的 JSON 将包含一个`signature`字段，您可以用它来验证通知确实来自他们。只需使用您的认证秘密解码签名。

在开发过程中，你可能希望利用这个代理包来测试你的程序集通知，或者使用隧道服务，比如 T2 ngrok T3。

## 摘要

在这个由两部分组成的系列文章中，我们全面了解了文件处理服务 [TransloadIt](https://transloadit.com/) 。

在第一部分中，我们讨论了一些优点和缺点，然后查看了一些关键概念。

这一部分，我们弄脏了手，用 jQuery、Node.js 和 Express 搭建了一个简单的用户头像组件。

您并不局限于 jQuery，事实上您可以自由地使用普通的 JavaScript 解决方案或您喜欢的框架。您甚至不需要从客户端应用程序中使用它，当涉及到服务器端技术时，您有很大的选择余地。不过，希望您现在已经对它如何用于图像处理有所了解。

你在你的项目中使用 TransloadIt 了吗？你知道更好的服务吗？请在评论中告诉我。

## 分享这篇文章