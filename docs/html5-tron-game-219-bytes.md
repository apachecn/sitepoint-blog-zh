# Tron:一款 219 字节的 HTML5 游戏

> 原文：<https://www.sitepoint.com/html5-tron-game-219-bytes/>

我从来没有热衷于这种多兆字节的网页废话。也许是因为我在拨号上网时代开始了 web 开发，当时 100Kb 的总页面重量被认为是过大的？或者可能只是因为我不明白为什么浪费带宽是必要的？

我之前提到过一些 [1KB JavaScript](https://www.sitepoint.com/js1k-competition-2012/) 和 [CSS](https://www.sitepoint.com/1-kilobyte-css-gallery/) 的比赛。有一些引人注目的条目，但很少有像 [219 字节 Tron](http://alokmenghrajani.github.com/tron/) 这样带宽友好的。是的，219 字节——包括所有的 HTML5 和 JavaScript 代码。

该项目一开始是一个挑战，几个合作者为记忆剃练习做出了贡献。可以 [**在这里玩**](http://alokmenghrajani.github.com/tron/tron.html)——使用 I、j、k、l 键启动和转向。我发现这个游戏在 Opera 中运行得最好，但在 Chrome 和 Safari 中也不错。Firefox 和 IE9 不兼容。

虽然它永远不会赢得任何游戏奖项，也不会取代你的*使命召唤*课程，但这是一项令人印象深刻的思维敏捷练习。开发人员[记录了每一行代码](http://alokmenghrajani.github.com/tron/)，还有一些有用的提示和技巧，你可以应用到你的 JavaScript 编程中。

*   在除 Firefox 之外的所有浏览器中，为元素分配一个 ID 会将节点对象暴露为 JavaScript 中的一个变量，例如

    ```
     <div id="test">Hello</div>
    <script>test.textContent = "Goodbye";</script> 
    ```

*   如果不包含>、空格或制表符，浏览器会很乐意接受不带引号的标签属性。
*   变量可以在函数调用中初始化，例如

    ```
     myFunction(a=1, b=2, c=3); 
    ```

*   如果运算符的两边都是布尔值，可以用二进制&代替&。
*   是网页上唯一必要的 HTML 标签！

请记住，Tron 将字节节省发挥到了极致。这些建议中有一些是有用的，但是你应该权衡一下:

1.  **功能。**例如，仅仅因为 onkeyup 比 onkeydown 短，这并不一定使它成为最适合使用的事件。
2.  **可读性。没有文档，Tron 的代码是不可能理解的。很少有开发者会坚持去理解它。**
3.  **兼容性。**我提倡跨浏览器操作，我怀疑 IE9 和 Firefox 可以通过一些额外的字节来修复。

[219 字节 Tron](http://alokmenghrajani.github.com/tron/) 令人咋舌。虽然您可能永远不会采用这些技术，但是在向页面添加额外的大量内容之前，您应该考虑什么是可能的。

## 分享这篇文章