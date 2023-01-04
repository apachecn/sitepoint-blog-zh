# jQuery XML 组合框

> 原文：<https://www.sitepoint.com/jquery-xml-combo-box/>

这是一种使用 jquery 和 xml 填充组合框(下拉)的简单方法，该组合框可用于根据 xml 配置数据文件(包含选项)将动态选项填充到组合框中。这种方法对于选项不断变化的表单很有用。

这是你怎么做的。

## HTML 代码

```
 <form>
 <select id="mySelect"><option>loading</option></select> 
</form> 
```

## jQuery 代码

```
$(document).ready(function(){
			$.ajax({
				type: "GET",
				url: "dropdown.xml",
				dataType: "xml",
				success: function(xml) {
					var select = $('#mySelect');
					$(xml).find('menuitem').each(function(){
						var title = $(this).find('title').text();
						select.append(""+title+"");
						$(this).find('value').each(function(){
							var value = $(this).text();
							select.append(""+value+"");
						});
					});
					select.children(":first").text("please make a selection").attr("selected",true);
				}
			});
		});
```

## XML 代码

```
 <dropdown><title>first set</title>
 		 <values><value>option a</value>
 			<value>option b</value></values> 

    	<title>second set</title>
 		 <values><value>option 1</value>
 			<value>option 2</value></values> 

    	<title>third set</title>
 		 <values><value>option 1a</value>
 			<value>option 2b</value></values></dropdown> 
```

[下载源文件](https://www.sitepoint.com/wp-content/uploads/jquery4u/2011/03/jquery-dropdown-xml.zip)

## 分享这篇文章