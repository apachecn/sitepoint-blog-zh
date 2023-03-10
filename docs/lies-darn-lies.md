# 谎言！该死的谎言！

> 原文：<https://www.sitepoint.com/lies-darn-lies/>

通过电子邮件与 Marcus Boerger 谈论关于 T2 PHP 5 标准库的文章，需要坦白；)

关于我使用的 DirectoryReader 示例，Marcus 提出了一个非常好的观点；

> 您的 DirectoryReader 是错误的。它在 valid 中执行非文件跳过，但应该在 next()中完成。

> 但是在必要的改变之后，它仍然有一个主要的设计缺陷。它使用递归。迭代器是关于展开的。

> 所以你应该做的是防止这种递归，如果你看看结果，看看你花了多少时间来正确地完成它，你就会明白为什么使用 FilterIterator 会更好。这可以用来很容易地过滤掉非文件，它清楚地说明了为什么迭代器可以用于函数式编程——在这种情况下，FilterIterator 提供的过滤算法不知道任何关于你的数据的事情。

下面是使用它的问题类和代码；

 `class DirectoryReader extends DirectoryIterator {`

 `function _ _ construct($ path){
parent::_ _ construct($ path)；
}

函数 current() {
返回 parent::getFileName()；
}

函数 valid(){
if(parent::valid()){
if(！parent::is file()){
parent::next()；
return $ this->valid()；
}
还真；
}
返回 FALSE
}

函数 rewind(){
parent::rewind()；
}
}

//为当前目录创建一个目录阅读器
$Reader = new DirectoryReader('。/');` 

`// Loop through the files in the directory ?!?
foreach ( $Reader as $Item ) {
echo $Item.'
';
}`

有人想拿起关塔那摩湾重新实施吗？

按照我对 Marcus 的理解，替代方案是使用 DirectoryIterator *本身*，然后将其传递给扩展 [FilterIterator](http://www.php.net/~helly/php/ext/spl/classFilterIterator.html) (或者更好地扩展[directory filter dots](http://www.php.net/~helly/php/ext/spl/classDirectoryFilterDots.html))—“规范”是只列出给定目录中的文件，忽略父节点和当前节点。

## 分享这篇文章