# HTML 与 XHTML:两种解析模式的比较

> 原文：<https://www.sitepoint.com/differences-html-xhtml/>

HTML5 有两种**解析模式**或**语法** : HTML 和 XML。这种差异取决于文件是带有`Content-type: text/html`标题还是`Content-type: application/xml+xhtml`标题。

如果用作`text/html`，则适用以下规则:

*   不是每个元素都需要开始标记。
*   不是每个元素都需要结束标记。
*   只有**无效元素**如`br`、`img`、`link`可以用/ >进行“自闭”。
*   标签和属性不区分大小写。
*   属性不需要加引号。
*   有些属性可能是空的(比如`checked`和`disabled`)。
*   特殊字符或实体不必转义。
*   文档必须包含 HTML5 文档类型。

## HTML 语法

让我们看另一个 HTML5 文档。

```
<!DOCTYPE html>
  <html>
    <head>
      <meta charset=utf-8>
      <title>Hi</title>
      <!-- 
      This is an example of a comment.
      The lines below show how to include CSS 
      -->
      <link rel=stylesheet href=style.css type=text/css>
      <style>
        body{
          background: aliceblue;
        }
      <style>
    </head>
    <body>
     <p>
        <img src=flower.jpg alt=Flower>
        Isn't this a lovely flower?

      <p>
        Yes, that is a lovely flower. What kind is it?

      <script src=foo.js></script>
    </body>
</html> 
```

同样，我们的第一行是 DOCTYPE 声明。和所有 HTML5 标签一样，它不区分大小写。如果你不喜欢按 Shift 键，你可以输入`来代替。如果你真的喜欢使用**大写锁定**，你也可以输入`来代替。``

 ``接下来是`head`元素。`head`元素通常包含关于文档的信息，比如它的标题或字符集。在这个例子中，我们的`head`元素包含一个`meta`元素，它定义了这个文档的字符集。包含一个字符集是可选的，但是你应该总是设置一个，而且[推荐你使用 UTF-8](https://www.w3.org/International/questions/qa-choosing-encodings) 。

我们的`head`元素还包含我们的文档标题`(<title>Hi</title>)`。在大多数浏览器中，`title`标签之间的文本显示在浏览器窗口或标签的顶部。

HTML 中的注释是不在浏览器中呈现的文本。它们仅在源代码中可见，通常用于给自己或同事留下关于文档的注释。一些生成 HTML 代码的软件程序也可能包含注释。注释可能出现在 HTML 文档的任何地方。每个都必须以

 `文档头还可能包含指向外部资源的`link`元素，如下所示。资源可能包括样式表、favicon 图像或 RSS 源。我们使用`rel`属性来描述我们的文档和我们所链接的文档之间的关系。在这种情况下，我们链接到一个级联样式表，或 CSS 文件。CSS 是我们用来描述文档外观而不是其结构的样式表语言。

我们也可以使用一个`style`元素(这里用`来描述)