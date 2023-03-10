# 具有相同 id 的 jQuery bind()元素

> 原文：<https://www.sitepoint.com/jquery-bind-elements-id/>

好吧，不知何故，您已经设法进入了一种情况，您需要绑定到相同 id 的元素。现在，如果可以的话，你应该给每个元素添加一个类并绑定到它！

**注意:**应该只对页面内已经动态引入的元素使用 live()函数，否则使用 bind()函数。

这个小函数查找具有重复 id 的元素。

```
(function(document, $){
    // little debug helper script to notify us when the
    // dom is updated with a contains duplicate ID'd elements
    $(document).bind('DOMNodeInserted', function (event) {

        var duplicateDomElements = [];

        $('[id]').map(function () {
            var ids = $('[id=' + this.id + ']');
            if (ids.length > 1 && ids[0] == this) {
                duplicateDomElements.push(this.id);
            }
        });

        if (duplicateDomElements.length > 0) {
            alert('Recent action duplicated a dom element ID with name(s) [' + duplicateDomElements + ']');
        }

    });
})(document, jQuery);
```

注意:使用 div#id 有时会比只使用#id 产生更慢的结果，所以要注意在你的选择器上放了多少前置标签。还有一个提示，如果两个类调用同一个函数，那么你可以像这样把选择器加在一起:

```
$('.clickButton1, .clickButton2').bind('click', function() {
	//your code
}

//instead of 

$('.clickButton1').bind('click', function() {
	//your code
}

$('.clickButton2').bind('click', function() {
	//your code
}
```

## 停止对重复元素的操作

停止对重复元素的进一步操作的修复方法是同时使用防止默认和停止传播，这将停止默认操作和对具有相同 id 的元素的任何立即操作。像这样:

```
e.preventDefault();
e.stopImmediatePropagation();
```

## 进一步的问题:将类分配给具有相同 id 的元素

。每一个都将 class 标签应用到 id 为
 `0 [object HTMLDivElement]
SSP0
0 [object HTMLDivElement]
SSP0` 的第一个元素上

如果将“div”应用于 jQuery 选择器，它似乎可以工作。

```
$('div#searchResultsContainer').each(function(index, value)
{		
console.log(index);					
    // $(this).addClass('SSP'+index);
});
```

**参见:** [jquery 绑定到已创建的元素](http://www.jquery4u.com/dom-modification/jquery-binding-created-elements/)

## 分享这篇文章