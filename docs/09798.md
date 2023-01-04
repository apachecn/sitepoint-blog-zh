# 数据库 JSON 文件

> 原文：<https://www.sitepoint.com/database-json-file/>

*这个[文章系列](https://www.sitepoint.com/10-example-json-files/)在 2017 年年中用最新的信息和新鲜的例子重写。*

大多数关系数据库使用 SQL 进行数据和模式操作。然而，每个 DBMS 供应商都开发了自己的 SQL 方言。这意味着在不同的 DBMS 平台之间交换数据需要数据转换。这主要是通过使用第三方工具实现的。

对于 NoSQL 数据库，大多数都支持 JSON 作为一种导入格式。这意味着您可以从一个 NoSQL 数据库(如 Mongo)导出 JSON 格式的数据，并将相同的数据导入另一个 NoSQL 数据库(如 RethinkDB ),而无需进行任何转换。

在本例中，我们将查看一个使用生成器创建的 JSON 文件，该文件可以导入到 Mongo 等 NoSQL 数据库中。

**products.json:**

```
[{
  "_id": {
    "$oid": "5968dd23fc13ae04d9000001"
  },
  "product_name": "sildenafil citrate",
  "supplier": "Wisozk Inc",
  "quantity": 261,
  "unit_cost": "$10.47"
}, {
  "_id": {
    "$oid": "5968dd23fc13ae04d9000002"
  },
  "product_name": "Mountain Juniperus ashei",
  "supplier": "Keebler-Hilpert",
  "quantity": 292,
  "unit_cost": "$8.74"
}, {
  "_id": {
    "$oid": "5968dd23fc13ae04d9000003"
  },
  "product_name": "Dextromathorphan HBr",
  "supplier": "Schmitt-Weissnat",
  "quantity": 211,
  "unit_cost": "$20.53"
}] 
```

要将这些数据导入 MongoDB，请使用以下命令:

```
 mongoimport --db api --collection products --drop --jsonArray --file products.json 
```

导入完成后，登录数据库并确认数据确实已导入:

```
db.products.find()
 # output
 { "_id" : ObjectId("5968dd23fc13ae04d9000001"), "product_name" : "sildenafil citrate", "supplier" : "Wisozk Inc", "quantity" : 261, "unit_cost" : "$10.47" }
{ "_id" : ObjectId("5968dd23fc13ae04d9000002"), "product_name" : "Mountain Juniperus ashei", "supplier" : "Keebler-Hilpert", "quantity" : 292, "unit_cost" : "$8.74" }
{ "_id" : ObjectId("5968dd23fc13ae04d9000003"), "product_name" : "Dextromathorphan HBr", "supplier" : "Schmitt-Weissnat", "quantity" : 211, "unit_cost" : "$20.53" } 
```

对于其他 NoSQL 数据库，请查看它们的参考手册，了解如何完成相同的任务。

以下是本系列中的其他示例:

*   [颜色 JSON 示例](https://www.sitepoint.com/colors-json-example/)
*   [谷歌地图 JSON 示例](https://www.sitepoint.com/google-maps-json-file/)
*   [YouTube JSON 示例](https://www.sitepoint.com/youtube-json-example/)
*   [Twitter JSON 示例](https://www.sitepoint.com/twitter-json-example/)
*   [GeoIP JSON 示例](https://www.sitepoint.com/geoip-json-example)
*   [WordPress JSON 示例](https://www.sitepoint.com/wordpress-json-example/)
*   [本地 REST JSON 示例](https://www.sitepoint.com/local-rest-json-file/)
*   [测试数据 JSON 示例](https://www.sitepoint.com/test-data-json-example/)
*   [JSON 服务器示例](https://www.sitepoint.com/json-server-example/)

## 分享这篇文章