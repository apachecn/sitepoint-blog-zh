# 跳过重复的 osapi 方法定义 friendconnect.js

> 原文：<https://www.sitepoint.com/skipping-duplicate-osapi-method-definition-friendconnect-js/>

当我包含 Google Friend Connect 时，出现了这个 JavaScript 错误信息。这是非常令人讨厌的，所以我决定尝试和修复它。

这是完整的错误信息:

 `Skipping duplicate osapi method definition chili.people.list on transport Googleapis; others may exist, but suppressing warnings
friendconnect.js (line 121)
Skipping duplicate osapi method definition pos.plusones.list on transport Googleapis; others may exist, but suppressing warnings
friendconnect.js (line 121)
Skipping duplicate osapi method definition chili.entities.starred.insert on transport Googleapis; others may exist, but suppressing warnings
friendconnect.js (line 121)
Skipping duplicate osapi method definition chili.entities.get on transport Googleapis; others may exist, but suppressing warnings
friendconnect.js (line 121)
Skipping duplicate osapi method definition chili.groups.list on transport Googleapis; others may exist, but suppressing warnings
friendconnect.js (line 121)
Skipping duplicate osapi method definition chili.activities.list on transport Googleapis; others may exist, but suppressing warnings
friendconnect.js (line 121)
Skipping duplicate osapi method definition chili.entitiesDefaultAcl.get on transport Googleapis; others may exist, but suppressing warnings
friendconnect.js (line 121)
Skipping duplicate osapi method definition Googleapis.newHttpRequest on transport Googleapis; others may exist, but suppressing warnings` 

如果我们检查 friendconnect.js 的第 121 行:

```
}if(j===a&&g.warn){g.warn(i)
```

第 121 行位于该函数内部(完整的 Google connect 脚本从这里加载: [https://www。Google.com/friendconnect/script/friendconnect.js](https://www.Google.com/friendconnect/script/friendconnect.js)):

```
function b(j,i){
	if(jSolution

I google'd it and someone said to "Clear your browser cache, and try again". This didn't work for me and I highly suspect it's a bug in google's friend connect code.

Include only the following code from the full google friend connect code supplied by the google website to insert.

//PASTE BEFORE  TAG (IN WORDPRESS HEADER.PHP CODE)
<!-- Include the Google Friend Connect javascript library. -->
<script type="text/javascript" src="https://www.google.com/friendconnect/script/friendconnect.js"></script>

//PASTE INTO THE WIDGET AREA (IN WORDPRESS A TEXT WIDGET)
<!-- Define the div tag where the gadget will be inserted. -->
<div id="div-6554152229350516721" style="width:210px;border:1px solid #cccccc;"></div>

//PASTE BEFORE  TAG (IN WORDPRESS FOOTER.PHP CODE)
<script>
window.JSON = {
parse: function(st){
		return st.evalJSON();
	},
stringify: function(obj){
		return Object.toJSON(obj);
	}
};
</script>
```

```
<!-- Render the gadget into a div. -->
<script type="text/javascript">
var skin = {};
skin['BORDER_COLOR'] = '#cccccc';
skin['ENDCAP_BG_COLOR'] = '#ff9900';
skin['ENDCAP_TEXT_COLOR'] = '#333333';
skin['ENDCAP_LINK_COLOR'] = '#0000cc';
skin['ALTERNATE_BG_COLOR'] = '#ffffff';
skin['CONTENT_BG_COLOR'] = '#ffffff';
skin['CONTENT_LINK_COLOR'] = '#0000cc';
skin['CONTENT_TEXT_COLOR'] = '#333333';
skin['CONTENT_SECONDARY_LINK_COLOR'] = '#7777cc';
skin['CONTENT_SECONDARY_TEXT_COLOR'] = '#666666';
skin['CONTENT_HEADLINE_COLOR'] = '#333333';
skin['NUMBER_ROWS'] = '5';
google.friendconnect.container.setParentUrl('/' /* location of rpc_relay.html and canvas.html */);
google.friendconnect.container.renderMembersGadget(
 { id: 'div-6554152229350516721',
   site: '15032953902720272541' },
  skin);
</script>
```

**这也可以修复类似的错误:**
不安全的 JavaScript 试图访问 URL 为 https://clients6.google.com/static/proxy.html 的框架
未捕获类型错误:无法调用未定义的方法‘tick dl’

## 分享这篇文章