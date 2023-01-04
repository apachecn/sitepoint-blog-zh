# 如何创建更友好的随机密码

> 原文：<https://www.sitepoint.com/how-to-create-friendlier-random-passwords/>

当谈到可访问性时，web 应用程序的一个几乎总是被忽略的方面是任何随机生成的字符串是多么容易阅读。如果你足够幸运，拥有近乎完美的视力，并且没有阅读障碍等学习或认知障碍，你可能不会在阅读随机生成的字符串时遇到任何问题，但对于许多用户来说，区分零和大写 o，1 和小写 l，甚至字母 b 和 d 可能很困难。

因此，在最近一个需要我根据请求自动生成密码的项目中，我决定用下面的 PHP 函数来实践这一点:

```
 function GenerateRandomString($length) {
	$characters = array('2', '3', '4', '5', '6', '7', '8', '9',
			'a', 'c', 'e', 'f', 'g', 'h', 'i', 'j', 'k', 'm', 'p',
			'q', 's', 't', 'u', 'v', 'w', 'x', 'y', 'z');
	$randomString = "";
	while(strlen($randomString) < $length) {
		$randomCharacterIndex = rand(0, count($characters) - 1);
		$randomString .= $characters[$randomCharacterIndex];
	}
	return $randomString;
} 
```

这里没什么特别的，除了前面提到的有时很麻烦的字符对被删除了。

现在，如果我们使用这个函数来生成密码，那么创建的密码将会更不安全——这个函数只能创建 28 个<sup>5</sup>(1700 万)不同的五字符密码，而如果我包括了丢失的字符，这个数字将会是 36 个<sup>5</sup>(6000 万)。这是一个你必须接受的交换条件，以换取更少的沮丧用户登录你的网站有困难，这可以通过强迫用户更改他们生成的密码来缓解。

**注意**:如果你知道任何其他人们有困难的字符，留下评论，我会更新这个帖子。谢谢！

## 分享这篇文章