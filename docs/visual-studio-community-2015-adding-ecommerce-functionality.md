# Visual Studio 社区 2015:添加电子商务功能

> 原文：<https://www.sitepoint.com/visual-studio-community-2015-adding-ecommerce-functionality/>

*本文由[微软](http://aka.ms/visualstudiocomm2013)赞助。感谢您对使 SitePoint 成为可能的赞助商的支持。*

欢迎回到我们的系列文章，使用微软的现代 IDE: Visual Studio Community 2015 为客户快速设计和构建一个有吸引力的功能性网站。如果您错过了前几期，请查看以下内容:

*   [Visual Studio 社区 2015:建立网站](https://www.sitepoint.com/design-develop-microsoft-community-2015-setting-up-site/)
*   [Visual Studio 社区 2015:添加电子邮件和联系人页面](https://www.sitepoint.com/design-develop-microsoft-community-2015-email-contact-pages/)

在这篇文章中，我们将添加一个电子商务解决方案到我们的网站。解决方案很简单，只需一个页面来为用户处理购物车、支付信息和成功或失败消息。

我们将把我们的支付信息提交给一个模拟的 ASP.NET Web API 应用程序。Web API 应用程序将像信用卡处理器一样工作。它将通过 POST 使用 JSON。然后，它将发回一个 HTTP 状态代码，指示成功或失败。基于这个反馈，我们可以决定做什么。但是为了简单起见，我们将根据收到的状态代码向用户显示一条消息。

虽然该解决方案很简单，但它将展示任何电子商务解决方案的核心组件。其中包括:

*   要购买的产品列表
*   添加或删除产品的能力
*   产品和采购的总价格
*   用于获取付款信息的表单
*   连接到某个第三方信用卡处理器
*   能够显示来自信用卡处理器的结果
*   成功后，交付购买的产品

结帐页面将在 Bootstrap 中进行样式设计，并使用一些 AngularJS 来帮助动态显示产品总数。最终结果将如下所示:

![Final result](img/a621efb8e859b2cf79ec01c91dd08d14.png)

## 创建结帐页面

为了创建结帐页面，我们需要添加一个视图和几个模型类。

打开视图文件夹，然后打开主页。右键单击主文件夹并选择添加新项目。选择 MVC 视图页面。将文件命名为 Checkout.cshtml。

![Selecting checkout.chtml](img/18f2c274b59dbac696ff147101bd5f7b.png)

我们将再次使用 Bootstrap 来设计我们的页面，我们已经为它添加了一个引用。以下代码将构成我们的结帐页面:

```
<form action="Checkout" method="post">
  <p>
  <div class="container" ng-controller="ctrl">
  <div class="row">
  <div class="col-sm-12">
 <div class="panel panel-info">
    <div class="panel-heading">
      <div class="panel-title">
        <div class="row">
        <div class="col-sm-12">
        <h5><span class="glyphicon glyphicon-shopping-cart"></span> Shopping Cart</h5>
        </div>
        </div>
      </div>
    </div>
    <div class="panel-body">
      <div class="row">
        <div class="col-sm-6">
        <h4 class="product-name"><strong>Lesson 1</strong></h4>
        <h4>Beginner Lessons</h4>
        </div>
        <div class="col-sm-4">
        <div class="col-sm-6 text-right">
        <h6><strong>97.00 <span class="text-muted">x</span></strong></h6>
        </div>
        </div>

        <div class="col-sm-2">
        <input type="text" ng-model="lesson1_1000" name="lesson1_1000" placeholder="0" class="form-control input-sm">
        </div>
      </div>
      <hr>
      <div class="row">
        <div class="col-sm-6">
        <h4 class="product-name"><strong>Lesson 2</strong></h4>
        <h4>Intermediate Lessons</h4>
        </div>
        <div class="col-sm-4">
        <div class="col-sm-6 text-right">
        <h6><strong>97.00 <span class="text-muted">x</span></strong></h6>
        </div>
        </div>

        <div class="col-sm-2">
        <input type="text" ng-model="lesson2_1010" name="lesson2_1010" placeholder="0" class="form-control input-sm">
        </div>
      </div>

      <hr />
      <div class="row">
        <div class="col-sm-12">
        <div class="form-group col-sm-6">
        <label for="cardName">NAME</label>
        <div class="input-group">
        <input type="text" class="form-control" name="cardName" placeholder="Name On Card" />
        </div>
        </div>
        </div>
      </div>

      <div class="row">
      <div class="col-sm-12">
        <div class="form-group col-sm-6">
        <label for="cardNumber">CARD NUMBER</label>
        <div class="input-group">
        <input type="text" class="form-control" name="cardNumber" placeholder="Valid Card Number" />
        </div>
        </div>
      </div>
      </div>

      <div class="row">
        <div class="form-group col-sm-8">
        <div class="col-sm-4">
        <label>EXPIRATION</label>
        </div>
        <div class="col-sm-3">
        <label for="cardCv">CV CODE</label>
        </div>
        </div>
      </div>

      <div class="row">
        <div class="form-group col-sm-6">
        <div class="col-sm-3">
        <input type="text" class="form-control" name="cardYear" placeholder="YY" />
        </div>
        <div class="col-sm-3">
        <input type="text" class="form-control" name="cardMonth" placeholder="MM" />
        </div>
        <div class="col-sm-3">
        <input type="text" class="form-control" name="cardCv" placeholder="CV" />
        </div>
        <div class="col-sm-3"></div>
        </div>
      </div>

      <hr>
    </div>
    <div class="panel-footer">
      <div class="row text-center">
      <div class="col-sm-9">
      <h4 class="text-right">Total $<strong>{{lesson1_1000 * 97 + lesson2_1010 * 97}}</strong></h4>
      </div>
      <div class="col-sm-3">
      <button type="submit" class="btn btn-success btn-block">
      Purchase
      </button>
      </div>
      </div>
    </div>
  </div>
  </div>
  </div>
  </div>
```

此代码已经添加了 AngularJS。在我们讨论 AngularJS 代码之前，让我们先讨论一下表单的布局和功能。

为了简单起见，我们的整个结帐都在一个表单上进行。这是通过在同一页面上显示可用课程来实现的。用户可以增加课程的数量。这也将合计靠近结帐按钮的价格。如果用户想要删除一个项目，他们只需将数量设置回零。

## 角度功能

AngularJS 用于更新总价，并显示错误或成功消息。要将 AngularJS 添加到项目中，打开`bower.json`文件并添加 AngularJS 引用，如下面最后一行所示:

```
"jquery-validation": "1.11.1",
"jquery-validation-unobtrusive": "3.2.2",
"hammer.js": "2.0.4",
"bootstrap-touch-carousel": "0.8.0",
"angularjs": "*"
```

使用 AngularJS，我们可以计算价格总和，因为产品数量是动态捕获的，如下所示

```
<input type="text" ng-model="lesson1_1000" name="lesson1_1000" placeholder="0" class="form-control input-sm">
```

`ng-model`将输入文本字段值绑定到 lesson1_1000。然后，我们使用以下公式计算总数:

```
<h4 class="text-right">Total $<strong>{{lesson1_1000 * 97 + lesson2_1010 * 97}}</strong></h4>
```

我们用产品的数量乘以产品的价格。为了确保我们不会得到类似“NaN”的东西来代替总价，我们需要初始化 AngularJS 变量。这就是 controller.js 文件的用处。

要添加控制器文件，请打开 wwwroot 文件夹，然后添加“js”文件夹。然后右键点击，添加`controller.js`:

![Selecting controller.js](img/43b6847906a9a10fff2c931cb13db9ca.png)

我们在这个文件中定义 AngularJS 控制器如下:

```
angular.module('myapp', []).controller('ctrl', 
    ['$scope', function ($scope) {
        function init() {
            $scope.lesson1_1000 = 0;
            $scope.lesson2_1010 = 0;
        }
        init();
    }]
);
```

我们所做的只是将产品变量初始化为零。然后我们调用`init()`函数来执行代码。

我们确实需要在客户端添加“myapp”和“ctrl”引用。打开 _Layout.cshtml 并修改 body 标记，如下所示:

```
<body ng-app="myapp">
```

现在打开 Checkout.cshtml 并修改“容器”div，如下所示:

```
<div class="container" ng-controller="ctrl">
```

这确保我们的产品变量在正确的范围内。当我们在这里时，将`controller.js`文件拖到`_Layout.cshtml`的头部分。这将创建对控制器文件的引用，如下所示:

```
<script src="~/js/controller.js"></script>
```

您可能注意到了我们的`Checkout.cshtml`底部的以下代码:

```
<div ng-hide="@Model.DisplaySuccess" class="alert alert-success" role="alert">Thank you for your purchase.<br />
You can <a href="/downloads/@Model.DownloadCode">download your product here</a>.</div>
<div ng-hide="@Model.DisplayError" class="alert alert-danger" role="alert">Sorry.  Your card was declined.  Please contact your bank or use another card.</div>
```

当 div 中的表达式为真时，将隐藏相关的 div。在这种情况下，我们只返回 true 或 false 值。这个值是通过 Razor 语法访问的。我们随视图发回的模型包含上述两个属性。它们的值是根据我们的事务处理的响应设置的。

## 模型类

在我们开始修改控制器之前，我们将添加一些需要的模型类。将以下文件(类)添加到模型文件夹:`Product.cs`、`Form.cs`和`Checkout.cs`。

在`Checkout.cs`中，添加以下内容:

```
public class Checkout
{
    public string DisplayError { get; set; }
    public string DisplaySuccess { get; set; }
    public string DownloadCode { get; set; }
}
```

这段代码仅用于隐藏或显示我们的成功和错误框。DownloadCode 创建一个可以附加到 URL 的代码，模仿购买课程的下载页面。我们将 Checkout 类实例和视图一起发送回去。

将以下内容添加到`Product.cs`:

```
public class Product
{
public int Id { get; set; }
public string Name { get; set; }
public int Price { get; set; }
}

class FormProduct
{
public int Id { get; set; }
public int qty { get; set; }
}

class ProductManager
{
public static int productTotal(List<FormProduct> formProducts)
{
    int total = 0;
    List<Product> products = ProductManager.GetProducts();
    foreach (var item in formProducts)
    {
        var match = products.Where(x => x.Id == item.Id).FirstOrDefault();
        if (match != null)
        {
            total += (match.Price * item.qty);
        }
    }
    return total;
}

public static List<Product> GetProducts()
{
    List<Product> products = new List<Product>();
    Product lesson1 = new Product() { Id = 1000, Name = "Lesson 1", Price = 97 };
    Product lesson2 = new Product() { Id = 1010, Name = "Lesson 2", Price = 97 };
    products.Add(lesson1);
    products.Add(lesson2);
    return products;
}
```

上面的代码包含了我们所有的产品相关类。Product 是我们的主要产品类，包含产品的实例。FormProduct 是一个管道类。这意味着将原始表单数据转换成产品类。ProductManager 是一个助手类。它合计来自表单的产品价格。GetProducts()是一个模拟的方法，类似于从数据源获取产品。

最后，将以下类添加到 Form.cs 中:

```
public class myForm
{
    public string lesson1_1000 { get; set; }
    public string lesson2_1010 { get; set; }
    [JsonProperty("cardName")]
    public string cardName { get; set; }
    [JsonProperty("cardNumber")]
    public string cardNumber { get; set; }
    [JsonProperty("cardYear")]
    public string cardYear { get; set; }
    [JsonProperty("CardMonth")]
    public string CardMonth { get; set; }
    [JsonProperty("CardCv")]
    public string CardCv { get; set; }
    [JsonProperty("totalPrice")]
    public int totalPrice { get; set; }
}
```

这个类有双重用途。它将绑定到我们的表单字段，使得在控制器中访问它们更容易。由于这个实例将包含所有的支付信息，当我们提交给电子商务处理器时使用它不是很好吗？

通过添加 totalPrice，我们拥有了处理支付所需的一切，允许我们序列化 myForm 并提交给支付处理器。

## 捕获提交的表单数据

我们的结帐页面的主要处理发生在 HomeController.cs 文件中。创建以下方法:

```
[HttpPost]
public IActionResult Checkout(myForm form)
{
    Checkout c = new Checkout();

    List<FormProduct> products = new List<FormProduct>();
    if (!string.IsNullOrEmpty(form.lesson1_1000))
    {
        products.Add(new FormProduct() { Id = 1000, qty = Convert.ToInt16(form.lesson1_1000) });
    }
    if (!string.IsNullOrEmpty(form.lesson2_1010))
    {
        products.Add(new FormProduct() { Id = 1010, qty = Convert.ToInt16(form.lesson2_1010) });
    }
    form.totalPrice = ProductManager.productTotal(products);

    var url = "http://localhost:50672/api/values/";
     string serializedData = JsonConvert.SerializeObject(form, Formatting.None);
    serializedData = JsonConvert.SerializeObject(serializedData);
     using (var client = new WebClient())
     {
         client.Headers[HttpRequestHeader.ContentType] = "application/json";

         var result = client.UploadData(url, "POST", Encoding.ASCII.GetBytes(serializedData));
         var response = System.Text.Encoding.UTF8.GetString(result);
         var decode = JsonConvert.DeserializeObject(response);

        c.DisplaySuccess = "true";
        c.DisplayError = "true";
        if (response.Contains("200"))
        {
            c.DisplaySuccess = "false";
            c.DownloadCode = DateTime.Now.Ticks.ToString();
        }
        else if (response.Contains("406"))
        {
            c.DisplayError = "false";
        }
     }
    return View(c);
}
```

我们首先创建一个`Checkout`类的实例。这将不会被使用，直到该方法结束，当我们得到我们的回应，从模仿第三方电子商务处理器。

接下来，我们将表单字段转换成 FormProduct 实例。Checkout()参数的类型是 myForm。这个类允许绑定到表单域，使它们更容易访问。myForm 类位于 HomeController 的下方，并定义在 home controller 的顶部:

我们称之为:

```
form.totalPrice = ProductManager.productTotal(products);
```

…获取提交产品的总价格。我们将总数赋回给`myForm`实例。如前所述，我们可以通过序列化将该类提交给电子商务流程，这是我们接下来要做的。

你可以看到连续化发生了两次。这可能是 JsonConvert 的一个更大的问题。由于没有序列化两次，我们的 JSON 是畸形的，将导致向电子商务处理器提交一个空值。

在我们继续之前，让我们为 JSON serialize 类添加一个 Nuget 包——JSON convert。

右击解决方案资源管理器中的“引用”节点。单击管理 Nuget 包。搜索`Newtonsoft.Json`。你可以在下面的截图中看到，我已经添加好了:

![Nuget package manager webapi](img/359dd746cc71dfb2d6b5ec3a7528079d.png)

接下来，我们将对 Web API 应用程序调用进行排队。Web API 应用程序必须正在运行，因为我们正在引用它的本地 URL。

```
client.Headers[HttpRequestHeader.ContentType] = "application/json";

         var result = client.UploadData(url, "POST", Encoding.ASCII.GetBytes(serializedData));
         var response = System.Text.Encoding.UTF8.GetString(result);
         var decode = JsonConvert.DeserializeObject(response);
```

一旦响应返回，我们反序列化它并寻找特定的响应。如果这是一个真实的电子商务响应，我们可能不会简单地寻找一个字符串。对于如何接收响应，可能会有更多的结构。

我们设置显示和隐藏成功或失败 div 的值。

成功将如下所示:

![Success message](img/9705ce834046b6732ed746646326eb8b.png)

你可以看到我们产品的下载链接。

失败将会是这样的:

![Failure message](img/f7f9373e4862b32584ad24dc697919f5.png)

您可以简单地在 Web API 应用程序中更改返回类型来模拟不同的响应。

## 电子商务处理

电子商务处理基本上会被 Web API 应用程序模仿。我们将在 Web API 应用程序中发布到单个端点。从同一个端点，我们将收到交易成功或失败的消息。

虽然电子商务第三方解决方案差异很大，但我们将使用一个最小但标准的解决方案。我们会将用户的付款信息和总费用一起发送给用户。

要创建 Web API 应用程序，请选择“文件”>“新建项目”>“ASP.NET Web 应用程序”>“Web API:

![ASP.NET](img/c621c5d766f2200e1e72404b3f4a1217.png)

我们需要做的修改是最小的。打开控制器文件夹，然后打开`ValueControllers.cs`文件。修改`POST`方法，如下所示:

```
[HttpPost]
public HttpResponseMessage Post([FromBody]string value)
{
    var product = JsonConvert.DeserializeObject<Product>(value);
    //ecommerce processing
    return new HttpResponseMessage(HttpStatusCode.NotAcceptable);
}
```

`value`参数是我们网站提交的 JSON。在 JsonConvert 的帮助下，这个 JSON 被反序列化为一个类，稍后我们将添加一个引用。从那里，我们可以做任何必要的事情来完成电子商务交易。

由于这只是一个模拟解决方案，我们将立即发回一个状态。这就是为什么返回类型是`HttpResponseMessage`。`NotAcceptable`将发回一个状态码 406，我们将在我们的网站中查找这个错误，并向用户显示一条适当的消息。

为了添加`JsonConvert`引用，我们将添加`Newtonsoft.Json` Nuget 包，就像我们在 web 应用程序中所做的一样。右击解决方案资源管理器中的“引用”节点。单击管理 Nuget 包。搜索`Newtonsoft.Json`。你可以在下面的截图中看到，我已经添加好了:

![Nuget package manager webapi](img/359dd746cc71dfb2d6b5ec3a7528079d.png)

如果您将鼠标悬停在 JsonConvert 上，您会在空白处看到一个黄色灯泡。将鼠标悬停在它上面，您会得到一个提示，为`JsonConvert`类添加正确的 using 语句。

![using hint](img/3d6c612447515a9eaa3038f704e9e9ea.png)

现在我们需要创建我们的产品类。请记住，Web API 应用程序只是嘲笑一个电子商务解决方案。但是这将让我们有机会看到我们提交的 JSON 实际上正在被反序列化为一个类。以下是进入`Product`类的内容:

```
class Product
{
[JsonProperty("cardName")]
public string cardName { get; set; }
[JsonProperty("cardNumber")]
public string cardNumber { get; set; }
[JsonProperty("cardYear")]
public string cardYear { get; set; }
[JsonProperty("CardMonth")]
public string CardMonth { get; set; }
[JsonProperty("CardCv")]
public string CardCv { get; set; }
[JsonProperty("totalPrice")]
public int totalPrice { get; set; }
}
```

在 Post 方法中设置一个断点，我们可以从 web 应用程序中看到绑定数据的样子:

![binded data](img/00f015c3739960f73266bad626173910.png)

MVC 免费给了我们这个绑定。除了确保我们的类属性与进入我们方法的属性相匹配之外，我们不需要做任何事情来实现它。

## 摘要

我们已经扩展了我们的吉他课程网站，允许我们的艺术家直接从他的网站上销售课程。通过一个简单的解决方案，我们将产品列表、购物车和结账都放在了同一个页面上。在一些 AngularJS 的帮助下，我们能够利用动态功能向用户提供即时反馈。

在下一篇文章中，我们将完全用 Visual Studio 创建一个移动应用程序。该应用程序的目的是通知用户新的课程。通过利用 Cordova 框架，我们可以利用现有的基于 web 的语言知识来编写一个功能齐全的移动应用程序。

## 分享这篇文章