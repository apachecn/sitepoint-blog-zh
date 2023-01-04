# 面向普通开发者的 CFC

> 原文：<https://www.sitepoint.com/cfcs-for-the-common-developer/>

最近在 [SitePoint ColdFusion 论坛](https://www.sitepoint.com/forums/forumdisplay.php?f=214)上有一个[关于 CFC 的很好的小讨论](https://www.sitepoint.com/forums/showthread.php?t=434270)，以及每个解释或教授 CFC 的人是如何抛出许多流行语的，这些流行语更多的是迷惑人们，而不是帮助他们。现在我知道会发生这种情况，因为我和许多其他经验丰富的 ColdFusion 开发人员一样对此感到内疚。所以希望通过这篇文章，我可以帮助那些对 CFC 感兴趣但不确定的人进入这个奇妙的世界。

那么什么是 CFC 呢？首先，CFC 实际上被称为 ColdFusion 组件，用最简单的术语来说，它只是一个 ColdFusion 模板(也称为 ColdFusion 页面或文件),而不是. cfm 或。它有一个. cfc 扩展名。

现在，仅仅因为一个文件有. cfc 扩展名，并不能使它立即成为 CFC。一旦你有了。创建 cfc 文件时，你需要写一些代码。通常，CFC 由 2 或 3 个基本部分组成。第一部分实际上是 CFC 标签，它们看起来像这样:

```
<cfcomponent displayname="Greeting" hint="I'm a greeting CFC">

</cfcomponent>
```

只有一个标记构成 CFC，那就是 CFComponent 标记，它需要一个开始和结束标记。现在，从这一点来看，你可以添加一些代码在这些标签内，或者你可以让它空白，这将让你一个空白的 CFC。你会注意到我的 CFComponent 标签有两个额外的属性**显示名称**和**提示**。这些是描述性属性，因为它们不做任何事情，但是它们描述了 CFC。**显示名称**属性是 ColdFusion auto 为 CFC 生成文档时显示的名称，而**提示**是描述。我们稍后会详细讨论文档:)

那么在这些 CFComponent 标记中可以放入什么呢？几乎所有您通常使用的 ColdFusion 标记都可以在 CFComponent 标记中使用。通常，CFC 是使用 CFFunction 标记的函数的集合，有时是使用 CFProperty 标记的属性的集合。

现在，我相信您已经看到了 CFFunction 标记，如果您使用 CF MX (version 6)或更高版本进行过任何广泛的开发，您应该已经使用过 CFFunction 标记。对于 CFC，您可以使用与代码中其他地方相同的语法。例如，如果我想让一个函数为我返回一个问候，我可以这样写:

```
<cffunction name="getGreeting" output="false" returntype="string" description="Returns a greeting string">
     <cfargument name="visitor" required="no" type="string" displayname="Visitor String" hint="The Visitors name">
          <cfif IsDefined('arguments.visitor') AND Len(Trim(arguments.visitor))>
               <cfreturn 'Welcome ' & arguments.visitor & '! I hope you enjoy your stay.'>
          <cfelse>
               <cfreturn 'Welcome guest! Enjoy your visit and please come back soon.'>
          </cfif>
</cffunction>
```

现在这段代码有点大了，所以我给每一行都标了号，这样我们就可以从头到尾看一遍，而不用我重新打印它(冒着剪切和粘贴错误的风险！).所以第 1 行用 CFFunction 标记设置了我们的函数，在这个标记中我使用了 4 个属性。 **name** 属性是我将用来引用或调用这个函数的措辞，在本例中它被称为 **getGreeting。**接下来，我将**输出**属性设置为 **false** ，这告诉 ColdFusion 将该函数视为在 CFSilent 标签中，从而在调用该函数时，只强制输出我在 CFReturn 标签中指定的文本。如果我将 output 设置为 true，那么我在这个函数中的任何代码(包括 HTML)都会在这个函数被处理时显示出来。99%的情况下，您希望确保将输出设置为 false，除非您有理由在不使用 cfreturn 标记的情况下处理和显示 HTML 和 CFML。(这个我还没碰到过，但肯定有人碰到过)。下一个属性是我已经设置为**字符串**的 **returntype** 。设置返回类型很有用，因为它允许 ColdFusion 对特定函数返回的数据进行错误检查。所以如果我不小心返回了一个数组，或者查询 ColdFusion 会出错，告诉我从这个函数返回的数据类型不是 string 类型。我的函数很少真正返回我告诉它的以外的任何东西，但是当你进入复杂的 CFC 时，它返回各种数据类型，设置返回类型会很有帮助。现在你不需要设置 returntype，但是如果 CFC 和函数对你来说是新的，我建议你设置，直到你完全理解发生了什么和为什么。好的，我们设置的最后一个属性是**描述**，这只是用来向开发人员描述正在发生的事情，如果您使用 ColdFusion 创建您的文档(稍后会详细介绍)，这将会在这些文档中输出，所以请始终尝试描述您的功能。

第 2 行是一个 CFArgument 标记。函数有能力接收数据，然后处理或处理这些数据，这种传入的数据称为参数，函数可以传入多个参数。上面的 CFArgument 标签包含 5 个属性。 **name** 属性是被传入的参数的名称，在本例中是**访问者**。接下来我们有一个**必需的**属性，它告诉 ColdFusion 这个参数是否是函数正常工作所必需的。在这种情况下，我们将 required 属性的值设置为 **no** ，这样我们就可以调用这个函数，并且不传入任何参数。接下来我们有一个**类型**属性，它告诉 ColdFusion 这个参数将是什么类型的数据。同样，通过指定类型，ColdFusion 将检查传入的数据，如果不是指定的类型，它将返回一个错误。在这种情况下，我们告诉我们的函数 visitor 将是类型为 **string** 的，所以它将允许任何东西进入。最后两个属性 **displayname** 和 **hint** 再次用于帮助我的开发伙伴了解正在发生的事情，也用于文档。

第 3 行开始我们的 if 语句，在那里我们检查我的 visitor 参数是否被传递到我们的 not 中。使用函数时，传入的所有变量都在 arguments 作用域内，该作用域只能由开始和结束函数标记内的代码访问。这里我使用 IsDefined 函数来查看变量是否存在，如果存在，那么下一个要执行的代码是我的 Len()和 Trim()函数。我在 len 函数中嵌套了 trim 函数，这样访问者变量数据中的任何空格或回车都将被去掉，我将只获得字符串中传递的实际数据的长度值。通过执行 trim，然后执行 len，我可以确保没有发送大量空格、制表符或回车，并且我实际上有一串可用的数据。我可以把这段代码写成**Len(Trim(arguments . visitor))gt1**，它会有和**Len(Trim(arguments . visitor))**一样的效果，因为后者会返回零或者某个正数。

OK 第 4 行有一个 CFReturn 标记，你应该注意到这里没有属性。CFReturn 标记接受一个表达式，在本例中是一个字符串，函数返回的就是这个表达式。在这里，我输入了一个简短的问候，并使用了 **&** 符号来连接两个字符串，将通过 visitor 变量传递给我的函数的数据括起来。

第 5 行是我们的 cfelse 语句，因此如果 arguments.visitor 不存在，或者 arguments.visitor 的值的长度在被修整后为 0，那么将执行第 6 行。

第 6 行是另一个 CFReturn 标记，但这次它只是一个简单的文本句子，没有像我们在第 4 行看到的那样连接变量。

第 7 行结束了我们的 CFIF 块，第 8 行将结束我们的 CFFunction 标记。因此，如果我们将组件代码与功能代码放在一起，看起来应该是这样的:

```
<cfcomponent displayname="Greeting" hint="I'm a greeting CFC">
     <cffunction name="getGreeting" output="false" returntype="string" description="Returns a greeting string">
          <cfargument name="visitor" required="no" type="string" displayname="Visitor String" hint="The Visitors name">
               <cfif IsDefined('arguments.visitor') AND Len(Trim(arguments.visitor))>
                    <cfreturn 'Welcome ' & arguments.visitor & '! I hope you enjoy your stay.'>
               <cfelse>
                    <cfreturn 'Welcome guest! Enjoy your visit and please come back soon.'>
               </cfif>
     </cffunction>
</cfcomponent>
```

所以现在你有了一个非常基本的氯氟化碳。如果需要，您可以将该文本复制到您选择的编辑器中，并将文件保存为 greeting.cfc。现在您有了这个 cfc，您可以用它做什么呢？怎么叫呢？有什么意义？所有这些都是很好的问题，但是你至少要等 24-48 小时才能得到答案。为什么？因为我在感恩节晚上 11 点写这篇文章，我打算凌晨 3 点起床，希望能给自己买一台任天堂 Wii。是的，我是一个游戏玩家，上一次我排队购买 Wii 时，我很幸运地发现我在 26 台的排队中排在第 29 位，我希望这种命运不会重演！所以，在这篇文章上线 24 小时后，请期待我的下一篇文章(不确定那会是什么时候，因为我的互联网连接在这里不稳定)。

## 分享这篇文章