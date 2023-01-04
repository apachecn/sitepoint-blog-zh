# 输入文本选择代码片段

> 原文：<https://www.sitepoint.com/input-text-selection-code-snippets/>

只是建立一个输入选择代码片段的集合。最新版本的 Chrome 和 Firefox 使用。setSelectionRange()函数。不要忘记 Firefox 在设置范围之前需要首先关注一个元素。参见 [Input.setSelectionRange](https://developer.mozilla.org/en-US/docs/Web/API/Input.setSelectionRange) 。

**相关帖子: [HTML5 输入自动对焦](http://www.jquery4u.com/tutorials/jqueryhtml5-input-focus-cursor-positions/)**

## 获取光标位置

```
// GET CURSOR POSITION
jQuery.fn.getCursorPosition = function(){
    if(this.lengh == 0) return -1;
    return $(this).getSelectionStart();
}
```

## 设置文本选择

```
jQuery.fn.getSelectionStart = function(){
    if(this.lengh == 0) return -1;
    input = this[0];

    var pos = input.value.length;

    if (input.createTextRange) {
        var r = document.selection.createRange().duplicate();
        r.moveEnd('character', input.value.length);
        if (r.text == '')
        pos = input.value.length;
        pos = input.value.lastIndexOf(r.text);
    } else if(typeof(input.selectionStart)!="undefined")
    pos = input.selectionStart;

    return pos;
}
```

## 设置光标位置

## 

```
//SET CURSOR POSITION
jQuery.fn.setCursorPosition = function(pos) {
  this.each(function(index, elem) {
    if (elem.setSelectionRange) {
      elem.setSelectionRange(pos, pos);
    } else if (elem.createTextRange) {
      var range = elem.createTextRange();
      range.collapse(true);
      range.moveEnd('character', pos);
      range.moveStart('character', pos);
      range.select();
    }
  });
  return this;
};
```

## 设置光标位置(v2)

## 

```
//different version of SET CURSOR POSITION function above
function setCursorPos(node,pos){

    var node = (typeof node == "string" || node instanceof String) ? document.getElementById(node) : node;
    node.focus(); //crucial for firefox

    if(!node){
        return false;
    }else if(node.createTextRange){
        var textRange = node.createTextRange();
        textRange.collapse(true);
        // textRange.moveEnd(pos); //see api textRange requires 2 params
        // textRange.moveStart(pos);
        textRange.moveStart('character', pos);
        textRange.moveEnd('character', 0);
        // console.log('textRange...');
        textRange.select();
        return true;
    }else if(node.setSelectionRange){
        node.setSelectionRange(pos,pos);
        // console.log('setSelectionRange...');
        return true;
    }

    return false;
}
```

## 分享这篇文章