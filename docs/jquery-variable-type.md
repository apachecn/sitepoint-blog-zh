# jquery 获取变量类型

> 原文：<https://www.sitepoint.com/jquery-variable-type/>

为了使用 jQuery 获得 a 变量类型，有一个 jQuery 函数被调用。type()返回“数组”、“字符串”、“数字”、“函数”、“对象”等……起初我以为它一定非常类似于 typeOf() JavaScript 函数。但是正如你在它的实现中看到的，它使用了 tostring()和 class2type()检查。有意思。


```
type: function( obj ) {
		return obj == null ?
			String( obj ) :
			class2type[ toString.call(obj) ] || "object";
	},
```

进一步了解 class2type 对象的实现。

```
var class2type = {
    "[object Array]": "array",
    "[object Boolean]": "boolean",
    "[object Date]": "date",
    "[object Function]": "function",
    "[object Number]": "number",
    "[object Object]": "object",
    "[object RegExp]": "regexp",
    "[object String]": "string"
};
```

jQuery.type()函数的使用示例。

```
var $forms = Array($('#register-form1'), $('#register-form2'), $('#register-form3'));
console.log($.type($forms));
//output: array
```

## 分享这篇文章