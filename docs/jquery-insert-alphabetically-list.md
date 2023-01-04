# jQuery 按字母顺序插入列表

> 原文：<https://www.sitepoint.com/jquery-insert-alphabetically-list/>

该函数将按字母顺序插入项目(假设它包含字母)。最初设计是为了实现一个平滑的单页加载/保存功能，可以按字母顺序对下拉列表中的所有内容进行排序。可以简单地修改以在任何类型的布局中工作(对于这个例子来说，有序列表更简单)。

```
function insert(){
    var name = $("input[name='insertvalue']").val();
    if(name!=''){
        var toinsert = true;
        $("ol.thelist > li").each(function(){
            var item = $(this).html();
            if(name.toUpperCase() < item.toUpperCase()){
                if(toinsert){
                    $(this).before('*   +name+' ')；toinsert = false} } });if(toinsert){ $("ol.thelist ")。追加('*   +姓名+'');
        }
        $("input[name='insertvalue']").val('')
    }
}
```

## 分享这篇文章