# 使用自动完成文本框让生活变得简单

> 原文：<https://www.sitepoint.com/life-autocomplete-textboxes/>

让我们面对现实吧:人们真的不喜欢填写表格，尤其是需要输入数值的时候。这就是为什么像 Microsoft Outlook 这样的应用程序包含自动完成文本框——检查用户键入的前几个字符，并从给定列表中建议一个单词的文本框。当您开始在地址栏中键入网址时，Web 浏览器会自动显示 URL 列表，这也是一种工作方式。

在本教程中，通过一点 JavaScript 技巧，我们将在 Internet Explorer (v5.5 及更高版本)和 Mozilla (v1.0 及更高版本)中创建相同类型的行为。

##### 简单浏览器检测

首先，我们需要做一些浏览器检测，这里有一些简单的代码来完成这项工作(当然，您也可以使用自己的代码):

```
var isOpera = navigator.userAgent.indexOf("Opera") > -1; 

var isIE = navigator.userAgent.indexOf("MSIE") > 1 && !isOpera; 

var isMoz = navigator.userAgent.indexOf("Mozilla/5.") == 0 && !isOpera;
```

这段代码显然不是很健壮，但是对于我们的目的来说已经足够了；让我们来看看这个项目的实质。

##### 选择文本框

这个过程的第一步是创建一个在文本框中选择一定数量文本的方法。我们称这个方法为`textboxSelect()`，它需要三个参数。第一个参数是我们希望方法作用的文本框；第二个参数是可选的，是选择应该开始的位置(如果省略该参数，则选择整个文本框)；第三个参数也是可选的，是选择应该结束的位置。如果提供了第二个参数，但没有第三个参数，则从文本框中文本的开始位置到结尾选择文本框。

我们将首先处理最简单的情况:如果只提供了一个参数，那么我们应该使用 textbox 的本机`select()`方法来选择 textbox 中的所有文本:

```
function textboxSelect(oTextbox, iStart, iEnd) { 

    switch(arguments.length) { 

        case 1: 

            oTextbox.select(); 

            break; 

        ... 

    } 

}
```

注意，我们使用 switch 语句来确定填充了多少个参数。如果只有 1，这意味着只提供了`oTextbox`。接下来，我们将跳到有三个参数的情况(指定了`iStart`和`iEnd`)。在这里，我们需要使用浏览器检测来确定要做什么。对于 Internet Explorer，我们将使用文本范围。

```
function textboxSelect (oTextbox, iStart, iEnd) { 

    switch(arguments.length) { 

        case 1: 

            oTextbox.select(); 

            break; 

       ** case 3: 

            if (isIE) { 

                var oRange = oTextbox.createTextRange(); 

                oRange.moveStart("character", iStart); 

                oRange.moveEnd("character", -oTextbox.value.length + iEnd);      

                oRange.select();                                              

            } else if (isMoz) { 

                ... 

            }                     

    } 

}**
```

在粗体代码中，我们首先为 textbox 对象创建一个文本范围。然后，必须将范围设置为其起始和结束坐标。为了移动范围的开始，我们使用了`moveStart()`方法。这个方法有两个参数:要移动的空格的类型(“字符”)，以及要移动多少个空格。下一行的`moveEnd()`方法具有相同的参数。这里的区别是`moveEnd()`要求第二个参数是一个负数(把它想象成把选择的结尾向后移动一格，向后移动两格，等等。).

为了得到这个参数，我们取文本框中文本长度的负值，并加上 iEnd 值。因此，如果`iEnd`是 8，并且文本框包含 10 个字符，则第二个参数变成-2，并且范围的末尾向后移动两个字符。最后，我们调用`select()`方法来突出显示文本框中的范围。

对 Mozilla 来说，完成同样的事情其实很容易。文本框有一个接受两个参数的`setSelectionRange()`方法:选择的开始和结束。我们可以直接传入`iStart`和`iEnd`:

```
function textboxSelect (oTextbox, iStart, iEnd) { 

    switch(arguments.length) { 

        case 1: 

            oTextbox.select(); 

            break; 

        case 3: 

            if (isIE) { 

                var oRange = oTextbox.createTextRange(); 

                oRange.moveStart("character", iStart); 

                oRange.moveEnd("character", -oTextbox.value.length + iEnd);      

                oRange.select();                                              

            } else if (isMoz) { 

                **oTextbox.setSelectionRange(iStart, iEnd);** 

            }                     

    } 

}
```

现在我们后退一步，看看给定两个参数的情况(`iEnd`未指定)。本质上，我们想要做的是，如果有三个参数，唯一的区别是`iEnd`必须等于文本框中的字符数。我们可以这样完成:

```
function textboxSelect (oTextbox, iStart, iEnd) { 

    switch(arguments.length) { 

        case 1: 

            oTextbox.select(); 

            break; 

        /3b#/case 2: 

            iEnd = oTextbox.value.length; 

            /* falls through */ 

        case 3:          

            if (isIE) { 

                var oRange = oTextbox.createTextRange(); 

                oRange.moveStart("character", iStart); 

                oRange.moveEnd("character", -oTextbox.value.length + iEnd);      

                oRange.select();                                              

            } else if (isMoz){ 

                oTextbox.setSelectionRange(iStart, iEnd); 

            }                     

    } 

}
```

注意，在这种情况下，我们不使用 break 语句。我们希望执行一直延续到下一个案例，因为我们已经将 iEnd 设置为适当的值。这将在所有三种情况下正确工作。

该函数的最后一部分是将焦点设置到 textbox，以便当用户键入时，它将替换选定的文本:

```
function textboxSelect (oTextbox, iStart, iEnd) {  

    switch(arguments.length) {  

        case 1:  

            oTextbox.select();  

            break;  

        case 2:  

            iEnd = oTextbox.value.length;  

            /* falls through */  

        case 3:           

            if (isIE) {  

                var oRange = oTextbox.createTextRange();  

                oRange.moveStart("character", iStart);  

                oRange.moveEnd("character", -oTextbox.value.length + iEnd);       

                oRange.select();                                               

            } else if (isMoz){  

                oTextbox.setSelectionRange(iStart, iEnd);  

            }                      

    }  

    **oTextbox.focus();**  

}
```

##### 替换文本框选择

我们需要做的另一项任务是用其他文本替换当前选中的文本。为此，我们将创建`textboxReplaceSelect()`方法，它将接受两个参数:要操作的文本框和要插入的文本。因为我们将使用范围，我们需要为 IE 和 Mozilla 创建不同的代码。首先，我们来看看 IE 代码:

```
function textboxReplaceSelect (oTextbox, sText) {  

    if (isIE) {  

        var oRange = document.selection.createRange();  

        oRange.text = sText;  

        oRange.collapse(true);  

        oRange.select();                                  

    } else if (isMoz) {  

        ...  

    }  

    oTextbox.focus();  

}
```

在第 4 行，我们从文档的当前选择创建了一个范围(我们可以安全地假设它在 textbox 内，因为它将在 textbox 的 keypress 事件上被调用)。在下一行中，我们用给定的字符串替换该范围的文本。在第 6 行，我们调用范围的`collapse()`方法，将范围的长度设置为 0；该参数是一个布尔值，如果值为 true，则折叠到范围的末尾；如果值为 false，则折叠到范围的开头。最后，我们选择折叠的范围，将光标放在我们刚刚插入的文本之后。

在 Mozilla 中，我们可以通过一些简单的字符串操作达到同样的效果。我们可以通过使用文本框的 Mozilla selectionStart 和 selectionEnd 属性来确定选择的起点和终点:

```
function textboxReplaceSelect (oTextbox, sText) {  

    if (isIE) {  

        var oRange = document.selection.createRange();  

        oRange.text = sText;  

        oRange.collapse(true);  

        oRange.select();                                  

    } else if (isMoz) {  

       ** var iStart = oTextbox.selectionStart;  

        oTextbox.value = oTextbox.value.substring(0, iStart) + sText + oTextbox.value.substring(oTextbox.selectionEnd, oTextbox.value.length);  

        oTextbox.setSelectionRange(iStart + sText.length, iStart + sText.length);**  

    }  

    oTextbox.focus();  

}
```

在第 9 行，我们只是保存文本框选择的起点；我们以后会需要它。第 10 行看起来很复杂，但实际上它只接受选择之前的字符串和选择之后的字符串，并在它们之间添加 sText。之后，我们只需要复制我们在 IE 中所做的:将光标设置为出现在刚刚插入的文本之后。因此，在第 11 行，我们将选择范围设置为旧的起点加上插入的文本长度。这有效地复制了我们为 IE 所做的。

##### 相称的

该过程的下一步是编写一个方法，该方法将搜索字符串数组并返回以给定字符串开头的第一个值(例如，我们需要传入“a”并让它返回数组中以字母“a”开头的第一个值)。这个方法将被称为 autocompleteMatch()，并将接受两个参数:要匹配的文本和要匹配的值的数组。

搜索很简单——我们只需遍历数组并检查每个值。如果方法返回 true，那么我们就返回值。如果没有找到匹配项，则返回空值。以下是完整的代码:

```
function autocompleteMatch (sText, arrValues) {  

    for (var i=0; i < arrValues.length; i++) {  

        if (arrValues[i].indexOf(sText) == 0) {  

            return arrValues[i];  

        }  

    }  

    return null;  

}
```

注意，为了正确工作，`arrValues`中的字符串必须按字母顺序排列。

##### 胆量

构建了其他方法后，我们可以进入自动完成文本框的核心部分:方法`autocomplete()`。该方法将接受三个参数:要操作的文本框、事件对象和一个可能值的数组。假设我们有一个名为`arrValues`的数组，调用如下:

```
<input type="text" onkeypress="return autocomplete(this, event, arrValues)" />
```

我们将首先查看被按下的键的键码:

```
function autocomplete(oTextbox, oEvent, arrValues) {  

    switch (oEvent.keyCode) {  

        ...  

    }  

}
```

注意，我们仍然使用我们的`EventUtil.formatEvent()`方法格式化事件对象。

有几个键是我们不想干扰的，比如光标键；我们只在这些特殊情况下返回 true:

```
function autocomplete(oTextbox, oEvent, arrValues) {  

    switch (oEvent.keyCode) {  

        **case 38: //up arrow   

        case 40: //down arrow  

        case 37: //left arrow  

        case 39: //right arrow  

        case 33: //page up   

        case 34: //page down   

        case 36: //home   

        case 35: //end                   

        case 13: //enter   

        case 9: //tab   

        case 27: //esc   

        case 16: //shift   

        case 17: //ctrl   

        case 18: //alt   

        case 20: //caps lock  

        case 8: //backspace   

        case 46: //delete  

            return true;  

            break;  

        ...**  

    }  

}
```

接下来，我们将采用默认情况:当用户键入一个字符时。在这种情况下，我们需要完成许多步骤:

1.  We must replace the selected text with the character the user typed
2.  We must try to get a match to the text the user has typed in
3.  如果找到了，我们必须将文本框设置为建议的文本，并将选择设置为仅包含用户没有键入的字母

这听起来可能很复杂，但是我们已经编写的助手函数会使它变得容易得多。

重要的第一步是确定用户输入的字符(在事件对象的`keyCode`属性(在 IE 中)和`charCode`属性(在 Mozilla 中)上使用`String.fromCharCode()`)。我们使用该字符替换当前选定的文本。然后，我们需要获得文本框中文本的长度。

```
function autocomplete(oTextbox, oEvent, arrValues) {   

    switch (oEvent.keyCode) {   

        case 38: //up arrow    

        case 40: //down arrow   

        case 37: //left arrow   

        case 39: //right arrow   

        case 33: //page up    

        case 34: //page down    

        case 36: //home    

        case 35: //end                    

        case 13: //enter    

        case 9: //tab    

        case 27: //esc    

        case 16: //shift    

        case 17: //ctrl    

        case 18: //alt    

        case 20: //caps lock   

        case 8: //backspace    

        case 46: //delete   

            return true;   

            break;   

        **default:   

            textboxReplaceSelect(oTextbox, String.fromCharCode(isIE ? oEvent.keyCode : oEvent.charCode);   

            var iLen = oTextbox.value.length;   

            ...**   

    }   

}
```

接下来，我们需要使用我们的`autocompleteMatch()`方法在值数组中搜索匹配值。

```
function autocomplete(oTextbox, oEvent, arrValues) {   

    switch (oEvent.keyCode) {   

        case 38: //up arrow    

        case 40: //down arrow   

        case 37: //left arrow   

        case 39: //right arrow   

        case 33: //page up    

        case 34: //page down    

        case 36: //home    

        case 35: //end                    

        case 13: //enter    

        case 9: //tab    

        case 27: //esc    

        case 16: //shift    

        case 17: //ctrl    

        case 18: //alt    

        case 20: //caps lock   

        case 8: //backspace    

        case 46: //delete   

            return true;   

            break;   

        default:   

            textboxReplaceSelect(oTextbox, String.fromCharCode(isIE ? oEvent.keyCode : oEvent.charCode);   

            var iLen = oTextbox.value.length;   

            **var sMatch = autocompleteMatch(oTextbox.value, arrValues);**   

            ...   

    }   

}
```

在我们请求一个匹配值之后，我们需要确定是否确实找到了一个匹配。为此，我们测试`sMatch`看它是否为空。如果它不为空，我们需要用 sMatch 替换 textbox 中的文本。然后，我们将使用`iLen`(用户实际输入的文本长度)来选择用户在我们的`textboxSelect()`方法的帮助下没有输入的文本。

```
function autocomplete(oTextbox, oEvent, arrValues) {   

    switch (oEvent.keyCode) {   

        case 38: //up arrow    

        case 40: //down arrow   

        case 37: //left arrow   

        case 39: //right arrow   

        case 33: //page up    

        case 34: //page down    

        case 36: //home    

        case 35: //end                    

        case 13: //enter    

        case 9: //tab    

        case 27: //esc    

        case 16: //shift    

        case 17: //ctrl    

        case 18: //alt    

        case 20: //caps lock   

        case 8: //backspace    

        case 46: //delete   

            return true;   

            break;   

        default:   

            textboxReplaceSelect(oTextbox, String.fromCharCode(isIE ? oEvent.keyCode : oEvent.charCode);   

            var iLen = oTextbox.value.length;   

            var sMatch = autocompleteMatch(oTextbox.value, arrValues);   

            **if (sMatch != null) {   

                oTextbox.value = sMatch;   

                textboxSelect(oTextbox, iLen, oTextbox.value.length);   

            } **   

            ...   

    }   

}
```

做完这些之后，我们需要做的最后一件事就是将 false 值返回给事件处理程序。如果我们不这样做，键入的字符将出现两次。

```
function autocomplete(oTextbox, oEvent, arrValues) {   

    switch (oEvent.keyCode) {   

        case 38: //up arrow    

        case 40: //down arrow   

        case 37: //left arrow   

        case 39: //right arrow   

        case 33: //page up    

        case 34: //page down    

        case 36: //home    

        case 35: //end                    

        case 13: //enter    

        case 9: //tab    

        case 27: //esc    

        case 16: //shift    

        case 17: //ctrl    

        case 18: //alt    

        case 20: //caps lock   

        case 8: //backspace    

        case 46: //delete   

            return true;   

            break;   

        default:   

            textboxReplaceSelect(oTextbox, String.fromCharCode(isIE ? oEvent.keyCode : oEvent.charCode);   

            var iLen = oTextbox.value.length;   

            var sMatch = autocompleteMatch(oTextbox.value, arrValues);   

            if (sMatch != null) {   

                oTextbox.value = sMatch;   

                textboxSelect(oTextbox, iLen, oTextbox.value.length);   

            }    

            return false;   

    }   

}
```

##### 例子

当然，最后一步是制作一个测试代码的例子。事不宜迟，就是这样:

```
<html>   

<head>   

  <title>Autocomplete Textbox Example</title>   

        <script type="text/javascript">   

var isOpera = navigator.userAgent.indexOf("Opera") > -1;   

var isIE = navigator.userAgent.indexOf("MSIE") > 1 && !isOpera;   

var isMoz = navigator.userAgent.indexOf("Mozilla/5.") == 0 && !isOpera;   

function textboxSelect (oTextbox, iStart, iEnd) {   

    switch(arguments.length) {   

        case 1:   

            oTextbox.select();   

            break;   

        case 2:   

            iEnd = oTextbox.value.length;   

            /* falls through */   

        case 3:            

            if (isIE) {   

                var oRange = oTextbox.createTextRange();   

                oRange.moveStart("character", iStart);   

                oRange.moveEnd("character", -oTextbox.value.length + iEnd);        

                oRange.select();                                                

            } else if (isMoz){   

                oTextbox.setSelectionRange(iStart, iEnd);   

            }                       

    }   

    oTextbox.focus();   

}   

function textboxReplaceSelect (oTextbox, sText) {   

    if (isIE) {   

        var oRange = document.selection.createRange();   

        oRange.text = sText;   

        oRange.collapse(true);   

        oRange.select();                                   

    } else if (isMoz) {   

        var iStart = oTextbox.selectionStart;   

        oTextbox.value = oTextbox.value.substring(0, iStart) + sText + oTextbox.value.substring(oTextbox.selectionEnd, oTextbox.value.length);   

        oTextbox.setSelectionRange(iStart + sText.length, iStart + sText.length);   

    }   

    oTextbox.focus();   

}   

function autocompleteMatch (sText, arrValues) {   

    for (var i=0; i < arrValues.length; i++) {   

        if (arrValues[i].indexOf(sText) == 0) {   

            return arrValues[i];   

        }   

    }   

    return null;   

}   

function autocomplete(oTextbox, oEvent, arrValues) {   

    switch (oEvent.keyCode) {   

        case 38: //up arrow    

        case 40: //down arrow   

        case 37: //left arrow   

        case 39: //right arrow   

        case 33: //page up    

        case 34: //page down    

        case 36: //home    

        case 35: //end                    

        case 13: //enter    

        case 9: //tab    

        case 27: //esc    

        case 16: //shift    

        case 17: //ctrl    

        case 18: //alt    

        case 20: //caps lock   

        case 8: //backspace    

        case 46: //delete   

            return true;   

            break;   

        default:   

            textboxReplaceSelect(oTextbox, String.fromCharCode(isIE ? oEvent.keyCode : oEvent.charCode);   

            var iLen = oTextbox.value.length;   

            var sMatch = autocompleteMatch(oTextbox.value, arrValues);   

            if (sMatch != null) {   

                oTextbox.value = sMatch;   

                textboxSelect(oTextbox, iLen, oTextbox.value.length);   

            }    

            return false;   

    }   

}   

        </script>   

        <script>   

                var arrValues = ["red", "orange", "yellow", "green", "blue", "indigo", "violet", "brown"];   

        </script>   

</head>   

<body>   

<h2>Autocomplete Textbox Example</h2>   

<p>Type in a color in lowercase:<br />   

<input type="text" value="" id="txt1" onkeypress="return autocomplete(this, event, arrValues)" /></p>   

</body>   

</html>
```

##### 总结

用户喜欢能够输入值而不是使用鼠标，自动完成文本框会让他们非常高兴。

这段代码的唯一限制是它是区分大小写的，尽管可以很容易地修改`autocompleteMatch()`函数使其不区分大小写(我会让您自己去弄清楚如何做到这一点！).这段代码不能在 Netscape Navigator 4.x 系列或 Opera 上运行。它的功能在其他浏览器中是未知的(但假设不起作用)。

## 分享这篇文章