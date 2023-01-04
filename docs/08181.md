# jQuery 将输入转换为文本

> 原文：<https://www.sitepoint.com/jquery-convert-input-text/>

使用 jQuery 将表单输入转换成文本元素。目前，该脚本只适用于文本输入，但可以很容易地扩展到其他输入类型，如文本区，电台，复选框等…

```
$form = $('#register-form1');
$form.find(':input').each( function(i,v)
{
    var iElem = $(v),
        name = iElem.attr('name'),
        type = iElem.attr('type'),
        labelElem = iElem.parents().find('label[for="'+name+'"]'),
        labelTxt = labelElem.html(),
        iVal = iElem.val();

    if(type == 'input')
    {
    iElem.parent().prepend('

+label text+''+iVal+'

');

    }
else if (type == 'password')
{
    iVal = iVal.substr(i).replace(/[S]/g, "*");
    iElem.parent().prepend('

+label text+' '+iVal+'

');
}
    //remove old input elements
    iElem.remove();
    labelElem.remove();
});
```

## 分享这篇文章