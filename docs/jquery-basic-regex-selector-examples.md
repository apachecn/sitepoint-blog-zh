# jQuery 正则表达式示例。匹配()

> 原文：<https://www.sitepoint.com/jquery-basic-regex-selector-examples/>

这是一些常见的 RegExp ( **[正则表达式](https://www.sitepoint.com/learn-regex/)** )选择器的参考帖子，这些选择器可以与 jQuery.match()函数一起使用。这对于在你的网页文本中找到几乎任何东西，然后用它做一些整洁的事情是非常方便的。对于表单验证也很方便。

通过以下示例了解如何使用正则表达式:

*   [基本 JAVASCRIPT 正则表达式示例](http://www.jquery4u.com/javascript/basic-javascript-regular-expression/)
*   [使用 JQUERY。EXEC()和。COMPILE() REGEX](http://www.jquery4u.com/javascript/jquery-exec-compile-regex/)

## 数字的 jQuery RegExp

```
 //select integers only
var intRegex = /[0-9 -()+]+$/;   
//match any ip address
var ipRegex = 'bd{1,3}.d{1,3}.d{1,3}.d{1,3}b';  
//match number in range 0-255
var num0to255Regex = '^([01][0-9][0-9]|2[0-4][0-9]|25[0-5])$';
//match number in range 0-999 
var num0to999Regex = '^([0-9]|[1-9][0-9]|[1-9][0-9][0-9])$';
//match ints and floats/decimals
var floatRegex = '[-+]?([0-9]*.[0-9]+|[0-9]+)'; 
//Match Any number from 1 to 50 inclusive
var number1to50Regex = /(^[1-9]{1}$|^[1-4]{1}[0-9]{1}$|^50$)/gm; 
```

## 用于验证的 jQuery 正则表达式

```
 //match email address
var emailRegex = '^[A-Z0-9._%+-]+@[A-Z0-9.-]+.[A-Z]{2,4}$'; 
//match credit card numbers
var creditCardRegex = '^(?:4[0-9]{12}(?:[0-9]{3})?|5[1-5][0-9]{14}|6(?:011|5[0-9][0-9])[0-9]{12}|3[47][0-9]{13}|3(?:0[0-5]|[68][0-9])[0-9]{11}|(?:2131|1800|35d{3})d{11})$'; 
//match username
var usernameRegex = '/^[a-z0-9_-]{3,16}$/'; 
//match password
var passwordRegex = '/^[a-z0-9_-]{6,18}$/'; 
//Match 8 to 15 character string with at least one upper case letter, one lower case letter, and one digit (useful for passwords).
var passwordStrengthRegex = /((?=.*d)(?=.*[a-z])(?=.*[A-Z]).{8,15})/gm; 
//match elements that could contain a phone number
var phoneNumber = /[0-9-()+]{3,20}/; 
```

## 日期的 jQuery RegExp

```
 //MatchDate (e.g. 21/3/2006)
var dateRegex = /(d{1,2}/d{1,2}/d{4})/gm; 
//match date in format MM/DD/YYYY
var dateMMDDYYYRegex = '^(0[1-9]|1[012])[- /.](0[1-9]|[12][0-9]|3[01])[- /.](19|20)dd$'; 
//match date in format DD/MM/YYYY
var dateDDMMYYYRegex = '^(0[1-9]|[12][0-9]|3[01])[- /.](0[1-9]|1[012])[- /.](19|20)dd$'; 
```

## URL 的 jQuery RegExp

```
 //match a url
var urlRegex = '/^(https?://)?([da-z.-]+).([a-z.]{2,6})([/w .-]*)*/?$/'; 
//match a url slug (letters/numbers/hypens)
var urlslugRegex = '/^[a-z0-9-]+$/'; 
//match a url string (Fixes spaces and querystrings)
var urlRegex = /(https?://)?([da-z.-]+).([a-z.]{2,6})([/w.-=?]*)*/?/ 
```

## 元音的 jQuery RegExp

```
 //select vowels only
var vowelRegex = /^[aeiou]/; 
```

## 空白的 jQuery RegExp

```
 //select whitespace
var whiteSpaceRegex = '^[ t]+'; 
//select whitespace and tabs
var whiteSpaceRegex = '^[ t]+|[ t]+$';  
//select whitespace and linebreaks
var whiteSpaceRegex = '[ trn]';  
//replace newline characters with 
 tags
newLineToBr = function(str) { return str.replace(/(rn|[rn])/g, '
'); } 
```

## 域名的 jQuery RegExp

```
 //match domain name (with HTTP)
var domainRegex = /(.*?)[^w{3}.]([a-zA-Z0-9]([a-zA-Z0-9-]{0,65}[a-zA-Z0-9])?.)+[a-zA-Z]{2,6}/igm; 
//match domain name (www. only) 
var domainRegex = /[^w{3}.]([a-zA-Z0-9]([a-zA-Z0-9-]{0,65}[a-zA-Z0-9])?.)+[a-zA-Z]{2,6}/igm; 
//match domain name (alternative)
var domainRegex = /(.*?).(com|net|org|info|coop|int|com.au|co.uk|org.uk|ac.uk|)/igm; 
//match sub domains: www, dev, int, stage, int.travel, stage.travel
var subDomainRegex = /(http://|https://)?(www.|dev.)?(int.|stage.)?(travel.)?(.*)+?/igm; 
```

## 图像的 jQuery RegExp

```
 //Match jpg, gif or png image	
var imageRegex = /([^s]+(?=.(jpg|gif|png)).2)/gm; 
//match all images
var imgTagsRegex = //ig;  
//match just .png images 
```

## 其他有用的 jQuery RegExp 示例

```
 //match RGB (color) string
var rgbRegex = /^rgb((d+),s*(d+),s*(d+))$/;  
//match hex (color) string
var hexRegex = '/^#?([a-f0-9]{6}|[a-f0-9]{3})$/'; 
//Match Valid hexadecimal colour code
var hexRegex = /(#?([A-Fa-f0-9]){3}(([A-Fa-f0-9]){3})?)/gm; 
//match a HTML tag (v1)
var htmlTagRegex = '/^(.*)|s+/>)$/'; 
//match HTML Tags (v2)
var htmlTagRegex = /(]+)>)/gm; 
//match /product/123456789
var productUrlRegex = '(/product/)?+[0-9]+';  
//Match Letters, numbers and hyphens
var lnhRegex = /([A-Za-z0-9-]+)/gm;  
//match all .js includes	
var jsTagsRegex = / 
```

这些模式旨在作为参考，其中许多还没有经过广泛的测试。请谨慎使用，并在使用前彻底测试。

## 分享这篇文章