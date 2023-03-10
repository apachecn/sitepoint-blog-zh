# 为什么我喜欢围棋编程

> 原文：<https://www.sitepoint.com/love-programming-go/>

我用 go 编程已经有几年了，虽然它在开始时需要一点时间，但它已经成为我选择的 Go 语言，因为它的速度、可靠性和整体生产力。我在大大小小的项目中都用过它，它从未让我失望过。我挑选了一些让 Go 脱颖而出的东西，这些东西总结了是什么让编写 Go 成为一种乐趣。

## 强大的标准库

很多语言和它们的生态系统都有一定程度的“第三方库”机构知识。“哦，不要使用内置的 CSV 解析器，使用 FastCSV”。“你在用薄的？你应该用独角兽”。“你在使用节点？IO.js 就是它所在的地方”。这种碎片化通常是由于标准库特性的实现不太完美，并且为语言的进入设置了很高的障碍。Go 语言以拥有坚如磐石的标准库而闻名。通常情况下，标准库包含了编写应用程序所需的全部内容。例如，它有一个内置的“http”堆栈，可以毫不费力地为您的 web 应用程序提供服务——谷歌的下载服务(dl.google.com)使用这个库来提供 Chrome、Earth、Android SDK 和其他大型谷歌下载。

## 静态打字

由于过去一直从事动态脚本编写(PHP 和 Ruby ),我最初对静态类型语言持谨慎态度。在经历了更新函数期望和回报的第一次挫折后，我现在害怕回到动态类型。类型检查器能够发现不正确的函数名和类型分配。因为当提供错误的类型时，我不必再编写测试来断言我的代码的错误行为，也不必检查方法名称是否正确，所以我的测试套件能够专注于预期的业务逻辑，而不是这些基本的断言。

## 快速编译

Go 不仅是一种快速的语言，而且针对编译时间进行了优化。实际上，这意味着你的 Go 二进制文件应该在两三秒内编译完成。这对开发人员的生产力来说是一个重大的胜利。编写应用程序时，在代码和命令行之间快速切换的能力极其重要。如果编译花费大量时间，它会将开发人员排除在他们的流程之外。即使 10 秒钟也足以让你的大脑关闭；你切换上下文并打开 Reddit 或 Hacker News，然后在编译完成后的某个时候，你记得返回并查看结果。这种持续的环境转换不是人类擅长的事情，你的整体生产力也不是最佳状态。我在围棋中成功避免了这种情况。

## 简单并发

通过使用“goroutines”异步运行代码和“channels”进行通信，向 Go 应用程序添加并发性非常简单。Go 有一个总结沟通模式的口头禅:

> 不通过共享内存进行交流；相反，通过交流来分享记忆。

通道提供了发送和接收数据的机制。这些通道确保了严格的排序，并提供了同步并发代码的方法。通过跨通道传递数据，而不是访问共享变量，不需要锁定对数据的访问，并且在其他语言中可能以复杂和意想不到的方式失败的任务可以在少量 Go 中简洁地编写。通过在多个 cpu 内核上运行 goroutines，您还可以轻松释放运行代码的硬件的全部能力。

## 官方格式

Go 对格式非常固执己见，这可能不适合某些开发人员。Go 自带的“gofmt”工具会自动格式化你的代码，以符合格式化标准。通过将这个工具与编辑器的保存命令挂钩，你可以确保你的文件格式正确。在代码审查期间，不再有“修复空白”或“请在此换行”的注释——您可以专注于重要的事情，代码的业务逻辑。看到我的代码自动格式化仍然让我兴奋不已，从那以后，我一直试图在我使用的其他语言中引入工具来实现这一点。一旦你离开，你就再也不会回来了。

## 面对错误处理

围棋中的错误处理是一等公民。您从任何可能发生错误的函数接收错误。这些通常是函数返回的第二个值，因此这种代码非常普遍:

```
data, err := db.Query(“SELECT * FROM table”)
if err != nil {
	// Handle your error here, perhaps by returning it
	// from this function for the calling code to deal with
}
```

将错误作为代码流程中不可或缺的一部分，会让您思考错误对您编写的每一段代码意味着什么。我可以诚实地说，在我的一生中，我从来没有这么多地考虑过错误，这是一件好事。我现在确切地知道我的代码的失败条件是什么样的，以及它们是如何被处理的。正因为如此，Go 代码是健壮的代码。忽略错误是一个有意识的决定，很少有人这样做。虽然有些人可能认为这很冗长，或者不整洁，而且它确实给代码增加了一些行，但是回报远远超过了成本。

如果这些特质中的任何一个引起了你的共鸣，我鼓励你去尝试一下。Learnable 刚刚发布了我的书“[用 Go](https://dashboard.tinypass.com/download?did=Docuoki) 提升你的 Web 应用程序”，这本书将带你从一个完全的 Go 新手，立刻开始编写 Go Web 应用程序。

[![Go_Book_Cover_AB](img/230f5add4d6bfe865eda4a9b98cec063.png)](https://dashboard.tinypass.com/download?did=Docuoki)[Buy This Book!](https://dashboard.tinypass.com/download?did=Docuoki)

## 分享这篇文章