# jQuery 检查是否存在水平滚动

> 原文：<https://www.sitepoint.com/jquery-check-horizontal-scroll-present/>

jQuery 函数用于**检查水平滚动是否存在**–hasHScrollBar()–(或者下面的垂直检查，util 函数用于检查元素是否存在滚动条)。


## jQuery hasHScrollBar()函数

```
//util function to check if an element has a scrollbar present
  jQuery.fn.hasScrollBar = function(direction)
  {
    if (direction == 'vertical')
    {
      return this.get(0).scrollHeight > this.innerHeight();
    }
    else if (direction == 'horizontal')
    {
      return this.get(0).scrollWidth > this.innerWidth();
    }
    return false;

  }
  //$('#c3 .mbcontainercontent').hasScrollBar('horizontal');
```

## 类似功能:

```
//util function to check if an element has a horizontal scrollbar present
  jQuery.fn.hasHScrollBar = function()
  {
    // log(this.get(0).scrollWidth);
    // log(this.width());
    // log(this.innerWidth());
      return this.get(0).scrollWidth > this.innerWidth();
  }
  $('#c3 .mbcontainercontent').hasScrollBar();

  //util function to check if an element has a vertical scrollbar present
  jQuery.fn.hasVScrollBar = function()
  {
    // log(this.get(0).scrollHeight);
    // log(this.height());
    // log(this.innerHeight());
      return this.get(0).scrollHeight > this.innerHeight();
  }
  $('#c3 .mbcontainercontent').hasScrollBar();
```

另一个版本…

```
function hasScroll(el, direction) {
    direction = (direction === 'vertical') ? 'scrollTop' : 'scrollLeft';
    var result = !! el[direction];

    if (!result) {
        el[direction] = 1;
        result = !!el[direction];
        el[direction] = 0;
    }
    return result;
}

alert('vertical? ' + hasScroll(document.body, 'vertical'));
alert('horizontal? ' + hasScroll(document.body, 'horizontal'));
```

## 分享这篇文章