# 边框半径和嵌入框阴影错误解决方案！

> 原文：<https://www.sitepoint.com/border-radius-and-inset-box-shadow-bug-solution/>

Windows 上的谷歌 Chrome 因`border-radius` /Inset `box-shadow` bug 而臭名昭著。这个 bug 现在已经蔓延到了 Mac 版 Chrome(版本 10.0.648.127)。

如果你不熟悉这个 bug，这里有一个问题的快速运行:当在输入元素上应用边框半径时，嵌入框阴影将忽略`border-radius`并表现得好像它根本不存在。结果？你会收到一个难看的硬角，在你的边框下面。

经过一番摆弄，我想出了一个解决方案，并可能找到了这个问题的实际原因。事实证明，如果您的边框半径大于元素的高度(考虑填充、字体大小等)，就会出现这种视觉错误。

实际上，我们在搜索输入的设计节上遇到了这个问题。这是我们最初使用的 CSS:

```
input.text, #s {
    float: left;
    width: 163px;
    border: 1px solid #C0C0BA;
    font-size: 85%;
    padding: 4px 15px 4px 30px;
    -webkit-box-shadow: inset 0px 0px 5px rgba(0, 0, 0, 0.17);
    -moz-box-shadow: inset 0px 0px 5px rgba(0, 0, 0, 0.17);
    box-shadow: inset 0px 0px 5px rgba(0, 0, 0, 0.17);
    background: white url(/wp-content/themes/designfestivimg/social-icons/mail.png) no-repeat 7px 4px;
    height: 14px;
    }

input {
    -webkit-border-radius: 20px;
    -moz-border-radius: 20px;
    border-radius: 20px;
    padding: 2px 3px 3px;
    font-family: "Liberation Serif Italic", "Times New Roman", Serif;
    font-style: italic;
    }
```

这是如何渲染的截图:

![The render of the box shadow bug magnified.](img/7fe33001f3b93f19f9689263ce5ab36a.png)

## 狐狸

通过改变`input`元素的边界半径，我能够修复愤怒插入`box-shadow`的问题。

下面是修复后的 CSS 成品:

```
input.text, #s {
    float: left;
    width: 163px;
    border: 1px solid #C0C0BA;
    font-size: 85%;
    padding: 4px 15px 4px 30px;
    -webkit-box-shadow: inset 0px 0px 5px rgba(0, 0, 0, 0.17);
    -moz-box-shadow: inset 0px 0px 5px rgba(0, 0, 0, 0.17);
    box-shadow: inset 0px 0px 5px rgba(0, 0, 0, 0.17);
    background: white url(/wp-content/themes/designfestivimg/social-icons/mail.png) no-repeat 7px 4px;
    height: 14px;
    }

input {
    -webkit-border-radius: 12px;
    -moz-border-radius: 12px;
    border-radius: 12px;
    padding: 2px 3px 3px;
    font-family: "Liberation Serif Italic", "Times New Roman", Serif;
    font-style: italic;
    }
```

[![](img/0a11c39bf88bc9a9390604b00a2028e8.png)](https://www.sitepoint.com/wp-content/uploads/2011/03/box-shadow-fixed.png)

这应该可以了。你遇到过这个 bug 吗？

## 分享这篇文章