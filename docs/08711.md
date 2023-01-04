# 使用 JSON 通过 AJAX 对 PHP 进行 jQuery

> 原文：<https://www.sitepoint.com/jquery-php-ajax-json/>

这就是你如何使用包含 JSON 数据的“POST”方法生成 AJAX 使用的数据，然后将它传递给 PHP 脚本，然后解码准备用作变量(名称值对)。在本例中，我使用了表单输入值来生成作为 JSON 字符串传递的数据，但是您也可以创建自己的 JSON 数据，传递的数据不必来自表单。


### jQuery / AJAX

将 JSON 数据传递给服务器端脚本的基本 AJAX 函数。

```
$.ajax({
   type: "POST",
   url: targetURL,
   async: false,
   data: JSON.stringify($('#form').serializeArray()),
   success: function(data){
      console.log(data);
      return true;
   },
   complete: function() {},
   error: function(xhr, textStatus, errorThrown) {
     console.log('ajax loading error...');
     return false;
   }
});
```

如果我们看一下生成的 JSON，它有名称值对。

**生成的 JSON 示例:**

```
data=[{"name":"product","value":"riserva shiraz wine glass"},{"name":"supid","value":"81"},{"name":"brandid","value":"60"},{"name":"blid","value":"7"},{"name":"cid","value":"381"}];
```

### PHP 可变动态

```
// decode JSON string to PHP object, 2nd param sets to associative array
$decoded = json_decode($_GET['data'],true);

output values:
foreach ($decoded as $value) {
   echo $value["name"] . "=" . $value["value"];
}

//set values:
foreach ($decoded as $value) {
    $$value["name"] = $value["value"];
}

//both:
foreach ($decoded as $value) {
    $$value["name"] = $value["value"];
    echo $value["name"] . "=" . $$value["name"];
    echo "
";
}
```

抱歉，没有演示，但请随意提问。

## 分享这篇文章