# jQuery 从字符串中去除有害字符

> 原文：<https://www.sitepoint.com/jquery-strip-harmful-characters-string/>

jQuery 函数从输入字段中去除所有潜在的有害字符。例如，在使用 AJAX 之前，用于过滤服务器请求的额外安全措施。

**参见:** [10 jQuery 安全插件](http://www.jquery4u.com/security/10-jquery-security/)

```
/**
 * Strip out all potentially harmful characters from an input field
 * @param {String} str
 * @returns {String}
 */
filterInputText = function(str)
{
	try
	{
		return str.replace(/s+/gm, ' ').match(/[a-zA-Z0-9(), .!/:%@&?+_=-$]+/gm).join('');
	}
	catch(e)
	{
		return '';
	}
}
```

## 分享这篇文章