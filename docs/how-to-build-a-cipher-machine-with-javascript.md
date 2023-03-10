# 如何用 JavaScript 搭建密码机

> 原文：<https://www.sitepoint.com/how-to-build-a-cipher-machine-with-javascript/>

最近，当我读到英国数学家艾伦·图灵将出现在英格兰银行新版 50 英镑纸币上的消息时，我欣喜若狂。图灵因为有效地为计算机绘制了蓝图而在计算机爱好者的心中占据了特殊的位置。他还帮助破解了二战中纳粹 U 型潜艇使用的臭名昭著的海军密码。为了纪念这一点，我决定用我的书 [*JavaScript 新手到忍者*](https://www.sitepoint.com/premium/books/javascript-novice-to-ninja-2nd-edition/) 中介绍的 JavaScript 技巧来制作一个密码机的快速教程。

我们要用的密码是凯撒密码，以罗马皇帝朱利叶斯·凯撒命名。这是最简单的密码之一，它只是将每个字母移动一定的位数。例如，短语“Hello World”将使用移位 3 变成“KHOOR ZRUOG”(这是朱利叶斯·凯撒被认为使用的移位)。

![Our cipher machine](img/89b51e9f228125c2d8b350fce05aeeb6.png)

你可以在这里看到一个[完成代码的例子](https://codepen.io/daz4126/pen/VoywMN)。试着写一些秘密信息，感受一下它是如何工作的。

首先，启动您最喜欢的文本编辑器，将以下内容保存为`caesar.html`:

```
<!doctype html>
<html lang='en'>
<head>
  <meta charset='utf-8'>
  <title>Caesar Cipher</title>
</head>
<body>
  <h1>Caesar Cipher</h1>
  <form>
    <label>Plaintext:</label>
    <textarea name='plaintext'>Top Secret</textarea>
    <label>Shift:</label>
    <input type='number' name='shift' value='5' min='1' max='26'>
  <input type='submit' value='encrypt'>
  </form>
  <h2>Output</h2>
  <div id='output'>
  </div>
<script src='main.js'></script>
</body>
</html>
```

这是一个标准的 HTML 页面，包含一个带有文本区域的表单，用于输入我们的消息。还有一个属性为`number`的输入字段，大多数浏览器会显示步进箭头，允许用户增加或减少值。我们还可以将值限制在 1 到 26 之间，并将默认值设置为 3(为了纪念凯撒)。底部还有一个空的`div`元素，带有一个`output`的`id`，我们将使用它来显示加密的消息。

本书第八章介绍了表格。

现在让我们开始编写 JavaScript 代码来进行加密！

创建一个名为`main.js`的文件，并将其保存在与`caesar.html`文件相同的文件夹中。

我们将从创建一个名为`alphabet`的全局变量开始。这将是一个存储字母表中每个字母的数组:

```
const alphabet = [
  'A','B','C','D','E','F',
  'G','H','I','J','K','L',
  'M','N','O','P','Q','R',
  'S','T','U','V','W','X',
  'Y','Z'
]; 
```

这个数组将有助于定位字母在字母表中的位置，并沿着设定的位置移动它们。这也意味着我们可以通过简单地更新这个变量来使用其他的字母表。

本书的第一章介绍了变量，第三章介绍了数组。

现在我们需要一种方法来访问页面的各个部分。为此，我们将使用文档对象模型，或 DOM:

```
const form = document.forms[0];
const output = document.getElementById('output'); 
```

这段代码使用全局变量来引用表单元素和将显示加密消息的`div`元素。这将使我们以后更容易在代码中引用这些元素。

这本书的第六章介绍了 DOM。

当有人输入他们想要加密的消息时，他们按下提交按钮，它将显示加密的消息。我们需要使用一个事件处理程序，它将在提交按钮被按下时触发，并让我们访问已经在`textarea`中写入的内容。

```
form.addEventListener ('submit',event => {
  event.preventDefault();
  output.innerHTML = [... form.plaintext.value ].map(char => encrypt(char)).join('');
}
); 
```

这本书的第七章涵盖了各种事件。

这个事件处理程序内部发生了很多事情，所以让我们更详细地看看每一行:

```
 event.preventDefault(); 
```

这是用来防止提交表单的默认行为，因为我们实际上不希望它被提交到服务器。

```
 output.innerHTML = [... form.plaintext.value ].map(char => encrypt(char)).join(''); 
```

这一行用加密的消息更新输出`div`的`innerHTML`属性。使用`form.plaintext.value`可以访问包含在`textarea`中的信息。这是一个包含用户输入的各种字符的字符串。我们通过将许多方法链接在一起，将这个字符串转换成加密的消息。

首先，我们使用 spread 操作符`...`将字符串变成数组。然后我们使用`map`方法迭代每个字符，并应用 encrypt 函数将其转换成另一个字符。

最后，我们使用`join('')`方法将数组转换回可以在输出 div 中显示的字符串。

剩下要做的就是编写上面事件处理程序中 map 方法使用的`encrypt()`函数。这是将应用凯撒密码的代码:

```
function encrypt(char) {
  const shift = Number(form.shift.value);
  if (alphabet.includes(char.toUpperCase()))
 { 
    const position = alphabet.indexOf(char.toUpperCase());
    const newPosition = (position + shift)%26;
    return alphabet[newPosition] 
  }
  else { return char }
  } 
```

这个函数接受一个字符作为参数，然后返回加密的字符。

本书第 4 章介绍了各种功能。

首先将表单中的 shift 输入字段的值赋给变量`shift`。这可以使用代码`form.shift.value`来访问。表单中输入的所有值都作为字符串提交，所以我们需要使用`Number()`函数将这个变量的类型改为数字。这将允许我们用它来执行算术和其他数字方法。

接下来我们有一个条件语句，检查参数是否是字母表的一部分。这使用了`includes()`数组方法，如果参数在数组中，则返回 true。注意，我们还使用了`toUpperCase()`方法来确保字符是大写字母，因为字母表中的所有字母都是大写的。

如果字符在`alphabet`数组中，那么我们可以继续加密它。如果不是，那么我们简单地按原样返回字符(这允许我们处理空格和标点符号)。

加密代码从`alphabet`数组中返回一个元素:

```
{ 
    const position = alphabet.indexOf(char.toUpperCase());
    const newPosition = (position + shift)%26;
    return alphabet[newPosition] 
  } 
```

首先，我们用`indexOf()`数组的方法找出字符在`alphabet`数组中的索引。这存储在变量位置中。接下来，我们通过将`shift`的值加到索引中，找到加密字符的位置。我们还使用模操作符`%`来使用算术“mod 26”。这意味着任何超过 25 的值都将“循环”到数组的开头，26 将变成 0，27 变成 1，28 变成 2，等等。这意味着移位 3 的字母“X”将回到字母表的开头并映射到“A”。该值存储在`newPosition`变量中。

然后，我们简单地通过提供变量`newPosition`作为数组`alphabet`的索引，返回字母表数组中对应于这个新位置的字符。

这就是我们需要的所有代码！试着在文本区输入一条信息，点击“提交”,你会看到你的秘密信息出现在下面。

使用一个单独的`encrypt()`函数的美妙之处在于，我们可以随时更新它以使用不同的加密方法，而不必改变代码的任何其他部分。

凯撒密码可能是最基本的密码之一，尽管它是恩尼格玛密码的基础。也许你可以尝试在`encrypt()`函数中使用不同的密码，甚至可以更新它来模仿一台英格玛机？

## 分享这篇文章