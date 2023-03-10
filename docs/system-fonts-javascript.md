# 使用 JavaScript 获取系统字体

> 原文：<https://www.sitepoint.com/system-fonts-javascript/>

使用 JavaScript 获得系统字体的几种方法。下面有带演示的参考链接。

## 使用 JavaScript 获取系统字体

```
try {
    //adds a swf to the page and reads fonts
    $('body').append('`');
    $('#flashcontent').flash(
    {
        "src": "resources/FontList.swf",
        "width": "1",
        "height": "1",
        "swliveconnect": "true",
        "id": "flashfontshelper",
        "name": "flashfontshelper"
    },
    { update: false });

    //timeout required for swf to settle.
    setTimeout(function()
    {
        console.log(window.fonts);
        return window.fonts;
    }, _this.settings.flashDelay);
}
catch(ex) {
    _this.errors.push('No fonts detected.');
    return fonts;
}`

[来源](http://hasseg.org/blog/post/526/) [演示](http://hasseg.org/stuff/fontList/example.html)

## 另一途径

```
try {
    //adds a swf to the page and reads fonts
    $('body').append('`').flash(
    {
        "src": "resources/fonts.swf",
        "width": "1",
        "height": "1",
        "swliveconnect": "true",
        "id": "flashfontshelper",
        "name": "flashfontshelper"
    },
    { update: false });

    //timeout required for swf to settle.
    setTimeout(function()
    {
        var fonts = "",
            obj = document.getElementById("flashfontshelper");

        //get fonts
        if (obj && typeof(obj.GetVariable) != "undefined") {
          fonts = obj.GetVariable("/:user_fonts").replace(/,/g,", ");
        }
        if (!fonts) {
          fonts = "No Flash or Java fonts detected";
          _this.errors.push('No Flash or Java fonts detected.');
        }
        console.log(fonts);
        return fonts;
    }, 100);
    return true;
}
catch(ex) {
    fonts = this.errors.push('No Flash or Java fonts detected.');
}`

错误:
未捕获的类型错误:对象[object Object]的属性' fontList '不是函数

[演示](https://panopticlick.eff.org/index.php?action=log&js=yes)

## 分享这篇文章

```

```