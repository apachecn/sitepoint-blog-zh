# jQuery 漂亮的馅饼点击悬停菜单

> 原文：<https://www.sitepoint.com/jquery-pretty-pie-click-hover-menu/>

![jquery-piehover-menu](img/044a23b2cd4e31fc947446f943b59cbf.png "jquery-piehover-menu")

嗨，伙计们，我偶然发现了这个非常酷的悬停菜单，它可以围绕一个元素旋转，当你在它周围移动鼠标时，菜单项会自动被选中。它还有一个很酷的功能，当你点击鼠标右键时，它就会出现，这对于一些应用程序来说很有用。

需求: jQueryUI，Firefox/Chrome——可能是 IE 的问题，但以后的版本似乎没问题。


```
//example pie hover menu
$(function() {

    $("#area").prettypiemenu({
        buttons: [
            { img: "ui-icon-minus", title: "plaah1" }, 
            { img: "ui-icon-plus",  title: "plaah2" },
            { img: "ui-icon-close", title: "plaah3" }
        ],
        onSelection: function(item) {
          alert (item + ' was clicked!'); 
        },
        showTitles: false
    });

    $("#test").prettypiemenu({
        buttons: [
            { img: "ui-icon-minus", title: "plaah1" }, 
            { img: "ui-icon-plus",  title: "plaah2" },
            { img: "ui-icon-close", title: "plaah3" },
            { img: "ui-icon-check", title: "plaah4" }
        ],
        onSelection: function(item) {
          alert (item + ' was clickedoo!'); 
        },
        closeRadius: 25,
        showTitles: true
    });      

    $( "#testbtn" ).button({ icons: {primary:"ui-icon-gear"}, text: false })
    .click(function(event) {
        event.preventDefault();
        var offset = $( "#testbtn" ).offset();
        var h = $( "#testbtn" ).height();
        var w = $( "#testbtn" ).width();
        var btn_middle_y = offset.top + h/2;
        var btn_middle_x = offset.left + w/2;
        $("#test").prettypiemenu("show", {top: btn_middle_y, left: btn_middle_x});
        return false;
    }); 
});
```

[试玩](http://www.cs.tut.fi/~laine9/demo/ppmenu/piedemo.html)
[下载页面](http://www.cs.tut.fi/~laine9/demo/ppmenu/dl.html)

## 分享这篇文章