# 长鳍金枪鱼:建筑。使用 Rake 的网络应用程序

> 原文：<https://www.sitepoint.com/albacore-building-net-applications-with-rake/>

如果你是一名. NET 开发人员，想要迁移到 Ruby，可以直接开始使用 Ruby 的方法之一就是使用 Albacore。在本文中，我们将看看如何使用 Albacore 来获取一个现有的 ASP.NET web 应用程序项目，添加一个 Rakefile，并构建这个项目。

## 什么是长鳍金枪鱼？

Albacore 是 Rake 的一组扩展，专门处理建筑。基于. NET 的项目。它的任务是构建、重新构建和清理项目，运行 nunit、xunit 或 mstest 单元测试，复制文件和扩展模板，以及生成程序集信息。

。NET 有它自己的构建系统 MSBuild。如果您非常乐意使用它来构建您的项目，那就没问题。您可能不会从本文的其余部分获得太多信息。如果你喜欢长鳍金枪鱼的声音，并认为它可能适合你，那么继续读下去。

## 背景

不过，首先，如果您不熟悉 Rake，请提供一点背景知识(如果您确实知道 Rake 是什么，请随意跳过这一部分)。Rake 是 Ruby Make，一个类似 Make 的 Ruby 构建程序。它围绕着任务，这些任务存在于 Rakefile 中。

一个简单的示例 Rakefile 如下所示: