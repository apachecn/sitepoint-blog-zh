# 回到基础:XML In。网

> 原文：<https://www.sitepoint.com/dot-net-xml/>

计算领域最令人兴奋的最新进展之一是 XML。作为一种比 SGML 更严格和简单的文档格式，XML 现在被广泛用于产生跨平台的可互操作的文件格式。

这也是。NET，是每一个东西。NET 开发人员需要认真对待的问题。我试图尽可能广泛地介绍 XML，因此它应该对所有开发人员都有用。

##### XML 101:学习爬行

在我们研究 XML 的细节之前，了解 XML 为什么存在以及它可以用在哪里是很重要的。正确的理解将允许你在你的项目中有效地使用它。

HTML 被设计用来显示数据和指定数据的外观，而 XML 被设计用来描述和组织数据。这样，XML 文件本身实际上不做任何事情。它没有说明如何显示数据或如何处理数据，就像文本文件没有说明一样。

但是 XML 与纯文本的关键区别在于它允许您以标准的方式组织数据。这很重要——这意味着其他系统可以解释您的 XML，而这在纯文本中是不容易实现的。这描述了“可互操作的文件格式”的含义——一旦你生成了一个 XML 文件，它就对所有人开放。文件中包含一个输入以及理解数据结构所需的所有信息。

我们举个例子。这里有一个文本文件和一个 XML 文件，它们都存储相同的信息:

```
mymusic.txt 

The Bends,Radiohead, Street Spirit 

Is This It?,The Strokes, Last Nite 

mymusic.xml 

<catalog> 

<cd> 

    <title>The Bends</title> 

    <artist>Radiohead</artist> 

    <tracks> 

      <track name="Street Spirit"/> 

    </tracks> 

  </cd> 

<cd> 

    <title>Is This It?</title> 

    <artist>The Strokes</artist> 

    <tracks> 

      <track name="Last Nite"/> 

    </tracks> 

  </cd> 

</catalog> 

注意我们的数据主题是如何在 XML 文件中定义的。我们可以清楚地看到，有一个包含 CD 的目录，每个目录包含一些曲目(音乐爱好者会注意到，为了节省空间，我删除了曲目列表！).您还可以看到，XML 的效率可能不如其他一些文件格式。然而，在许多情况下，由于大小增加而导致的效率损失可以通过处理定义良好的 XML 文件的速度来弥补，因为解析器(读取 XML 的程序)可以预测结构。

我们解释纯文本文件的方式取决于我们如何设计自己的格式。没有信息告诉其他人实际数据的含义、顺序，或者如何在其他项目中解析(读取)它。相比之下，XML 文件清楚地显示了每条信息代表什么，以及它在数据层次结构中的位置。这种“描述数据的数据”被称为元数据，是 XML 的一大优势，因为您可以创建自己的规范和数据结构，以便被任何其他系统解释。

术语

为了开始有效地使用 XML，需要对它的术语和文件结构有很好的了解。

```

```
<catalog> 

<cd> 

    <title>The Bends</title> 

    <artist>Radiohead</artist> 

    <tracks> 

      <track name="Street Spirit"/> 

    </tracks> 

  </cd> 

</catalog> 

XML 文件是分层的，每个标签定义一个元素。所有元素都需要一个开始标签和一个结束标签(`<catalog>`是开始标签，`</catalog>`是结束标签)。有些元素是独立的，不需要包含任何信息。这些标签可以通过在开始标签的末尾添加`"/>"`来自动关闭，就像上面的 track 元素一样。

目录的结构是这样的，它包含 CD，而 CD 又包含曲目。这是我们的层次结构，当我们需要解析文档时，这将是很重要的。例如，歌曲“街头精神”对应于 CD“The Bends”，正如歌曲“最后一夜”对应于 CD“就是它吗？”如果我们不使用合适的层次结构，我们将无法在解析期间确定这一点。

有时，信息出现在开始和结束标记之间是没有意义的。例如，如果我们需要多条信息来描述一个元素，我们可能希望在一个标签中包含多条信息。因此，我们以 attribute="value "的形式定义元素的属性。

一旦你产生了自己的一套元素和结构，这些格式可以被称为方言。例如， [RSS](https://www.sitepoint.com/article/get-off-your-rss) 就是一种 XML 方言。

名称空间

有这么多不同的方言，很容易产生意思上的冲突。例如，以下面的 XML 文件为例，这两个文件都描述了一些数据:

```

```
<film-types> 

  <film-type>Action</film-type> 

  <film-type>Adventure</film-type> 

</film-types>
```

```
<film-types> 

  <film-type>black and white</film-type> 

  <film-type>colour</film-type> 

</film-types>
```

第一个文件指定电影的类型，而第二个文件指定不同类型的相机胶片。但是，作为这些文件的消费者，我们如何区分它们呢？

名称空间提供了答案。XML 名称空间允许我们像电话区号限定电话号码一样限定元素。可能有成千上万个 545-321 的电话号码。当我们添加区号或者国际区号时，我们会使该号码具有唯一性:+44 020 545-321。

XML 名称空间的“区号”是一个 URI，它与名称空间的前缀相关联。我们使用 xmlns 声明来定义名称空间，后跟前缀，相当于唯一标识名称空间的 URI:

`xmlns:movie="https://www.sitepoint.com/movies">`

通过将这个名称空间定义作为属性添加到标记中，我们可以在该标记及其包含的任何标记中使用前缀 movie 来完全限定我们的元素:

```
<movie:film-types xmlns:movie="https://www.sitepoint.com/movies"> 

  <movie:film-type>Action</movie:film-type> 

  <movie:film-type>Adventure</movie:film-type> 

</movie:film-types>
```

类似地，对于第二个，我们可以选择不同的名称空间“camera”:

```
<camera:film-types xmlns:camera="https://www.sitepoint.com/camera"> 

  <camera:film-type>black and white</camera:film-type> 

  <camera:film-type>colour</camera:film-type> 

</camera:film-types>
```

解析器现在可以识别“电影类型”的两种含义，并相应地处理它们。

##### 有效的 XML

为了使 XML 文件有效，它至少需要符合 XML 规范 1.0 版。这准确地标准化了 XML 文件的格式，以便其他系统能够理解它。例如，XML 1.0 要求所有 XML 文件都由一个根元素组成；也就是说，单个元素包含所有其他元素。在上面的音乐库示例中，catalog 是我们的根元素，因为它包含了所有其他元素。

完整的 XML 规范可以在这里阅读，尽管，我们很快就会看到。NET 为您提供了自动编写有效 XML 的工具。

##### XML 模式

虽然 XML 文件可能符合 XML 规范，但它可能不是特定方言的有效形式。XML 模式允许您验证某些元素是否存在，同时确保所显示的值是正确的类型。

有几种不同的模式规范:XSD、DTD 和 XSX。尽管 DTD(文档类型定义)是目前最常用的模式，但 XSD (XML 模式定义)是一个正在被接受的新标准，因为它为 XML 验证提供了最细粒度的控制。

由于 XSD 有许多特性，这篇介绍将集中在一些您能够使用的简单特性上。

模式文件的第一行通常如下所示:

`<xs:schema xmlns:xs="https://www.w3.org/2001/XMLSchema">`

上面定义了这个文件是一个模式，我们将用于验证的所有元素都属于 XML 模式名称空间(我们给它分配了前缀 xs)。您可以在这个标记中设置额外的名称空间和许多 XSD 选项。更多信息请查看完整规格。

我们可能想要检查的主要验证之一是元素是否是正确的类型(例如，当我们期望一个数字时，我们不想接收一个文本字符串)。该检查在 XSD 使用元素标签执行:

```
<xs:element name="foo" type="xs:integer"/>
```

这意味着任何名为 foo 的元素都必须包含一个整数。

因此，下面将验证。

`<foo>10</foo>`

但是，下面的代码不会。

`<foo>This is some text</foo>`

您可以检查一系列不同的类型；同样，更多细节请参见规范。

我们还可以使用枚举器来检查一组有效值。例如，我们的元素 foo 可能只能接受值“apple”、“orange”和“grape”在这里，我们希望定义我们自己的类型，因为它不仅仅是一个字符串。XSD 提供的简单类型让我们这样做。

```
<xs:element name="foo">  

  <xs:simpleType>  

    <xs:restriction base="xs:string">  

      <xs:enumeration value="Apple"/>  

      <xs:enumeration value="Orange"/>  

      <xs:enumeration value="Grape"/>  

    </xs:restriction>  

  </xs:simpleType>  

</xs:element> 

注意限制`element`，它给了我们一个基础类型。因为我们有一个文本字符串列表，所以它被设置为字符串类型。

因此，我们对元素的值进行了基本的验证，但是那些元素的属性呢？同样，我们可以在元素标签中定义属性:

```

```
<xs:element name="foo">  

  <xs:attribute name="colour" type="xs:string"/>  

</xs:element>
```

同样，属性可以有自己的类型，使用我们在上面元素中使用的`simpleType`元素。

默认情况下，所有属性都是必需的。但是，如果我们并不总是要求在元素上放置一个属性，我们可以在属性元素上使用`use="optional"`属性来覆盖这个缺省值:

```
<xs:attribute name="colour" type="xs:string" use="optional"/>
```

复杂元素是包含其他元素的元素，例如前面给出的目录示例中的“CD”元素:

```
<cd>  

    <title>The Bends</title>  

    <artist>Radiohead</artist>  

    <tracks>  

      <track name="Street Spirit)"/>  

    </tracks>  

  </cd>
```

这里，我们需要确保 CD 元素包含标题、艺术家和 tracks 元素。我们使用一个序列:

```
<xs:element name="cd">  

  <xs:complexType>  

    <xs:sequence>  

      <xs:element name="title" type="xs:string"/>  

      <xs:element name="artist" type="xs:string"/>  

      <xs:element name="tracks" type="xs:string"/>  

    </xs:sequence>  

  </xs:complexType>  

</xs:element>
```

我们可以创建自定义类型或在其他元素中定义属性，以帮助我们创建层次结构。

您应该注意到“tracks”本身是一个复杂的类型，因为它是由其他元素组成的。因此，我们需要定义另一个`complexType`，这次是在我们的 tracks 元素中。我们的模式将如下所示:

```
<xs:element name="cd">  

  <xs:complexType>  

    <xs:sequence>  

      <xs:element name="title" type="xs:string"/>  

      <xs:element name="artist" type="xs:string"/>  

      <xs:element name="tracks">  

        <xs:complexType>  

          <xs:sequence>  

            <xs:element name="track">  

              <xs:attribute name="name" type="xs:string"/>  

            </xs:element>  

          </xs:sequence>  

        </xs:complexType>  

      </xs:element>  

    </xs:sequence>  

  </xs:complexType>  

</xs:element>
```

当然，这只是触及了 XSD 能力的表面，但它应该给你一个很好的理解，从这里开始写你自己的。话又说回来，你总是可以作弊，多亏了。NET 和微软的 [XSD 推理工具](http://apps.gotdotnet.com/xmltools/xsdinference/)，它从任何给定的 XML 文件构建一个“最佳猜测”XSD 模式文件。

##### 学习阅读和写作

在我们开始涉及 XML 更令人兴奋的方面之前，我们需要知道如何在. NET 中生成和使用 XML 文件。NET 在系统下组织它的 XML 类。Xml 名称空间，所以您可能想要利用并熟悉我们需要使用的关键类:

*   `XmlTextReader`:`XmlTextReader`类只是读取 XML 文件的方法之一。它以类似于 DataReader 的方式处理 XML 文件，一个元素一个元素地遍历文档，边走边做决定。这是迄今为止最容易用来快速解析 XML 文件的类。
*   `XmlTextWriter`:类似地，`XmlTextWriter`类提供了一种逐行编写 XML 文件的方法。
*   `XmlValidatingReader` : `XmlValidatingReader`用于根据模式文件验证 XML 文件。

##### 读取 XML 文件。网

让我们来熟悉一下`XmlTextReader`。`XmlTextReader`基于`XmlReader`类，但是被特别设计成读取字节流，使其适合于位于磁盘、网络或流中的 XML 文件。

与任何类一样，第一步是创建一个新的实例。构造函数获取它将读取的 XML 文件的位置。下面是用 C#实现的方法:

`// file  
XmlTextReader reader = new XmlTextReader("foo.xml");  

// url  
XmlTextReader reader = new XmlTextReader("https://www.sitepoint.com/foo.xml");  

// stream (here, a StringReader s)  
XmlTextReader reader = new XmlTextReader(s);`

一旦加载完毕，我们只能向前移动文件。这意味着您需要构建您的解析例程，以便它们是顺序独立的。如果您不能确定元素的顺序，您的代码必须能够处理任何顺序。

我们使用`Read`方法浏览文件:

`while (reader.Read())  
 {  
   // parse our file  
 }`

这个循环将一直持续到我们到达文件的末尾，或者我们正式地中断这个循环。我们需要检查每个节点，确定其类型，并获取我们需要的信息。属性暴露了正在被读取的节点的当前类型，这就是事情变得有点复杂的地方！

XmlReader 将以下元素视为 3 个不同的节点:

`<foo>text</foo>`

元素的`<foo>`部分被认为是一个`XmlNodeType.Element`节点。文本部分被认为是一个`XmlNodeType.Text`节点，结束标签`</foo>`被认为是一个`XmlNodeType.EndElement`节点。

下面的代码展示了如何通过我们之前创建的 reader 对象输出 XML 标记:

`while (reader.Read())  
 {  
   switch (reader.NodeType)  
   {  
   case XmlNodeType.Element:  
     Console.Write("<"+reader.Name+">");  
     break;  

case XmlNodeType.Text:  
     Console.Write(reader.Value);  
     break;  

case XmlNodeType.EndElement:  
     Console.Write("</"+reader.Name+">");  
     break;  
   }        
 }`

下面是这段代码的输出:

```
<foo>text</foo>
```

那么，属性呢？嗯，这些可以通过多种方式获得。类型为`XmlNodeType.Attribute`的属性可以用上面显示的方式进行解析。然而，更优雅的是，当我们遇到一个`XmlNodeType.Element`类型时，我们可以使用`XmlTextReader.MoveToNextAttribute`遍历属性:

`case XmlNodeType.Element:  
   Console.Write("<"+reader.Name);  
   while (reader.MoveToNextAttribute())  
   {  
     Console.WriteLine(reader.Name+" = "+reader.Value);  
   }  
 break;`

现在，如果我们输入这个 XML:

`<foo first="1" second="2">text</foo>`

我们将收到以下输出:

`<foo first="1" second="2">text</foo>`

之前，我们忽略了这些属性，并输出了:

`<foo>text</foo>`

请注意，如果一个元素不包含任何属性，循环将永远不会开始。这意味着我们不必先检查是否有属性。也就是说，可以使用 AttributeCount 属性找到节点上的属性数。

##### 在中验证 XML。网

还记得那些模式文件吗？使用`XmlValidatingReader`类，我们可以很容易地根据模式文件验证 XML 文件。然而，我们必须先使用一些其他的类。

我们可以将`XmlValidatingReader`对象指向我们希望使用的模式文件，方法是用我们的模式文件填充`XmlSchemaCollection`:

`XmlSchemaCollection xsdCollection = new XmlSchemaCollection();  
 xsdCollection.Add("schemafile", "schema.xsd");`

在这个例子中，我们使用包含在 schema.xsd 文件中的模式。

实际的`XmlValidatingReader`类在其构造函数中接受一个`XmlReader`。因此，我们需要创建一个`XmlTextReader`,并用我们希望验证的 XML 文件填充它:

```
XmlTextReader reader;   

  XmlValidatingReader validatingReader;   

  reader = new XmlTextReader("foo.xml");   

  validatingReader = new XmlValidatingReader(reader);
```

然后，我们将我们的`schemaCollection`添加到我们希望验证阅读器使用的模式中:

`validatingReader.Schemas.Add(xsdCollection);`

如果验证器发现错误，就会触发一个事件。我们需要通过创建一个事件处理程序来捕获这个事件，并编写我们对错误的响应:

`validatingReader.ValidationEventHandler += new ValidationEventHandler(validationCallBack);  

public void validationCallBack (object sender, ValidationEventArgs args)  
{  
 Response.Write("Error:" + args.Message);  
}`

最后，我们可以在验证阅读器上使用`Read`方法来验证我们的文件。我们实际上可以像以前一样读取和处理文件中的节点，但是，出于本例的目的，我们将只使用一个空的 while 循环来遍历文件，并在遍历过程中对其进行验证:

`while (validatingReader.Read()){}`

当在文件处理过程中发现错误时，我们通过`validationCallBack`方法捕获和处理的事件将被触发。

##### 正在写入 XML。网

现在我们已经很好地理解了如何读取和验证 XML 文件，我们可以继续编写 XML 了。

通过`XmlTextWriter`类，编写 XML 变得非常容易。同样，采用仅向前的方法，我们可以从不同的节点类型构建 XML 文件，这些节点类型按顺序输出。

首先，我们需要创建该类的一个实例:

`XmlTextWriter writer = new XmlTextWriter("newfoo.xml", null);`

第二个参数在这里被设置为 null，它允许我们指定在 XML 文件上使用的编码格式。将其设置为 null 会生成一个标准的 UTF-8 编码的 XML 文件，该文件的文档元素上没有编码属性。

我们现在可以开始使用我们的代码编写元素和属性，包括在`XmlTextWriter`中公开的方法。让我们写下之前的 CD 目录示例:

```
<catalog>   

<cd>   

    <title>The Bends</title>   

    <artist>Radiohead</artist>   

    <tracks>   

      <track name="Street Spirit "/>   

    </tracks>   

  </cd>   

</catalog> 

我们的第一行是元素目录。我们使用`WriteStartElement`方法将这个写给编写器:

`writer.WriteStartElement("catalog");`
```

请注意，在写入 CD 元素之前，我们不想关闭这个元素。因此，我们希望添加的下一个节点是一个开始 CD 元素:

`writer.WriteStartElement("cd");`

我们现在有了一个标题元素(`<title>The Bends</title>`)。和以前一样，我们编写了一个 start 元素，但是这个类型会有所不同，因为我们有一个想要使用的特定值。由于 title 元素没有属性，我们可以使用 WriteElementString 方法:

`writer.WriteElementString("title", "The Bends");  
writer.WriteElementString("artist", "Radiohead");  

writer.WriteStartElement("tracks");`

现在我们已经到达了一个元素，track，它有一个属性，name。我们首先需要写元素的开始，然后写属性，最后，关闭元素:

```
writer.WriteStartElement("track");   

writer.WriteAttributeString("name", "Street Spirit");   

  writer.WriteEndElement();
```

请注意，我们不需要说明希望关闭哪个元素，因为编写器会自动为最后打开的元素(在本例中是 track)编写结束标记。

我们现在可以用同样的方式关闭剩余的元素:

```
writer.WriteEndElement();   

writer.WriteEndElement();
```

最后，我们需要“刷新”编写器，或者换句话说，将我们请求的信息输出到我们的 XML 文件，然后关闭编写器以释放我们的文件和资源:

`writer.Flush()  
 writer.Close()`

##### 摘要

至此，对 XML 及其在. NET 中的使用的介绍就完成了。希望 XML 的世界不再像您最初想象的那样令人生畏，您现在已经准备好为您的下一个项目驾驭它的力量了。

## 分享这篇文章