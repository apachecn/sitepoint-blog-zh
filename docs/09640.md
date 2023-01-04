# jQuery 循环选择选项

> 原文：<https://www.sitepoint.com/jquery-loop-select-options/>

简单的 jQuery 代码片段循环表单中的选择框选项(下拉框),从每个选项中获取值和文本。用于操作表单选择框中的值。

```
$('#select > option').each(function() {
    alert($(this).text() + ' ' + $(this).val());
});

$('#select  > option:selected').each(function() {
    alert($(this).text() + ' ' + $(this).val());
});
```

这个函数将为匹配给定类的选择返回一个文本/值对数组。

```
function getSelects(klass) {
    var selected = [];
    $('select.' + klass).children('option:selected').each( function() {
         var $this = $(this);
         selected.push( { text: $this.text(), value: $this.val() );
    });
    return selected;
}
```

## 分享这篇文章