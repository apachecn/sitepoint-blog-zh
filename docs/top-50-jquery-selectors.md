# 50 大 jQuery 选择器

> 原文：<https://www.sitepoint.com/top-50-jquery-selectors/>

在我们编写的所有代码中，我们几乎都使用了 **jQuery 选择器**，这是指定我们想要从 DOM 中得到什么元素的最强大的方法。这个列表对于每个 jQuery 开发人员来说都是很好的参考资料。请注意，它们没有任何特定的顺序。

1.  **$(" *)**–该选择器选择文档中的所有元素。
2.  **$(" p>*)**–该选择器选择段落元素的所有子元素。
3.  **$(" # specialID ")**–这个选择器函数获取 id="specialID "的元素。
4.  **$(”。specialClass ")**–该选择器获取具有 special class 类的所有元素。
5.  **$(“李:不是(。my class)")**–选择匹配的所有元素
6.  那是没有的。
7.  **$(" a # specialID . specialClass ")**–该选择器匹配具有一个 specialID and 和一个 special class 类的链接。
8.  **$(“p a.specialClass”)** – This selector matches links with a class of specialClass declared within

    元素。

9.  **$(" ul Li:first ")**–该选择器仅获取第一个
10.  元素的

11.  **$(“#container p”)** – Selects all elements matched by

    它们是 id 为 container 的元素的后代。

12.  **$(【李】【ul】)**–选择匹配的所有元素

    *   **$(" strong+em ")**–选择紧跟在**匹配的兄弟元素之后的*匹配的所有元素。***
    *   **$(" p ~ ul ")**–选择匹配的所有元素

        。

    *   **$("code，em，strong ")**–选择所有与 `or *or **.***`匹配的元素
    *   **$(“p strong, .myclass”)** – Selects all elements matched by **that are descendants of an element matched by

        以及具有 myclass 类的所有元素。** 
    ***   **$(":empty ")**–选择所有没有子元素的元素。*   **$(“p:empty”)** – Selects all elements matched by

        没有孩子。

        *   **$(" div[p]")**–选择包含一个

        匹配元素的

        匹配的所有元素.*   **$(“p[.myclass]”)** – Selects all elements matched by

        它包含一个具有 myclass 类的元素。

        *   **$(" a[@ rel]")**–选择与*   **$(" input[@ name = myname]")**–选择与<input>匹配的、名称值与 my name 完全相同的所有元素。*   **$("input[@name^=myname]"**–选择所有与<input>匹配且名称值以 myname 开头的元素。*   **$(“a[@rel$=self]”)** – Selects all elements matched by

        其类值以 bar 结尾

        *   **$(" a[@ href * = domain . com]")**–选择与 href 值包含 domain.com 的*   **$(" Li:even ")**–选择匹配的所有元素*   具有偶数索引值。*   **$(" tr:odd ")**–选择由
        匹配的所有具有奇数索引值的元素。*   **$("李:第一个")**–选择第一个*   元素。*   **$("李:最后一个")**–选择最后一个*   元素。*   **$(" Li:visible ")**–选择匹配的所有元素*   可见的。*   **$(" Li:hidden ")**–选择匹配的所有元素*   隐藏起来的。*   **$(":单选")**–选择表单中的所有单选按钮。*   **$(":选中")**–选择表格中所有选中的方框。*   **$(":input ")**–仅选择表单元素(input、select、textarea、button)。*   **$(":text ")**–仅选择文本元素(input[type=text])。*   **$(“李:eq(2)”)**–选择第三个*   元素*   **$(“李:eq(4)”)**–选择第五个*   元素*   **$(" Li:lt(2)")**–选择匹配的所有元素*   第三个元素之前的元素；换句话说，前两个*   元素。*   **$(“p:lt(3)”)** – selects all elements matched by

        第四个元素之前的元素；换句话说，前三个

        元素。

        *   **$(" Li:gt(1)")**–选择匹配的所有元素*   第二个之后。*   **$(“p:gt(2)”)** – Selects all elements matched by

        第三个之后。

        *   **$(“div/p”)** – Selects all elements matched by

        匹配的元素的子元素

        .*   **$(“div//code”)** – Selects all elements matched by `that are descendants of an element matched by.``*   **$(“//p//a”)** – Selects all elements matched by *   **$(" Li:first-child ")**–选择匹配的所有元素*   是他们父母的第一个孩子。*   **$(" Li:last-child ")**–选择匹配的所有元素*   是他们父母的最后一个孩子。*   **$(":parent ")**–选择作为另一个元素的父元素的所有元素，包括文本。*   **$(" Li:contains(second)")**–选择匹配的所有元素*   包含文本秒的。*   **$(" TD:gt(4)")**–查找 TD #5 及更高版本*   **$(" input:not(:checked)")**–查找所有未被检查的输入*   **$("div，span，p . my class ")**–查找与这三个选择器中的任何一个匹配的元素。*   **$(" input[id][name $ = " man "]"**-查找所有具有 id 属性并且其 name 属性以 man 结尾的输入`**

## **`分享这篇文章`**