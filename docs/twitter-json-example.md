# Twitter JSON 文件示例

> 原文：<https://www.sitepoint.com/twitter-json-example/>

*这个[文章系列](https://www.sitepoint.com/10-example-json-files/)在 2017 年年中用最新的信息和新鲜的例子重写。*

最大的社交网络之一 Twitter 多年来一直通过 REST API 向开发者提供对其平台的访问。他们还为对实时数据感兴趣的开发人员提供了一个流式 API。要访问这些 API，你需要首先注册一个应用程序[，这里是](https://apps.twitter.com/)。确保你已经阅读了[开发者协议](https://dev.twitter.com/overview/terms/agreement-and-policy)，否则如果你创建一个违反他们条款的应用程序，你将被锁定。

一旦您注册了您的应用程序，您将能够生成您的应用程序访问 Twitter 数据所需的以下密钥。

*   消费者密钥(也称为 API 密钥)
*   消费者秘密
*   访问令牌密钥
*   访问令牌秘密

Twitter API 使用 JSON 格式与第三方应用程序通信。因此，您可以使用任何支持 JSON 的编程语言来开发应用程序。在这个例子中，我们将使用 NodeJS。

首先，git 克隆 [json-examples 项目](https://github.com/brandiqa/json-examples)，安装依赖项并创建一个. env 文件。

```
git@github.com:sitepoint-editors/json-examples.git
cd json-examples
npm install
touch .env 
```

在**中。env** 文件，您需要填充以下设置:

```
TWITTER_CONSUMER_KEY=
TWITTER_CONSUMER_SECRET=
TWITTER_ACCESS_TOKEN_KEY=
TWITTER_ACCESS_TOKEN_SECRET= 
```

接下来，我们来看看`twitter-json-example.js`代码。

```
require('dotenv').config();
var Twitter = require('twitter');

const CONSUMER_KEY = 'TWITTER_CONSUMER_KEY';
const CONSUMER_SECRET = 'TWITTER_CONSUMER_SECRET';
const ACCESS_TOKEN_KEY = 'TWITTER_ACCESS_TOKEN_KEY';
const ACCESS_TOKEN_SECRET = 'TWITTER_ACCESS_TOKEN_SECRET';

// Validate Twitter API Keys
const keys = [CONSUMER_KEY, CONSUMER_SECRET, ACCESS_TOKEN_KEY, ACCESS_TOKEN_SECRET ]
keys.forEach((key) => {
  if(!process.env[key])
    throw new Error(key + ' has not been set!');
});

var client = new Twitter({
  consumer_key: process.env[CONSUMER_KEY],
  consumer_secret: process.env[CONSUMER_SECRET],
  access_token_key: process.env[ACCESS_TOKEN_KEY],
  access_token_secret: process.env[ACCESS_TOKEN_SECRET]
});

var params = {screen_name: 'sitepointJS', count: 3};
client.get('statuses/user_timeline', params, function(error, tweets, response) {
  if (!error) {
    console.log(JSON.stringify(tweets));
  }
}); 
```

为了轻松地使用 Twitter REST API，我们获得了一个名为 [Twitter](https://www.npmjs.com/package/twitter) 的 npm 包的帮助。首先，我们验证已经定义了所有的 API 键。然后，我们在路径`statuses/user_timeline`上执行查询。要了解更多关于 Twitter API 路径的信息，请查看 [Apigee Twitter 控制台](https://apigee.com/console/twitter)。

要执行代码，只需:

```
node twitter-json-example.js 
```

等待几秒钟，您很快就会收到一个 JSON 输出。下面我展示了部分结果:

```
[{
  "created_at": "Thu Jun 22 21:00:00 +0000 2017",
  "id": 877994604561387500,
  "id_str": "877994604561387520",
  "text": "Creating a Grocery List Manager Using Angular, Part 1: Add &amp; Display Items https://t.co/xFox78juL1 #Angular",
  "truncated": false,
  "entities": {
    "hashtags": [{
      "text": "Angular",
      "indices": [103, 111]
    }],
    "symbols": [],
    "user_mentions": [],
    "urls": [{
      "url": "https://t.co/xFox78juL1",
      "expanded_url": "http://buff.ly/2sr60pf",
      "display_url": "buff.ly/2sr60pf",
      "indices": [79, 102]
    }]
  },
  "source": "<a href=\"http://bufferapp.com\" rel=\"nofollow\">Buffer</a>",
  "user": {
    "id": 772682964,
    "id_str": "772682964",
    "name": "SitePoint JavaScript",
    "screen_name": "SitePointJS",
    "location": "Melbourne, Australia",
    "description": "Keep up with JavaScript tutorials, tips, tricks and articles at SitePoint.",
    "url": "http://t.co/cCH13gqeUK",
    "entities": {
      "url": {
        "urls": [{
          "url": "http://t.co/cCH13gqeUK",
          "expanded_url": "https://www.sitepoint.com/javascript",
          "display_url": "sitepoint.com/javascript",
          "indices": [0, 22]
        }]
      },
      "description": {
        "urls": []
      }
    },
    "protected": false,
    "followers_count": 2145,
    "friends_count": 18,
    "listed_count": 328,
    "created_at": "Wed Aug 22 02:06:33 +0000 2012",
    "favourites_count": 57,
    "utc_offset": 43200,
    "time_zone": "Wellington",
  },
}] 
```

以下是本系列中的其他示例:

*   [颜色 JSON 示例](https://www.sitepoint.com/colors-json-example/)
*   [谷歌地图 JSON 示例](https://www.sitepoint.com/google-maps-json-file/)
*   [YouTube JSON 示例](https://www.sitepoint.com/youtube-json-example/)
*   [GeoIP JSON 示例](https://www.sitepoint.com/geoip-json-example)
*   [WordPress JSON 示例](https://www.sitepoint.com/wordpress-json-example/)
*   [数据库 JSON 示例](https://www.sitepoint.com/database-json-file/)
*   [本地 REST JSON 示例](https://www.sitepoint.com/local-rest-json-file/)
*   [测试数据 JSON 示例](https://www.sitepoint.com/test-data-json-example/)
*   [JSON 服务器示例](https://www.sitepoint.com/json-server-example/)

## 分享这篇文章