# 示例客户表单 JSON 文件

> 原文：<https://www.sitepoint.com/customer-form-json-file-example/>

这是一个客户表单 JSON 文件的例子，您可能会看到它用于存储配置设置来设置您的系统。它还可以用来包含记录信息，使用简单的 JSON 格式可以很容易地在组件之间共享这些信息。

**也:** [查看更多 JSON 例子。](http://www.jquery4u.com/json/10-example-json-files/)

```
{
     "firstName": "John",
     "lastName": "Smith",
     "age": 25,
     "address":
     {
         "streetAddress": "21 2nd Street",
         "city": "New York",
         "state": "NY",
         "postalCode": "10021"
     },
     "phoneNumber":
     [
         {
           "type": "home",
           "number": "212 555-1234"
         },
         {
           "type": "fax",
           "number": "646 555-4567"
         }
     ]
 }
```

## 分享这篇文章