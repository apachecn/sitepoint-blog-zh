# 使用你的网络开发技能，建立一个电子桌面应用程序

> 原文：<https://www.sitepoint.com/use-your-web-dev-skills-to-build-a-desktop-app-with-electron/>

*这篇文章最初发表在[Okta 开发者博客](https://developer.okta.com/blog/2018/09/17/desktop-app-electron-authentication)上。感谢您对使 SitePoint 成为可能的合作伙伴的支持。*

Electron 是一个框架，用于使用 JavaScript、HTML 和 CSS 等 web 技术构建跨平台的桌面应用程序。它是为 GitHub 的 Atom 编辑器创建的，自那以后得到了广泛的采用。电子为我日常使用的几个应用提供动力:Slack、Kitematic 和 Visual Studio Code 等等。

电子 2.0 于 2018 年 5 月初发布，同时发布的还有对项目的更改，以遵守严格的语义版本化。这对开发者来说是个好消息，因为这意味着补丁发布会更加稳定，新功能只会出现在主要版本中。当开源项目正确地使用语义版本控制时，最终用户不会经常看到突破性的变化，并且倾向于高效。

电子 3.0 于 2018 年 9 月 18 日发布，包含主要版本凸起和一些新功能。更多信息见[电子博客](https://electronjs.org/blog/electron-3-0)。

用 web 技术开发桌面应用程序是一个吸引人的想法。在我看来，杰拉德·桑斯的这条推文一针见血:

> 人们总是问什么是最好的框架。他们应该问的是，使用 framework X，他们需要什么样的技能才能高效工作？问题通常是他们缺乏技能，而不是他们使用哪个框架[# angular](https://twitter.com/hashtag/angular?src=hash&ref_src=twsrc%5Etfw)[# react js](https://twitter.com/hashtag/reactjs?src=hash&ref_src=twsrc%5Etfw)[# vuejs](https://twitter.com/hashtag/vuejs?src=hash&ref_src=twsrc%5Etfw)[# JavaScript](https://twitter.com/hashtag/javascript?src=hash&ref_src=twsrc%5Etfw)✨🚀[pic.twitter.com/8DWwM4PJoT](https://t.co/8DWwM4PJoT)
> 
> — ᐸGerardSans/ᐳ😉🇬🇧(@ gerardsans)[2018 年 8 月 5 日](https://twitter.com/gerardsans/status/1026040566868529152?ref_src=twsrc%5Etfw)