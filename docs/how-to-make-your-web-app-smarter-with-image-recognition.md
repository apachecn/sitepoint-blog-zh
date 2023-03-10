# 如何通过图像识别让您的 Web 应用更加智能

> 原文：<https://www.sitepoint.com/how-to-make-your-web-app-smarter-with-image-recognition/>

Clarifai 是一个提供图像和视频识别的 API，使用起来非常简单，实现起来非常有趣。在这篇文章中，我们将探索从网络上拖放图像到一个简单的网络应用程序中，该应用程序将读取它们并告诉我们它认为它们是什么。

在这个演示中，我们将使用 Node.js 作为服务器，一个相对基本的前端使用 jQuery 处理 AJAX 请求。如果你不擅长 Node.js，那也没关系，只要你能熟练地运行`npm install`来获取模块，并在命令行中运行`node app.js`来运行你的 web 应用程序。您不需要在其中进行太多的定制，通过运行现有的代码，您最终可能会学到一两件事情！

## 代码

这个演示的所有示例代码都可以在 [GitHub](https://github.com/sitepoint-editors/Clarifai-Node-Demo) 上获得。

## 入门指南

首先，我们进入 [Clarifai](http://www.clarifai.com) 主页，点击右上角的“立即注册”按钮:

![Clarifai Homepage](img/758ae17816144c4e60047485c1009760.png)

使用您的电子邮件和详细信息注册:

![Clarifai's signup page](img/55f57016295d5b1ddcad0b9a14f9e6ed.png)

我们希望创建一个新的应用程序，因此我们通过单击左侧的“Applications”菜单项进入应用程序屏幕。

![Creating a new application in Clarifai](img/2705721de66ec0bd7480f90bcc8160fa.png)

Clarifai 还不允许我们创建应用程序，因为我们需要选择一个计划:

![Finding the choose a plan button](img/1954a8103dac80a884ee98abc217f3b8.png)

让我们选择一个计划，这样我们就可以开始工作了。对于我们的演示，免费计划应该非常合适。如果需要，我们可以在以后升级:

![Choosing a Clarifai plan](img/3620c185ded3eb6a58beebc33c3c8610.png)

我们现在可以创建一个应用程序，为此，我们可以单击左侧的“应用程序”菜单项或“创建应用程序”链接:

![Navigating back to create an application](img/6d88e5443b426354eadb33b94abe06a2.png)

单击“创建新应用程序”按钮:

![The "Create a new application button"](img/0eed878c1a54a5f8c9c397ccd5b3c91b.png)

我们为我们的新应用程序命名(例如“图像识别器”)，保留默认模型，并设置我们的语言(我们保留了英语，您可能更喜欢不同的语言！).要完成，请单击“创建应用程序”:

![Creating a Clarifai application](img/86c12c52601ecea7a3e393daca709d2d.png)

我们的新应用程序详细信息现在应该出现了。我们希望复制到某个安全地方的两个最重要的位是我们的“客户端 ID”和“客户端机密”——我们将需要这些来访问我们的服务器上的 Clarifai，我们接下来将设置它。

![Finding your Clarifai keys](img/f3cd2137cfbb9dcae8cbf8569464df5b.png)

## 设置 Node.js 服务器

Clarifai 有一个 Node.js 客户端，我们可以用它来连接 GitHub 上的服务[。将回购文件下载到您的计算机上。特别是，我们想要的是](https://github.com/Clarifai/clarifai-nodejs) [clarifai_node.js](https://github.com/Clarifai/clarifai-nodejs/blob/master/clarifai_node.js) 文件。

为您的节点服务器创建一个目录，并将“clari fai _ Node . js”JavaScript 文件添加到根目录中。

我们的 Node.js 服务器函数将位于一个名为`app.js`的 JavaScript 文件中。这是我们管理 Clarifai 支持的图像识别请求的地方。`app.js`有以下 JavaScript:

```
var Clarifai = require("./clarifai_node.js"),
    express = require("express"),
    app = express(),
    server = require("http").Server(app),
    bodyParser = require("body-parser"),
    port = process.env.PORT || 5000;

app.use(bodyParser.json());

Clarifai.initAPI("YOUR_CLIENT_ID", "YOUR_CLIENT_SECRET");

function identifyClarifaiError(err) {
  // Default error function from Clarifai we won't go into but you can find it in the GitHub download of this code!
}

app.post("/examineImage", function(req, resp) {
  var imageURL = req.body.imageRequested;
  console.log("Response was ", imageURL);

  Clarifai.tagURL(imageURL, "Image from browser", commonResultHandler);

  function commonResultHandler(err, res) {
    if (err != null) {
      identifyClarifaiError(err);
    }
    else {
      if (typeof res["status_code"] === "string" && 
        (res["status_code"] === "OK" || res["status_code"] === "PARTIAL_ERROR")) {

        if (res["results"][0]["status_code"] === "OK") {
          var tags = res["results"][0].result["tag"]["classes"];
          console.log("Tags found were: ", tags);
          resp.send(tags);
        }
        else {
          console.log("We had an error... Details: " +
            " docid=" + res.results[0].docid +
            " local_id=" + res.results[0].local_id + 
            " status_code="+res.results[0].status_code +
            " error = " + res.results[0]["result"]["error"]);

          resp.send("Error: " + res.results[0]["result"]["error"]);
        }
      }    
    }
  }
});

app.get("/", function(request, response) {
  response.sendFile(__dirname + "/public/index.html");
});

app.get(/^(.+)$/, function(req, res) {
  res.sendFile(__dirname + "/public/" + req.params[0]);
});

app.use(function(err, req, res, next) {
  console.error(err.stack);
  res.status(500).send("Something broke!");
});

server.listen(port, function() {
  console.log("Listening on " + port);
});
```

很大一部分代码是基本的 Node express 服务器功能，我们不会在本文中介绍，如果您不太确定这些部分的意思，您可以让它们保持原样，只是享受一个运行的节点服务器。

与 Clarifai 特别相关的部分从包含我们的`clarifai_node.js`文件的代码行开始:

```
var Clarifai = require("./clarifai_node.js"),
```

使用 Clarifai 的下一行启动 API 的初始化。它让我们能够使用客户端 ID 和客户端秘密访问 API，这些是我们之前在安全的地方复制的。将它们粘贴到适当的位置:

```
Clarifai.initAPI("YOUR_CLIENT_ID", "YOUR_CLIENT_SECRET");
```

然后我们有一个 POST 请求，节点服务器将寻找并响应这个请求。当通过`/examineImage`访问时，该请求期望接收到我们的 POST 主体中一个名为`imageRequested`的图像的 web URL。它会将找到的任何 URL 记录到我们的控制台中:

```
app.post("/examineImage", function(req, resp) {
  var imageURL = req.body.imageRequested;
  console.log("Response was ", imageURL);
```

然后我们从 Clarifai 节点 API 客户端运行一个名为`tagURL()`的函数。这个函数有三个参数——我们希望 Clarifai 检查的图像 URL，我们给图像起的名字(如果你愿意，你可以改变这个名字并根据 URL 修改它，但是为了简单起见，我们把它作为所有的通用名字)和回调函数。

```
Clarifai.tagURL(imageURL, "Image from browser", commonResultHandler);
```

在`commonResultHandler()`内，我们对 Clarifai 返回给我们的东西做出反应。如果它返回一个错误，我们把它传递给`identifyClarifaiError()`函数，我们可以保持不变(你可以在上面的 GitHub 下载中找到这个函数)。它包含对来自 Clarifai 的状态代码的一系列检查。出于我们在这个基本演示中的目的，我们不会涵盖它的所有功能，因为您不需要调整它。

```
function commonResultHandler(err, res) {
  if (err != null) {
    identifyClarifaiError(err);
  }
  // Continues further
```

如果我们没有返回明确的错误，我们会仔细检查 Clarifai 返回的数据在其`res["status_code"]`中是否也包含错误状态:

```
else {
  if (typeof res["status_code"] === "string" && 
    (res["status_code"] === "OK" || res["status_code"] === "PARTIAL_ERROR")) {
```

Clarifai 在`res["results"]`中返回一个结果数组——每一个结果对应一幅图像。因为我们只提供了一个图像，所以我们只需要检索数组中的第一个项目。每一项都有一个 JSON 对象，它是该图像的数据对象。返回的 JSON 如下所示:

```
{
  "docid": 6770681588539017000,
  "url": "https://encrypted-tbn2.gstatic.com/images?q=tbn:ANd9GcQSoU65AMIOpJ2rwtvdJyuSExIjcwQfuIup8sm6tesdWwtCEajzVw",
  "status_code": "OK",
  "status_msg": "OK",
  "local_id": "Image from browser",
  "result": {
    "tag": {
      "concept_ids": [
        "ai_l8TKp2h5",
        "ai_VPmHr5bm"
      ],
      "classes": [
        "people",
        "adult"
      ],
      "probs": [
        0.9833399057388306,
        0.9695020318031311
      ]
    }
  },
  "docid_str": "c009c46cf0c7b68b5df64b083c2547b4"
}
```

我们使用的最重要的位在`result`对象中。这包含三个数组，一个列出了它所找到的元素的 Clarifai 概念 id，一个列出了它们的“类”(每个概念的人类可读名称)，一个列出了每个正确的概率。这些的顺序与每个对象的标签相匹配，所以在上面的例子中，`"ai_l8TKp2h5"`的概念 ID 被称为`"people"`，Clarifai 大约有`0.9833399057388306`的百分比确定这个图像中有人。

使用这些数据，我们可以列出这些类来显示 Clarifai 发现了什么。在下面的代码中，我们检查结果中的状态代码是`"OK"`，然后发送标签数组作为对前端 AJAX 请求的响应。

```
if (res["results"][0]["status_code"] === "OK") {
  var tags = res["results"][0].result["tag"]["classes"];
  console.log("Tags found were: ", tags);
  resp.send(tags);
}
```

否则，如果状态代码不是`"OK"`，我们会记录错误的详细信息，并将其发送回我们的 web 应用程序:

```
else {
  console.log("We had an error... Details: " +
    " docid=" + res.results[0].docid +
    " local_id=" + res.results[0].local_id + 
    " status_code="+res.results[0].status_code +
    " error = " + res.results[0]["result"]["error"]);

  resp.send("Error: " + res.results[0]["result"]["error"]);
}
```

## 我们的前端 JavaScript

大部分前端可以按照你喜欢的方式制作。在我们的例子中，前端将是一个相对简单的，允许从网络上的其他地方拖动到应用程序的图像。我们读取它的 URL，将其发送到上面的节点服务器，然后等待显示标签列表。

我们完整的前端 JavaScript 文件如下所示:

```
var baseUrl = window.location.origin,
    dropArea = document.getElementById("dropArea");

dropArea.addEventListener("drop", imageDropped, false);

function imageDropped(evt) {
  evt.stopPropagation();
  evt.preventDefault(); 

  var imageHTML = evt.dataTransfer.getData("text/html"),
      dataParent = $("<div>").append(imageHTML),
      imageRequested = $(dataParent).find("img").attr("src"),
      $imageFound = $("#imageFound");

  console.log(imageRequested);

  $imageFound.attr("src", imageRequested);

  $.ajax({
    type: "POST",
    url: baseUrl + "/examineImage",
    contentType: "application/json; charset=utf-8",
    dataType: "json",
    data: JSON.stringify({"imageRequested": imageRequested}),

    success: function(data) {
      console.log(data);
      var tags = "";
      for (var i = 0; i The initial line of code reads in the URL we've got in the browser bar, as this is also the URL for our server:

[code language="js"]
var baseUrl = window.location.origin,
```

然后，我们告诉 JavaScript 留意`#dropArea`元素，并添加一个事件监听器，如果我们在它上面放置东西，它将运行`imageDropped()`:

```
dropArea = document.getElementById("dropArea");

dropArea.addEventListener("drop", imageDropped, false);
```

`imageDropped()`首先防止将文件拖入浏览器时会发生的常见行为(通常会将文件加载到您拖入的浏览器窗口中):

```
function imageDropped(evt) {
  evt.stopPropagation();
  evt.preventDefault();
```

一旦我们确定已经避免了浏览器通常的拖放功能，我们就可以从事件的拖放数据中获取 HTML。数据通常应该包括一个`<img>`标签，但有时也有其他标签，如`<meta>`标签和其他`<div>`标签。为了确保我们总是有一个父元素可以查看，我们将所有的数据添加到一个`<div>`中。然后我们在其中找到`<img>`，读取它的`src`属性，并将这个值放入一个名为`imageRequested`的变量中:

```
var imageHTML = evt.dataTransfer.getData("text/html"),
    dataParent = $("<div>").append(imageHTML),
    imageRequested = $(dataParent).find("img").attr("src")
```

在我们的 HTML 中有一个 ID 为`#imageFound`的`<img>`标签，然后我们将拖动的图像放入其中，这样我们可以直观地看到结果下面的图像。我们还记录了用于调试的图像的 URL(如果您愿意，可以删除`console.log`):

```
$imageFound = $("#imageFound");

console.log(imageRequested);

$imageFound.attr("src", imageRequested);
```

使用我们新获取的图像 URL，我们已经检索并存储到`imageRequested`中，我们将它以`{"imageRequested": "http://www.somewebsite.com/yourimage.jpg"}`的格式发送到 JSON 对象中的节点服务器的`/examineImage`地址。在成功检索到标签(Clarifai 称之为类)后，我们将它们转换成逗号分隔的字符串，并将该字符串放入 HTML 的`#dropArea`元素中。如果有错误，我们会记录发生了错误。

```
$.ajax({
  type: "POST",
  url: baseUrl + "/examineImage",
  contentType: "application/json; charset=utf-8",
  dataType: "json",
  data: JSON.stringify({"imageRequested": imageRequested}),

  success: function(data) {
    console.log(data);
    var tags = "";
    for (var i = 0; i I won't cover the HTML in detail as it isn't too exciting and could definitely be optimized! It looks like so:

[code language="html"]
<!doctype html>
<html>
<head>
  <title>Image recognition tester</title>
  <meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
  <script src="//code.jquery.com/jquery-1.12.0.min.js"></script>
  <link href="https://fonts.googleapis.com/css?family=Lora" rel="stylesheet" type="text/css"/>
  <style type="text/css">
    #dropArea {
      border: 1px solid #fff;
      bottom: 10%;
      color: #fff;
      display: flex;
      justify-content: center;
      flex-direction: column;
      font-family: "Lora", Arial, sans-serif;
      font-size: 30px;
      left: 10%;
      position: absolute;
      right: 10%;
      text-align: center;
      text-shadow: 0 0 10px rgba(0,0,0,0.5);
      top: 10%;
    }
    #imageFound {
      background-size: 100% cover;
      background: none 0 0 no-repeat #000;
      height: 100%;
      left: 0;
      position: absolute;
      top: 0;
      width: 100%;
    }
  </style>
</head>
<body>
  <img src="" id="imageFound" />
  <div id="dropArea" ondragover="return false;">Drop your image from the web into here!</div>
  <script src="./main.js"></script>
</body>
</html>
```

## 在活动

如果我们在本地运行我们的节点服务器，我们可以通过`localhost:5000`访问它，所以使用`node app.js`运行服务器并在您的 web 浏览器中访问页面。

![Our web app in action before an image has been loaded](img/98b511f45eec42489f158d28196dbb1f.png)

在单独的窗口中访问另一个网站，并将图像从该窗口拖到此窗口:

![Dragging and dropping an image](img/d56a15fe6357e8b326c304d0666351e3.png)

当它识别出图像后，它会告诉我们一个标签列表，按照从最可能到最不可能的顺序，它认为图像包含:

![Our image is recognized and tagged](img/1557eb25600c492ae252da8c3bb3a3fa.png)

## 结论

Clarifai 的图像识别能力有很大潜力。这项服务的 API 可以添加到一系列人工智能应用程序中，让我们的人工智能对周围的世界有一个很好的视觉理解。例如，我们可以将这一功能添加到 Siri 风格的个人助理中，就像我们在文章[如何使用 Api.ai](https://www.sitepoint.com/how-to-build-your-own-ai-assistant-using-api-ai/) 和[定制具有意图和上下文的 Api.ai 助理](https://www.sitepoint.com/customizing-your-api-ai-assistant-with-intent-and-context/)中构建的那样。您可以将它添加到 Nodebot 或任何其他支持 web 的应用程序中。Clarifai 的服务还可以进行视频识别，这带来了全新水平的潜力！

你打算在哪里使用 Clarifai 的图像识别？我很想知道这个 API 在哪里被使用！请在下面的评论中告诉我，或者在 Twitter 上通过 [@thatpatrickguy](https://www.twitter.com/thatpatrickguy) 与我联系。

## 分享这篇文章