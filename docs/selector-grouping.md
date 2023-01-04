# 选择器分组

> 原文：<https://www.sitepoint.com/selector-grouping/>

我们可以使用逗号(，)分隔符对选择器进行分组。以下声明块将应用于与组中的任一选择器匹配的任何元素:

```
td, th {
  ⋮ declarations
}
```

我们可以把逗号看作逻辑 OR 操作符，但重要的是要记住，组中的每个选择器都是独立的。初学者的一个常见错误是编写这样的组:

```
#foo td, th {
  ⋮ declarations
}
```

初学者可能会认为上面的声明块将应用于所有 td 和 th 元素，它们是 ID 为“foo”的元素的后代。但是，上面的选择器组实际上相当于:

```
#foo td {
  ⋮ declarations
}
th {
  ⋮ declarations
}
```

要实现真正的目标，请按如下方式编写选择器组:

```
#foo td, #foo th {
  ⋮ declarations
}
```

> 重要提示:不需要尾随逗号
> 不要在组中的最后一个选择器后留下逗号！

## 分享这篇文章