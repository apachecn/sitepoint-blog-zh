# moz-border-radius (CSS 属性)

> 原文：<https://www.sitepoint.com/moz-border-radius-css-property/>

Gecko 相当于 CSS3 的边界半径属性，尽管在一些方面有所不同。速记属性允许 web 开发人员指定圆形边框，或者在没有定义边框的情况下指定圆形背景。

[在壁虎 2.0 中`moz-border-radius`更名为`border-radius`](https://developer.mozilla.org/en-US/docs/Web/CSS/border-radius)；-moz-border-radius 直到 Gecko 12.0 才被支持作为别名。

为了符合 CSS3 标准，Gecko 2.0

*   更改了对<percentage>值的处理，以符合规格。你可以在任意大小的元素上指定一个椭圆作为边框，边框半径:50%；</percentage>
*   made rounded corners clip content and images (if overflow: visible is not set)

    注意:Gecko 13.0(Firefox 13.0/Thunderbird 13.0/SeaMonkey 2.10)中移除了对前缀版本(-moz-border-radius)的支持。

    ## 边框半径

    `border-radius`属性是一个简写属性，最多可以接受四个值。这些值(按顺序)代表`top-left`、`top-right`、`bottom-right`和`bottom-left`角。与任何使用单位值的简写属性一样，任何省略的值都是从现有值继承的。

    您可以使用手写语法显式定位元素的各个角。例如:

    ```
    .media img {
      border-top-left-radius: 20%;
      border-top-right-radius: 20%;
      border-bottom-right-radius: 20%;
      border-bottom-left-radius: 20%;
    }
    ```

    您可以清楚地看到为什么在大多数情况下，您将使用速记语法。即使你想瞄准一个角落，这样做也更有效:

    ```
    .media img {
      border-radius: 20% 0 0 0;
    }
    ```

## 分享这篇文章