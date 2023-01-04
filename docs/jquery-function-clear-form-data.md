# jQuery 函数来清除所有表单数据

> 原文：<https://www.sitepoint.com/jquery-function-clear-form-data/>

非常有用的 jQuery 函数，可以清除我在 Karl Swedberg 的网站上找到的所有表单数据。它只是从表单中移除所有数据，包括文本输入、选择框、单选按钮、复选框等。有两个版本，第二个版本可能更有用，因为您可以将它作为 jQuery 函数直接应用于 DOM 元素。

```
function clearForm(form) {
  // iterate over all of the inputs for the form
  // element that was passed in
  $(':input', form).each(function() {
    var type = this.type;
    var tag = this.tagName.toLowerCase(); // normalize case
    // it's ok to reset the value attr of text inputs,
    // password inputs, and textareas
    if (type == 'text' || type == 'password' || tag == 'textarea')
      this.value = "";
    // checkboxes and radios need to have their checked state cleared
    // but should *not* have their 'value' changed
    else if (type == 'checkbox' || type == 'radio')
      this.checked = false;
    // select elements need to have their 'selectedIndex' property set to -1
    // (this works for both single and multiple select elements)
    else if (tag == 'select')
      this.selectedIndex = -1;
  });
};
```

## 重置输入按钮

<input type="reset">

您可以放置一个 reset 类型的隐藏输入，然后触发它来清除表单。

```
<input type="reset" style="display:none;">
```

```
$('form > input[type=reset]').trigger('click'); //with a reset button in the form set to display: none;
```

## jQuery 元素函数

```
$.fn.clearForm = function() {
  return this.each(function() {
    var type = this.type, tag = this.tagName.toLowerCase();
    if (tag == 'form')
      return $(':input',this).clearForm();
    if (type == 'text' || type == 'password' || tag == 'textarea')
      this.value = '';
    else if (type == 'checkbox' || type == 'radio')
      this.checked = false;
    else if (tag == 'select')
      this.selectedIndex = -1;
  });
};
//usage
$('#flightsSearchForm').clearForm();
```

[来源](http://www.learningjquery.com/2007/08/clearing-form-data)

## 分享这篇文章