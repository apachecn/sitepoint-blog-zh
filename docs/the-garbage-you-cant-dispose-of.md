# 你处理不掉的垃圾…

> 原文：<https://www.sitepoint.com/the-garbage-you-cant-dispose-of/>

看来[杰夫的](http://www.procata.com/blog)在 [WACT](http://wact.sourceforge.net) 探索 [Rephlux 和 PHP 内存使用](http://www.procata.com/blog/archives/2004/05/27/rephlux-and-php-memory-usage/)时有了不愉快的发现。

将以下内容添加到 WACT 的单元测试套件中(该套件基本上执行了 WACT 拥有的所有内容)；

 `foreach (array_keys($GLOBALS) as $key) {
echo "$key=" . strlen(serialize($GLOBALS[$key]));
}`

这显示:' _ PEAR _ destructor _ object _ list = 146270 '

> 为了支持这个 gem，PEAR 将每个从 PEAR 继承的对象实例的引用放入全局变量 _PEAR_destructor_object_list 中。对象会一直停留在那里，直到解构关闭函数被调用。这个引用可以防止在脚本的生命周期中对任何从 PEAR 继承的对象或它们引用的内存进行垃圾收集。它这样做没有任何好的理由。

有问题的文件是 PEAR.php 的。事实上，我是罪魁祸首，因为我在 [XML_HTMLSax](http://pear.php.net/XML_HTMLSax) 中扩展了它(现在在最新的 3.0.0 版本中已经过时)。

我猜还有其他项目，比如 [PhpDocumentor](http://pear.php.net/package/PhpDocumentor) 以及任何通过 cron 完成“类似构建”任务的人，他们使用 PEAR 代码也可以在不贡献内存的情况下完成任务。对于 PHP web 应用程序来说，这可能不是什么大问题，但是仍然会浪费开销。

也许当 PEAR 包开发人员考虑 PHP5 支持和打破向后兼容性时，另一个任务可能是修改任何直接扩展 PEAR.php 的类。更好的方法是将 PEAR.php 本身削减为具有单一方法的单一类——isError()——这大概是人们实际使用的唯一方法。

(更新)-使这两个方法 isError 和 raiseError 打字太快…

## 分享这篇文章