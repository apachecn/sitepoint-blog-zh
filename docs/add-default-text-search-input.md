# jQuery 向搜索输入框添加默认文本

> 原文：<https://www.sitepoint.com/add-default-text-search-input/>

使用 jQuery 向搜索框添加默认文本。**简单却有效！**

1.  当您单击搜索框时，文本会消失
2.  当框为空时，文本显示为默认值
3.  当您将鼠标悬停在该框上时，该框会显示阴影并以白色突出显示

![jquery-default-search-box-text](img/d51fc88129be621c906064945b678315.png "jquery-default-search-box-text")

[见现场演示](http://blogoola.com/)

**JavaScript 中包含以下内容:**

```
$('#search').blur(function(){
	if (this.value == '') {
		this.value = 'Search BLOGOOLA';
	}
})
```

**HTML 中包含以下内容:**

```
 <form method="get" id="searchform" action="http://blogoola.com/index.php">

<fieldset class="search">
      <input type="hidden" id="page" name="page" value="find-blogs">
      <input type="text" class="searchbox" id="search" name="search" value="Search BLOGOOLA" onclick="javascript: make_blank('search')">
</fieldset>

    </form> 
```

**CSS 中包含以下内容:**

```
#searchform { opacity:0.8 }
#searchform:hover { opacity:1.0 }
#searchform fieldset { border:0px; padding:0px; margin:0px; }
#searchform input { width:190px; height:16px; margin:0px 0px 0px 10px; padding:2px 5px 2px 5px; border-width:1px 1px 1px 1px; border-style:solid solid ridge solid; font-family:Arial, Helvetica, sans-serif; font-size:small; }
#searchform button{ float:right; width:30px; height:22px;  margin:0px 0px 0px 0px; padding:0px 0px 0px 0px; border-width:1px 1px 0px 1px; border-style:solid solid ridge solid; background-repeat:no-repeat; background-image:url('img/search.png'); }
#searchform button:hover { cursor:pointer; background-color:#E2E2E2; }
```

**这是表单按钮的图像:**
![search-icon](img/d9664c04b7677dd6ff40c38992158585.png "search-icon")

## 分享这篇文章