# jQuery Set Get Delete Cookies 示例

> 原文：<https://www.sitepoint.com/jquery-set-delete-cookies/>

jQuery 可以用来操作浏览器 cookie，这个小演示向你展示了如何使用 Klaus Hartl 在 GLP 许可下开发的 jQuery 插件来创建、访问和删除 cookie。

```
<html>
<head>
  <title>jquery cookie

  <script type="text/javascript" src="jquery-1.2.1.min.js">
  <script type="text/javascript" src="jquery.cookie.js">

  <script type="text/javascript">

    $(function($) {

      function displayMessage(msg) {
        $('#message').html(msg).css({color: 'green'});
      }

      displayMessage('jQuery cookie plugin test');

      $('#setSessionCookie').click(function() { 
        $.cookie('test', 'Hmmm, cookie');
        displayMessage("Cookie 'test' has been set.");
      });

      $('#setCookie').click(function() {
        $.cookie('test', 'Hmmm, cookie', { expires: 7 });
        displayMessage("Cookie 'test' has been set and will expire in 7 days.");
      });

      $('#getCookie').click(function() {
        displayMessage("The value of the cookie named 'test' is: " + $.cookie('test'));
      });

      $('#deleteCookie').click(function() {
        $.cookie('test', null);
        displayMessage("Cookie 'test' has been deleted.");
      });

      $('#testCookiesEnabled').click(function() {
        $.cookie('testcookiesenabled', null);
        $.cookie('testcookiesenabled', 'enabled');
        if ($.cookie('testcookiesenabled')) {
          displayMessage("Cookie: "+ $.cookie('testcookiesenabled'));
        } else {
          displayMessage("Cookies disabled");
          $.cookie('testcookiesenabled', null);
        }
      });

    });

  </script>

</head>

<body>

<p><span id="message" style="forecolor: red;">

</p><p><input type="button" id="testCookiesEnabled" value="Cookies enabled?"/>

<p><input type="button" id="setSessionCookie" value="Set session cookie"/&</p>
<p><input type="button" id="setCookie" value="Set cookie expires in  7 days"/></p>

<p><input type="button" id="getCookie" value="Show cookie value"/></p>

<p><input type="button" id="deleteCookie" value="Delete the cookie"/></p>

</body>
</html>
```

## jquery.cookie.js

 `/**
* Cookie 插件
*
*版权所有(c)2006 Klaus Hartl(stilbuero . de)
*在 MIT 和 GPL 许可下双重授权:【http://www.opensource.org/licenses/mit-license.php】* T4
* http://www.gnu.org/licenses/gpl.html
*
*/

/**
*用给定的名称和值以及其他可选参数创建一个 cookie。
*
* @例$。cookie('the_cookie '，' the _ value ')；
* @desc 设置了一个 cookie 的值。
* @例$。cookie('the_cookie '，' the_value '，{ expires: 7，path: '/'，domain: 'jquery.com '，secure:true })；* @desc 创建一个包含所有可用选项的 cookie。
* @example $。cookie('the_cookie '，' the _ value ')；* @desc 创建一个会话 cookie。
* @example $。cookie('the_cookie '，null)；* @desc 通过传递 null 作为值来删除 cookie。请记住，您必须使用设置 cookie 时使用的相同路径和域
*。
*
* @ param String name cookie 的名称。
* @ param String value cookie 的值。
* @param Object options 包含键/值对的对象文字，用于提供可选的 cookie 属性。
* @option Number|Date expires 指定从现在起的截止日期(以天为单位)的整数或 Date 对象。
*如果指定了负值(例如过去的日期)，cookie 将被删除。
*如果设置为空或省略，cookie 将是会话 cookie，并且在浏览器退出时不会被保留
*。
* @ option String path cookie 的 path 属性值(默认:创建 cookie 的页面的路径)。
* @ option String domain cookie 的 domain 属性的值(默认:创建 cookie 的页面的域)。
* @选项布尔安全如果为真，将设置 cookie 的安全属性，cookie 传输将
*需要安全协议(如 HTTPS)。
* @ type undefined
*
* @ name $。Cookie
* @ cat Plugins/Cookie
* @作者 Klaus Hartl/Klaus . Hartl @ stil buero . de
*/

/**
*获取具有给定名称的 cookie 的值。
*
* @例$。cookie(' the _ cookie ')；
* @desc 获得一块饼干的价值。
*
* @ param String name cookie 的名称。
* @返回 cookie 的值。
* @ type String
*
* @ name $。Cookie
* @ cat Plugins/Cookie
* @ author Klaus Hartl/Klaus . Hartl @ stilbuero . de
*/
jquery . Cookie = function(name，value，options) {
if (typeof value！= 'undefined') { //给定名称和值，set cookie
options = options | | { }；
if(value = = = null){
value = "；
options . expires =-1；
}
var expires = "；
if(options . expires&(type of options . expires = = ' number ' | | options . expires . toutcstring)){
var date；
if(type of options . expires = = ' number '){
Date = new Date()；
date . settime(date . gettime()+(options . expires * 24 * 60 * 60 * 1000))；
} else {
date = options . expires；
}
expires = '；expires = '+date . toutcstring()；//使用 expires 属性，IE
}
不支持 max-age 注意:需要在下面的表达式中用圆括号将 options.path 和 options.domain
//括起来，否则由于某种原因在打包版本中计算为 undefined
//…
var path = options . path？；path = '+(options . path):"；
var domain = options.domain？；domain = '+(options . domain):"；
var secure = options.secure？；安全“:”；
document . cookie =[名称，' = '，encodeURIComponent(值)，过期，路径，域，安全]。join(")；
} else { //只给定名称，get cookie
var cookieValue = null；
if(document . cookie&document . cookie！= " {
var cookies = document . cookie . split('；');
for(var I = 0；我` 

## `分享这篇文章`