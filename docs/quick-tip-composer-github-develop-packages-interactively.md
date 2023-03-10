# 快速提示:Composer & Github——交互式开发包

> 原文：<https://www.sitepoint.com/quick-tip-composer-github-develop-packages-interactively/>

在这个快速技巧中，我将向您展示如何结合使用 [Composer](https://getcomposer.org) 和 Github 来简化您的包开发过程。

## 使用 Composer 存储库

假设您有一个托管在 Github 上的包，并且希望在项目中使用它的同时开发它。设置好 Github 库之后，您可以使用 [Composer 库](https://getcomposer.org/doc/05-repositories.md#vcs)配置来告诉 Composer 您的代码库。
假设我在 Github 上有一个 URL 为`https://github.com/Whyounes/laravel5-twig/`的包，我的包`composer.json`文件如下所示。

```
// composer.json

{
  "name": "whyounes/laravel5-twig",
  "description": "Twig for Laravel 5"
}
```

Composer 有一种很好的方式通过 Github 或任何 VCS 加载包。你只需指定你的回购网址，它会自动扫描你的`composer.json`包信息。请记住，在安装或更新时，存储库比[包装商](https://packagist.org)拥有更高的优先级。

```
// composer.json

"require": {
	"laravel/framework": "5.0.*",
	"whyounes/laravel5-twig": "dev-master"
},
"repositories": [
    {
        "type": "vcs",
    	"url": "https://github.com/Whyounes/laravel5-twig"
	}
]
```

现在你可以继续处理你的本地包，并把你的修改推送到 Github，如果你对本地包做了一些修改，并在推送到 Github 之前运行`composer update`，你会丢失它们。
分支用于版本化你的包。版本名可以像`1.0`、`1.0.*`等这样编号，或者你必须在你的分支名前面加上`dev-*`，在我们的例子中`dev-master`版本将寻找`master`分支。你可以查看[文档](https://getcomposer.org/doc/articles/aliases.md#aliases)了解更多信息。

## 分享这篇文章