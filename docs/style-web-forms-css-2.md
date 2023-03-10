# 使用 CSS 文章样式化 Web 窗体

> 原文：<https://www.sitepoint.com/style-web-forms-css-2/>

##### 把它放在一起

作为本文的总结，这里有几个典型的表单——用于登录和用户注册——展示了我们如何使用 CSS 将表单变成一个有吸引力的界面特性。

##### 设计登录表单的样式

我们要看的第一个表单是登录表单。您可能会在网站上找到这样的表单，它要求用户登录，以便访问更多的功能(例如，在线公告栏)。

HTML 看起来像这样:

```
<form name="login" id="login" method="post" action="#">  
  <label>Username:  
  <input type="text" name="user" tabindex="1" />  
  </label>  
  <label>Password:  
  <input type="password" name="password" tabindex="2" />  
  <input type="submit" name="Submit" value="Submit" tabindex="3" />  
  </label>  
</form>
```

没有任何样式，表单如下所示:

![1166_image6](img/7d9fdd069d8ea66923ac3e2361926944.png)

首先，我们将设计表单标签本身的样式。在附加的样式表中添加以下 CSS:

```
form#login {  
  background-color: #CCCCCC;  
  color: #000000;  
  border: 1px solid #999999;  
  font-family: Verdana, Arial, Helvetica, sans-serif;  
  font-size: 10px;  
  text-align: right;  
}
```

我们只希望设计 id 为#login 的表单，我们定义的规则为这种表单提供了背景颜色、默认文本颜色和边框。我们已经设置了表单中文本的字体系列和大小，并右对齐了表单的内容。

![1166_image7](img/9546e670dc70269200216eb2b6de27d9.png)

//

登录表单的输入字段通常只需要很小，因为输入只是一个简短的用户名和密码。我们可以使用 CSS 设置文本输入框的宽度。为此，我创建了一个名为' #login '的类。'文本'。

```
#login .text {  
  font-family: Verdana, Arial, Helvetica, sans-serif;  
  font-size: 11px;  
  width: 100px;  
  margin-right: 6px;  

}
```

我还添加了一个右边距设置，在输入框的末尾和下一个标签的开头之间插入一点空间。

为了使这些更改影响表单，我们需要将类名添加到用户名和密码字段中。

```
<input name="user" type="text" id="user" tabindex="1" class="text"  />  
<input name="password" type="password" id="password" tabindex="2" class="text" /> 
```

![1166_image8](img/8b03d04291a89ebef607f990d4ec2b82.png)

这个表单的最后一步是设计提交按钮的样式，这个按钮现在在表单的末尾看起来有点大而且笨重。所以我们将创建另一个类，这次是为#login 表单中的按钮创建的。

```
#login .buttons {  
  font-family: Verdana, Arial, Helvetica, sans-serif;  
  font-size: 10px;  
  background-color: #333333;  
  color: #FFFFFF;  
  margin-right: 6px;  
}
```

将这个类应用到您的提交按钮来查看完整的登录表单。

```
<input name="Submit" type="submit" tabindex="3" value="Submit" class="buttons" /> 
```

![1166_image9](img/03f8d71f68b827cda1e3ea6800cb9cde.png)

我已经创建了这个表单，但没有使用表格进行布局，然而，我们讨论的所有技术都可以应用于表格中的表单。下一个例子将研究表格中的样式。

**Go to page:** [1](/style-web-forms-css) | [2](/style-web-forms-css-2/) | [3](/style-web-forms-css-3/) | [4](/style-web-forms-css-4/)

## 分享这篇文章