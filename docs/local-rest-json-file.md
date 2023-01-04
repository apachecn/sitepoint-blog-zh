# 本地 REST JSON 文件

> 原文：<https://www.sitepoint.com/local-rest-json-file/>

*这个[文章系列](https://www.sitepoint.com/10-example-json-files/)在 2017 年年中用最新的信息和新鲜的例子重写。*

在现代 web 开发中，建立 API 服务来连接应用程序和数据库是很常见的。通常，API 服务将使用 XML 或 JSON 格式在客户机和服务器之间交换数据。

在这个例子中，我们将查看一个使用 FeathersJS 构建的定制 NodeJS API 服务。如果您还没有这样做，请下载在[介绍 JSON 示例页面](https://www.sitepoint.com/10-example-json-files/)中记录的项目。

接下来，确保您的系统上运行了 MongoDB。默认情况下，API 服务将访问名为`api`的数据库，并将创建或覆盖名为`customers`的集合。如果需要更改，您可以在以下文件`api/config/default.json`中设置自己的数据库连接参数:

```
// api/config/default.json`

{
  "host": "localhost",
  "port": 3030,
  "public": "../public/",
  "paginate": {
    "default": 10,
    "max": 50
  },
  "mongodb": "mongodb://localhost:27017/api"
} 
```

接下来，安装依赖项并启动 API 服务器:

```
cd api
npm install
npm start 
```

几秒钟后，将生成一些假数据，然后 API 服务将准备好为请求提供服务。使用 URL: `http://localhost:3030/customers`启动浏览器。您应该会看到生成的 JSON 格式的客户数据。以下是漂亮的输出格式:

**部分输出:**

```
{
  "total": 25,
  "limit": 10,
  "skip": 0,
  "data": [{
    "_id": "5968fcad629fa84ab65a5247",
    "first_name": "Sabrina",
    "last_name": "Mayert",
    "address": "69756 Wendy Junction",
    "phone": "1-406-866-3476 x478",
    "email": "donny54@yahoo.com",
    "updatedAt": "2017-07-14T17:17:33.010Z",
    "createdAt": "2017-07-14T17:17:33.010Z",
    "__v": 0
  }, {
    "_id": "5968fcad629fa84ab65a5246",
    "first_name": "Taryn",
    "last_name": "Dietrich",
    "address": "42080 Federico Greens",
    "phone": "(197) 679-7020 x98462",
    "email": "betty_schaefer1@gmail.com",
    "updatedAt": "2017-07-14T17:17:33.006Z",
    "createdAt": "2017-07-14T17:17:33.006Z",
    "__v": 0
  },
  ...
  ]
} 
```

然后，您可以使用这些 JSON 数据来填充您的前端视图。JSON 的美妙之处在于它抽象了运行数据库的底层技术。您可以轻松地切换到不同的数据库类型，而无需更改您的前端逻辑。

以下是本系列中的其他示例:

*   [颜色 JSON 示例](https://www.sitepoint.com/colors-json-example/)
*   [谷歌地图 JSON 示例](https://www.sitepoint.com/google-maps-json-file/)
*   [YouTube JSON 示例](https://www.sitepoint.com/youtube-json-example/)
*   [Twitter JSON 示例](https://www.sitepoint.com/twitter-json-example/)
*   [GeoIP JSON 示例](https://www.sitepoint.com/geoip-json-example)
*   [WordPress JSON 示例](https://www.sitepoint.com/wordpress-json-example/)
*   [数据库 JSON 示例](https://www.sitepoint.com/database-json-file/)
*   [测试数据 JSON 示例](https://www.sitepoint.com/test-data-json-example/)
*   [JSON 服务器示例](https://www.sitepoint.com/json-server-example/)

## 分享这篇文章