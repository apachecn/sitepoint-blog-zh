# 继续——被遗忘的陈述

> 原文：<https://www.sitepoint.com/continue-the-forgotten-statement/>

我是`continue`的忠实粉丝，部分原因只是因为它是一个积极鼓励的词(<q>继续，一切都好</q>)，但主要是因为它可以**减少代码，提高效率**，这几乎总是一件好事。

这里有一个简单的摘要:`continue`语句可以在迭代器中使用，比如一个`for`循环，并且意味着<q>继续下一次迭代</q>；这与`break`的说法相反，后者意味着<q>完全放弃这个循环</q>。

因此，每当迭代器中的代码完成该迭代时，我们可以使用`continue`语句来防止解释器读取不必要的代码，并减少我们必须键入的实际代码量。

例如，这段代码:

```
for(var i=0; i<ary.length; i++)
{
	if(ary[i] == 'foo')
	{
		//something
	}

	else
	{
		//whatever
	}	
}
```

也可以这样写:

```
for(var i=0; i<ary.length; i++)
{
	if(ary[i] == 'foo')
	{
		//something

		continue;
	}

	//whatever
}
```

我也非常喜欢将`continue`作为一种跳过我们从不感兴趣的条件的方法，比如在遍历对象的属性时忽略外部原型；所以与其这样:

```
for(var i in obj)
{
	if(obj.hasOwnProperty(i))
	{
		//whatever
	}
}
```

我们可以这样做:

```
for(var i in obj)
{
	if(!obj.hasOwnProperty(i)) { continue; }

	//whatever
}
```

那么真正的区别是什么呢？嗯，我们已经**避免了对支撑条件**的需要，这可以使代码更有效(因为解释者不必评估该条件)，并且我认为这也使代码更容易阅读——每一级支撑都引入了额外的认知负荷，因此减少它们是可取的。`continue`语句是在迭代代码中避免这种情况的一种方式，这也是我如此喜欢它们的原因。

## 分享这篇文章