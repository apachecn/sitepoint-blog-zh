# JSON 服务器示例

> 原文：<https://www.sitepoint.com/json-server-example/>

*这个 JSON 服务器示例是[系列文章](https://www.sitepoint.com/10-example-json-files/)的一部分，该系列文章在 2017 年年中用最新信息和新示例重写。*

JSON 服务器是前端开发人员的流行工具，可以在不到一分钟的时间内快速建立一个完全伪造的 REST API。您需要首先通过 npm 安装它:

```
npm install -global json-server 
```

接下来，在 JSON 文件中保存一些数据，并将其命名为 **db.json** :

```
{
  "clients": [
    {
      "id": "59761c23b30d971669fb42ff",
      "isActive": true,
      "age": 36,
      "name": "Dunlap Hubbard",
      "gender": "male",
      "company": "CEDWARD",
      "email": "dunlaphubbard@cedward.com",
      "phone": "+1 (890) 543-2508",
      "address": "169 Rutledge Street, Konterra, Northern Mariana Islands, 8551"
    },
    {
      "id": "59761c233d8d0f92a6b0570d",
      "isActive": true,
      "age": 24,
      "name": "Kirsten Sellers",
      "gender": "female",
      "company": "EMERGENT",
      "email": "kirstensellers@emergent.com",
      "phone": "+1 (831) 564-2190",
      "address": "886 Gallatin Place, Fannett, Arkansas, 4656"
    },
    {
      "id": "59761c23fcb6254b1a06dad5",
      "isActive": true,
      "age": 30,
      "name": "Acosta Robbins",
      "gender": "male",
      "company": "ORGANICA",
      "email": "acostarobbins@organica.com",
      "phone": "+1 (882) 441-3367",
      "address": "697 Linden Boulevard, Sattley, Idaho, 1035"
    }
  ]
} 
```

最后，使用以下命令启动服务器:

```
json-server --watch src/db.json 
```

现在，您可以通过合适的客户端访问简单的 REST API。目前，Chrome、Firefox 或 Safari 等现代浏览器就可以了。打开 http://localhost:3000/clients，您将看到 JSON 格式的整个微型数据库。您可以使用请求格式`http://localhost:3000/clients/{id}`通过 **id** 查看项目。例如，打开 http://localhost:3000/clients/59761 c 233d 8d 0 f 92 a6b 0570d 将产生:

```
{
  "id": "59761c233d8d0f92a6b0570d",
  "isActive": true,
  "age": 24,
  "name": "Kirsten Sellers",
  "gender": "female",
  "company": "EMERGENT",
  "email": "kirstensellers@emergent.com",
  "phone": "+1 (831) 564-2190",
  "address": "886 Gallatin Place, Fannett, Arkansas, 4656"
} 
```

要了解更多关于 JSON 服务器的信息，请查看教程[使用 json-server 模仿 REST APIs】](https://www.sitepoint.com/mock-rest-apis-using-json-server/)

**亦:** [查看更多 JSON 实例](https://www.sitepoint.com/10-example-json-files/)。

以下是本系列中的其他示例:

*   [颜色 JSON 示例](https://www.sitepoint.com/colors-json-example/)
*   [谷歌地图 JSON 示例](https://www.sitepoint.com/google-maps-json-file/)
*   [YouTube JSON 示例](https://www.sitepoint.com/youtube-json-example/)
*   [Twitter JSON 示例](https://www.sitepoint.com/twitter-json-example/)
*   [GeoIP JSON 示例](https://www.sitepoint.com/geoip-json-example)
*   [WordPress JSON 示例](https://www.sitepoint.com/wordpress-json-example/)
*   [数据库 JSON 示例](https://www.sitepoint.com/database-json-file/)
*   [本地 REST JSON 示例](https://www.sitepoint.com/local-rest-json-file/)
*   [测试数据 JSON 示例](https://www.sitepoint.com/test-data-json-example/)

## 分享这篇文章