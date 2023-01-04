# 测试驱动到分心

> 原文：<https://www.sitepoint.com/test-driven-to-distraction/>

Ruby 和 TDD 是不可分割的。这种语言有助于测试驱动的开发，以至于它将`Test::Unit`库作为标准。

我们都知道我们应该测试，并且这个过程会产生更健壮、更高质量的代码。但是，当测试经常比被测代码多很多行的时候，我们从编写测试中还能得到什么好处呢？

在整篇文章中，我将使用 [MiniTest::Spec](http://bfts.rubyforge.org/minitest/MiniTest/Spec.html) 。这是一个超级酷的规范风格的“框架”,也是 Ruby 1.9 的标准配置。我喜欢 [RSpec](https://www.relishapp.com/rspec) ，手边有相同类型的语法是一大优势。但是您选择的测试框架在这里并不重要，所有讨论的内容都可以应用。尽管如此，我还是强烈推荐尝试 MiniSpec。

## 为什么要做这些测试？

我们已经说过测试你的代码会使它更加健壮并提高质量。嗯，严格来说那不是真的。您不会免费获得健壮性或质量，因为您的代码只会和您的测试一样好或健壮。

因此，我们将编写好的测试，我们将产生处理许多不同场景的代码，但这就是我们得到的全部吗？这似乎不值得努力。凭借经验和对我们编码背后故事的良好了解，我们可以完成健壮的代码，而不用浪费时间编写测试。

我相信你已经知道，“我们得到更多”。

测试给了我们防止退化的能力。我们发现一个 bug，编写一个测试，实现解决方案，然后这个 bug 就再也不会出现了。

拥有测试也给了我们自信地重构代码的能力。我无法想象在没有测试存在的情况下，在任何合适规模的应用程序中工作。如果没有保护遗留代码的测试防火墙，我如何实现一个新特性呢？

测试也形成了一个你的同事容易理解的合同或规范。测试将解释我们的对象的预期行为。对于像 Ruby 这样嘲笑正式接口结构的语言，在与其他开发人员交流时，测试是必须的。

在更适合我们的开发风格的层面上，编写和发展测试的行为实际上将我们引向精炼的解决方案。当我们一步步通过测试时，我们获得了该领域的知识。

测试也很有趣。但是，让我们把注意力集中在我们刚刚谈到的关于 TDD 的内容上。

## 红色，绿色，开始工作

引用“Red，Green，Refactor”使得 TDD 的整个过程看起来几乎微不足道。您编写测试，看着它失败，并尽可能少地做些事情来使它通过。现在，我们有几个选择。

1.  返工有问题的测试？
2.  添加更多测试？
3.  绿灯下重构代码？

我敢肯定，我们以前都读过这本书，并认为'叶，没问题'。但是，当我们谈到编写这些小魔术的机制时，真的那么简单吗？嗯，考试很快就会变成彻底的令人沮丧和没有生产力的杂务。他们变得匆忙，就像一块块的垃圾，简单地在‘优秀开发人员清单’上打了个勾，这个清单只存在于我们自己的头脑中，我们继续前进，不愿意再玩那段代码。

让我们不要接近那个不幸的地方。取而代之的是，我们将收集我们想要达到的目标的知识，一步一步地走，直到我们到达那里。我可以在这里举个例子，它处理一个包含固定宽度，非分隔数据的文件。

我们有一个示例文件，它代表了卡通片*中对学校*进行编码的班级。该文件有一个定义类名的头，一个提供所包含的行的校验和的尾，以及包含所有学生的名字、姓氏、年龄和性别的数据行。

```
Test Unit 101
WIGGUM        RALF          13MALE
COYOTE        WYLIE         72MALE
RABBIT        JESSICA       25FEMALE
003
```

我们的目标是什么？我们想处理这些文件。我们希望验证文件，提取内容以备后用。然而现在，我们只是在控制台上把它吐出来。

因此，让我们来看看驱动代码来完成这一目标的第一轮测试。

```
require 'minitest/spec'
require 'minitest/autorun'

require './student_file'

describe StudentFile do

  describe "parse" do
    it "will return an instance of StudentFile" do
      StudentFile.parse('test_file.txt').must_be_kind_of(StudentFile)
    end
  end

  it "will return the correct class name" do
    valid_file.class_name.must_equal "Class Name"
  end

  it "will return true if the file is valid" do
    valid_file.valid_file?.must_be :==, true
  end

  it "will return false if the file is invalid" do
    invalid_file.valid_file?.must_be :==, false
  end

end

def valid_file
  StudentFile.new(["Class Namen", "RABBIT        JESSICA       25FEMALEn", "001n"])
end

def invalid_file
  StudentFile.new(["Class Name"])
end
```

所以，我们是其中的一部分。我们已经确定应该有一个类级方法`parse`，它接受一个文件名并返回一个新的`StudentFile`实例。我们现在可以测试类名是否是我们期望的，并验证文件中期望的校验和行数。

## 使用代码进行转换

在阅读[get Real](http://gettingreal.37signals.com/)的时候，有一章是讲听你的代码反推。引导您找到廉价轻便的功能解决方案。对于测试，我发现如果它很难测试，它可能没有被设计得像它应该的那样好，并开始重新思考我的界面。因此，除了便宜和轻便之外，我们在某个地方遗漏了一个步骤，这使得测试过程比它应该的更加困难。当我们来测试我们的同学对控制台的输出时，我们遇到了几个有趣的问题。

我们如何在控制台上测试输出？毫无疑问，经验丰富的专业人士会知道，但这让我重新思考我们试图实现的目标。我之前提到过“把它吐到控制台”，这只是一个临时输出的无聊评论，让我们有点头疼。然而，如果我们只关注输出数据而不是输出到哪里，我们就会看到一个简单的解决方案。我们将输出传递给构造函数。`StringIO`将很好地完成测试目的，我们可以为`STDOUT`控制台设置一个默认值，瞧！我们有更容易测试的东西，同时也更加灵活。

```
def self.parse(filename, output=STDOUT)
  f = File.new(filename)
  self.new(f.readlines, output)
end
```

这使得我们的测试看起来像

```
it "will display error if file is invalid" do
  out = StringIO.new
  invalid_file(out).print
  out.string.must_be :==, "INVALID FILE"
end

def invalid_file(op=STDOUT)
  StudentFile.new(["An invalid file"], op)
end
```

## 尽情摇摆吧

在我们进一步讨论之前，让我们简单地讨论一下 mocks 和 stubs。当考虑将解析后的内容发送到控制台时，我们可以使用 MiniTest [嘲讽框架](http://bfts.rubyforge.org/minitest/MiniTest/Mock.html)或类似于 [flexmock](http://flexmock.rubyforge.org/) 的东西。然而，对于这个例子，我觉得没有必要，因为`StringIO`会做得一样好。Als，o 输出在我们测试的范围内。嘲笑那种事情真的是个好主意吗？

我倾向于只在少数情况下使用模拟。

1.  当依赖第三方(如 gem 或网络服务)的响应/行为时
2.  当依赖于当前域之外的一段代码的响应/行为时。(例如，已经在其他地方测试过的数据库包装、电子邮件脚本)
3.  当它允许我在一个复杂的测试中关注一个元素时。

第三种情况不会经常发生。然而，当我们有一段有很多依赖项的代码时，模拟这些依赖项是有意义的。

## 继续写代码

我们现在有一种方法来测试控制台的输出。在继续之前，让我们看看文件中的数据。我的第一个实现看起来像这样:

```
def students
  row = []
  @lines[1..-2].each do |line|
    row << ("| " << name(line) << "| "  << age(line) << "| " << gender(line) << "|")
  end
  row.join("n")
end 

def name(row)
  (row[14..27].strip << " " << row[0..13].strip).ljust(28)
end

def age(row)
  row[28..29].ljust(4)
end

def gender(row)
  row[30..-1].strip.ljust(13)
end
```

即使是我写的时候，感觉也不对劲。传递那个`line`变量感觉太可怕了。然而，我通过了测试，所以我现在可以放心地进行重构，即使是在将学生行提取到它自己的类中的时候。

通过将学生数据行提取到它们自己的对象中，我们可以为这些行编写测试，这样就不需要在我们的学生文件测试中测试这些数据行。

```
describe StudentLine do

  before do
   @student = StudentLine.new("RABBIT        JESSICA       25FEMALE")
  end

  it "will return the name of the student" do
    @student.full_name.musti_equal "JESSICA RABBIT"
end

it "will return the age of the student" do
  @student.age.must_equal "25"
  end

  it "should return the gender" do
    @student.gender.must_equal "FEMALE"
  end

end
```

上面的类不仅清理了`StudentFile`类的实现，还将学生文件的测试代码与从一串文本中解析学生数据的细节隔离开来。因此，如果我们像这样将数据的打印包装在一个私有方法中(从技术上讲，我们不应该测试它):

```
def initialize(lines, output=STDOUT)
  @class_name = lines.shift.strip
  @footer = lines.pop
  @lines = []

  lines.each do |line|
    @lines << StudentLine.new(line) if line.length > 33
  end

  @output = output
end

def print
  if self.valid_file?
    @output.print formatted_output
  else
    @output.print "INVALID FILE"
  end
end

private

def formatted_output
  [class_name.upcase, separator, header, separator, students, separator].join("n")
end

def header
  "| STUDENT                     | AGE | GENDER       |"
end

def separator
  "+-----------------------------+-----+--------------+"
end

def students
  row = []
  @lines.each do |line|
    row << ("| " << line.full_name.ljust(28) << "| "  << line.age.ljust(4) << "| " << line.gender.ljust(13) << "|")
  end
  row.join("n")
end
```

## 包扎

我们有一个脚本来解析我们的文件并产生我们想要的结果。您可以在此[要点](https://gist.github.com/1308123)处查看完成的源代码。事实是，我们又进入了重构阶段。我们可以再进行几次交互，进一步清理代码。然而，我们不能一直这样做。我们必须在某个时候划清界限，而不是纠结于我们的代码没有持续重构所能带来的那么令人惊叹。它可能永远也不会到达那里。然而，我们有一个经过充分测试的脚本。它做我们需要的，下一次我们有能力给它一些爱，这正是我们要做的。我们的测试支持我们。

希望这篇文章表明测试不是一件“好事”，它们对任何项目都是必不可少的。有大量的工具可供我们使用，我强烈建议您尝试一下`MiniTest::Spec`。我给你一些关于生产性测试的提示:

*   不要管哪个测试框架更好，都有可取之处。更重要的是，你实际上是在测试你的代码。
*   使用您的测试来感受您的工作领域，小步骤远远优于大型集成风格的测试。
*   专注于你试图在微观层面上实现的目标，但不要忘记驱动你测试的更大的图景。
*   模仿和存根都很棒，但是不要过度使用它们。
*   重构步骤有很多工作，不要吝啬。
*   划清界限。我们可以永远重构。尽可能经常地付出一点爱。

## 分享这篇文章