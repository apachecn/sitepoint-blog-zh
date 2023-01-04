# JavaScript 对象文字数组示例

> 原文：<https://www.sitepoint.com/javascript-object-literal-notation-arrays/>

可以用 JavaScript 对象文字符号声明数组吗？

## 示例 1–这适用于数组

**声明:**

```
NAMESPACE = {

    data:
    {
        items: Array() //array
    }

}
```

**数据存储:**

```
NAMESPACE.data.items.push(data[0]);
```

## 示例 2–这适用于对象

**声明:**

```
NAMESPACE = {

    data:
    {
        items: {} //object
    }

}
```

**数据存储:**

```
NAMESPACE.data.items[data['key']] = data;
```

请在评论中分享你的想法。

## 分享这篇文章