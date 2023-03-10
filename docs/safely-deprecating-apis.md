# 安全地放弃 API

> 原文：<https://www.sitepoint.com/safely-deprecating-apis/>

弃用是软件开发生命周期中的一个阶段，在这个阶段，某个特性因为过时而需要被删除或避免。不推荐使用的功能不会立即取消；相反，它们会被保留，但会发出警告消息，建议使用替代方法。这使用户有时间在不需要的代码最终被删除之前更新他们的代码。

弃用可能有各种原因——也许 API 不再有用，已经走到了生命的尽头，或者重构代码以提高其可重用性和可测试性，从而淘汰了特定的方法。

在本文中，我将与您分享一些关键点，当您放弃 API 时，您应该遵循这些要点，这样您就可以继续发展您的代码，并为依赖它的人提供合理的警告。

## 为重构做准备

即使这很乏味，也要确保你首先记录你的代码。这将帮助您更好地理解什么代码负责什么功能，以及它作为一个整体是如何工作的。使用标准化的文档格式，以便您的 IDE 可以使用它，并且可以轻松地将其编译成可读的文档。

然后，为了确保您的服务或库在您废弃其 API 的一部分后继续工作，请用您的单元测试来测试原始代码。如果测试失败，您必须修复问题，这样您就可以区分由于弃用 API 而导致的失败和已经存在的失败。

## 采用单一责任原则

基于各种组件的目的和功能逻辑地组织您的代码，并采用单一责任原则(SRP)。该原则确保执行多项工作的方法和类被重构，这样每个方法和类都有一个单独的任务。这增加了代码的可重用性和可测试性。如果由于应用了 SRP，您的 API 中有许多类和方法，也不用担心；可重用和可测试的代码允许针对最少量的代码进行不恰当的更改。

## 与你的用户交流

根据用户群的规模，建立良好的沟通可能会很困难。一个好的解决办法是建立一个他们可以订阅的邮件列表。一旦您在列表上宣布了预期的更改，您可以允许用户继续使用旧方法的时间取决于一些因素，如重要性和为什么要进行更改。根据您的 API 和项目可用的资源，托管一个项目 wiki 也可能是有益的，开发者可以在 wiki 上共享替代策略和变通方法，以避免不推荐的方法调用。

对于库，应该更新提供 API 的方法和类，以发出描述性的警告消息，这些消息可以在使用时被记录。如果您正在提供 web 服务，请定义不影响当前实现但允许您在服务调用中包含元数据的头。这些标题可以让您与用户就服务变更进行交流。

## 删除旧代码

在进行必要的修改后，一定要仔细阅读你的代码，以确保不需要的、未使用的或旧的代码可以安全地删除。代码分析工具也很有帮助。

当合适的折旧期过去后，您可以继续删除旧代码。在删除不再需要的代码后，执行测试以确保系统仍然正常运行以避免失败是很重要的。

## 摘要

弃用可以提高软件代码、其结构或用户界面中 web 服务或库的内部一致性。这使得用户更容易理解系统是如何工作的，从而降低了开发新系统的成本。重构有助于改善用户对应用编程接口的体验，并使未来对代码的修改更容易实现。代码变得更加可重用和可测试。但是重构的过程也意味着一些代码因为不必要而应该被删除，以保持项目的整洁。

在功能被删除或转换之前弃用它们，可以让依赖您的代码的用户有时间更新他们自己的代码。通过遵循本文中的建议，您会发现保持代码新鲜更容易，并且您的用户不会有任何不愉快的惊喜。

图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")

## 分享这篇文章