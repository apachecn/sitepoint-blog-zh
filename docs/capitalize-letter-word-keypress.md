# 将按键上每个单词的首字母大写

> 原文：<https://www.sitepoint.com/capitalize-letter-word-keypress/>

将字符串中每个单词的第一个字母大写的 jQuery 代码片段。这可用于防止用户在表单中输入数据时，对标题或文本输入全部大写字母。

 `//用法
$(“输入”)。keyup(function(){
toUpper(this)；
})；

//function
function to upper(obj){
var mystring = obj . value；
var sp = mystring . split(')；
var wl = 0；
var f，r；
var word = new Array()；
for(I = 0；I<sp . length；i++){ f = sp[I]。子字符串(0，1)。toupper case()；r = sp[i]。子字符串(1)。toLowerCase()；word[I]= f+r；} new string = word . join(')；obj.value = newstring 返回 true} [/js]` 

## `分享这篇文章`