# FXRuby 简介

> 原文：<https://www.sitepoint.com/an-introduction-to-fxruby/>

FXRuby 是一个用于开发跨平台图形用户界面(GUI)的强大库。它基于 FOX toolkit(一个用 C++编写的开源、高度优化的库),为 Ruby 开发人员提供了用他们喜欢的语言编写应用程序的可能性，同时利用了 FOX 的底层性能和功能。

在本文中，我将向您展示如何使用 FXRuby，向您介绍一些更常用的小部件，并演示如何构建一个具有实际价值的简单应用程序。

本教程的代码可以从我们的 GitHub repo 获得[。](https://github.com/sitepoint-editors/password-generator)

## 装置

假设您的机器上安装了 Ruby 1.9。

**Windows 7:**

*   `gem install fxruby`

**Ubuntu 12.04:**

*   `sudo apt-get install ruby1.9.1-dev g++ libxrandr-dev libfox-1.6-dev`
*   `sudo gem install fxruby`

麦克·OS X:

*   `sudo gem install fxruby`

如果你遇到任何麻烦，更详细的说明可以在这里找到:[https://github.com/lylejohnson/fxruby/wiki](https://github.com/lylejohnson/fxruby/wiki)

## 你好，世界！

所以，让我们从习惯性的“你好，世界！”申请。

为此，在您的计算机上创建一个新文件，将其命名为`hello_world.rb`，并输入以下代码:

```
require 'fox16'
include Fox
app = FXApp.new
main = FXMainWindow.new(app, "Hello, World!" , :width => 200, :height => 50)
app.create
main.show(PLACEMENT_SCREEN)
app.run
```

用命令`ruby hello_world.rb`运行这个文件，您应该会看到类似这样的内容:

![](img/588271de4a401cf7f64933ad9582570c.png)

让我们来看看上面的代码在做什么:

*   我们首先需要 fox16 库。
*   FXRuby 的所有类都是在 Fox 模块中定义的，所以在程序的全局名称空间中包含 Fox 就不需要在这些类前面加上前缀`Fox::`。
*   然后我们创建一个`FXApp`类的实例(其中 App 代表应用程序对象)。`FXApp`实例是 FXRuby 程序的核心，有许多重要的任务，比如管理事件队列和处理信号。
*   接下来我们创建一个`FXMainWindow`的实例，将之前构造的`FXApp`对象作为第一个参数传递给它。这将我们正在创建的窗口与我们的应用程序关联起来。我们还传递给它另外三个参数:窗口标题、窗口宽度和窗口高度。
*   对`FXApp#create`的调用确保了应用程序的窗口被创建。
*   然而在 FXRuby 中窗口是默认不可见的，所以我们需要调用`FXMainWindow#show`来显示它。参数`PLACEMENT_SCREEN`确保它在屏幕上居中。
*   最后，我们通过调用`FXApp#run`开始程序的主循环。直到程序退出，该方法才会返回。

## 是时候进行一点重构了

虽然上面的代码工作得很好，但它不太像 Ruby，而且当你开始向应用程序添加小部件时，事情很快变得混乱。因此，FXRuby 中一个常见的习语是将应用程序的窗口创建为`FXMainWindow`的子类，如下所示:

```
require 'fox16'
include Fox

class HelloWorld < FXMainWindow
  def initialize(app)
    super(app, "Hello, World!" , :width => 200, :height => 50)
  end
  def create
    super
    show(PLACEMENT_SCREEN)
  end
end

app = FXApp.new
HelloWorld.new(app)
app.create
app.run
```

您会注意到，我们已经在我们的`HelloWorld`类中定义了一个`create`方法。我们需要这样做，因为当我们调用`app.create`时，我们的`FXApp`实例将依次调用与之相关的所有窗口的`create`方法。我们也可以使用这个方法让我们的新的`HelloWorld`对象在创建后自己调用`show`。

另一个流行的 FXRuby 习语是将`FXApp`和`HelloWorld`构造移到一个启动块中，就像这样:

```
if __FILE__ == $0
  FXApp.new do |app|
    HelloWorld.new(app)
    app.create
    app.run
  end
end
```

这里，`__FILE__`是当前文件的名称，`$0`是开始执行的文件的名称。通过比较这两个文件，我们可以确保我们的文件是正在运行的主文件，而不是另一个文件需要或加载的文件。对于这样一个小应用程序来说，这无疑是大材小用了，但却展示了 FXRuby 程序的典型风格。

## 一些更刺激的东西

“你好，世界！”应用程序是伟大的，但是让我们转移到更有实用价值的东西上来。在下一节中，我将向您展示如何制作一个简单的密码生成器，按下按钮，它将输出任意长度的随机密码。

### 布局

在我们开始编码之前，让我们花点时间考虑一下哪些元素应该出现在我们的 GUI 中。我们需要一个文本字段，用户可以输入所需密码的长度。我们还需要一个复选框，以便用户可以选择在密码中包含特殊字符。密码本身应该显示在某种文本区域，最后我们需要两个按钮:一个生成密码，一个复制到剪贴板。

下面是我们的 GUI 应该是什么样子的简单模型:

![](img/5cc1ae5d3048112ce6cfee2b47348d0c.png)

在 FXRuby 中，我们使用布局管理器来控制小部件的位置和大小。在这种情况下，我将使用类`FXHorizontalFrame`(水平排列其子元素)和`FXVerticalFrame`(垂直排列子元素)的对象。每个布局管理器的第一个参数是它的父窗口。我还向垂直框架(`LAYOUT_FILL`)传递了一个布局提示，告诉它在水平和垂直方向上尽可能多地占用可用空间。

为了创建小部件本身，我将使用以下 FXRuby 类的实例:

*   `FXLabel`–创建一个标签，我们可以在上面显示一些文本
*   `FXTextField`–创建一个文本字段，用户可以在其中键入单行输入
*   `FXCheckButton`–创建复选按钮，允许用户选择或取消选择选项
*   `FXText`–创建显示输出的文本区域
*   `FXButton`–创建执行命令的按钮

```
require 'fox16'
include Fox

class PasswordGenerator < FXMainWindow
  def initialize(app)
    super(app, "Password generator", :width => 400, :height => 200)
    hFrame1 = FXHorizontalFrame.new(self)
    chrLabel = FXLabel.new(hFrame1, "Number of characters in password:")
    chrTextField = FXTextField.new(hFrame1, 4)
    hFrame2 = FXHorizontalFrame.new(self)
    specialChrsCheck = FXCheckButton.new(hFrame2, "Include special characters in password")
    vFrame1 = FXVerticalFrame.new(self, :opts => LAYOUT_FILL)
    textArea = FXText.new(vFrame1, :opts => LAYOUT_FILL | TEXT_READONLY | TEXT_WORDWRAP)
    hFrame3 = FXHorizontalFrame.new(vFrame1)
    generateButton = FXButton.new(hFrame3, "Generate")
    copyButton = FXButton.new(hFrame3, "Copy to clipboard")
  end
  def create
    super
    show(PLACEMENT_SCREEN)
  end
end

if __FILE__ == $0
  FXApp.new do |app|
    PasswordGenerator.new(app)
    app.create
    app.run
  end
end
```

如果您在计算机上运行这段代码，您应该会看到我们的应用程序的框架，如下所示:

![](img/978a6e530ffbfec98b150e5833dbc39c.png)

### 基于用户输入生成随机字符串

要创建我们的密码，我们可以使用 Ruby 的`Integer#chr`方法，该方法返回一个字符串，其中包含由接收者的值表示的字符。如果用户想在密码中包含特殊字符，那么我们可以使用 ASCII 图表上 33-126 的 93 个字符中的任何一个。否则，我们坚持使用值 48-57、65-90 和 97-122，它们分别代表数字 1-9、大写 a-z 和小写 A-Z。

```
def generatePassword(pwLength, charArray)
  len = charArray.length
  (1..pwLength).map do
    charArray[rand(len)]
  end.join
end

numbers = (1..9).to_a
alphabetLowerCase = ("a".."z").to_a
alphabetUpperCase = ("A".."Z").to_a
allPossibleChars = (33..126).map{|a| a.chr}
```

```
p generatePassword(25, numbers + alphabetLowerCase + alphabetUpperCase) 
=> "fO0470a7tfdAM80u8jZ2aA0SG"
p generatePassword(25, allPossibleChars) 
=> "o>0]bl{6._l;s%MFCYz1Gl;hV"
```

当然，要记住这么长的随机密码，你需要使用密码管理器，比如 [KeePass](http://keepass.info/) ，但是每个人都这么做，对吗？:-)

### 连接两者

因此，我们已经有了 GUI 的框架，并且我们的`generatePassword`方法正在做它应该做的事情。是时候把两者联系起来了。

在 FXRuby 中，我们使用`connect`方法将用户动作(比如鼠标点击)与代码块关联起来。在`FXButton`的情况下，当它被点击时，它发送一个`SEL_COMMAND`消息给它的目标。语法如下:

```
FXButton.connect(SEL_COMMAND)do
  # This code fires when the button is clicked
  p "Yay! I was clicked!"
end
```

让我们将它应用到我们的代码中。

你会注意到，在`generateButton.connect`块中，我做了如下操作:

*   我添加了一行来清除我们想要显示输出的`FXText`小部件。如果我们不这样做，那么每次我们生成一个新的密码，它将被附加到旧的。
*   然后，我将调用`generatePassword`的结果附加到现在空白的`FXText`小部件上。
*   我用参数`chrTextField.text.to_i`调用`generatePassword`。这是用户在文本字段中输入的任何内容的整数值。

```
require 'fox16'
include Fox

NUMBERS = (1..9).to_a
ALPHABET_LOWER = ("a".."z").to_a
ALPHABET_UPPER = ("A".."Z").to_a
ALL_POSSIBLE_CHARS = (33..126).map{|a| a.chr}

class PasswordGenerator < FXMainWindow
  def initialize(app)
    super(app, "Password generator", :width => 400, :height => 200)

    hFrame1 = FXHorizontalFrame.new(self)
    chrLabel = FXLabel.new(hFrame1, "Number of characters in password:")
    chrTextField = FXTextField.new(hFrame1, 4)

    hFrame2 = FXHorizontalFrame.new(self)
    specialChrsCheck = FXCheckButton.new(hFrame2, "Include special characters in password")

    vFrame1 = FXVerticalFrame.new(self, :opts => LAYOUT_FILL)
    textArea = FXText.new(vFrame1, :opts => LAYOUT_FILL | TEXT_READONLY | TEXT_WORDWRAP)

    hFrame3 = FXHorizontalFrame.new(vFrame1)
    generateButton = FXButton.new(hFrame3, "Generate")
    copyButton = FXButton.new(hFrame3, "Copy to clipboard")

    generateButton.connect(SEL_COMMAND) do
      textArea.removeText(0, textArea.length)
      textArea.appendText(generatePassword(chrTextField.text.to_i, ALL_POSSIBLE_CHARS))
    end
  end

  def generatePassword(pwLength, charArray)
    len = charArray.length
    (1..pwLength).map do
      charArray[rand(len)]
    end.join
  end

  def create
    super
    show(PLACEMENT_SCREEN)
  end
end

if __FILE__ == $0
  FXApp.new do |app|
    PasswordGenerator.new(app)
    app.create
    app.run
  end
end
```

### 特殊字符

现在，让用户能够选择密码中是否包含特殊字符。最直接的方法是声明一个实例变量`@includeSpecialCharacters`，我们可以将其初始化为`false`。然后，我们可以将复选框连接到一个代码块，每当复选框被选中或取消选中时，该代码块将更新该实例变量的值(通过将其值与`true`进行异或运算)。

```
@includeSpecialCharacters = false
specialChrsCheck = FXCheckButton.new(hFrame2, "Include special characters in password")
specialChrsCheck.connect(SEL_COMMAND) { @includeSpecialCharacters ^= true }
```

我还包含了第二个名为`chooseCharset`的方法，它接收`@includeSpecialCharacters`作为参数，并返回一个数组，该数组包含一组用来构造密码的字符。

```
def chooseCharset(includeSpecialCharacters)
  if includeSpecialCharacters
    @charSets.first
  else
    @charSets.last
  end
end
```

字符集本身在初始化时被传递给`PasswordGenerator`对象，并在实例变量`@charSets`中可用。

```
charSets = [ALL_POSSIBLE_CHARS, NUMBERS + ALPHABET_LOWER + ALPHABET_UPPER]
PasswordGenerator.new(app, charSets)
```

我们的`generatePassword`方法将依次接受`chooseCharset`返回的数组作为参数，并相应地生成密码。

## 收尾工作

如果用户只需按一下按钮就可以将生成的密码复制到剪贴板上，那就太好了。幸运的是,`FXText`小部件提供了现成的剪贴板支持(也就是说，你可以使用 Ctrl + C 将它的文本复制到剪贴板),并且它不需要太多额外的代码来让我们以编程的方式与剪贴板交互。

当按下“复制到剪贴板”按钮时，要做的第一件事是调用`FXWindow#acquireClipboard`:

```
copyButton.connect(SEL_COMMAND) do
  acquireClipboard([FXWindow.stringType])
end
```

我们向该方法传递一个包含`FXWindow.stringType`(FOX 预先注册的拖动类型之一)的数组，以表明我们有一些字符串数据要放在剪贴板上。如果成功的话，`acquireClipboard`方法将返回`true`。

现在，每当另一个窗口请求剪贴板的内容时，FOX 将发送一条`SEL_CLIPBOARD_REQUEST`消息给当前剪贴板的所有者。正如我们在主窗口上调用的`acquireClipboard`,主窗口现在是剪贴板的所有者，需要响应这个消息类型:

```
self.connect(SEL_CLIPBOARD_REQUEST) do
  setDNDData(FROM_CLIPBOARD, FXWindow.stringType, Fox.fxencodeStringData(textArea.text))
end
```

`setDNDData`方法有三个参数。第一个告诉 FOX 我们要完成哪种数据传输，第二个是数据类型，最后一个是数据本身。

### 美学

完成这些后，我将对 GUI 的布局做两个小的改动。首先，我将把`FXTextField`和`FXCheckButton`放在一个分组框中，其次我将给这两个按钮一个统一的宽度。

groupbox 将是类`FXGroupBox`的一个对象。它是一个布局管理器，和其他布局管理器一样，它的第一个参数指定它的父窗口。它还接受各种布局提示作为附加参数。在这里，我使用了`FRAME_RIDGE`和`LAYOUT_FILL_X`，它们给了它一个脊状的框架，并告诉它尽可能多地占据水平方向的可用空间。为了给 groupbox 添加一些外部填充，我引入了一个类`FXPacker`的对象，它将封装所有其他的布局管理器。

```
packer = FXPacker.new(self, :opts => LAYOUT_FILL)
groupBox = FXGroupBox.new(packer, nil, :opts => FRAME_RIDGE | LAYOUT_FILL_X)
```

给我们的两个按钮一个统一的宽度稍微容易一些。我们只是在创建它们的直接父节点`FXHorizontalFrame`时包含布局提示`PACK_UNIFORM_WIDTH`。

```
hFrame3 = FXHorizontalFrame.new(vFrame1, :opts => PACK_UNIFORM_WIDTH)
```

### 最终的错误修复

在 Ruby 1.87 中，在`chrTextField`中输入一个负数会导致解释器进入一个死循环。为了避免这个问题，我们可以将`[0, chrTextField.text.to_i].max`作为第一个参数传递给`generatePassword`,后者将取值 0 或用户输入的任何值，这取决于哪个值更高。

### 这是最终的代码

```
require 'fox16'
include Fox

NUMBERS = (1..9).to_a
ALPHABET_LOWER = ("a".."z").to_a
ALPHABET_UPPER = ("A".."Z").to_a
ALL_POSSIBLE_CHARS = (33..126).map{|a| a.chr}

class PasswordGenerator < FXMainWindow
  def initialize(app, charSets)
    super(app, "Password generator", :width => 400, :height => 200)
    @charSets = charSets

    packer = FXPacker.new(self, :opts => LAYOUT_FILL)
    groupBox = FXGroupBox.new(packer, nil, :opts => FRAME_RIDGE | LAYOUT_FILL_X)

    hFrame1 = FXHorizontalFrame.new(groupBox)
    chrLabel = FXLabel.new(hFrame1, "Number of characters in password:")
    chrTextField = FXTextField.new(hFrame1, 4)

    hFrame2 = FXHorizontalFrame.new(groupBox)

    @includeSpecialCharacters = false
    specialChrsCheck = FXCheckButton.new(hFrame2, "Include special characters in password")
    specialChrsCheck.connect(SEL_COMMAND){ @includeSpecialCharacters ^= true }

    vFrame1 = FXVerticalFrame.new(packer, :opts => LAYOUT_FILL)
    textArea = FXText.new(vFrame1, :opts => LAYOUT_FILL | TEXT_READONLY | TEXT_WORDWRAP)

    hFrame3 = FXHorizontalFrame.new(vFrame1, :opts => PACK_UNIFORM_WIDTH)
    generateButton = FXButton.new(hFrame3, "Generate")
    copyButton = FXButton.new(hFrame3, "Copy to clipboard")

    generateButton.connect(SEL_COMMAND) do
      textArea.removeText(0, textArea.length)
      pwLength = [0, chrTextField.text.to_i].max
      charSet = chooseCharset(@includeSpecialCharacters)
      textArea.appendText(generatePassword(pwLength, charSet))
    end

    copyButton.connect(SEL_COMMAND) do
      acquireClipboard([FXWindow.stringType])
    end

    self.connect(SEL_CLIPBOARD_REQUEST) do
      setDNDData(FROM_CLIPBOARD, FXWindow.stringType, Fox.fxencodeStringData(textArea.text))
    end
  end

  def generatePassword(pwLength, charArray)
    len = charArray.length
    (1..pwLength).map do
      charArray[rand(len)]
    end.join
  end

  def chooseCharset(includeSpecialCharacters)
    if includeSpecialCharacters
      @charSets.first
    else
      @charSets.last
    end
  end

  def create
    super
    show(PLACEMENT_SCREEN)
  end
end

if __FILE__ == $0
  FXApp.new do |app|
    charSets = [ALL_POSSIBLE_CHARS, NUMBERS + ALPHABET_LOWER + ALPHABET_UPPER]
    PasswordGenerator.new(app, charSets)
    app.create
    app.run
  end
end
```

您也可以从我们的 GitHub repo 中的[获得该代码。](https://github.com/sitepoint-editors/password-generator)

## 结论

我希望在这篇文章中，我能够全面地介绍 FXRuby 的工作原理，并展示用您喜欢的语言创建跨平台的图形用户界面是多么容易。最后，我想介绍几个资源，它们在我使用这个库时给了我很大的帮助:

*   FX Ruby——用 Ruby 创建简洁明了的图形用户界面，作者 Lyle Johnson(FX Ruby 的创造者)—[http://pragprog.com/book/fxruby/fxruby](http://pragprog.com/book/fxruby/fxruby)
*   github 上的 FX ruby-[https://github.com/larskanis/fxruby](https://github.com/larskanis/fxruby)(Lars Kanis 是当前的维护者)
*   FXRuby 的文档—[http://rubydoc.info/github/larskanis/fxruby/1.6/frames](http://rubydoc.info/github/larskanis/fxruby/1.6/frames)
*   FXRuby 邮件列表(低流量，但是提问的好地方)–[http://rubyforge.org/mail/?group_id=300](http://rubyforge.org/mail/?group_id=300)

## 分享这篇文章