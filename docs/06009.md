# 整合聚合物/Dart 和 Symfony–第 2 部分

> 原文：<https://www.sitepoint.com/integrating-polymerdart-symfony-part-2/>

在前一篇文章中，我们在 Symfony 应用程序中实现了一个 Dart 小部件。让我们现在处理其余的。这可能会变得更高级，所以拿起一杯咖啡，集中注意力——我建议你在完成第 1 部分后再继续学习！

### 避开 JSONP 并在模板中显示对象的成员

如果服务器(以及配置和编程)由我们自己管理，那么从同一个服务器的 RESTful API 获取数据的过程将会很简单。我们可以在返回的响应头中启用 CORS。搞定了。但是如果远程服务器的 RESTful API 没有设置这个头，当我们试图从 Dart 应用程序中调用这个 API 调用时，我们将会面临一个 CORS 错误。

一个经常讨论的技术是使用 [JSONP](http://en.wikipedia.org/wiki/JSONP) 。Dart 也有一些提供`jsonp`功能的包。你可以在这里搜索“jsonp”找到这些包[。](http://pub.dartlang.org/)

然而，在我们的实现中，由于 JSONP 的[“hacky”性质](http://stackoverflow.com/questions/2067472/what-is-jsonp-all-about)，我们将采用另一种方式。

一个公平的假设是，任何从事网站开发的人都应该至少有一个自己管辖的网站。

在我的第二个集成到主页的 Dart 小部件中，我试图从两个不同的远程服务器上获取一些天气信息(它们都没有启用 CORS)。显然，我无法控制这两台服务器，所以我执行以下操作:

首先，在 Symfony 中创建一个控制器，从这两个远程服务器获取数据:

```
 public function WeatherAction($type, $param)
    {
        if ($type == 1) // Local weather info
        {
            $weather = file_get_contents("http://m.weather.com.cn/data/$param.html"); //101190401 for Suzhou, my hometown
        }
        else
        {
            $weather = file_get_contents("http://api.openweathermap.org/data/2.5/weather?q=$param&units=metric"); //suzhou,china
        }
        $response = $this->render('trrsywxBundle:Default:json.html.twig', array('res' => $weather));
        $response = $this->setExtraHeader($response);

        return $response;
    }
```

这两个站点都在调用时返回 JSON 字符串，但是都没有设置 CORS 头。在 Dart 中，这样的 API 调用会失败，但在 Symfony 的环境中，它是成功的——只有 JavaScript 受到 CORS 的限制，服务器端语言仍然可以消耗其他域上的资源。还要注意，通过提供额外的头来包装来自远程服务器的返回，我们可以启用 CORS。

接下来，在 Dart 中，我们创建了另一个聚合物应用程序来获取 Symfony 抓取的信息并做进一步处理:

```
 void getWeather()
  {
    var path1='http://rsywx/app_dev.php/json/weather/1/$code';
    var path2='http://rsywx/app_dev.php/json/weather/2/$city';

    HttpRequest.getString(path1).then((String res)=>processWeather1(res));
    HttpRequest.getString(path2).then((String res)=>processWeather2(res));

    if(one)
      one=false;
    else
      one=true;
  }

  void processWeather1(String res)
  {
    w1=new Weather1.created(res);    
  }

  void processWeather2(String res)
  {
    w2=new Weather2.created(res);
  }
}
```

从我们自己的 Symfony 网站上检索数据后，过程很简单。

在这个实现中，每次“刷新”都会从两个服务器获取天气信息。通过确定“刷新”点击要显示的内容，可以对其进行微调，以消除冗余的 API 调用(变量`one`在每次调用`getWeather`函数时切换其布尔状态，该函数由每个“刷新”请求调用)。

在我们的 Dart 聚合模板中也使用了`one`来决定显示哪条天气信息，因为这两条信息将在我们的 HTML 流中共享同一个槽。显示天气信息的 HTML 模板如下所示:

```
<polymer-element name="weather-tag" attributes="city code">
  <template>
    <meta charset="utf-8">
    <template if='{{!one}}'>
        <div class="ui message">
          //Display local weather information
        </div>
    </template>
    <template if='{{one}}'>
        <div class="ui message">
        //Display local weather information from another source
        </div>

    </template>

  </template>
  <script type="application/dart" src="weather.dart"></script>
</polymer-element>
```

我们声明了两个类来简化天气信息的实例化和更容易的访问。`Weather2`类如下所示:

```
class Weather2
{
  @observable var desc, city, temp, humidity;
  Weather2.created(String s)
  {
    Map w=JSON.decode(s);
    this.temp=w['main']['temp'].toStringAsFixed(1);
    this.humidity=w['main']['humidity'];
    this.city=w['name'];
    this.desc=w['weather'][0]['description']; 
  }
}
```

基本上，我们只是从返回的 JSON 字符串中提取某些数据，并将它们放入类的成员中。

应该特别注意类成员的声明。我们用`@observable`装饰它们。这是为了防止 Dart 编译时树抖动丢弃这些类成员的引用，因为(很可能的原因是)当 Dart 程序被编译成 JavaScript 时，它们在我们的聚合表达式中没有被显式引用。感谢[京特·佐奇鲍尔](http://stackoverflow.com/users/217408/gunter-zochbauer)在[我在 StackOverflow](http://stackoverflow.com/questions/20727807/dart-compiled-to-js-but-object-notation-is-not-working) 提出的问题中提供的提示。

**在 Dartium 中运行应用程序时，这不是必需的。但是如果我们把它编译成 JS，这是必须的。**

借助于定义的天气类，我们可以用一种更面向对象的方式显示聚合物模板中的对象:

```
 <template if='{{one}}'>
        <div class="ui message">
        <p>Today in {{w2.city}}: {{w2.desc}}, temperature {{w2.temp}} celcius, humidity {{w2.humidity}}%
        <br><i title="Refresh" class="refresh icon small link" on-click="{{getWeather}}"></i></p>
        </div>

    </template>
```

不使用类，我们可以将变量命名为`w2city`、`w2temp`等。但是这既不方便用户，也不方便开发者，对吗？

### 两个 Dart，一个 HTML？省道的限制

到目前为止，这个过程还算顺利。现在我需要将上面创建的两个 Dart 小部件集成到同一个主页中。我毫不犹豫地修改了我的 HTML 模板，如下所示:

```
<!DOCTYPE html>
<html lang="zh-CN">
    <head>
        <script src="/packages/shadow_dom/shadow_dom.debug.js"></script>
        <script src="/packages/custom_element/custom-elements.debug.js"></script>
        <script src="/packages/browser/interop.js"></script>
        <meta charset="utf-8">

        <link rel="stylesheet" type="text/css" href="/css/semantic.css">
        <link rel="stylesheet" type="text/css" href="/css/rsywx.css">

        <script src="/getqotd.html_bootstrap.dart.js"></script>
        <script src="/getweather.html_bootstrap.dart.js"></script>

        <title>...</title>
    </head>

    <body id="home">
        <polymer-element name="qotd-tag">
            <template>
                ...
            </template>
        </polymer-element>
        <polymer-element name="weather-tag" attributes="city code">
            <template>
                ...
            </template>
        </polymer-element>
        {% block content %}
        <div class="ui inverted page grid masthead segment">
            <div class="ui grid">
                <div class="row">
                    <div class="ten wide column">
                        <qotd-tag></qotd-tag>
                        <weather-tag code="101190401" city="Suzhou,china"></weather-tag>
                    </div>
                </div>
            </div>
        </div>
```

我们插入了必要的文件，特别是两个编译过的 JS 文件，并声明了标记，然后按照前面描述的步骤在需要的地方插入标记。

看起来很有希望，对吧？

**不幸的是，没有**

在上面的实现中，只有 JS 首先被导入的小部件(`qotd`)才能正常显示。如果我们切换两个 JS 导入的顺序，让`weather` JS 先被导入，那么只有`weather`小部件可以被显示。

我认为这个问题是由于编译的两个 JS 文件在函数名、原型声明等方面有很多冲突。例如，我们可以在两个文件中找到以下声明:

```
ght:function(a){return new P.C7(this,P.WV.prototype.h,a,"h")},
zw:function(a,b){if(this.Gv>=4)throw H.b(this.q7())
this.pb(a,b)},
```

因此，只有一个 JS 可以存活和运行。只是，我很奇怪为什么第二个 JS 会失败(而不是覆盖第一个 JS 中的一切)。

为了解决这个问题，我们必须再创建一个 Dart/Polymer 应用程序，导入在前面步骤中为 QOTD 和天气创建的两个 Dart 文件。然后我们需要将这两个 Dart 小部件包装成一个新的小部件。

Github 存储库包含最终的 Dart 应用程序，运行良好。我放了一个 Youtube 视频来演示这个结果(很抱歉主页大部分是中文的，但是你会看到 Dart 是如何进入 Symfony 页面并响应用户交互的。您可能需要切换到 720p 以获得更清晰的视图。)

这显然是 Dart 编译的 JS 的一大弊端。

这是不灵活的，并且使扩展变得困难。我们现在正在集成两个小部件。如果我们想稍后看到一些股票报价，该怎么办？我最喜欢的球队的比分？新闻提要？每当我们需要添加或删除小部件组件时，开发新的或修改现有的 Dart 小部件是唯一的选择吗？

这种方法当然可行，但效率非常低。另外，并不是所有的 web 开发者都同时是 Dart 开发者。要求 web 开发人员打开 Dart IDE 并开始 Dart 编码来插入一个简单的新组件是不现实的。

为什么我们不能有一个 Dart 开发，让 Dart(和 Dart 程序员)可以构建各种自包含的小部件(组件),而 web 开发人员只需抓取编译好的 JS/html/CSS/image 文件，只需修改 web 页面并开始制定“新的”小部件？如果能够做到这一点，Dart 将会更快地被广泛接受。

这是 Dart 中已确认的问题。[一个功能请求已经发布](https://code.google.com/p/dart/issues/detail?id=15783&q=taylor&colspec=ID%20Type%20Status%20Priority%20Area%20Milestone%20Owner%20Summary)到 Dart Google Group，以寻求他们的关注和进一步改进。通过投票来帮忙。

### 结论

在这个系列中，我们演示了如何将 Dart/Polymer 集成到 Symfony 中，以带来我们的页面动态内容和用户交互。给出了详细的分步说明，以帮助用户管理集成，特别是将聚合物模板插入 Twig 引擎。

此外，我们还讨论了 Dart 编程中的几个主题:绕过 JSONP 并在 Polymer 模板中显示对象。

最后，我们强调了当前 Dart JS 编译的局限性:除非创建一个新的包装 Dart 应用程序，否则两个或多个独立的 Dart 应用程序不能存在于一个 HTML 中。我真的希望这个问题能尽快解决。

这给我们带来了这个系列的结束。欢迎随时评论，提出问题！

## 分享这篇文章