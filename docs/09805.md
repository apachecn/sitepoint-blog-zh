# YouTube JSON 文件示例

> 原文：<https://www.sitepoint.com/youtube-json-example/>

*这个[文章系列](https://www.sitepoint.com/10-example-json-files/)在 2017 年年中用最新的信息和新鲜的例子重写。*

YouTube 是一种在线视频分享服务，由 PayPal 的前员工于 2005 年创建。今天，它作为谷歌的子公司运营，根据 Alexa 的数据，它目前是全球第二大最受欢迎的网站。

在这个例子中，我将向你展示如何使用 [YouTube 数据 API](https://developers.google.com/youtube/v3/) 。该 API 允许您将 YouTube 功能集成到网站中。它还为开发者提供了一种支持视频、播放列表和频道搜索的`search.list`方法。该 API 有多个针对不同平台的客户端库，包括 Android、Go、Java、JavaScript、NodeJS 等。NET、PHP、Python 和 Ruby。

让我们看看如何使用 JavaScript 分四步执行简单的搜索:

1.  按照[介绍页](https://www.sitepoint.com/10-example-json-files/)中的概述克隆项目，如果您还没有这样做的话。

2.  在你的机器**中全局安装 [http-server](https://www.npmjs.com/package/http-server) 包**。

    ```
    $ npm i -g http-server 
    ```

3.  使用此[链接](https://developers.google.com/youtube/v3/getting-started)获取您的 YouTube API 密钥。一旦你获得了这些，打开`src/youtube-example.html`文件并用你的 api 密匙替换`undefined`。

    **HTML/JS 代码**:

    ```
    <!DOCTYPE html>
    <html>
    <head>
    <title>YouTube JSON Example</title>

    <script type="text/javascript"> const api = undefined; // Specify your api key here

      // Load API library
      function onLoad() {
        if(api) {
          gapi.client.load('youtube', 'v3', loadYouTubeApi);
        } else {
          document.getElementById('output').innerHTML += 'API key has not been specified!';
        }
      }

      // Set API Key
      function loadYouTubeApi() {
        gapi.client.setApiKey(api);
        search('sitepoint');
      }

      // Call the search.list()
      function search(queryTerm) {
        let request = gapi.client.youtube.search.list({
            part: 'id',
            q: queryTerm
        });
        // Execute the request call and output it in HTML view
        request.execute((response) => {
          const responseString = JSON.stringify(response, '', 2);
          document.getElementById('output').innerHTML += responseString;
        })
      } </script>
    <script src="https://apis.google.com/js/client.js?onload=onLoad" type="text/javascript"></script>
    </head>
    <body>
    <h1>YouTube Search Results</h1>
    <pre id="output"></pre>
    </body>
    </html> 
    ```

4.  要运行 html 代码，您需要通过服务器加载它。直接用浏览器打开是不行的。导航到`src`文件夹并启动我们刚刚安装的`http-server`。

    ```
    $ http-server

    Starting up http-server, serving ./
    Available on:
    http://127.0.0.1:8080
    http://10.0.2.15:8080 
    ```

服务器启动并运行后，您可以打开浏览器并使用 URL 访问该页面:http://127 . 0 . 0 . 1:8080/YouTube-example . html。几秒钟后，该页面将填充一些 JSON 数据，如下所示:

**YouTube API JSON 响应**(部分结果):

```
{
  "kind": "youtube#searchListResponse",
  "etag": "\"m2yskBQFythfE4irbTIeOgYYfBU/PaiEDiVxOyCWelLPuuwa9LKz3Gk\"",
  "nextPageToken": "CAUQAA",
  "regionCode": "KE",
  "pageInfo": {
    "totalResults": 4249,
    "resultsPerPage": 5
  },
  "items": [
    {
      "kind": "youtube#searchResult",
      "etag": "\"m2yskBQFythfE4irbTIeOgYYfBU/QpOIr3QKlV5EUlzfFcVvDiJT0hw\"",
      "id": {
        "kind": "youtube#channel",
        "channelId": "UCJowOS1R0FnhipXVqEnYU1A"
      }
    },
    {
      "kind": "youtube#searchResult",
      "etag": "\"m2yskBQFythfE4irbTIeOgYYfBU/AWutzVOt_5p1iLVifyBdfoSTf9E\"",
      "id": {
        "kind": "youtube#video",
        "videoId": "Eqa2nAAhHN0"
      }
    },
    {
      "kind": "youtube#searchResult",
      "etag": "\"m2yskBQFythfE4irbTIeOgYYfBU/2dIR9BTfr7QphpBuY3hPU-h5u-4\"",
      "id": {
        "kind": "youtube#video",
        "videoId": "IirngItQuVs"
      }
    }
  ]
} 
```

以下是本系列中的其他示例:

*   [颜色 JSON 示例](https://www.sitepoint.com/colors-json-example/)
*   [谷歌地图 JSON 示例](https://www.sitepoint.com/google-maps-json-file/)
*   [Twitter JSON 示例](https://www.sitepoint.com/twitter-json-example/)
*   [GeoIP JSON 示例](https://www.sitepoint.com/geoip-json-example)
*   [WordPress JSON 示例](https://www.sitepoint.com/wordpress-json-example/)
*   [数据库 JSON 示例](https://www.sitepoint.com/database-json-file/)
*   [本地 REST JSON 示例](https://www.sitepoint.com/local-rest-json-file/)
*   [测试数据 JSON 示例](https://www.sitepoint.com/test-data-json-example/)
*   [JSON 服务器示例](https://www.sitepoint.com/json-server-example/)

## 分享这篇文章