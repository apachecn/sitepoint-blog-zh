# jQuery 中的选项卡控件可用性悬停触发器

> 原文：<https://www.sitepoint.com/tab-control-usability-hover-triggers-jquery/>

jQuery 中的选项卡控件可用性悬停触发器。已在 Chrome、FF、IE9 和 IE8 中测试和运行。

加载是标签演示工作的关键。
[js dild](https://jsfiddle.net/jquery4u/Z4w7D/)

[https://jsfiddle.net/jquery4u/Z4w7D/show/](https://jsfiddle.net/jquery4u/Z4w7D/show/)

## tabindex 属性

*   除 Safari 之外，所有主流浏览器都支持 tabindex 属性。
*   HTML 5–tabindex 属性可用于任何 HTML 元素

## jQuery

```
$(document).ready(function () {

    /* "trigger" hover by adding a CSS class */
    $('.transition-container').on('mouseover', function (e) {
        $(this).find('.transition').addClass('activeHover');
        $(this).on('mouseout', function (e) {
            $(this).find('.transition').removeClass('activeHover');
        });
    });

    /* get key code */
    function getKeyCode(key) {
        //return the key code
        return (key == null) ? event.keyCode : key.keyCode;
    }

    /* for usabiity capture keyboard tabbing */
    $(document).on('keyup', function (eventObj) {
        //tab keycode = 9
        if (getKeyCode(eventObj) == '9') {
            var $el = $(document.activeElement);
            //remove any current active elements
            $('.transition').removeClass('activeHover');
            if ($el.hasClass('transition-container')) {
                $el.find('.transition').addClass('activeHover');
            }
        }
    });

});

/* Internet Explorer sniffer code to add class to body tag for IE version */
var ie = (function () {

    var undef,
    v = 3,
        div = document.createElement('div'),
        all = div.getElementsByTagName('i');

    while (
    div.innerHTML = '',
    all[0]);

    if (v > 4) {
        $('body').addClass('ie' + v);
    }

}());
```

## 半铸钢ˌ钢性铸铁(Cast Semi-Steel)

```
/* transition speed */
 .transition {
    -webkit-transition: -webkit-transform .2s ease-out;
    -moz-transition: -moz-transform .2s ease-out;
    -o-transition: -o-transform .2s ease-out;
    -ms-transition: -ms-transform .2s ease-out;
    transition: transform .2s ease-out;
}
/* IE 9 scale */
 .ie9 .activeHover {
    -ms-transform: scale(1.2, 1.2);
}
/* IE 8 scale */
 .ie8 .activeHover {
    filter: progid:DXImageTransform.Microsoft.Matrix(M11=1.1, M12=0, M21=0, M22=1.1, SizingMethod='auto expand');
}
/* modern browsers transition */
 .activeHover {
    -webkit-transform:scale(1.2);
    -moz-transform:scale(1.2);
    -o-transform:scale(1.2);
    transform:scale(1.2);
}
/* IE 7 scale */
 .ie7 .activeHover {
    filter: none;
    transform: none;
}
.transition-container {
    border: 1px solid blue;
    margin: 15px;
    padding:5px;
}
*:active {
    border: 1px solid red;
}
}
```

## HTML5

```
 单击以获得焦点，并开始按 TAB 键在项目之间切换。按 SHIFT+TAB 返回上一页。

    ![](img/811761aaee4ed90f82b7f1be7e60588b.png)

    ![](img/559cd4517caa98261d0fc24313e7fc99.png)

    ![](img/b50cdf163ba67e157d21b6e0ee993e9d.png)

    ![](img/cd1af39573085693eebdf176e327348c.png)

    ![](img/069e8c44d6c4116b36ea9f1268b357f3.png)

    ![](img/dff474ad0dbcd2c1b0f0d3d95a538f5d.png)

    ![](img/48d96faad8b86ac778c2a522d1eb0054.png) 
```

## 分享这篇文章