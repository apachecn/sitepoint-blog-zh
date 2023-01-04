# 测试数据 JSON 示例

> 原文：<https://www.sitepoint.com/test-data-json-example/>

*这个测试数据 JSON 示例是[文章系列](https://www.sitepoint.com/10-example-json-files/)的一部分，该系列在 2017 年年中用最新信息和新鲜示例重写。*

在当今的现代编码实践中，构建新的应用程序通常需要前端和后端构建方法。通常，两个独立的团队被指派同时在每个领域工作。

在早期阶段，前端开发人员将需要数据来测试他们创建的视图。后端开发人员还需要数据来测试 CRUD 逻辑、安全性和他们正在处理的其他定制业务流程。在这两种情况下，测试数据通常在开始时是不可用的。您可以自己创建一些，但是，它很慢，并且经常导致不确定的结果。

幸运的是，我们生活在一个可以获得在线服务的时代，这些服务可以轻松地免费生成数百行测试数据。一个这样的服务是 [Mockaroo](https://www.mockaroo.com/) 。它支持以多种数据格式生成数据，包括 JSON。这是我从他们的网站上得到的一个例子:

```
[{
  "id": 1,
  "first_name": "Jeanette",
  "last_name": "Penddreth",
  "email": "jpenddreth0@census.gov",
  "gender": "Female",
  "ip_address": "26.58.193.2"
}, {
  "id": 2,
  "first_name": "Giavani",
  "last_name": "Frediani",
  "email": "gfrediani1@senate.gov",
  "gender": "Male",
  "ip_address": "229.179.4.212"
}, {
  "id": 3,
  "first_name": "Noell",
  "last_name": "Bea",
  "email": "nbea2@imageshack.us",
  "gender": "Female",
  "ip_address": "180.66.162.255"
}, {
  "id": 4,
  "first_name": "Willard",
  "last_name": "Valek",
  "email": "wvalek3@vk.com",
  "gender": "Male",
  "ip_address": "67.76.188.26"
}] 
```

该服务提供了令人难以置信的 132 个字段，您可以使用这些字段为您的应用程序生成测试数据。您可以免费生成多达 1000 行测试数据。如果你需要创造更多，有商业计划。

如果你想对生成的数据有更多的控制，有另一个叫做 [json-generator](http://www.json-generator.com/) 的在线服务可能有你想要的。它需要 JavaScript 输入以 JSON 格式输出定制的测试数据。请参见下面的示例:

**JavaScript 输入:**

```
[
  '{{repeat(5, 7)}}',
  {
    _id: '{{objectId()}}',
    isActive: '{{bool()}}',
    balance: '{{floating(1000, 4000, 2, "$0,0.00")}}',
    age: '{{integer(20, 40)}}',
    eyeColor: '{{random("blue", "brown", "green")}}',
    name: '{{firstName()}} {{surname()}}',
    gender: '{{gender()}}',
    company: '{{company().toUpperCase()}}',
    email: '{{email()}}',
    phone: '+1 {{phone()}}',
    friends: [
      '{{repeat(3)}}',
      {
        id: '{{index()}}',
        name: '{{firstName()}} {{surname()}}'
      }
    ],
    favoriteFruit: function (tags) {
      var fruits = ['apple', 'banana', 'strawberry'];
      return fruits[tags.integer(0, fruits.length - 1)];
    }
  }
] 
```

**JSON 测试数据输出**(部分结果):

```
[
  {
    "_id": "5973782bdb9a930533b05cb2",
    "isActive": true,
    "balance": "$1,446.35",
    "age": 32,
    "eyeColor": "green",
    "name": "Logan Keller",
    "gender": "male",
    "company": "ARTIQ",
    "email": "logankeller@artiq.com",
    "phone": "+1 (952) 533-2258",
    "friends": [
      {
        "id": 0,
        "name": "Colon Salazar"
      },
      {
        "id": 1,
        "name": "French Mcneil"
      },
      {
        "id": 2,
        "name": "Carol Martin"
      }
    ],
    "favoriteFruit": "banana"
  }
] 
```

首页显示的示例代码展示了您可以定制数据生成方式的多种方式。

以下是本系列中的其他示例:

*   [颜色 JSON 示例](https://www.sitepoint.com/colors-json-example/)
*   [谷歌地图 JSON 示例](https://www.sitepoint.com/google-maps-json-file/)
*   [YouTube JSON 示例](https://www.sitepoint.com/youtube-json-example/)
*   [Twitter JSON 示例](https://www.sitepoint.com/twitter-json-example/)
*   [GeoIP JSON 示例](https://www.sitepoint.com/geoip-json-example)
*   [WordPress JSON 示例](https://www.sitepoint.com/wordpress-json-example/)
*   [数据库 JSON 示例](https://www.sitepoint.com/database-json-file/)
*   [本地 REST JSON 示例](https://www.sitepoint.com/local-rest-json-file/)
*   [JSON 服务器示例](https://www.sitepoint.com/json-server-example/)

## 分享这篇文章