# 使 Adobe Edge HTML5 导出响应迅速

> 原文：<https://www.sitepoint.com/making-adobe-edge-html5-exports-responsive/>

一些代码片段使 Adobe Edge HTML5 动画导出响应迅速。下面的代码计算出浏览器宽度(mobile device inc)的百分比比例(响应宽度),并使用 CSS3 来缩放导出，以便调整大小以匹配容器。然后，可以将它放入 iframe(整体)中，也可以将其分离出来并包含在包装器 div 中。

1.将包装 HTML 添加到舞台。
2。包括 responsive.js 和 reponsive.css 文件。
3。更改以下导出尺寸的原始尺寸。
4。如果已经加载了 jquery (_edge.js)，可以选择注释掉它。

## jQuery

```
(function($,W)
{
    $(function()
    {
        //responsive scaling for stage for HTML5
        function repositionScale()
        {
            console.log('rescaling...');
            //Original image dimensions:
            var imageH = 1136,
                imageW = 640;

            //work out the percentage scale (responsive by width)
            var scale = (W.innerWidth/imageW);
            console.log(scale)
            $('#stage-container').scale(scale);

        }
        repositionScale();

        //respond on window resize
        $(W).bind('resize', function(e)
        {
            //could use a debounce here instead of setTimeout
            if(W.RT) clearTimeout(W.RT);
            W.RT = setTimeout(function()
            {
                //recalculate the vertical position of the main nav
                repositionScale();
            }, 300);
        });
    });

})(jQuery, window);

/*!
/**
* Monkey patch jQuery 1.3.1+ to add support for setting or animating CSS
* scale and rotation independently.
* https://github.com/zachstronaut/jquery-animate-css-rotate-scale
* Released under dual MIT/GPL license just like jQuery.
* 2009-2012 Zachary Johnson www.zachstronaut.com
*/
(function ($) {
    // Updated 2010.11.06
    // Updated 2012.10.13 - Firefox 16 transform style returns a matrix rather than a string of transform functions.  This broke the features of this jQuery patch in Firefox 16\.  It should be possible to parse the matrix for both scale and rotate (especially when scale is the same for both the X and Y axis), however the matrix does have disadvantages such as using its own units and also 45deg being indistinguishable from 45+360deg.  To get around these issues, this patch tracks internally the scale, rotation, and rotation units for any elements that are .scale()'ed, .rotate()'ed, or animated.  The major consequences of this are that 1\. the scaled/rotated element will blow away any other transform rules applied to the same element (such as skew or translate), and 2\. the scaled/rotated element is unaware of any preset scale or rotation initally set by page CSS rules.  You will have to explicitly set the starting scale/rotation value.

    function initData($el) {
        var _ARS_data = $el.data('_ARS_data');
        if (!_ARS_data) {
            _ARS_data = {
                rotateUnits: 'deg',
                scale: 1,
                rotate: 0
            };

            $el.data('_ARS_data', _ARS_data);
        }

        return _ARS_data;
    }

    function setTransform($el, data) {
        $el.css('transform', 'rotate(' + data.rotate + data.rotateUnits + ') scale(' + data.scale + ',' + data.scale + ')');
    }

    $.fn.rotate = function (val) {
        var $self = $(this), m, data = initData($self);

        if (typeof val == 'undefined') {
            return data.rotate + data.rotateUnits;
        }

        m = val.toString().match(/^(-?d+(.d+)?)(.+)?$/);
        if (m) {
            if (m[3]) {
                data.rotateUnits = m[3];
            }

            data.rotate = m[1];

            setTransform($self, data);
        }

        return this;
    };

    // Note that scale is unitless.
    $.fn.scale = function (val) {
        var $self = $(this), data = initData($self);

        if (typeof val == 'undefined') {
            return data.scale;
        }

        data.scale = val;

        setTransform($self, data);

        return this;
    };

    // fx.cur() must be monkey patched because otherwise it would always
    // return 0 for current rotate and scale values
    var curProxied = $.fx.prototype.cur;
    $.fx.prototype.cur = function () {
        if (this.prop == 'rotate') {
            return parseFloat($(this.elem).rotate());

        } else if (this.prop == 'scale') {
            return parseFloat($(this.elem).scale());
        }

        return curProxied.apply(this, arguments);
    };

    $.fx.step.rotate = function (fx) {
        var data = initData($(fx.elem));
        $(fx.elem).rotate(fx.now + data.rotateUnits);
    };

    $.fx.step.scale = function (fx) {
        $(fx.elem).scale(fx.now);
    };

    /*

    Starting on line 3905 of jquery-1.3.2.js we have this code:

    // We need to compute starting value
    if ( unit != "px" ) {
        self.style[ name ] = (end || 1) + unit;
        start = ((end || 1) / e.cur(true)) * start;
        self.style[ name ] = start + unit;
    }

    This creates a problem where we cannot give units to our custom animation
    because if we do then this code will execute and because self.style[name]
    does not exist where name is our custom animation's name then e.cur(true)
    will likely return zero and create a divide by zero bug which will set
    start to NaN.

    The following monkey patch for animate() gets around this by storing the
    units used in the rotation definition and then stripping the units off.

    */

    var animateProxied = $.fn.animate;
    $.fn.animate = function (prop) {
        if (typeof prop['rotate'] != 'undefined') {
            var $self, data, m = prop['rotate'].toString().match(/^(([+-]=)?(-?d+(.d+)?))(.+)?$/);
            if (m && m[5]) {
                $self = $(this);
                data = initData($self);
                data.rotateUnits = m[5];
            }

            prop['rotate'] = m[1];
        }

        return animateProxied.apply(this, arguments);
    };
})(jQuery);
```

## CSS3

```
/* wraps the stage */
#stage-wrapper {
    position: relative;
    text-align: center;
    margin: 0 auto;
    height: 100%;
    width: 100%;
}

/* container is scaled */
#stage-container {
    transform-origin: 0 0;
    -ms-transform-origin: 0 0;
    -webkit-transform-origin: 0 0;
    -moz-transform-origin: 0 0;
}
```

## 超文本标记语言

在舞台周围加一层包装纸。使用您自己的 adobe edge 导出 id。

```

## 分享这篇文章

```