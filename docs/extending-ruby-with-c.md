# 代码 Safari:用 C 扩展 Ruby

> 原文：<https://www.sitepoint.com/extending-ruby-with-c/>

GitHub 最近发布了一个新的 Ruby 库，用于解析 Markdown: [Redcarpet](https://github.com/blog/832-rolling-out-the-redcarpet) 。它是 C 库 [Upskirt](http://fossil.instinctive.eu/libupskirt/index) 的包装器，这让我想到:你是如何*为 Ruby 编写 C 扩展的？对于我们这些习惯了舒适的 Ruby land 的人来说，降到 C 是一个可怕的前景。segfaults 的幽灵在边界出没，迫使毫无准备的旅行者在第一眼看到分号时就回头。*

如果没那么难呢？也许只要有一点勇气、一点热情和一点头脑，我们就能发现为自己编写 C 扩展的能力。

## 第一步

我的第一个调查步骤总是相同的:克隆存储库，并运行默认的`rake`。

```
$ git clone git://github.com/tanoku/redcarpet.git
$ cd redcarpet
$ rake
```

在一个制作良好的 gem 中(就像这个一样),这将运行完整的测试套件。在这种情况下，我们还可以在输出中看到一些 C 代码正在被编译。我们可以深入研究`Rakefile`以了解更多信息。

```
# excerpt from redcarpet/Rakefile
DLEXT = Config::MAKEFILE_CONFIG['DLEXT']
RUBYDIGEST = Digest::MD5.hexdigest(`#{RUBY} --version`)
&nbsp;
file "ext/ruby-#{RUBYDIGEST}" do |f|
rm_f FileList["ext/ruby-*"]
touch f.name
end
CLEAN.include "ext/ruby-*"
&nbsp;
file 'ext/Makefile' => FileList['ext/*.{c,h,rb}', "ext/ruby-#{RUBYDIGEST}"] do
chdir('ext') { ruby 'extconf.rb' }
end
CLEAN.include 'ext/Makefile', 'ext/mkmf.log'
&nbsp;
file "ext/redcarpet.#{DLEXT}" => FileList["ext/Makefile"] do |f|
sh 'cd ext &amp;&amp; make clean &amp;&amp; make &amp;&amp; rm -rf conftest.dSYM'
end
CLEAN.include 'ext/*.{o,bundle,so,dll}'
&nbsp;
file "lib/redcarpet.#{DLEXT}" => "ext/redcarpet.#{DLEXT}" do |f|
cp f.prerequisites, "lib/", :preserve => true
end
&nbsp;
desc 'Build the redcarpet extension'
task :build => "lib/redcarpet.#{DLEXT}"
```

由于`rake`的常见用法是只运行有依赖关系的任务，很容易忘记它最初是作为 make 的 Ruby 版本而设计的。因此，它提供了除了`task`之外的方法，这些方法对于构建项目来说非常方便。`file`就是这样一种方法，创建一个生成命名文件的任务。

```
$ rake ext/Makefile
$ ls ext/Makefile
ext/Makefile # it exists!
```

按照上面代码中的依赖链，我们可以提取编译 C 扩展所需的基本步骤:

```
cd ext
ruby extconf.rb
make
```

`make`是构建几乎所有 C 代码的标准，但是`extconf.rb`是新的。

```
# redcloth/ext/extconf.rb
require 'mkmf'
&nbsp;
dir_config('redcarpet')
create_makefile('redcarpet')
```

`mkmf` 的[文档证实了我们对这段代码的预期:](http://www.ruby-doc.org/stdlib/libdoc/mkmf/rdoc/index.html)

> 为扩展模块创建 Makefile 的模块

大概这编译了类似命名的`redcarpet.c`文件，所以让我们看看，我们应该有创建我们自己的扩展的基本要素。

```
// excerpt from redcarpet/ext/redcarpet.c
#include <stdio.h>
#include "ruby.h"
&nbsp;
static VALUE rb_cRedcarpet;
&nbsp;
// ...
&nbsp;
static VALUE
rb_redcarpet_toc(int argc, VALUE *argv, VALUE self)
{
// ...
}
&nbsp;
static VALUE
rb_redcarpet_to_html(int argc, VALUE *argv, VALUE self)
{
// ...
}
&nbsp;
void Init_redcarpet()
{
rb_cRedcarpet = rb_define_class("Redcarpet", rb_cObject);
rb_define_method(rb_cRedcarpet, "to_html", rb_redcarpet_to_html, -1);
rb_define_method(rb_cRedcarpet, "toc_content", rb_redcarpet_toc, -1);
}
```

这看起来并不可怕。`Init_redcarpet`方法建立一个类，然后向其中添加一些方法，这些方法映射到文件中定义的其他函数。我们能做到的。事实上，让我们！

## 我们自己的延伸

本着尽可能做最简单的事情的精神，让我们编写一个简单地将数字相加的 C 扩展。功能上可能没用，但学习有用！从我们对 Redcarpet 的调查中，我们知道创建我们的扩展只需要三个步骤:

1.  用 C 代码写一个`Init`函数。
2.  创建一个`extconf.rb`文件，它可以创建一个 Makefile 来编译 C 代码。
3.  运行 Makefile 来编译扩展。

当然，还有一个隐藏的第四步:测试它是否全部工作！

首先，C 代码:

```
// fastadd/fastadd.c
#include "ruby.h"
&nbsp;
static VALUE rb_cFastadd;
&nbsp;
void Init_fastadd()
{
rb_cFastadd = rb_define_class("Fastadd", rb_cObject);
}
```

然后是一些 extconf:

```
# fastadd/extconf.rb
require 'mkmf'
&nbsp;
dir_config('fastadd')
create_makefile('fastadd')
```

然后放在一起测试

```
$ ruby extconf.rb
Creating Makefile
$ make
gcc # ...
$ ruby -r./fastadd -e 'puts Fastadd.new'
#<Fastadd:0x0000010086dde0>
```

鸿运当头。这是一个真正的 Ruby 类，完全用 c 语言定义。我开始感到兴奋了。让我们回到红地毯代码，看看我们还能做些什么。

## 又是红地毯

在`redcarpet.c`中出现的第一个真正的逻辑是下面的`rb_redcarpet__render`方法:

```
VALUE text = rb_funcall(self, rb_intern("text"), 0);
```

请注意，“intern”是“转换为符号”的 Ruby 名称。在这种情况下，上面的代码看起来非常简单:调用一个名为“text”的实例方法。但是文本没有在文件的其他地方定义！如果您尝试在我们的 Fastadd 扩展中运行该代码，您将会看到默认情况下没有提供它(您会得到一个方法未找到错误)。Redcarpet 一定在做一些其他的恶作剧，如果它不在这个文件中，它一定在其他地方。返回 redcarpet 和 grep 的根目录进行“文本”。忽略`test`目录中的许多匹配，它揭示了`lib/redcarpet.rb`中`Redcarpet`类的另一个定义。

```
class Redcarpet
# Original Markdown formatted text.
attr_reader :text
&nbsp;
def initialize(text, *extensions)
@text = text
extensions.each { |e| send("#{e}=", true) }
end
end
&nbsp;
&nbsp;
require 'redcarpet.so'
```

多么厚颜无耻！这根本不是纯 C 的练习。Redcarpet 在 Ruby 中定义一个类，然后在 C 语言中重新定义(或添加)它。工作中的公开课。这真的很方便:我们不仅可以在 Ruby 中轻松地设置我们的大部分类，我们甚至可以提供一个默认的 Ruby 实现，然后在我们的 C 代码中覆盖它。

让我们将它添加到我们的`Fastadd`类中，因为它需要知道要添加什么数字。

```
# fastadd/fastadd.rb
class Fastadd
attr_reader :n
&nbsp;
def initialize(n)
@n = n
end
end
&nbsp;
require './fastadd.so'
```

## 实际工作

让我们让我们的`Fastadd`类能够给数字加 1。快如闪电！你会注意到 Redcarpet C 代码中有大量的`VALUE`类型——它们代表 Ruby 对象。我们需要一种方法来将它们转换成本地 C 类型，这样我们就可以在 C 语言中以自然的方式处理它们。我不确定如何做到这一点，但是搜索“ruby c extension convert VALUE to int”让我找到了这个漂亮的 tops 备忘单，它列出了一些用于进行这种转换的宏和函数。`NUM2INT`看起来对我们的目的特别方便。

```
// fastadd/fastadd.c
#include "ruby.h"
&nbsp;
static VALUE rb_cFastadd;
&nbsp;
static VALUE rb_fastadd_add_one(int argc, VALUE *argv, VALUE self)
{
int n = NUM2INT(rb_funcall(self, rb_intern("n"), 0));
&nbsp;
return INT2NUM(n + 1);
}
&nbsp;
&nbsp;
void Init_fastadd()
{
rb_cFastadd = rb_define_class("Fastadd", rb_cObject);
rb_define_method(rb_cFastadd, "add_one", rb_fastadd_add_one, -1);
}
```

尝试一下:

```
$ make
# gcc output
$ ruby -r./fastadd -e 'puts Fastadd.new(3).add_one'
4
```

赢家。

## 包扎

当然，这只是你能用 C 扩展做什么的开始。这里有一些额外的练习，你可以通过练习来提高你的技能:

*   我们[培育了](http://en.wikipedia.org/wiki/Cargo_cult_programming)红地毯代码，并最终将`#add_one`定义为一个参数数量可变的方法。将其正确定义为不带参数的方法。
*   对`@n`实例变量的访问是通过 reader 方法完成的。而是直接访问它。
*   调查 Redcarpet 需要在其 gemspec 中做哪些额外的工作来将 C 扩展作为 gem 的一部分。

请在评论中告诉我们你的进展。下周加入我们，在代码丛林中进行更多激动人心的冒险。

## 分享这篇文章