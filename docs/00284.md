# 无服务器功能及其部署指南

> 原文：<https://www.sitepoint.com/serverless-functions/>

在过去的几年里，无服务器功能(有时也称为“无服务器”或“无服务器计算”)已经成为一种流行的技术。然而，围绕这个术语仍然有许多混乱。没有服务器你怎么运行代码？该技术的利弊是什么？你可能会在什么情况下使用它？在这篇文章中，我希望回答这些问题，并给你一个很好的技术概述。

## 什么是无服务器功能？

当你第一次听到“无服务器”这个词时，一定会引起好奇心。"没有服务器，你如何在网上运行代码？"你可能会想。这实际上意味着，作为开发人员，您不必担心代码运行的服务器。无服务器提供商将硬件供应、网络配置、软件安装和扩展全部抽象化。

从开发的角度来看，无服务器功能是您上传到无服务器提供商(如 AWS 或 Google)的一组代码。该代码可以配置为通过 URL 响应请求，按计划运行(即通过 cron 作业)，或者从其他服务或无服务器功能调用。

无服务器功能非常适合向前端应用程序添加少量后端功能，而没有运行完整服务器的复杂性和成本。

另一方面，您也可以构建具有无服务器功能的完整应用程序。与提供文件存储、数据库系统和身份验证的其他云服务相结合，可以构建大型、健壮和可扩展的应用程序，而无需配置一台服务器。

## 无服务器功能的优势

无服务器功能在按需启动的微型容器中运行。它们是为相当短时间运行的流程而设计的，因此计费是考虑到这一点而设置的。与通常按小时计费的全服务器实例不同，无服务器功能通常按千兆秒计费。由于最小计费持续时间为毫秒级，低频或零星工作负载作为无服务器功能运行比传统服务器实例便宜得多。轻负载和原型甚至可以归入一些提供商的免费层。

无服务器功能的按需调用意味着它们可以快速轻松地扩展，开发人员无需额外工作。这使得它们非常适合流量可能会不可预测地激增的情况，因为该函数的更多实例将自动用于处理负载。该功能随后将缩减，这意味着您不必为未使用的容量付费。

无服务器模式的一个关键优势是不必处理服务器。运行 web 应用程序需要服务器管理方面的大量时间和专业知识，以便用最新的安全补丁保持软件最新，并确保正确配置服务器，从而保证安全和高性能。对于初创企业和小企业来说，雇人处理服务器管理是一笔很大的额外开销。使用无服务器，开发人员可以专注于创建解决方案。

## 无服务器功能的缺点

当然，没有技术是完美的，无服务器功能也不是没有缺点。正如我前面提到的，无服务器模型在设计时就考虑到了短期流程。由于最大执行时间以分钟为单位(例如，AWS 上为 15 分钟，Google 上为 9 分钟)，它不适合处理大批量数据等长时间运行的作业。

另一个广泛讨论的问题是冷启动时间。这是在准备开始运行之前，提供者为您的无服务器功能提供和初始化容器所花费的时间。一旦一个函数运行完毕，这个容器会被保留一段时间，以便在再次执行代码时重用。这种“冷启动”延迟会给函数的响应时间增加半秒到一秒的延迟。这有变通办法，包括无服务器框架的[预热插件](https://www.serverless.com/blog/keep-your-lambdas-warm)，它按计划 pings 你的函数以保持容器活动。

虽然无服务器功能使您不必担心服务器的供应和维护，但这并不是说没有学习曲线。使用无服务器构建应用程序需要一种不同于使用传统整体代码库的思维方式。您必须以不同的方式构建您的代码，将功能分解成更小的、离散的服务，以适应无服务器功能的约束。部署也更加复杂，因为每个功能都是独立版本和更新的。

还有供应商锁定的问题，这有时被认为是无服务器技术的一个缺点。目前，这个领域的主要提供商(AWS、Google、Azure)都有自己不同的实现和管理工具。这使得将无服务器应用从一个云提供商转移到另一个云提供商变得困难。像[无服务器框架](https://www.serverless.com/)这样的项目试图抽象出底层服务，以使应用程序在提供商之间可移植。

## 无服务器功能用例

尽管无服务器功能可以用来构建整个应用程序，但是让我们来看看一些不太雄心勃勃的用例，在这些用例中，无服务器功能可以让普通开发人员受益。

### 表单邮件程序

完全静态的网站并不少见，除了当用户点击发送时客户希望通过电子邮件发送给他们的联系表单。该网站的主机提供商可能支持也可能不支持服务器端脚本，即使这样，它也可能不是你熟悉的语言。将无服务器功能设置为表单邮件程序允许您将该功能添加到静态托管的站点中。

### Cron 作业

有时，您可能需要在后台运行计划任务。通常，为了设置 cron 作业，您必须购买一台服务器，这台服务器在作业之间会处于闲置状态。使用无服务器功能，您只需为作业运行所花费的时间付费(如果它属于免费层，可能根本不用付费)。

### 缩略图生成器

想象一下，您的 React 应用程序允许用户上传一张照片，作为整个应用程序的头像。您希望调整上传图像的大小，这样就不会浪费带宽来提供比所需图像大得多的图像。可以使用无服务器功能来处理上传请求，将图像调整到所需的大小，并保存到 S3 或谷歌存储等服务中。

## 无服务器功能的实际例子

为了更好地理解使用无服务器函数，让我们看一个真实的例子。我们将创建一个带有时事通讯注册表单的静态页面，该页面使用无服务器功能将用户的姓名和电子邮件地址保存到 Google 电子表格中。

根据提供者的不同，无服务器函数可以用多种语言编写，但是我们将使用 JavaScript，因为 Netlify 支持 Node.js 函数。我将假设您已经在本地机器上安装了 Node/npm 的最新版本，以便进行后续操作。

### 1.注册一个网络账户

在本例中，我们将使用 Netlify 作为主机，因为他们提供了一个包含无服务器功能的免费层，并且很容易启动和运行。首先，去他们的网站[注册一个免费账户](https://app.netlify.com/signup)。

### 2.安装 Netlify CLI 工具

为了在本地测试我们的示例站点并部署到 Netlify，我们将使用他们的 CLI 工具。这可以作为全局 npm 模块从命令行安装:

```
npm install -g netlify-cli 
```

安装 CLI 后，运行以下命令将打开一个浏览器窗口，将 CLI 连接到您的帐户:

```
netlify login 
```

### 3.创建项目文件夹并安装依赖项

让我们为项目创建一个文件夹，并初始化一个新的 npm 项目:

```
mkdir serverless-mailinglist && cd serverless-mailinglist
npm init -y 
```

这将为我们的项目设置一个`package.json`文件，准备安装依赖项。说到这里，我们需要几个包来实现我们的无服务器功能:

```
npm install dotenv google-spreadsheet 
```

第一个是 [dotenv](https://www.npmjs.com/package/dotenv) ，它允许我们从项目根目录下的`.env`文件中加载值，并将它们暴露给节点脚本(我们的无服务器函数)，就像它们是环境变量一样。另一个是 [google-spreadsheet](https://www.npmjs.com/package/google-spreadsheet) ，一个包装了 Google Sheets API 并使其更容易使用的包。

### 4.启用 Google Sheets API 并创建凭证

为了使用 Sheets API，我们需要做一些准备工作。首先，你需要通过前往[API 控制台](https://console.developers.google.com/apis/library/sheets.googleapis.com)为你的 Google 账户启用 API。从顶部的菜单中创建一个新项目，然后点击**启用**按钮。

完成后，您需要创建一个服务帐户。该帐户将为您提供一组凭证，这些凭证具有访问 API 的必要权限。为此，请按照下列步骤操作:

1.  确保您在 [Sheets API 管理屏幕](https://console.cloud.google.com/apis/api/sheets.googleapis.com/overview)上。
2.  点击左侧工具条中的**凭证**，然后点击 **+创建凭证**，从下拉菜单中选择**服务账户**。
3.  填写表单，选择服务帐户的名称。您选择的名称加上项目名称将构成服务帐户 ID 的一部分。例如，如果您将帐户命名为“邮件列表”，项目名称为“Sitepoint Serverless Demo”，那么 ID 将类似于`mailing-list@sitepoint-serverless-demo.iam.gserviceaccount.com`。点击**创建**。
4.  您可以跳过页面上剩余的两个可选部分。点击**继续**然后**完成**。
5.  接下来，单击新创建的服务帐户。这将把你带到一个显示账户详细信息的屏幕。点击顶部菜单中的**键**，然后**添加键**和**创建新键**。选择 JSON 作为键类型。
6.  点击**创建**按钮，一个 JSON 密钥文件将被下载到您的计算机上。(注:这是**唯一的**副本，请妥善保管！)

### 5.创建注册表单页面

让我们继续创建一个简单的注册页面，允许用户向我们的邮件列表提交他们的详细信息。在项目根目录下创建一个`index.html`文件，内容如下:

```
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <title>Sign Up For Beta Form</title>
    <link rel="stylesheet" href="style.css">
    <link href='https://fonts.googleapis.com/css?family=Lato:400,700' rel='stylesheet' type='text/css'>
  </head>
  <body>
    <form action="/.netlify/functions/subscribe" method="post">
      <div class="header">
         <p>Get Great Content Every Week</p>
      </div>
      <div class="description">
        <p>I publish new articles every week. Be sure to subscribe to my newsletter to make sure you never miss a post!</p>
      </div>
      <div class="input">
        <input type="text" class="button" id="name" name="name" placeholder="YOUR NAME">
      </div>
      <div class="input">
        <input type="text" class="button" id="email" name="email" placeholder="NAME@EXAMPLE.COM">
        <input type="submit" class="button" id="submit" value="SIGN UP">
      </div>
    </form>
  </body>
</html> 
```

和一个`style.css`文件，规则如下:

```
body {
  background: #A6E9D7;
  font-family: 'Lato', sans-serif;
  color: #FDFCFB;
  text-align: center;
  background-image: url(https://images.pexels.com/photos/326311/pexels-photo-326311.jpeg?auto=compress&cs=tinysrgb&dpr=2&h=650&w=940)
}

form {
  width: 450px;
  margin: 17% auto;
}

.header {
  font-size: 35px;
  text-transform: uppercase;
  letter-spacing: 5px;
}

.description {
  font-size: 14px;
  letter-spacing: 1px;
  line-height: 1.3em;
  margin: -2px 0 45px;
}

.input {
  display: flex;
  align-items: center;
}

.button {
  height: 44px;
  border: none;
}

#email {
  width: 75%;
  background: #FDFCFB;
  font-family: inherit;
  color: #737373;
  letter-spacing: 1px;
  text-indent: 5%;
  border-radius: 5px 0 0 5px;
}

#name {
  width: 100%;
  background: #FDFCFB;
  font-family: inherit;
  color: #737373;
  letter-spacing: 1px;
  text-indent: 5%;
  border-radius: 5px;
  margin-bottom: 1em;
}

#submit {
  width: 25%;
  height: 46px;
  background: #E86C8D;
  font-family: inherit;
  font-weight: bold;
  color: inherit;
  letter-spacing: 1px;
  border-radius: 0 5px 5px 0;
  cursor: pointer;
  transition: background .3s ease-in-out;
}

#submit:hover {
  background: #d45d7d;
}

input:focus {
  outline: none;
  outline: 2px solid #E86C8D;
  box-shadow: 0 0 2px #E86C8D;
} 
```

### 6.创建一个无服务器函数来处理表单

现在我们有了表单，我们需要为无服务器函数创建代码，该函数将处理 POST 请求并通过 API 将数据保存到 Google 电子表格中。为了让 Netlify 部署我们的功能，我们必须遵循他们的命名约定，并在我们的项目文件夹中创建文件夹路径`netlify/functions/`。

在这个新的 functions 文件夹中，创建一个 JavaScript 文件`subscribe.js`:

```
if (!process.env.NETLIFY) {
  require('dotenv').config();
}

const { parse } = require('querystring');
const { GoogleSpreadsheet } = require('google-spreadsheet');

exports.handler = async (event, context) => {
  const doc = new GoogleSpreadsheet(process.env.GOOGLE_SPREADSHEET_ID_FROM_URL);

  await doc.useServiceAccountAuth({
    client_email: process.env.GOOGLE_SERVICE_ACCOUNT_EMAIL,
    private_key: process.env.GOOGLE_PRIVATE_KEY.replace(/\\n/g, '\n')
  });

  await doc.loadInfo();
  const sheet = doc.sheetsByIndex[0]; 

  try {
    if (event.httpMethod === 'POST') {
      /* parse the string body into a useable JS object */
      const data = parse(event.body);
      await sheet.addRow(data);

      return {
        statusCode: 302,
        headers: {
          Location: '/success.html'
        }
      };
    } else {
      return {
        statusCode: 500,
        body: 'unrecognized HTTP Method, must be POST'
      };
    }
  } catch (err) {
    console.error('error ocurred in processing ', event);
    console.error(err);
    return {
      statusCode: 500,
      body: err.toString()
    };
  }
}; 
```

*注:函数代码改编自博文[Google Sheets v4 API with Netlify Dev](https://www.swyx.io/netlify-google-sheets/)。*

Netlify 的默认配置意味着在`netlify/functions`路径下的 JavaScript 文件可以在`/.netlify/functions/` URL(注意`netlify`前的句点)加上文件名减去扩展名被调用。文件`netlify/functions/subscribe.js`将在相对 URL `/.netlify/functions/subscribe`处可用。

基于节点的无服务器功能的基本要求是导出一个处理函数，当端点接收到请求时将调用该函数。向该函数传递两个参数。`event`参数提供对请求细节的访问，比如头和 HTTP 方法。`context`参数提供了对调用函数的上下文信息的访问，例如，包括已验证用户的详细信息。

函数代码本身通过提供的凭证连接到 Google Sheets API。然后解析请求体，并通过 API 将提交的姓名和电子邮件地址添加到电子表格中。完成后，该函数返回 302 响应，将用户重定向到成功页面。(创建此页面留给读者来完成。)

为了能够在本地测试该功能，我们需要在项目根目录下创建一个`.env`文件，并添加一些变量:

```
GOOGLE_SERVICE_ACCOUNT_EMAIL=mailing-list@sitepoint-serverless-demo.iam.gserviceaccount.com
GOOGLE_PRIVATE_KEY=-----BEGIN PRIVATE KEY-----\nMIIEvQIBADANB \\etc
GOOGLE_SPREADSHEET_ID_FROM_URL=1N8afdqnJjBhwXsvplIgU-5JoAFr3RapIrAS9oUybFnU 
```

服务帐户电子邮件是您在步骤 4 中创建的邮件，私钥来自您下载的 JSON 密钥文件。最后一个，电子表格 ID，我们将在下一步中得到。

### 7.创建电子表格并共享

去谷歌工作表，创建一个新的电子表格。您给它起什么标题并不重要，但是记下 URL 中的 ID，并将其添加到您在上一步中创建的`.env`文件中。

在电子表格的第一行中，添加两个列标题: **name** 和 **email** (注意大小写与 HTML 表单中的输入名称匹配很重要)。由无服务器功能创建的条目将作为附加行添加到下面。

现在，您必须授予您创建的服务帐户访问电子表格的权限。点击**分享**按钮，在输入框中输入服务账号邮箱地址。确保分配编辑者权限。

### 8.使用 Netlify CLI 进行本地测试

Netlify CLI 工具的一个很好的特性是，它允许您在将代码发布到他们的服务之前在本地测试您的代码。要启动开发服务器，请运行以下命令:

```
netlify dev 
```

将自动打开一个新的浏览器选项卡，并显示该站点。填写并提交表单将运行无服务器功能(本地提供)，然后在成功后重定向您的浏览器。如果你跳转到 Google Sheets 上的电子表格，你应该会在新的一行中看到你输入的详细信息。

### 9.部署到网络

CLI 工具很好地模拟了在您的机器上本地运行的 Netlify 服务，但是如果您希望看到项目在他们的服务器上运行，您也可以使用 CLI 来发布您的项目。

运行以下命令:

```
netlify deploy 
```

然后按照提示操作。您的站点，包括无服务器功能，将被发布到 Web 上。不要忘记，您还需要设置环境变量来镜像您的`.env`文件中的那些变量。您可以从 Netlify 站点的管理面板或通过 CLI 工具进行设置:

```
netlify env:set VAR_NAME value 
```

## 无服务器:只是一种时尚，还是后端的未来？

无服务器同时被谴责为一种时尚，并被预示为后端应用程序的未来。亚马逊的 Lambda 功能自 2014 年就已经存在，是 AWS 的一个关键产品。当然，在许多情况下，实际服务器的灵活性和功能是必需的，它们可以 24/7 运行，并具有完整的 shell 访问权限。

但是，正如我们所看到的，对于某些类型的工作负载，severless 的廉价成本、可伸缩性和低维护成本使其成为一个不错的选择。随着无服务器生态系统中越来越多的书籍、课程、框架和服务，可以肯定无服务器功能将长期存在。

## 分享这篇文章