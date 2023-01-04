# X#至 Xen 至 Cω

> 原文：<https://www.sitepoint.com/x-to-xen-to-c-omega/>

微软不断变化的未来编程语言还有另一个名字，C Omega。你过去可能见过它是 X#，或者 Xen。C Omega 的[研究](http://www.cl.cam.ac.uk/~gmb/Papers/vanilla-xml2003.html)将在面向对象编程模型中引入数据访问的概念。如果您厌倦了使用 API 访问 XML 和传统 DBMSs 的麻烦，并且想知道为什么一种语言的核心中没有 XML 和 SQL 的概念，那么 C Omega 可能值得关注。

它已经被隐藏起来，复活了，并且有了大量的名称更改，但是 C Omega 可能看起来像是为 C#铺平了道路，没有 XML 或 SQL 类，但是具有处理数据访问的内置功能。

例如，摘自研究论文，这是我们将来处理 XML 的方式:

 `public class card {
sequence{
string name;
string title;
string email;
string? phone;
logo? logo;
};
}`

 `公共类标识{
属性字符串 url
}

公共类测试{` 

`static void Main() {
card c = <card><name>John Doe</name>
<title>CEO, Widget Inc.</title>
<email>john.doe@widget.com</email> <phone>(202) 456-1414</phone></card>;
c.*.{ Console.WriteLine(it); };
}
}`

这是一项引人入胜的研究，可能会彻底改变未来我们编写应用程序的方式。但是你认为这是未来吗，或者这只是不必要的复杂化？添加评论:)

## 分享这篇文章