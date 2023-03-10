# AtoZ CSS 截屏:必需的伪类

> 原文：<https://www.sitepoint.com/atoz-css-screencast-required/>

这个截屏是我们 AtoZ CSS 系列的一部分。你可以在这里找到系列[的其他条目。](https://www.sitepoint.com/blog/)

## 副本

是一个基于状态的伪类，在设计表单样式时很有用。

结合`:valid`和`:invalid`，我们可以在向用户提供视觉反馈方面有更多的控制，而不必编写大量的 JavaScript。

在这一集里，我们将学习:

*   如何使用 HTML5 表单验证
*   使用`:required`和其他基于状态的伪类
*   向用户显示验证消息的创造性技术

### HTML5 验证

当要求用户填写表单时，通常会有一些必填字段。这些可能是他们的姓名、电子邮件地址或信用卡详细信息。

在 HTML5 中，当行为被构建到现代浏览器中时，我们可以在没有 JavaScript 的情况下利用客户端验证。

要在表单中创建一个必填字段，需要将`required`属性添加到 HTML 中。

```
<form action="#" method="#"> 
  <div> 
    <input id="name" type="text" required> 
    <label for="name">Your name</label> 
  </div> 
  <div> 
    <input id="email" type="email" required> 
    <label for="email">Your email</label> 
  </div> 
  <div> 
    <input id="phone" type="tel" pattern="[0-9]+"> 
    <label for="phone">Your phone number</label> 
  </div> 
  <div> 
    <textarea id="message" required></textarea> 
    <label for="message"> 
  </div> 
  <div><input type="submit"></div> 
</form>
```

由于`required`是一个布尔属性，它在元素中的存在就足以设置值，所以不需要指定属性值。

同样值得注意的是，在 HTML5 元素中，像`input`这样没有结束标签的元素，不再需要用斜杠自结束；我认为没有它们看起来更整洁，所以我把它们去掉了。

将这些属性添加到标记中后，当我试图在没有完成这些字段的情况下提交表单时，我会收到一系列要求“填写”字段的工具提示消息——这些是由浏览器自动生成的，据我所知不能进行自定义样式。

### 使用`:required`

将`required`属性添加到标记中后，我们可以用它来添加一些视觉反馈，让用户知道哪些字段必须填写。

HTML 结构在这里很重要，每个输入和标签组合都包含在一个`<div>`中，标签在输入的之后*出现。这一点以后会变得清楚。我们仍然可以让标签出现在输入之前，方法是将它们浮动到表单容器的相对两侧，并相应地清除每个容器。*

我想在每个标签后添加一个星号作为`required`输入。使用前面几集的通用兄弟选择器和[伪元素](https://www.sitepoint.com/atoz-css-pseudo-elements/)的知识，我可以为跟随`required`输入的任何标签生成星号。

如果您的设计需要不同的标记结构，标签在输入之前，可以实现类似的效果，但是您需要向任何`required`字段的父容器添加一个类，以添加所需的样式。

`required`的反义词是`optional`,我们可以在表单中将文本“optional”添加到任何没有明确要求的输入中，使这一点变得清楚。在这里，`:optional`伪类已经覆盖了我们。

```
input:optional ~ label:after { 
  content: "(optional)"; 
  display: block;
  color: #888; 
}
```

当我们的用户填写表单时，我们可以通过样式化`:valid`和`:invalid`状态来提供额外的视觉反馈，看看他们是否正确地填写了他们的详细信息。

```
input:not([type="submit"]):valid:focus,
textarea:valid:focus {
  box-shadow: 0 0 10px rgba(101,169,10,0.8);
  outline: 0;
}
input:not([type="submit"]):invalid:focus,
textarea:invalid:focus {
  box-shadow: 0 0 10px rgba(204,63,133,0.8);
  outline: none;
}
```

这两个片段将在填充无效输入时在无效输入后面添加一个微妙的红色阴影，在任何有效输入后面添加一个微妙的绿色阴影。

### 验证消息

我们可以使用我们已经介绍过的相同技术，使用基于状态的伪类和用伪元素生成内容，向表单添加更多的描述性验证消息。

我已经创建了一系列消息，这些消息将通过有效和无效字段的各种组合的`content`属性注入。

```
input[type="text"]:focus:invalid~label:before {
  content: "Please enter your full name";
}
input[type="email"]:focus:invalid~label:before {
  content: "Please enter a valid email";
}
input[type="tel"]:focus:invalid~label:before {
  content: "Please only use numbers";
}
textarea:focus:invalid~label:before {
  content: "Please enter a message";
}
input:focus:valid~label:before,
textarea:focus:valid~label:before {
  content: "Perfect, thanks!";
}
```

然后，当用户聚焦输入时，这些可以被动画化到适当的位置，并且将根据有效或无效状态而改变。

```
label:before {
  content: "";
  position: absolute;
  bottom: 0;
  right: 0;
  font-size: 1rem;
  opacity: 0;
  transition: 0.2s;
}
input:focus~label:before,
textarea:focus~label:before {
  bottom: 100%;
  opacity: 1;
}
input:focus:invalid~label:before,
textarea:focus:invalid~label:before {
  color: #cc3f85;
}
input:focus:valid~label:before,
textarea:focus:valid~label:before {
  color: #65a90a;
}
```

现在，当我们填写表单时，我们得到了关于过程中每一步的更多信息，这应该是对相当枯燥的表单的一个很好的 UX 增强。

## 分享这篇文章