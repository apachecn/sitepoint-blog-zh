# 与 Diaspora*建立自己的社交网络:简介

> 原文：<https://www.sitepoint.com/build-your-own-social-network-with-diaspora-an-introduction/>

## 什么是 Diaspora*？

可以从两个不同的角度来看待 Diaspora :首先，从最终用户的角度来看，他们可以发现 Diaspora 与其他社交网络非常相似，如 *Google+* 、*脸书*，甚至 *Twitter* 。我曾经认为 Diaspora 是不同的，有一些独特的功能，直到 *Google+* 出现并包含了许多这些功能。

从另一个角度来看，Diaspora 是从开发者的角度来看的，是一个社交网络引擎。根据你的经验，在几周内，甚至几天内，建立一个具有复杂功能和非常简单优雅的用户界面的社交网络是可能的。

## 为什么流散？

*Diaspora* 是开源和分布式的，这意味着您可以将您部署的实例与其他部署的实例连接起来，并使一个巨大的社交网络完全连接起来，同时在您的服务器上维护您的数据。此外，它是用 Ruby on Rails 编写的，UI 使用了 Backbone。

## 装置

随着当天介绍的结束，让我们看看*散居*的行动。首先，您需要安装一些依赖项:

***Ruby*** :你可以在本教程中找到你需要的一切[在 Ubuntu 上安装 Ruby 与 RVM](https://www.sitepoint.com/installing-ruby-with-rvm-on-ubuntu/)

***rails*** :这两个教程对安装 Rails 很有帮助 [Rails 简介，深潜:安装 Rails，第一部分](https://www.sitepoint.com/rails-development-101-installing-rails-part-one/)，这个[位置:安装 Rails 第二部分](https://www.sitepoint.com/rails-development-101-installing-rails-part-2/)

***MySQL*** :至少出于开发的目的，你将需要 *MySQL* ，所以这个命令将为你安装它并运行 MySQL 服务器