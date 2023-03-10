# 你到底在哪里？

> 原文：<https://www.sitepoint.com/where-on-earth-are-you/>

因为这个网站拥有真正的全球读者，你这个读者可能在世界的任何地方。如果我问你你在地球上的什么地方，你可能会主动说出一个城市、一个州或者仅仅是你所在的国家的名字。答案可能是模棱两可的——那是法国的巴黎还是德克萨斯的巴黎？

现代地理定位应用程序使用纬度和经度来识别几米范围内的人和地方的位置，但你不太可能用这些来回答问题。地理定位——即使用 GPS、Wi-Fi 三角测量或使用您的 IP 地址来获取您的位置——非常好，但有时您只需询问某人他们在哪里。

接下来的挑战是做两件事:弄清楚你可能在谈论什么地方，必要时消除歧义，然后确定那究竟是哪里。

这就是我将在本文中向您展示的方法；通过使用一个免费的 web 服务，我们将编写一个简单的程序来询问用户他们在哪里(如果有必要的话，请他们澄清)，然后以具体的术语确定他们的响应。

## 标识符(WOEIDs)究竟在哪里

我非常谨慎地使用了“地球上的哪里”这个短语，因为有一个数据库，其中的地点不是通过名称来标识的，而是通过所谓的*地球上的哪里标识符* (WOEIDs)来标识的。WOEID 数据库已经由 Yahoo！Flickr 也在使用它，T2 的 Twitter 也在使用它来分析基于地理的趋势。

每个条目都有那个地方的纬度和经度，以及人们给它们起的名字。更复杂的是，并不总是一对一的关系。我称之为布鲁塞尔的地方你可以称之为布鲁塞尔，而当地居民更可能称之为布鲁塞尔。然后就是特例，比如 Peking/北京。

## 起搏器入门

雅虎！s [Placemaker](http://developer.yahoo.com/geo/placemaker/) web 服务是一个“地理提取”服务，它使用 WOEID 数据库并执行我们需要的那种解释；解析自由格式的文本，识别可能的地点，并返回关于它们的信息，比如 WOEID 和非常重要的纬度和经度。

在使用 Placemaker 服务之前，你需要一个应用程序 ID，为了做到这一点，你需要一个 Yahoo！身份证。如果你使用他们的任何服务，你已经有一个了。[去雅虎！网站](http://developer.yahoo.com/wsregapp/)生成申请 ID，要求您填写以下表格:

![YDN Developer Registration](img/2e0d25b0541983267298372378bbba5d.png "YDN Developer Registration")

表格的大部分内容应该是不言自明的——在*身份验证方法*下，勾选“通用，无需用户身份验证”,因为您使用该服务所需要的只是下一页提供的应用 ID。记下这个应用程序 ID，因为您可能会需要它(尽管您可以随时从[开发者区](https://developer.apps.yahoo.com/wsregapp/?view)查看您现有的应用程序 ID)。

### 提出请求

Placemaker 是一个类似 REST 的 web 服务，只有一个端点；要使用该服务，您需要向 URL `http://wherein.yahooapis.com/v1/document`发出 POST 请求。有许多可用的响应类型:除了标准的 XML 和 JSON(以及 JSONP)之外，您还可以选择获取 RSS 格式的结果。虽然我通常更喜欢 JSON(纯粹是个人偏好)，但是在这个例子中，我将使用 XML。

让我们看看您将要使用的五个请求参数。

![POST Parameters](img/15f3b0a372248f7f4ccf1f212379030b.png "POST Parameters")

第一个是`appid`，是您之前获得的应用程序 ID。如果您忽略这一点，请求将被拒绝。第二个，`documentContent`，将是您正在搜索的字符串——之所以这样命名是因为该服务允许解析整个文档，但是我实际上只是传递用户提供的一个地点的名称。

`documentType`和`outputType`都指的是你所期待的回应，尽管它们可能有点令人困惑；(响应的)T2 是纯文本，而对应的 T3 是 XML。(如果`outputType`被设置为 JSON，`documentType`也将是纯文本。)

设置`autoDisambiguate` false 会覆盖服务的默认行为，即只返回最可能的位置。我们希望通过询问用户来解决这种不确定性，而不是让服务为我们做决定。

最后`inputLanguage`告诉服务文档(也就是用户输入)是哪种语言；这有助于服务了解如何解释地名。这不是一个必需的参数(可以预见，它默认为美国英语)，但是值得一提，因为要记住一个单独的 WOEID 可以对应多种语言中的多个名称，这一点很重要。

### 代码

现在让我们深入研究代码，首先通过 POST 从用户那里获得文本并发出请求。

```
<?php
$appId = YOUR_APP_ID_HERE;
$name = $_POST["name"];
$handle = curl_init("http://wherein.yahooapis.com/v1/document");
curl_setopt($handle, CURLOPT_POST, 1);
curl_setopt($handle, CURLOPT_POSTFIELDS, sprintf("documentContent=%s&documentType=%s&outputType=%s&autoDisambiguate=%s&appid=%s&inputLanguage=%s",
    urlencode($name), 
    "text/plain", 
    "xml", 
    "false",
    $appId,
    "en-US"));
curl_setopt($handle, CURLOPT_FOLLOWLOCATION, 1);
curl_setopt($handle, CURLOPT_RETURNTRANSFER, true);
$response = curl_exec($handle);
curl_close($handle);
```

我假设您已经实现了一个表单来捕获名称，为了简洁起见，我还省略了像转义输入这样的事情。代码简单地使用 cURL 将端点设置为`http://wherein.yahooapis.com/v1/document`，将变量 POST 为前面讨论过的变量，并将 web 服务的响应作为字符串返回。不过，在通过 POST 传递之前，不要忘记对名称进行 URL 编码。

在我们开始操作响应之前，让我们看看我们期望的是什么(此时您可能希望将它回显到屏幕上)。下面是一个简化的 XML 响应示例:

```
<?xml version="1.0" encoding="UTF-8"?>
 <contentlocation />
 <processingTime>0.009052</processingTime>
 <version> build 110725</version>
 <documentLength>6</documentLength>
 <document>
  <administrativeScope>..</administrativeScope>
  <geographicScope>..</geographicScope>
  <localscopes>..</localscopes>
  <extents>..</extents>
  <placeDetail>..</placeDetail>
  <placeDetail>..</placeDetail>
  <placeDetail>..</placeDetail>
  <placeDetail>..</placeDetail>
  ...
 </document>
</xml>
```

当您告诉服务不要消除搜索结果的歧义时，响应包含两个方面:最能描述文档的位置的范围，以及文档可以引用的位置。换句话说，该服务提供了一个最佳猜测，并在`administrativeScope`、`geographicScope`、`localscopes`和`extents`元素中为您提供了关于那个地方的更多信息。

例如，如果你查询“巴黎”,那么它会给你关于法兰西岛地区和法国国家的地理信息——但是这些范围并不涉及巴黎、德克萨斯或其他任何同名的地方。我们感兴趣的是那些地方，因为我们希望用户澄清。这些替代项可以在 placeDetails 元素中找到，如下所示:

```
<placeDetails>
 <placeId>3</placeId>
 <place>
  <woeId>615702</woeId>
  <type>Town</type>
  <name><![CDATA[Paris, Ile-de-France, FR]]></name>
  <centroid>
   <latitude>48.8569</latitude>
   <longitude>2.34121</longitude>
  </centroid>
 </place>
 <placeReferenceIds>1</placeReferenceIds>
 <matchType>0</matchType>
 <weight>1</weight>
 <confidence>6</confidence>
</placeDetails>
```

让我们看看`placeDetails`元素的内容。`placeId`表示响应上下文中的一个给定位置，可用于区分具有相同 WOEID 的多个匹配，尽管这与`placeReferenceIds`一起超出了本文的范围。

`weight`和`confidence`告诉我们关于这场比赛的更多信息。`weight`是指该位置在整个文档中的相对权重。这在我们使用服务的上下文中很大程度上是不相关的，因为我们只是发送了一个指向单个位置的文本片段(至少这是我们向用户要求的)。更确切地说，当一个数字被提及时，用于排列位置，这是由它们在文档中的位置和被提及的次数等因素决定的。`confidence`值(在 1-10 的范围内，10 是最确定的)是这个地方在文档中实际被引用的置信度，也是这个地方而不是另一个地方被引用的置信度。最后一个条款很重要，因为它考虑了一个相对可能性，即如果 100 个人说“伦敦”，大多数人可能指的是英国的首都，而不是美国各种较小的伦敦之一。换句话说，我们可以根据置信度的值对结果进行降序排序。

最后，也是最重要的，还有实际的地方。`place`元素有一个类型；这通常是城镇，但也可能是州、郊区，当然也可能是乡村。质心元素包含几何中心的纬度和经度，名称应该是不言自明的，并且`woeId`是我一直在谈论的标识符。因为这是一个被广泛接受的标识符，所以没有什么可以阻止我们使用它来获取其他地方的信息。

### 解读回应

现在我们已经熟悉了响应的结构，让我们试着使用它。下面是解析响应的代码:

```
<?php
$places = array();
$xml = simplexml_load_string($response); 
foreach ($xml->document->placeDetails as $xmlPlaceDetail) {
    $xmlPlace = $xmlPlaceDetail->place;
    $xmlCentroid = $xmlPlace->centroid;

    $place = new stdClass();
    $place->id = (int)$xmlPlaceDetail->placeId;
    $place->woeid = (int)$xmlPlace->woeId;
    $place->name = (string)$xmlPlace->name;
    $place->lat = (float)$xmlCentroid->latitude;
    $place->lng = (float)$xmlCentroid->longitude;
    $place->confidence = (int)$xmlPlaceDetail->confidence;

    $places[$place->id] = $place;
}
```

上面的代码使用 [SimpleXML 解析响应](https://www.sitepoint.com/bending-xml-to-your-will/)，遍历`placeDetail`元素。每次遇到一个，它就用`place`和`centroid`元素来提取我们想要的信息，所有这些信息都被分配给一个新的对象，表示正在讨论的地点。

我们现在有一个可能性列表；然而，该服务还为我们提供了一种度量，即用户认为某个地方比另一个地方更有可能，因此让我们使用一个简单的回调对该数组进行排序:

```
<?php
function confidenceSort($a, $b) {
    if ($a->confidence == $b->confidence) {
        return 0;
    }
    return ($a->confidence > $b->confidence) ? -1 : 1;
}
uasort($places, "confidenceSort");
```

我们应该会得到一系列的排名，按照可信度的降序排列。

## 接下来呢？

我已经向您展示了如何向用户询问一个地点名称，检索关于可能选项的信息，并根据它们表示一个地点相对于另一个地点的可能性对它们进行排序，并且您可以在 GitHub 上找到一个简单的演示。现在你该怎么做取决于你自己——你可以要求用户做出澄清，或者尝试使用偏置来减少备选方案的数量。

一些查询可以产生几十个结果，所以可能值得尝试通过根据您对用户的了解将搜索偏向给定位置来影响搜索——您可以使用`focusWoeId`参数传递一个地点的 WOEID。

你可能希望阅读更多关于 WOEIDs 的[，或者通过查看 Yahoo！美国](http://developer.yahoo.com/geo/geoplanet/guide/concepts.html) [Geoplanet 服务](http://developer.yahoo.com/geo/geoplanet/)——不仅为城镇和城市，也为地标。您可能还希望处理整个文档——想象一下对博客内容进行地理标记的可能性，或者增强基于文本的搜索，以根据用户的位置而不是文本内容来查找文档。浏览文档，玩一玩，如果你被卡住了，雅虎上有一个 [Placemaker 论坛！](http://developer.yahoo.com/forum/Placemaker)您可以在这里找到更多信息并寻求帮助。

如果你真的用这个构建了什么有趣的东西，我很乐意在评论中听到它！

## 分享这篇文章