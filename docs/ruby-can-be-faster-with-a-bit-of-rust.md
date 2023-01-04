# Ruby 上一点铁锈会更快

> 原文：<https://www.sitepoint.com/ruby-can-be-faster-with-a-bit-of-rust/>

![rust](img/e6c9d6b1c34b79b0dc7caa996959ec60.png)

2013 年春天，萨姆·萨弗伦向 GitHub 提交了 [fast_blank](https://github.com/SamSaffron/fast_blank) 。它是一个 C 扩展，为 ActiveSupport 的`String#blank?`方法提供了一个快速的替代方法，该方法返回一个字符串是否只是空白。

在 2015 年的 Full Stack Fest 上，耶胡达·卡茨[发表了一篇演讲](https://www.youtube.com/watch?v=2BdJeSC4FFI)，讲述了他对《Rust》中的`fast_blank`进行重写的调查。他最初的目标是尝试一个简单的一行程序(不包括他的`Buf`实现),看看它比最初的 C 扩展慢多少。

```
extern "C" fn fast_blank(buf: Buf) -> bool {
  buf.as_slice().chars().all(|c| c.is_whitespace())
} 
```

令他惊讶的是，速度更快了。

## 什么是‘铁锈’？

Rust 是迄今为止创建一种现代、跨平台、系统编程语言的最坚实的尝试之一。

Mozilla 将 Rust 设计成一种语言，它可以提供现代网络浏览器所需的性能和安全性，同时比以前的静态语言更容易访问(至少在某些方面)。它还附带了出色的第一方包管理！我们没有宝石，而是有了板条箱。

但是这些好处是有代价的:有一个陡峭的学习曲线。毫无疑问，Rust 团队中的受虐狂设计了一种非常难懂的语言。有许多语法边缘情况，[包括表达式的范围语法不同于模式的语法这一事实](https://internals.rust-lang.org/t/vs-for-inclusive-ranges/1539)。高级教程经常引用不赞成使用的习惯用法，并且并不总是清楚为什么某一行会抛出错误。

这并不是说 Rust 有缺陷或设计不佳——只是青春期的混乱教养。对于那些能够在神秘的深渊中生存下来的人来说，Rust 承诺了安全性、速度和灵活性。

## 入门指南

如果你在 OSX，你可以用[自制软件](http://brew.sh)安装 Rust。确保你的自制软件是最新的，因为以前版本的 Rust 公式不包括`cargo`。这里用的是 Rust 1.4.0。

```
$ brew install rust 
```

`cargo`是 Rust 的项目/包经理。让我们从创建一个可执行项目开始:

```
$ cargo new hello_world --bin 
```

**Gemfile** 的锈当量是 **Cargo.toml** :

```
[package]
name = "hello_world"
version = "0.1.0"
authors = ["Robert Qualls <robert@example.com>"] 
```

如果我们看一下 **src/main.rs** ，我们可以看到源代码:

```
fn main() {
    println!("Hello, world!");
} 
```

让我们继续编译这个项目:

```
$ cargo build 
```

这将生成 **Cargo.lock** ，其处理方式应与 **Gemfile.lock** 相同。

```
$ cargo run
Hello 
```

## 静态编程

在 Ruby 中，运行时几乎任何事情都可能发生。这提供了很大的灵活性，但意味着编译成字节码时很少会被捕获。静态编程是一个不同的世界，主要是因为你现在必须处理*内存管理*。这包括管理指针、数据类型和分配。

基本术语表:

*   **数据类型**–编译器确定标识符地址范围的一种方式
*   **指针**–与存储器地址相关的标识符
*   **别名**–当两个指针指向同一个内存时
*   **悬空指针**–当指针指向释放的内存时
*   **内存泄漏**–在程序的生命周期中，未使用的内存从未被释放
*   **可变性**——在标识符的生命周期中将不同的值与标识符关联起来的能力

处理悬空指针和内存泄漏的传统方法是引入一个像 Ruby 一样的垃圾收集器。当变量不再被使用时，它们的内存会在收集器执行扫描时自动释放。垃圾收集器的问题是它们会导致不可预测的滞后峰值，并且会对语言的宿主环境造成额外的限制。

Rust 没有内置的垃圾收集器。同时，您永远不会手动释放内存。取消分配是由设计决定的。一旦您适应了编译代码所需的模式，您可能会忘记不涉及垃圾收集器。

如果你试图在 Rust 中做一些内存不安全的事情，它很可能根本无法编译。其实无论你怎么做，你的 Rust 代码很可能都不会编译。

## 所有权和借款

那么，Rust 是如何在不期望您手动释放内存的情况下运行的呢？

谈到管理内存，Rust 主要关心两件事:

1.  一个功能是*获得某物的所有权*还是*借用它*。
2.  资源是可变的(混乱中立的)还是不可变的(合法的好的)。

在非托管编程语言中，程序员负责分配和释放内存。例如，在 C 语言中，这是通过`malloc`和`free`函数来完成的。不幸的是，这使得程序员有可能在程序使用完内存之前释放内存。

在 Rust 中，**所有权是解除分配**的权利。当一个函数完成执行时，它拥有的任何东西都会自动从内存中释放出来。

例如，以下代码将会起作用:

```
fn print_vec(v: Vec<i32>) {
    println!("{:?}", v);
}

fn main() {
    let v = vec![1,2,3];
    print_vec(v);
} 
```

然而，对`print_vec`的调用赋予了它对我们的 vector 的所有权(以及解除分配的任务)。因此，如果我们试图调用它的第二次…

```
fn print_vec(v: Vec<i32>) {
    println!("{:?}", v);
}

fn main() {
    let v = vec![1,2,3];
    print_vec(v);
    print_vec(v);
} 
```

我们得到一个编译错误:

```
src/main.rs:7:15: 7:16 note: `v` moved here because it has type
`collections::vec::Vec<i32>`, which is non-copyable
src/main.rs:7     print_vec(v); 
```

这可以通过将向量借给带有`&`的函数调用来解决:

```
fn print_vec(v: &Vec<i32>) {
    println!("{:?}", v);
}

fn main() {
    let v = vec![1,2,3];
    print_vec(&v);
    print_vec(&v);
} 
```

目前代码没有修改向量。当我们试图这样做时会发生什么？

```
fn print_vec(v: &Vec<i32>) {
    println!("{:?}", v);
}

fn main() {
    let v = vec![1,2,3];
    v[0] = 0;
    print_vec(&v);
    print_vec(&v);
} 
```

我们得到另一个错误！

```
src/main.rs:7:5: 7:6 error: cannot borrow immutable local variable `v` as
mutable
src/main.rs:7     v[0] = 0; 
```

每当我们想在 Rust 中修改一个变量时，它必须被指定为可变的:

```
fn print_vec(v: &Vec<i32>) {
    println!("{:?}", v);
}

fn main() {
    let mut v = vec![1,2,3];
    v[0] = 0;
    print_vec(&v);
    print_vec(&v);
} 
```

如果您发现这种范式具有挑战性，请不要担心。尽管这是 Rust 中内存管理的默认方法，但是您也可以使用[引用计数](https://doc.rust-lang.org/alloc/rc/)，当引用的最后一个所有者不在时，它将释放引用。可以把它想象成垃圾收集精简版。

如果你想要一个跟踪垃圾收集器，有 [Manishearth/rust-gc](https://github.com/Manishearth/rust-gc/) 。最后，对于自行车收藏家来说，你有[菲茨根/培根-拉詹-cc](https://github.com/fitzgen/bacon-rajan-cc) 。

## 红宝石生锈

了解 Rust 工作原理的一个很好的方法是把它和 Ruby 放在一起看。

### 函数定义

红宝石:

```
def add(x, y)
  x + y
end

puts add(1, 2) 
```

生锈:

```
fn add(x: i32, y: i32) -> i32 { x + y }

fn main() {
    println!("{}", add(1,2));
} 
```

这里，`-> i32`设置函数返回类型，`i32`表示 32 位有符号整数。`{}`是 Rust 中的通用字符串插值器，它将把其余的参数按顺序插入到`println!`宏中。

### 动态数组

Rust 包括可以在运行时生长的`Vec`。

红宝石:

```
names = ["bobby", "harry", "sally"]
names << "thor" 
```

生锈:

```
fn main() {
    let mut names: Vec<&str> = vec!["bobby","harry","sally"];
    // Alternatively
    // let mut names = vec!["bobby","harry","sally"];
    names.push("thor");
} 
```

`vec!`是引用一个`Vec`的宏。

### 关闭

红宝石:

```
arr = [1, 2, 3]
puts arr.map { |i| i * 2 }.inspect 
```

生锈:

```
fn main() {
    let arr = [1, 2, 3];
    let mapped = arr.iter().map(|&x| x * 2);
    let output = mapped.collect::<Vec<i32>>();
    println!("{:?}", output);
} 
```

`{:?}`告诉插值器使用`std::fmt::Debug`特征的类型实现(想想 Java 接口或 Ruby mixin 模块)进行格式化。

### 希腊字母的第 11 个

红宝石:

```
is_even = ->(n) { n.even? }
puts is_even.call(5) 
```

生锈:

```
fn main() {
    let is_even = |n: i32| n % 2 == 0;
    println!("{:?}", is_even(5));
} 
```

### 类和实例方法

红宝石:

```
class Triangle
  attr_accessor :base, :height

  def initialize(base, height)
    @base, @height = base, height
  end

  def area
    (@base * @height) / 2.0
  end
end

triangle = Triangle.new(7, 5)
puts triangle.area 
```

生锈:

```
struct Triangle {
    base: f32,
    height: f32
}

impl Triangle {
    fn area(&self) -> f32 {
        (self.base * self.height) / 2f32
    }
}

fn main() {
    let triangle = Triangle { base: 7f32, height: 5f32 };
    println!("{}", triangle.area());
} 
```

在 Rust 中，`struct`表示数据，而`impl`表示行为。

一个`impl`实例方法的第一个参数是`self`、`&self`或`&mut self`的特例，这取决于所需的所有权级别。您可能认识到从 Python 中将`self`传递给实例方法。

*注意`area`中缺少分号是隐式返回。*

### 静态方法

红宝石:

```
module StaticMath
  def self.add(x, y)
    x + y
  end
end

puts StaticMath::add(1, 2) 
```

生锈:

```
struct StaticMath;
impl StaticMath {
    fn add(x: i32, y: i32) -> i32 {
        x + y
    }
}

fn main() {
    println!("{}", StaticMath::add(1,2));
} 
```

或者:

```
struct StaticMath;
impl StaticMath {
    fn add(&self, x: i32, y: i32) -> i32 {
        x + y
    }
}

fn main() {
    println!("{}", StaticMath.add(1,2));
} 
```

静态方法(Rust 中的“关联方法”)是由*而不是*创建的，指定其中一个`self`作为第一个参数，然后使用`::`而不是`.`来调用方法。尽管另一个例子看起来可行，但 Rust 团队可能并不打算以这种方式创建静态方法。

注意，在 Ruby 中，`::`或`.`都可以用来调用*的任何一个*方法，而在 Rust 中，这种区别更加明显。

## 制作图书馆

让我们利用我们对 Rust 的了解来创建一个快速库，并从 Ruby 中使用它。

```
$ cargo new libadd 
```

我们需要在 **Cargo.toml** 中添加一些行，这样 Rust 就知道我们正在制作一个动态库。

```
[lib]
name = "add"
crate-type = ["dylib"] 
```

注意`crate-type`中的`dylib`不是 OSX 特有的。它只是告诉 Rust 我们想要一个动态库，而不是默认的`rlib`(Rust 格式)。

我们需要添加我们希望公开给 **src/lib.rs** 的函数:

```
#[no_mangle]
pub extern "C" fn add(x: i32, y: i32) -> i32 {
  x + y
} 
```

现在可以编译这个库了:

```
$ cargo build --release 
```

Ruby 可以通过一个外来函数接口与 Rust 通信。`Fiddle`和`FFI`是允许我们这样做的两个库。

## 小提琴

自带 Ruby，所以你不需要安装任何东西。

```
require "fiddle"
require "fiddle/import"

module Rust
  extend Fiddle::Importer
  lib_ext = "dylib" if `uname` =~ /Darwin/
  lib_ext = "so" if `uname` =~ /Linux/
  dlload "./libadd/target/release/libadd.#{lib_ext}"
  extern 'int add(int, int)'
end

puts Rust.add(1, 2) 
```

## 自由流体指数

`ffi`是需要安装的宝石。

```
$ gem install ffi 
```

它的用法和`fiddle`非常相似。

```
require "ffi"

module Rust
  extend FFI::Library
  lib_ext = "dylib" if `uname` =~ /Darwin/
  lib_ext = "so" if `uname` =~ /Linux/
  ffi_lib "./libadd/target/release/libadd.#{lib_ext}"
  attach_function :add, [:int, :int], :int
end

puts Rust.add(1,2) 
```

## 结论

本文*所涵盖的内容仅仅触及了 Rust 语言的皮毛。此时，你最大的挑战将是学习如何安全地将 Rust 集成到你的 Ruby 项目中。如果让 Rust 负责释放，它可能会在 Ruby 使用完指针之前释放它们。如果您将解除分配留给 Ruby，那么该项目就容易出现内存泄漏。这似乎是社区目前正在讨论的问题。*

Rust 有可能变得过于神秘而无法被广泛采用。现在开源的 Swift 是否会登上系统编程的舞台还有待观察。但是现在，如果你想要快速安全的东西，你可能会在这种不那么腐蚀性的语言中找到一个朋友。

## 分享这篇文章