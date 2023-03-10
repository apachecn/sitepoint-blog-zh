# PHPBench.com:实况 PHP 基准测试，揭开“最佳实践”的神秘面纱

> 原文：<https://www.sitepoint.com/phpbench-live-php-benchmarks-best-practices/>

作为 SitePoint 博客的新投稿人，我将涉及 PHP web 开发、JavaScript 和通用 web 技术。

当谈到优化 PHP 的性能时，可用的资源是无穷无尽的，相互冲突的观点也是无穷无尽的。每个人似乎都有自己编写“快速”PHP 代码的方法；使用单引号、避免 require_once()和在 is_array()之前使用 isset()是一些最常见的方法。但是，由于可靠的基准很少，我们如何知道这些技术——通常被吹捧为“性能最佳实践”——是否真的带来了好处呢？Chris Vincent 在 PHPBench.com[的新 PHP 基准测试套件旨在“澄清”PHP 性能技术，对各种方法的实际效果有一个简单、全面的了解。

基准测试套件涵盖了所有常见的基础，采用一个简单的任务——比如迭代一个数组——并对几乎所有可能实现它的方法进行速度测试。然而，最重要的是，Chris 将原始数据从聚光灯下转移出来，转而关注这些选项之间的比较。每个测试都将最快的技术作为执行时间的基础值；所有其他选项都以与此相关的百分比来衡量。例如，`foreach($aHash as $val)`的脚本执行时间为 558%，而`while(list($key) = each($aHash)) $tmp[] = $aHash[$key]`为 100%。](http://www.phpbench.com/)

测试分数也是实时生成的；刷新页面将产生一组略有不同的结果。Chris 使用相对简单的结果页面的标题来建议刷新几次，只是为了确保一致性，并避免对任何特定测试的一次性影响。就个人而言，我更喜欢在受控的环境中执行测试，并且在每次添加新的测试时重新生成测试；异常对任何人都没有帮助，除了操作简单之外，实时基准测试几乎没有提供任何真正的好处。为透明起见，提供了每个测试的代码。

他目前的一套测试非常全面；不少于 13 个测试比较了各种`echo`和`print`调用的性能，有足够的多样性来检查任何使用场景。他也接受新测试的建议。然而，最重要的是，他的许多测试有助于识别最佳实践的价值。例如，PHP 经常因为`==`比较的模糊结果而受到批评；控制结构测试告诉我们,`===`不仅更安全，而且执行时间大约是它的 2/3。

虽然大多数测试显示了微不足道的性能差异，但还是有一些有趣的结果。例如，迭代一个数组并修改每个元素，使用`while(list($key) = each($aHash))`比简单的`foreach ($aHash as $key=>$val)`快 100 倍，而`array_keys()`比`while(list($key,$val) = each($aHash));`慢大约 20 倍。

当然，下一步是将测试套件下载到您的本地机器或服务器上，并自己运行测试，看看您的生产环境的因素如何影响结果；克里斯的目标是在不久的将来让这成为可能。列出的许多技术显然要么是内存高效型的，要么是 CPU 周期高效型的，而基础设施的限制将决定您在向外扩展之前的发展方向。尽管如此，管理代码中的瓶颈确实可以帮助您充分利用您的服务器——毕竟，如果您可以实现 100 倍的速度，为什么不呢？——PHPBench.com 可能会成为 PHP 性能的权威参考。

许多评论者已经注意到了测试的有用性和可靠性的问题。正如评论中对克里斯[的描述，在创建网站时考虑他的意图很重要。在所有“使用这种语法，因为它稍微快一点”的帖子中，PHPBench.com 是为了看看使用一种特定的语法是否有任何实际的好处而建立的；大多数测试表明，显然没有。我自己做了一些测试，也看到了类似的结果，但是如果有些地方看起来不对劲，请随时向 Chris 指出。该网站并不试图从单引号和双引号中找出非实质性的性能优势。进一步来说，PHP Bench 可以用来测量算法性能和其他更密集的程序；目前，基本的语法测试只是反映了系统的潜力。希望这能澄清一些事情。](https://www.sitepoint.com/phpbench-live-php-benchmarks-best-practices/#comment-738130)

## 分享这篇文章