# 用载波处理图像

> 原文：<https://www.sitepoint.com/processing-images-with-carrierwave/>

CarrierWave 是一个管理 Rails 应用程序中上传文件的宝石，在本教程中，我们将经历上传和处理图像的过程。

现在让我们说，我们希望有一个应用程序，用户可以上传图像，并显示在画廊。

## 入门指南

我们需要做的第一件事是创建一个新的 rails 项目，我们将在其中添加图像上传和处理功能，所以继续启动一个终端窗口并执行以下命令:

```
$> rails new your_proyect_name 
```

`If you already have a created project you can skip this step.`

进入项目的目录，在你最喜欢的文本编辑器中打开它，打开 gem 文件，到文件的底部，在那里添加 [carrierwave](https://github.com/jnicklas/carrierwave) gem。

```
gem 'carrierwave' 
```

将 gem 添加到 gem 文件后，返回到终端窗口，执行“bundle”命令来安装 gem。

## 断头台

现在让我们创建一个 scaffold 资源来添加 carrierwave 的功能，在您的终端窗口中运行以下命令:

```
$> rails g scaffold your_scaffold title:string description:text image:string 
```

这个命令将为我们创建必要的文件和文件夹。一旦我们有了 scaffold 资源，就该在浏览器中测试它了，在终端窗口中运行下面的命令。

```
$> rails s 
```

等待它给出最后的提示，打开您的浏览器并访问:localhost:3000 如果您没有得到任何错误，现在运行我们的迁移以在我们的数据库中创建必要的表，因此回到您的终端窗口运行以下命令:

```
$> rake db:migrate 
```

## 上传者

一旦我们的数据库被迁移，我们需要用下面的命令创建我们的上传器。

```
$> rails g uploader your_uploader 
```

这个命令将在你的应用程序目录下创建一个名为“uploaders”的文件夹，在这个文件夹中你会找到你创建的上传程序类，在你的文本编辑器中打开上传程序，你会看到这个文件已经有了一些代码，并且附带了很好的关于你如何定制它的文档，但是为了这个教程的目的，我们可以让它保持原样。现在打开您的图像模型来安装上传程序: