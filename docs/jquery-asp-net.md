# jQuery 和 ASP.NET

> 原文：<https://www.sitepoint.com/jquery-asp-net/>

## 介绍

在 ASP.NET 使用 jQuery 并不复杂。只要遵循这些步骤，你应该达到我们的主要目标。使用下面的 HTML 代码来包含 jQuery。

C#服务器代码:

```
protected override void Render(HtmlTextWriter writer)
{
    this.Page.ClientScript.RegisterClientScriptInclude("jQuery",
        "https://ajax.googleapis.com/ajax/libs/jquery/1.2.6/jquery.min.js");
    this.Page.ClientScript.RegisterStartupScript(this.Page.GetType(),
        "startup", "");
    base.Render(writer);
}
```

现在您已经了解了如何将 jQuery 放入 ASP.NET 页面并在页面加载时运行脚本。我们将看到如何使用 jQuery 动态地将数据传递给服务器并从服务器获得响应。

## jQuery 选择器

这是 jQuery 中最有用的特性。这有助于在 HTML 文档中选择元素。jQuey 有很多选择器选项，你可以通过 ID、tage 名称、class 名称以及特定的属性来选择一个元素或元素数组。
有用的选择器示例:

```
$("#selectDiv") //will return an array containing element which has id = selectDiv

$("div") //will return an array containing all "div" elements.

$(".header") //will return an array containing elements which has class name = header

$("input[name='emailaddress']") //will return an array containing "input" elements which has name = emailaddress
```

## jQuery 可链接性

在 jQuery 中，让代码简短是一个很好的概念。在每个 jQuery 方法中，它返回查询对象本身，所以它允许在其上执行函数并维护一个链。
举例:

```
$("div").addClass("sample").html("html changed").show();
```

## jQuery 对象访问器

这可以用在任何 jQuery 选择器返回的对象上。
举例:

```
$(". removeItem").each{function()
{
    $(this).bind("click",
        return confirm("Are you sure you wish to delete this item?"));
}
```

## jQuery 事件

示例:

```
$("p").bind("click", function()
{
  $(this).append("clicked");
});
function clickMe()
{
    $("#debugDiv").append("clicked!");
}

$("p").bind("click",clickMe); // Will bind clickMe function on click event of paragraphs

$("p").unbind("click",clickMe); // Will unbind clickMe function on click event of paragraphs
$(document).ready(function()
{
   $("p").append("This text appended on DOM ready");
});
```

## 使用 jQuery 和 ASP.NET 的 Ajax

带有 jQuery 的 Ajax 并不难实现。使用 jQuery Ajax 从服务器获得异步回复有一些选项。
“load”方法是 jQuery Ajax 最简单的形式。
举例:

```
$("#serverResponse").load("AjaxCall.aspx"); // Response from page AjaxCall.aspx will get loaded in
// Div "serverResponse" Send data to the server using jQuery Ajax Example:

$.get("AjaxCall.aspx" // Call page AjaxCall.aspx
,{name: $("#txtName").val()} //Pass querystring "name" with value of text box "txtName"
,function(data) // On successfully retrival of response
{
      $("#serverResponseSendData").html(data); // Apply recevied html response to html of div "serverResponseSendData"
});
```

## 带有 JSON 数据的 jQuery Ajax

Javascript 对象符号或 JSON 是在 Ajax 调用中传输数据的一种很好的形式。

```
public static string datatableToJSON(DataTable dt)
{
    StringBuilder jsonStringBuilder = new StringBuilder();
    StringWriter jsonStringWriter = new StringWriter(jsonStringBuilder);

    JsonWriter jsonWriter = new JsonTextWriter(jsonStringWriter);

    if (dt != null && dt.Rows.Count > 0)
    {
        jsonWriter.Formatting = Formatting.None;

        jsonWriter.WriteStartArray();
        for (int i = 0; i IndexItem CodePrice";

        for(var i=0;i<dtitems .length="" traverse="" through="" items="" in="" this="" object="" var="" rowclass="(i%2" :="" htmlgrid="" class="">"+dtItems[i].index+""+dtItems[i].itemcode+""+dtItems[i].price+"";
            // Build grid from retrived data in current item
        }

        htmlGrid += "";

        $("#divJSONGrid").html(htmlGrid); // apply created grid HTML to a div "divJSONGrid"
    }
    });</dtitems>
```

## jQuery 的效果

jQuery 有它自己的特效，比如显示、隐藏、滑动等等。效果方法有持续时间和回调函数的参数。这将在动画效果完成后被调用。
举例:

```
$('#divSample').hide(1000); // will hide the div "divSample", and it will animate for 1000 miliseconds

$('#divSample').show(1000); // will show the div "divSample"

$('#divSample').toggle (1000); // will toggle display of the div "divSample"
$("#divSample3").animate( // will animate the div "divSample"
   // to height 200px, width 400px and opacity of .5, for 1000 milisecond
{height:200, width:400, opacity: .5}, 1000, function()
{
   alert("Animation complete!"); // call method on completion of animation
});
```

## 分享这篇文章