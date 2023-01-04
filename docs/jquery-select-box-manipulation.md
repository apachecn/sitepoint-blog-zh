# jQuery 选择框操作

> 原文：<https://www.sitepoint.com/jquery-select-box-manipulation/>

## **简介**

在 jQuery 中操作选择框需要额外的技巧和交互，但并不复杂。这篇文章将帮助你做关于选择框的事情，没有任何问题。

## **选择框创建**

这应该是简单而直接的。

```
jQuery('#some_element').append('<select>');
更新 2011 年 1 月 6 日–选择选择框选项的 3 种不同方式。
//select select box option
jQuery('#selectboxid option').attr('selected', true);
jQuery('#selectboxid option').attr('selected', selected);
$("#selectboxid").attr('selectedIndex', indexnumber);
用 jQuery 选择框选项
下面是在选择框中添加选项的代码。只需粘贴并编辑它的一些部分，以满足您的需求。
//obj is the list of option values

function(obj)

{

var create = '</select>';

for(var i = 0; i '+obj[i]+'';

}

create += '';

jQuery('#some_element').append(create);

}
```

或者，您可以在元素列表中创建选项，并使用纯 jQuery 将其追加到列表中。

```
function(id, obj)

{

jQuery('#some_element').append('<select id="'+id+'">');

jQuery.each(obj, function(val, text) {

jQuery('#'+id).append(

jQuery('<option>');

for(var i = 0; i '+obj[i]+'Getting Value in Select Box
We might need to know what the current value of the option selected is.

[js]

//#selectbox is the id of the select box

jQuery('#selectbox option:selected').val();
[code lang="js"]

You can get the text of the option by doing this.
//#selectbox 是选择框的 id
jQuery(' # select box option:selected ')。text()；
//#selectbox 是选择框的 id
$(" # select list option[value = ' this the one ']")。text()；
//#selectbox 是选择框的 id
$("#selectList option:first ")。文本()
//#selectbox 是选择框的 id
$("#selectList option:eq(3)")。文本()
//#selectbox 是选择框的 id
$(" # select list option:not(option:first，option:last)")。each(function(){
$(这个)。text()；
});
[/抄送]

        在选择框中获取多个值
使用此代码检索多个值。
jQuery('#some_element:selected').each(function(){

alert(jQuery(this).text());

alert(jQuery(this).val());

});

var current = [];

jQuery('#some_element:selected').each(function(index, selectedObj){

current[index] = $(selectedObj).text();

});

var foo = jQuery('#multiple :selected').map(function(){return jQuery(this).val();}).get();
移除选择框中的元素
这里没什么特别的。只需粘贴并修改这段代码，这样将要删除的项目就是您选择的项目。
jQuery('#selectbox: selected').remove();

Here we will remove all elements except the first and last one.

//#selectbox is the id of the select box

$("#selectbox option:not(option:first, option:last)").remove();

        在选择框中选择选项
执行此操作以在选择框中选择一个选项。
jQuery('#selectbox option').attr('selected', 'selected');
取消选择选项
上面代码的反转
jQuery('#selectbox option').attr('selected', false);

         onChange 事件找到选中的选项
$('#selectbox').change(function(){

var val = $(this).find('option:selected').text();

alert('i selected: ' + val);

});

onchange find select box selected items.

$('#selectbox').change(function(){

$(this).find('option:selected').each(function () {

alert('i selected: ' + $(this).text());

}

});
结论
如果你正确地遵循了这些步骤，你应该能把事情做好。如你所见，没那么难！

来源:
http://hung red . com/how-to/tutorial-jquery-select-box-manipulation-plugin/

分享这篇文章</option></select> 
```