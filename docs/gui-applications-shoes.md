# 鞋子的图形用户界面应用

> 原文：<https://www.sitepoint.com/gui-applications-shoes/>

![shoes-icon](img/3f4401e1ed59de4c8a2cb6bc4228db2f.png)

如果你已经用 Ruby 写了一段时间的代码，你可能至少听说过鞋子。Shoes 是一个 DSL / GUI 工具包，最初是由著名的 _why The Lucky Stiff 开发的。它也是帮助用户学习 Ruby 和 GUI 编程的 [Hackety Hack](http://hackety.com/) 程序的基础。

这里可以找到官网[。](http://shoesrb.com/)

鞋子中的一个简单应用程序如下所示:

```
# hello_world.rb
require 'green_shoes'

Shoes.app do
  para "Hello World"
end
```

鞋子让创建 GUI 变得相当简单。本教程涵盖了基础知识，并展示了一些可以用它创建的东西的例子。

本教程中的代码可在 [github](https://github.com/rlqualls/sitepoint_shoes_tutorial) 上获得。

## 穿上鞋

鞋子令人困惑的一点是它有几种口味。

*   红色鞋子——最初的鞋子平台
*   蓝鞋–QT 实施
*   紫色的鞋子——JRuby/SWT 实现
*   绿鞋——纯 Ruby/GTK2 实现
*   鞋子 4-鞋子的下一个版本

在撰写本文时，如果您安装了 shoes gem 并试图使用它，您将得到以下消息:

```
Sorry, this gem currently does nothing. Team Shoes is working on Gemifying Shoes, and this is just a placeholder until then.
```

鞋 github 项目已经有一段时间没有更新了，所以看起来 Team Shoes 认为 gemifying 鞋不是他们优先考虑的事情。部分原因可能是因为 Shoes 不仅仅是一个 GUI 库，它还是一个应用程序平台。它甚至自带了 Ruby 解释器。

看起来它从未走出开发的早期阶段。浏览代码，许多方法都没有实现，所以您可能不想使用这个方法。

[紫色 _ 鞋](https://github.com/ashbb/purple_shoes)和绿色 _ 鞋是同一个人创作的。这可能是红鞋的第二个最佳选择。我测试了几个跨平台的例子，它们看起来工作正常。然而，它已经几年没有更新了，所以你的里程可能会有所不同。如果你想尝试紫色的鞋子，你需要 JRuby。

[green_shoes](http://ashbb.github.io/green_shoes/) 是一款 GTK2 味道的鞋子。这似乎是最近更新和实施最多的红鞋替代品。**和**不完全一样吗，但是如果你只是想尝一尝而不装红鞋的话已经够近了。

绿鞋有一页是关于红鞋和绿鞋的区别。另外，你会注意到手册里有很多“注意:绿鞋不支持……”。如果你对穿鞋很认真，你可能会希望看到你可以穿红色的鞋子去工作，或者计划大量修改绿色的鞋子。

Team Shoes 目前似乎正在进行名为 Shoes4 的重写工作。代码可从 github 上的[获得。不幸的是，它看起来还不能使用。](https://github.com/shoes/shoes4)

## 入门指南

我在安装红鞋平台时遇到了麻烦，所以我将在本教程中使用绿鞋宝石。因为 Green Shoes 使用 GTK2，所以在尝试运行任何东西之前，请确保您已经安装了 GTK2 库。

首先，去抢绿鞋宝石吧:

```
gem install green_shoes
```

鞋子应用程序的结构如下所示:

```
Shoes.app do
  element/slot(args) do
    element/slot(args) do
      ...etc
    end
  end  
end
```

在文章开头的例子中，“para”创建了一个内容为“Hello World”的段落元素。

```
# hello_world.rb
require 'green_shoes'

Shoes.app do
  para "Hello World"
end
```

在 Shoes DSL 中，GUI 元素是使用元素创建方法就地创建的。这与实例化窗口、实例化布局、实例化窗口小部件、将窗口小部件添加到布局以及将布局添加到窗口的标准实践相反。鞋子在引擎盖下会这样。

可以将元素存储在变量中，以便它们可以在应用程序的其他地方被引用。

```
Shoes.app do
  @paragraph = para "This is a paragraph."
  @button = button("change it") do
    @paragraph.text = "Now it says something else."
  end
end
```

本教程中的变量都是实例变量(前缀为' @ ')，以避免与 Shoes DSL 方法混淆。

## 堆栈和流程

Shoes 以两种称为插槽的方式组织其元素。

*   堆栈–垂直
*   流动–水平

```
# stacks_flows.rb

require 'green_shoes'

Shoes.app do

  stack do
    para "stack item 1", width: 100
    button "stack item 2"  
  end

  flow do
    para "flow item 1", width: 100
    button "flow item 2"
  end
end
```

值得指出的是，在绿鞋中，文本块(段落、标题等)占据整个宽度，除非指定宽度。即使 TextBlock 在流中也是如此。

堆栈和流可以设置边距来分隔元素。

```
# margins.rb
require 'green_shoes'

Shoes.app do

  caption "No margin set:"

  stack do
    para "stack item 1", width: 100
    button "stack item 2" 
  end

  flow do
    para "flow item 1", width: 100
    button "flow item 2"
  end

  caption "Margin: 10"

  stack(margin: 10) do
    para "stack item 1", width: 100
    button "stack item 2" 
  end

  flow(margin: 10) do
    para "flow item 1", width: 100
    button "flow item 2"
  end
end
```

有时，通过将流放入堆栈来实现所需的布局，反之亦然。

```
# flow_inside_stack.rb
require 'green_shoes'

Shoes.app do

  stack do
    para "stack item 1", width: 100

    flow do
      para "flow item 1", width: 100
      button "flow item 2"
    end

    button "stack item 2" 
  end
end
```

## 响应用户交互

正如在 Ruby 中所期望的，Shoes 使用块来处理事件，比如按钮点击或文本改变。

```
# event_handling.rb
# Interactive Shoes elements take an event handler as a block
require 'green_shoes'

Shoes.app do
  stack(margin: 10) do
    @edit_box = edit_box do |e|
      @copy_box.text = @edit_box.text
    end  

    @copy_box = para "what you type in the edit box goes here"

    @button = button("obfuscate") do
      ob_bytes = @copy_box.text.each_byte.map { |b| b+1 }
      @copy_box.text = ob_bytes.map { |b| b.chr}.join
    end
  end
end
```

## 制作 YARV 指令查看器

“鞋子”基本文本框由`#edit_box`创建。可以读取或设置`EditBox#text`。这是一个应用程序，它采用 Ruby 代码并显示与之相关的虚拟机指令。**注意**:如果你穿紫色的鞋子，这个例子就不成立。

```
# yarv_viewer.rb
# Show YARV instructions for given Ruby code
require 'green_shoes'

Shoes.app(width: 900, height: 550) do
  stack(margin: 10) do
    flow(margin: 10) do
      caption "Ruby Code", width: 400
      caption "YARV Instructions", width: 400
    end
    flow(margin: 10) do
      @code = edit_box(width: 400, height: 400)
      @yarv = edit_box(width: 400, height: 400) 
    end
    flow(margin: 10) do
      @compile_button = button("compile") do
        @yarv.text = RubyVM::InstructionSequence.compile(@code.text).disasm
      end
    end
  end
end
```

## 使用鞋子的进度条

鞋子提供了一个准系统进度条。其值`#fraction`的范围是从 0.0 到 1.0。将 Progress 与其他元素分开的一个原因是，它似乎需要手动按像素方式放置，而不是简单地放入一个槽中。

```
# downloader.rb
# Basic downloader with progress-bar
require 'green_shoes'
require 'open-uri'

Shoes.app(width: 350) do
  title "Downloader"
  stack(margin: 10) do

    # user input
    flow do
      @url_line = edit_line
      @download_button = button("download") do
        url = @url_line.text 
        uri = URI.parse(url)
        filename = File.basename(uri.path)

        Thread.new do
          open(filename, 'wb') do |f|
            f.write(open(url, 
              :content_length_proc => @content_length_proc,
              :progress_proc => @progress_proc
            ).read)
          end
        end
      end
    end

    # labels
    caption "File Size:" 
    @expected_bytes = para ""
    caption "Downloaded:"
    @current_bytes = para ""
    caption "Progress:"
    @progress_bar = progress left: 10, top: 300

    # open-uri event handlers
    @content_length_proc = lambda do |content_length|
        @content_length = content_length
        @expected_bytes.text = content_length
    end

    @progress_proc = lambda do |bytes|
      @current_bytes.text = bytes
      @progress_bar.fraction = bytes / @content_length.round(1)
    end
  end
end
```

Ruby 的`#open`方法接受一个可以指定两个事件处理程序的散列，这两个事件处理程序可以用来确定进度。

1.  `:content_length_proc`–提供对下载文件大小的访问
2.  `:progress_proc`–提供到目前为止下载的字节数

这些事件处理程序(本例中为 lambdas)用于设置`@progress_bar.fraction`。

## 包装 CLI 工具

鞋子的一个可能用途是为命令行工具创建简单的包装器，比如 ImageMagick 的`convert`。

```
# image_converter.rb
# Basic image converter using ImageMagick
require 'green_shoes'
require 'gtk2'

Shoes.app do
  title "--Image Converter--"

  caption "Image Path:"
  @path_line = edit_line

  button("select image") do
    dialog = Gtk::FileChooserDialog.new(
            "Select Image",
            nil,
            Gtk::FileChooser::ACTION_OPEN,
            nil,
            [Gtk::Stock::CANCEL, Gtk::Dialog::RESPONSE_CANCEL],
            [Gtk::Stock::OPEN, Gtk::Dialog::RESPONSE_ACCEPT])

    if dialog.run == Gtk::Dialog::RESPONSE_ACCEPT
      @path_line.text = dialog.filename
    end
    dialog.destroy
  end

  caption "Choose a new image format:"
  list_box items: ["jpg", "png", "gif"] do |list|
    @conversion_type = list.text
  end

  button("convert") do
    @file_path = @path_line.text
    @filename = File.basename(@file_path, '.*')
    @conversion_path = "#{File.dirname(@file_path)}/#{@filename}.#{@conversion_type}"
    if @file_path != @conversion_path
      Thread.new do
        @status.text = "converting..."
        system("convert #{@file_path} #{@conversion_path}")
        @status.text = ""
      end
    else
      alert("The image is already in that format")
    end
  end
  @status = para ""
end
```

虽然鞋子并不提供特别的必需品，但有时可以从其他图书馆借。在这个例子中，我借用了 GTK 的 FileChooserDialog 来让用户更容易指定图像。这不是很干净，但是 green_shoes 无论如何都使用了`gtk2`，所以在这种情况下还不算太差。**注意**:这不适用于紫色的鞋子。

## 绘画和动画

Shoes 从动画工具包中获得灵感，如在其绘图和动画设施中进行处理。

```
# drawing.rb
# Show how to draw basic shapes in Shoes
require 'green_shoes'

Shoes.app do
  background darkgray
  fill darkblue
  arrow 10, 10, 100
  fill red
  rect 10, 100, 100
  fill yellow
  oval 10, 200, 50
  strokewidth(2)
  line 10, 300, 100, 300
end
```

鞋的一个意想不到的事情是，颜色可以不使用符号或字符串来指定。例如，您可以使用深蓝，而不是:深蓝。也可以使用`#rgb`和十六进制颜色代码字符串如' #FF1B77 '来指定颜色。

Shoes 提供了随时间移动元素的`#animate`方法。它有一个参数，即动画的每秒帧数。你不会得到处理级别的控制——或者肯定不会得到 Actionscript 级别的控制，但是这足够玩了。

```
# animation.rb
# Shows how to animate a bouncing ball
require 'green_shoes'

Shoes.app do
  fill blue
  @gravity = 0.05
  @velocity = 0.0
  @ball_radius = 60
  @ball = oval(top: 10, 
              left: width / 2 - @ball_radius, 
              radius: @ball_radius)
  button("drop ball") do
    animate(60) do
      @velocity += @gravity
      @ball.top += @velocity 
      if @ball.top > height - @ball_radius * 2
        @ball.top = height - @ball_radius * 2
        @velocity = @velocity * -0.8
      end
    end
  end
end
```

如果你以前从未编写过任何动画代码，那么在边界检查结束后设置的位置可能并不直观。本质上，一旦“球”越过边界(“地面”)，该条件为真。即使在速度反转后，条件仍然成立，所以速度将简单地再次反转…一次又一次，导致球在边界
周围抖动，直到能量耗尽。球的边缘被设置在边界处，以防止这种情况发生，并保持球在反弹的假象。这个问题的一个看似显而易见的解决方案是使用==操作符，但是球的边缘几乎不可能正好在任何给定帧的边界上。

## 结论

鞋子带来了一些好主意，非常适合小型玩具项目。不幸的是，如果你想认真对待它，可能会涉及一些 DIY，原因如下:

*   开发工作已经在各种口味之间进行了划分，目前还没有红鞋宝石。
*   Shoes 从来没有打算完全取代标准的 GUI 库。它主要是作为一种教育工具而产生的。
*   至少有了绿鞋，错误报告似乎就坏了。不管发生了什么，我只见过一个普通的油嘴滑舌的错误。调试并不容易。

鞋子有很多本教程中没有提到的功能。如果您想了解更多信息，请务必查看以下资源:

*   [绿鞋手册](http://ashbb.github.io/green_shoes/)
*   [原版鞋子手册](http://shoesrb.com/manual/Hello.html)
*   [没人知道鞋子](http://cloud.github.com/downloads/shoes/shoes/nks.pdf)

## 分享这篇文章