# 示例产品数据库 JSON 文件

> 原文：<https://www.sitepoint.com/products-database-json-file/>

这是一个产品数据库 JSON 文件的示例，您可能会看到它用于存储设置您的系统的配置设置。它还可以用来包含产品记录信息，使用简单的 JSON 格式可以很容易地在组件之间共享这些信息。

**也:** [查看更多 JSON 例子。](http://www.jquery4u.com/json/10-example-json-files/)

```
{
        "name":"Product",
        "properties":
        {
                "id":
                {
                        "type":"number",
                        "description":"Product identifier",
                        "required":true
                },
                "name":
                {
                        "description":"Name of the product",
                        "type":"string",
                        "required":true
                },
                "price":
                {
                        "type":"number",
                        "minimum":0,
                        "required":true
                },
                "tags":
                {
                        "type":"array",
                        "items":
                        {
                                "type":"string"
                        }
                }
        }
}
```

## 分享这篇文章