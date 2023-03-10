# XML 安全性入门

> 原文：<https://www.sitepoint.com/getting-started-xml-security/>

 **##### 行动纲要

满足隐私、机密性和完整性方面的安全要求对于在线开展业务至关重要。随着越来越多的人接受 XML 技术用于文档和协议，安全性应该与 XML 解决方案集成在一起是合乎逻辑的。XML 安全标准定义了 XML 词汇表和处理规则，以满足安全需求。这些标准使用传统的加密和安全技术，以及新兴的 XML 技术，为满足安全需求提供灵活、可扩展和实用的解决方案。

XML 安全标准包括用于完整性和签名解决方案的 XML 数字签名、用于机密性的 XML 加密、用于公钥注册、定位和验证的 XML 密钥管理(XKMS)、用于传达认证、授权和属性断言的安全断言标记语言(SAML)、用于定义访问控制规则的 XML 访问控制标记语言(XACML)以及用于定义隐私策略和偏好的隐私偏好平台(P3P)。主要用例包括保护 Web 服务(WS-Security)和数字版权管理(可扩展版权标记语言 2.0–XrML)。

##### 介绍

安全性在商业世界中一直是至关重要的，以确保内容和交易的完整性，维护隐私和机密性，并确保信息得到适当的使用。然而，在今天基于 web 的业务环境中，提供这种安全性的方式已经发生了变化。使用物理安全不再像过去那样有效，当时所有计算资源都被锁在中央计算室，所有作业都在本地提交。

由于硬件和软件系统的异构性以及相互冲突的管理、应用和安全要求，创建单一的普适安全基础设施的努力不能有效地扩展到因特网。要管理的东西太多，应用程序太多，变化太多，技术变化太快，以至于无法设计一个单一的基础架构来有效地满足所有需求。需要可扩展的标准，这些标准能够适应不断变化的需求，能够在继续使用遗留技术的同时结合新技术，并且能够根据需要模块化地部署，而不需要使用不必要的部分。这些标准应该能够很好地协同工作，而不是重复功能，并且应该与新技术相适应，以实现开放的分布式系统、应用程序集成和内容管理。

新安全标准的一个基本要求是，它们能够自然地处理使用可扩展标记语言(XML)创建的内容。XML 正被越来越多的应用程序和内容类型广泛采用。它还形成了分布式系统协议的基础，以集成互联网上的应用程序，如 Web 服务协议。XML 语言是基于文本的，旨在扩展和组合。在不妨碍标准 XML 工具[ XMLRef ]进一步处理的情况下，为整个 XML 文档或这些文档的一部分提供完整性、机密性和其他安全性好处应该是很自然的。因此，XML 安全性必须以这样一种方式与 XML 集成，即保持 XML 的优点和功能，同时添加必要的安全性功能。这在基于 XML 的协议中尤其重要，例如 XML 协议( [XMLProt](http://www.w3c.org/TR/xmlp-am/) ，简单对象访问协议， [SOAP](http://www.w3c.org/TR/soap1-part0/) ，它们被明确设计为允许消息的中间处理和修改。

旧的安全技术提供了一组可用于 XML 安全的核心安全算法和技术，但是用于实现安全需求的实际格式不适合大多数 XML 安全应用程序。一个原因是，这些标准使用二进制格式，需要专门的软件来解释和使用，甚至提取部分安全信息。第二个原因是，这些标准不是为 XML 设计的，不支持管理内容的通用 XML 技术方法，例如用统一资源标识符字符串(URIs)指定内容，或者使用其他 XML 标准定义来定位 XML 内容的一部分(如 XPath [ [XPath](http://www.w3c.org/TR/xpath) ])。此外，一些现有的安全技术假定特定于安全的软件将与应用程序集成以实现安全性。实际上，由于定制集成的细节问题，情况并不总是如此。

XML 安全通过定义一个公共框架和处理规则来解决这些问题，这些框架和规则可以使用公共工具在应用程序之间共享，从而避免了为增加安全性而对应用程序进行大量定制的需要。XML 安全重用了遗留安全系统的概念、算法和核心技术，同时引入了支持与 XML 的可扩展集成所必需的更改。这允许与广泛的现有基础设施和跨部署的互操作性。

XML 安全性通过定义最小的模块化机制来获得强大的结果，从而减少了采用的障碍。通过采用现有的技术并支持使用 XML 范例和工具，XML Security 最大限度地减少了修改应用程序以满足安全需求的需要。

本文档总结了关键的 XML 安全技术，并概述了它们如何与 XML 结合在一起。它应该作为进一步探索相关规范的路线图和基础。大多数规范都处于定义过程的早期，并且目前正在经历快速的变化，因此鼓励感兴趣的读者自己去查阅规范以了解最新的细节。

本文假设您对 XML 和安全性概念有基本的了解，但是为了提供一个起点，本文对这两个主题做了一个非常简短的介绍。接下来是对以下核心 XML 安全标准的概述:

*   **完整性和签名**–XML 数字签名
*   **保密性**–XML 加密
*   **密钥管理**–XML 密钥管理规范(XKMS)
*   **认证和授权声明**–安全声明标记语言(SAML)
*   **授权规则**–XML 访问控制标记语言(XACML)

以及主要的 XML 安全应用程序:

*   **Web 服务安全性**–路线图和 WS-Security
*   **隐私**–隐私偏好平台(P3P)
*   **数字版权管理**–可扩展版权标记语言 2.0 (XrML)

***XML***

XML 规范[ [XML](http://www.w3c.org/TR/REC-xml) ]定义了使用标签来结构化信息的语法和规则。任何人都可以定义元素标签和属性的词汇表来构造感兴趣的信息。通过遵循 XML 规范中定义的规则，他们可以创建“格式良好的”XML，即可以由通用 XML 工具处理的 XML。他们还可以通过创建 XML 模式或文档类型定义(DTD)来明确定义他们所定义的文档的结构。这允许对文档进行验证。

由不同的人创建的 XML 语言可以被组合。例如，如果您定义了一种表示地址的语言，而我为采购订单定义了一种语言，我可能会在我的采购订单语言中重用您的地址语言。为了将元素与适当的模式相关联并避免元素冲突，可以使用 XML 名称空间。XML 名称空间将标签与唯一标识符(URIs)相关联，并且可以用于避免歧义[ [名称空间](http://www.w3c.org/TR/REC-xml-names/) ]。一个结构良好的 XML 文档可以使用通用的 XML 感知工具进行处理，包括理解 XML 语法和处理的一般规则的解析器。一个优点是这些工具的使用不依赖于特定文档中定义的特定词汇。这意味着一旦创建了通用工具，它们就可以用于 XML 的许多应用。这允许重用工具和培训，这是 XML 的另一个优点。

已经定义了许多 XML 语言，包括用于创建网页的 [XHTML](http://www.w3c.org/TR/xhtml1/) ，用于创建技术文档的 [DocBook](http://www.oasis-open.org/docbook/specs) ，用于内容分发(联合)的 [RSS](http://purl.org/rss/1.0/spec) ，用于表示信息的 [RDF](https://www.w3.org/TR/REC-rdf-syntax/) ，用于数学标记的 [MathML](https://www.w3.org/TR/REC-MathML/) ，用于商业报告的 [BRML](http://www.research.ibm.org/rules/home.html) ，以及许多其他语言。

下面的例子展示了一种用于管理办公室医疗记录的语言，包括像`<PatientRecord>`、`<Name>`和`<Diagnosis>`这样的 XML 元素。它还展示了与实验室相关联的 XML 名称空间的使用，以允许不与 office `<Diagnosis>`元素冲突的`<lab:Diagnosis>`元素。

```
<PatientRecord  

  xmlns:lab="http://www.lab.org/">  

     <Name>John Doe</Name>  

     <Account>123456</Account>  

     <Visit date="10pm March 10, 2002">  

          <Diagnosis>Broken second metacarpal</Diagnosis>  

          <lab:Diagnosis>  

            <lab:Xray>encoded xray image</lab:Xray>  

          </lab:Diagnosis>  

     </Visit>  

</PatientRecord>

```

*示例 1–带有 XML 名称空间的示例 XML 文档*

 **安全性对在线业务至关重要。为满足安全需求而设计的技术不断发展，但需求却相对稳定。这些要求包括认证、授权、完整性、签名、机密性、隐私和数字权利管理，简要概述如下:

**认证——是谁？**

确定试图执行某些操作(如访问资源或参与事务)的一方的身份或角色。一个角色可能适用于多方，例如“人力资源人员”。

授权-他们能做什么？

确定是否允许某一方执行所请求的操作，例如查看网页、更改密码或让某个组织承担一笔 1000 万美元的交易。

**完整性——确保信息完好无损**

确保信息不会因恶意或意外而被更改。举例来说，这可以是通过网络传输的信息，例如从 web 浏览器传输到 web 服务器，存储在数据库或文件系统中的信息，或者在 web 服务消息中传递并由中介处理的信息。

**签名——创建并验证类似于手写签名的电子签名**

制作或验证旨在等同于手写签名的电子签名。这种签名可用于不同的目的，如批准、确认收到、接受或同意。

**保密性——使未授权方无法读取内容**

确保内容只能由合法方查看，即使绕过了其他访问控制机制。保密性通常与加密技术相关，尽管其他方法如隐写术(信息隐藏)可能有类似的目的。

**隐私–限制访问和使用个人身份信息**

个人和公司需要个人身份信息，以便为个人提供服务。例如，医生办公室需要医疗记录来跟踪患者的健康状况。隐私是指控制如何处理这些信息，以及是否在个人不知情或未同意的情况下将这些信息重新发布给其他人。可以通过技术和法律手段的结合来管理隐私。保密技术可用于保护隐私，但不能防止信息的不当共享。

**数字版权管理——根据许可协议限制内容的使用和共享**

确保根据许可协议使用内容。通常，访问规则与内容结合在一起，强制控制与使用内容所需的客户端结合在一起。

传统上，安全技术要求应用程序具备安全或公钥基础设施(PKI)“能力”。这通常包括将专门的安全代码与应用程序集成在一起，以满足安全需求。这导致定制过程缓慢、繁琐且不灵活。另一种方法是创建通用的 XML 工具和通用的 XML 安全性，然后允许它们用于各种 XML 应用程序。这允许将通用 XML 安全过滤器应用于任意内容，而不需要对每个应用程序进行大量定制，从而降低了成本和延迟。

##### XML 安全性

XML 安全标准提供了一套满足安全需求的技术标准。这些标准被设计成符合通用的 XML 范例。XML 安全标准利用了现有的 XML 标准，并增强了 XML 标准，如下所示:

1.  XML 安全标准定义了用于表示安全信息的 XML 词汇表，使用 XML 技术(如 XML Schema)进行定义。一个例子是在 XML 数字签名建议中定义的用于携带签名或加密密钥信息的`<KeyInfo>`元素。这个定义在许多规范中使用。规范为 XML 词汇表定义了一个共享的含义。

*   XML 安全标准尽可能使用其他现有的 XML 标准，以利用当前的 XML 成果。例如，XML 数字签名允许 XPath 表达式提取部分 XML 进行处理(在[ [XMLDigSig](http://www.w3c.org/TR/xmldsig-core/) 中定义，并在[ [XPathFilter](http://www.w3c.org/TR/xmldsig-filter2/) ]中扩展)。*   XML 安全标准旨在提供 XML 的灵活性和可扩展性。它们允许对 XML 文档、XML 元素和元素内容以及任意二进制文档应用安全性。它们支持通过使用 XML 名称空间和可扩展的 XML 模式定义来扩展 XML 词汇表。*   XML 安全技术可以应用于端到端安全，这在 XML 消息通过许多处理中介进行路由时尤其重要。持久安全性与内容相关联，而不是与传输管道相关联。安全仍然与内容有关。XML 安全技术也可以与 SSL/TLS 等传输安全技术结合使用。*   XML 安全技术尽可能重用现有的加密和安全技术，而无需重新发明轮子。例如，X.509 V3 证书[ [X509Cert](http://www.ietf.org/rfc/rfc3280.txt) ]在需要时无需重新定义即可使用——它们只是以文本格式编码。通过将唯一的 URI 标识符与现有的算法(如 SHA1 摘要算法)相关联，并定义它们在 XML 安全处理模型中的使用方式，现有的算法也被引入 XML 安全标准世界。

以下部分概述了核心 XML 安全标准，这些标准旨在提供 XML 兼容技术来满足安全需求。接下来是一些重要的 XML 安全标准，用于将这项技术应用到 Web 服务和数字版权管理等领域。

***核心 XML 安全标准***

核心 XML 安全标准是:

1.  完整性和签名的 XML 数字签名，

3.  用于保密的 XML 加密，

5.  用于密钥管理的 XML 密钥管理(XKMS ),

7.  安全声明标记语言(SAML ),用于进行身份验证和授权声明，以及

9.  用于陈述授权规则的 XML 访问控制标记语言(XACML)。

XML 数字签名建议尤其重要；因为作为第一个 XML 安全建议，它建立了一种方法和一些其他标准共享的词汇表。(例如，XML 数字签名建议中定义的`<KeyInfo>`元素是其他标准使用的元素。)签名功能对于内容完整性也很重要，因此 XML 数字签名也包含在其他安全标准中。

每个标准都定义了描述该安全方面所需的安全信息所必需的 XML 词汇表，以及理解如何应用标准所必需的处理规则。

**完整性&签名:XML 数字签名(XML DigSig)**

*目的和益处*

数字签名有两个用途:

1.  提供持久的内容完整性，以及

3.  创建和验证便携式电子签名

持久的完整性使内容的用户能够检测到对内容的意外更改，无论是恶意的还是意外的。与简单的校验和不同，数字签名使用加密技术将内容的摘要与内容的签名者相关联。摘要是一个数字“指纹”，它是一个短的固定长度的值，对于内容是唯一的，没有内容就无法确定。对摘要使用加密技术使得除了原始签名者之外的任何人都很难在不被发现的情况下更改内容。持久完整性带来的好处是，内容不仅在传输过程中受到保护，而且在存储和处理过程中也受到保护。

电子签名提供了与手写签名等同的数字签名，可用于多种目的，如内容批准、接收确认和合同协议。使用数字签名使得在线移动业务工作流成为可能，而不需要手动批准步骤。这可以减少由地理分隔和时区差异导致的延迟、成本和不便。数字签名使用加密技术来构造签名，这种签名比创建“电子签名”的其他技术更强大、更便于携带。

*特性*

XML 数字签名建议定义了支持所有数字签名创建和验证的机制，包括签名和验证的能力:

1.  整个 XML 文档以及 XML 文档的元素和元素内容部分，

3.  任意文档，包括二进制文档，

5.  复合文档包括多个文档和/或 XML 元素和元素内容，

7.  要包含在签名中的属性，

9.  反签名(包含其他签名的签名)

此外，XML 签名建议支持将多个 XML 签名应用于一个 XML 文档或文档的不同部分，从而支持各种用例。XML 数字签名规范和相关规范( [XML 规范化](http://www.w3c.org/TR/xml-c14n))也定义了一些技术，使得签名验证可以是健壮的，即使 XML 中允许有变化，例如空白。担心的原因是加密算法关注的是精确的文本，而 XML 允许一些灵活性。规范化用于减少差异，以便所有 XML 安全应用程序可以互操作。

基于所需的应用程序，可以用不同的方式处理 XML `<Signature>`元素。它可以放在文件中，而不是放在签名的文件中。这就是所谓的“分离”签名，在签署非 XML 内容时使用。当 XML 内容被签名时，`<Signature>`元素可以被添加到 XML 中。这很方便，因为签名可以捆绑在内容中，并与内容一起嵌入，这样就很容易跟踪它们。当放置在 XML 文档中时，`<Signature>`元素可以被添加到文档元素下被签名的文档中(一个“封装的”签名)。在某些情况下，将被签名的内容放在`<Signature>`元素中是有用的，一个例子是签名属性(一个“封装”签名)。

例如，如果将签名作为封装签名添加到`<PatientRecord>`中，则`<Signature>`元素将是`<PatientRecord>`的子元素，如下所示:

```
<PatientRecord >   

    <Name>John Doe</Name>   

    <Account> 123456 </Account>   

    <Visit date="10pm March 10, 2002">   

        <Diagnosis> Broken second metacarpal </Diagnosis>   

    </Visit>   

    <Signature xmlns='https://www.w3.org/2000/09/xmldsig#'>   

        ...    

    </Signature>   

</PatientRecord>

*Example 2 -  Enveloped Signature*

当签名作为文档的一部分添加到文档中时，它会更改文档。为了验证签名，需要比较没有签名的原始文档。XML 数字签名建议定义了一种机制，用于在验证过程中删除`<Signature>`。

另一种可能是用一个`<Signature>` 文档元素创建一个新的 XML 文档，并将签名的元素作为`<Signature>`元素的子元素。这通常是为与签名相关的信息保留的，如签名的目的，例如:

```

```
<Signature xmlns='https://www.w3.org/2000/09/xmldsig#'>   

    <SignedInfo> ... </SignedInfo>   

    <SignatureValue> ... </SignatureValue>   

    <Object>   

        <SignatureProperties>   

            <p:Purpose xmlns:p="http://www.myexample.com/schemas">   

              Approval   

            </p:Purpose>   

        </SignatureProperties>   

    </Object>   

</Signature>

*Example 3 - Enveloping Signature*

*关键概念*

以下概念是理解 XML 数字签名的核心:

1.  只有当签名的内容没有改变时，签名才有效。这些内容用一个短的、固定长度的摘要来表示，如果内容发生变化，摘要也会随之变化。因此，只有当用于创建签名的摘要与用于稍后验证签名的摘要相同时，签名才有效。验证者可以创建一个摘要来查看它是否相同。

*   XML `<Signature>`元素是一个 XML 结构，它在`<SignatureValue>`元素中包含一个加密签名值，以及一个已经签名的 XML 结构，即`<SignedInfo>`结构。这意味着为了使签名有效，`<SignedInfo>` 结构的内容不应该改变。
*   签名者为要包含在签名中的每个项目创建一个 `<Reference>`。每个`<Reference>`包括一个条目的摘要和一个条目的唯一标识符(URI)。它还确定如何重新创建摘要，指定算法和其他必要的信息。每个引用都是`<SignedInfo>`结构的一部分。*   为了验证签名，接收者必须通过独立地为项目生成相同的摘要来验证每个`<Reference>`。验证者可以使用 URI 来帮助定位物品，并使用算法信息来知道如何生成摘要。如果项目没有改变，摘要应该是相同的。*   引用可以指使用 URI 的任何内容，包括图像和文本文件等非 XML 内容。不要求使用 URI 来获得物品，但是它通常是有用的。可以使用一种特殊形式的 URI 来引用与签名相同的文档中的 XML 元素，从而允许签名与要签名的 XML 内容一起传输。*   在创建摘要之前，<reference>可以指定一个或多个要应用于一个项目的变换。一种用途是对 XML 文档中已知不可更改的部分进行签名——例如样板文件。这可以通过定义 transform 来提取要签名的文档部分来完成，例如使用标准的 XML XPath 表达式。</reference>*   摘要算法要求内容完全相同才能生成相同的摘要。即使是不改变意思的小改动，比如多加一个空格，也会使文摘失效。另一方面，XML 允许在不改变文档的情况下对 XML 文本的语法进行一些修改。换句话说，两个 XML 文档可能被认为是相同的，即使它们没有完全相同的文本。例如，一个 XML 文档可能对一个属性使用单引号，而对另一个属性使用双引号。这些对于 XML 解析器来说是一样的，但是对于摘要算法来说却非常不同。对于摘要来说，有一个完整的潜在问题列表。为了解决这个问题，可以使用规范化转换，这种转换使用一组规则将任何 XML 文档转换成一种形式，比如总是对属性使用某种类型的引号。

*例题*

创建后，XML 数字签名可以与已签名的内容分开存储(分离签名)或嵌入已签名的 XML 内容中(封装签名)。事实上，签名的内容也可以放在签名本身中(包封签名)。继续前面的 PatientRecord 示例，假设整个 PatientRecord 将由医生办公室签名，并且签名将作为 PatientRecord 的一部分进行维护。这会产生以下结果，显示 XML 签名的布局:

```
<PatientRecord >    

  <Name>John Doe</Name>    

  <account id="acct">123456</Account>    

  <Visit date="10pm March 10, 2002">    

    <Diagnosis>Broken second metacarpal</Diagnosis>    

    <lab:Diagnosis>    

      <lab:Xray>xhzhez</lab:Xray>    

    </lab:Diagnosis>    

  </Visit>    

  <Signature xmlns='https://www.w3.org/2000/09/xmldsig#'>    

    <!-- the SignedInfo element and all it contains    

         is what is signed -->    

    <SignedInfo>    

      <!-- Canonicalization is used to ensure    

           that XML is handled consistently    

           by different XML processors    

           in light of white space and other    

           variations. -->    

      <CanonicalizationMethod algorithm="URI for algorithm" />    

      <!-- the SignatureMethod is protected    

           by the signature, avoiding substitution    

           attacks and defines how the signature    

           is created  -->    

      <SignatureMethod    

Algorithm="https://www.w3.org/2000/07/xmldsig#rsa-sha1" />    

      <!-- each item to be signed, XML document,    

           portion of XML document or arbitrary    

           content is represented using a    

           Reference. Each Reference contains    

           a digest of the item, a URI to    

           refer to the item, and possibly     

           transforms to apply to the item    

           before creating the digest  -->    

      <Reference URI="">    

        <Transforms    

Algorithm="https://www.w3.org/TR/2000/WD-xml-c14n-20000710" />    

        <DigestMethod    

Algorithm="https://www.w3.org/2000/07/xmldsig#sha1" />    

        <DigestValue>    

          Short, fixed-length "fingerprint" of referenced item     

        </DigestValue>    

      </Reference>    

    </SignedInfo>    

    <SignatureValue>    

      encoded output of signature algorithm    

    </SignatureValue>    

    <!-- Optional KeyInfo used to convey key    

         information needed to verify    

         signature -->    

    <KeyInfo>    

      <KeyName>Sally Smith's Integrity Key</KeyName>    

    </KeyInfo>    

    <!-- optional Object to allow additional    

         information to be associated with    

         signature, such as meta information    

         for example (time and purpose of    

         signing) -->    

    <Object>    

      <SignatureProperties>    

        <p:Purpose xmlns:p="http://www.myexample.com/schemas">    

          Integrity    

        </p:Purpose>    

      </SignatureProperties>    

    </Object>    

  </Signature>    

</PatientRecord>

*Example 4 - Detailed XML Signature Example*

请注意，只有一处提到了“URI”，意思是“本文件”。如果只对`<Account>`元素进行签名，可以使用 id 属性值来引用它，如下所示:`<Reference URI="#acct">`。如果没有 id 属性(可能没有预料到签名)，可以使用 XPath 表达式，产生下面的`<Reference>`:

```

```
<Reference URI="">    

    <Transforms>    

        <Transform    

Algorithm="https://www.w3.org/TR/1999/REC-xpath-19991116">    

            <XPath>    

                /PatientRecord/account     

            </XPath>    

        </Transform>    

        <Transform    

Algorithm="https://www.w3.org/TR/2000/WD-xml-c14n-20000710" />    

    </Transforms>    

    <DigestMethod    

Algorithm="https://www.w3.org/2000/07/xmldsig#sha1" />    

    <DigestValue> kjsdf </DigestValue>    

</Reference>

*Example 5 - Reference Transform Using XPath*

**保密性:XML 加密(XML Enc)** 

*目的和益处*

XML 加密建议定义了 XML 词汇表和处理规则，使机密性能够应用于各种内容。XML 加密的目的是在传输和存储过程中维护信息的机密性。其他保密性技术，如安全套接字层(SSL)/传输层安全性(TLS)或虚拟专用网络(VPN)，仅在信息传输时提供保密性，而不是在信息存储在服务器上时提供保密性。

内容的所有者可以对其进行加密以使其保密。这将使内容无法理解，直到它被解密。加密通常使用对称密钥加密来执行，因为即使对于大型文档，这也是一种有效的技术。对称密钥加密使用相同的密钥进行加密和解密。要将机密信息发送给接收者，发送者还必须与接收者共享对称密钥，但不能与任何其他人共享。如果没有人与人之间的接触，这可能会很困难。

为了避免这个问题并使与许多人共享机密内容变得更容易，设计了非对称或公钥加密。公钥加密使用一对匹配的密钥，一个用于加密，一个用于解密。这允许发送方使用接收方的公钥进行加密，这是一个可以广泛共享的密钥。解密需要使用只有接收者知道的私钥。这有助于解决建立保密通信的困难。因为公钥加密不如对称加密有效，所以它们通常一起使用。对称密钥用于加密内容，然后使用公钥加密对对称密钥进行加密。然后，加密的内容和加密的对称密钥都被发送给接收者。

总而言之，发送者可以使用以下步骤秘密地发送内容:

1.  使用对称密钥加密内容

3.  使用收件人的公钥加密对称密钥

5.  将加密内容、加密密钥和必要的算法信息打包在一起

7.  将包裹发送给收件人

接收方可以通过以下步骤获得原始内容:

1.  解包以获得算法信息、加密对称密钥和加密内容

3.  用他们的私钥解密对称密钥

5.  用对称密钥解密内容

*特性*

XML 加密建议定义了 XML 加密和解密的框架和处理规则。它定义了一个 XML 词汇表，用于打包处理加密内容所需的所有信息，例如加密算法和参数、关于密钥的信息以及加密内容。XML 加密建议支持以下功能:

1.  XML 和非 XML 内容可以被加密，从而使该建议具有广泛的适用性。

*   机密性可以以精细的粒度级别应用于 XML 内容。它可以应用于 XML 元素和 XML 元素内容以及整个 XML 文档。这对于保护要通过中间处理器路由的 XML 协议消息部分是很有价值的。*   XML 加密从格式良好的 XML 生成格式良好的 XML。这允许 XML 内容的一部分被加密，然后由 XML 工具进行处理。*   XML 加密与 XML 数字签名兼容，可以与 XML 数字签名结合使用。*   XML 加密允许对可能与加密内容打包在一起的对称密钥进行加密。*   XML 加密支持多种加密算法和技术。

*关键概念*

1.  当 XML 元素或元素内容被加密时，它会被一个`<EncryptedData>`元素替换。

*   当非 XML 内容被加密时，结果是包含一个`<EncryptedData>`元素的新 XML 文档。*   一个`<EncryptedData>`元素可以包括一个类型属性来帮助接收者解密它。该类型可以指示 XML 元素或元素内容被加密，或者给出其他信息的类型，例如图像。这是使用现有的邮件附件标准来完成的，称为 MIME 类型。*   `<EncryptedData>`元素定义用于加密的算法，提供加密的内容，并且可能包括确定解密所需的密钥所必需的信息。*   用于加密内容的对称密钥可以在`<EncryptedKey>`元素中传送。*   XML 加密支持选择适当的加密算法，并为常见情况定义 XML 标识符，还可以扩展到其他情况。*   用于标识关键信息的定义基于 XML 数字签名定义并进行扩展。*   用户定义的属性可以与加密元素相关联，例如时间戳或日志引用。*   实际的密文(加密的结果)是使用一个`<CipherData>`元素指定的。这可能包含一个`<CipherValue>` 元素中的实际加密数据，或者是一个存储在别处的加密数据的 URI 引用(`<CipherReference>`)。当加密的数据很大并且大多数方(如处理中介)不需要时，引用是有用的。

XML 安全中的一个重要问题是 XML 数字签名和 XML 加密的交互。假设您收到一个带有 XML 签名和一个`<EncryptedData>`元素的文档，如下所示:

```
<PatientRecord     

  xmlns:lab="http://www.lab.org/">     

    <Name> John Doe </Name>     

    <Account> 123456 </Account>     

    <EncryptedData Type='element'>     

        ...      

    </EncryptedData>     

    <Signature>     

        <SignedInfo>     

            <Reference URI="">     

                ...     

            </Reference>     

        </SignedInfo>     

    </Signature>     

</PatientRecord>

*Example 6 - Encrypted and Signed Document*

在这种情况下，签名表明它适用于整个 `<PatientRecord>`文档，因为`<Reference>` URI 是`""`。这就产生了一个问题:是签名还是加密先出现？了解这一点很重要，因为只有在内容没有改变的情况下，签名才能被验证为正确。如果文档的一部分在签名后被加密，签名将不会被验证，除非加密的部分首先被解密。为了知道如何验证签名，了解加密和解密的顺序是非常重要的。XML 加密转换建议[ [ XMLDecTrans ](http://www.w3c.org/TR/xmlenc-decrypt) ]定义了一个解决方案。签名时，签名者必须确定哪些`<EncryptedData>`元素是签名的一部分。这允许签名验证者明确地知道在验证签名之前哪些`<EncryptedData>`元素必须被解密。

XML 加密还带来了其他安全问题，尤其是那些“已知明文攻击”。如果知道特定的文本被加密了，比如一个元素(比如`<Visit>`)，那么破解加密就更容易了。因为 XML 很冗长，而且元素的名称可以从 XML 词汇表的模式定义中得知，所以更有可能发生明文攻击。因此，在选择加密算法及其参数时必须格外小心，以防止使用这些信息会破坏机密性。

*例题*

 `<EncryptedData>`和`<EncryptedKey>` 元素的使用可以使用先前的例子来总结，其中账户信息为了保密而被加密。在 XML 加密候选建议[ [ XML Enc ](http://www.w3c.org/TR/xmlenc-core/) ]中解释了关于该示例的 EncryptedData 和 EncryptedKey 部分的附加信息:

```

```
<PatientRecord     

  xmlns:lab="http://www.lab.org/tests">     

  <Name>John Doe</Name>      

  <EncryptedData     

Type='https://www.w3.org/2001/04/xmlenc#Element'     

xmlns='https://www.w3.org/2001/04/xmlenc#'>     

    <EncryptionMethod     

Algorithm='https://www.w3.org/2001/04/xmlenc#3des-cbc'/>      

    <ds:KeyInfo     

xmlns:ds='https://www.w3.org/2000/09/xmldsig#'>      

      <EncryptedKey Id='EK'     

xmlns='https://www.w3.org/2001/04/xmlenc#'>     

        <EncryptionMethod     

Algorithm="https://www.w3.org/2001/04/xmlenc#rsa-1_5" />      

        <ds:KeyInfo     

xmlns:ds='https://www.w3.org/2000/09/xmldsig#'>      

          <ds:KeyName>     

            Dr Kutter's public key pair     

          </ds:KeyName>     

        </ds:KeyInfo>       

        <CipherData>     

          <CipherValue>xyzabc</CipherValue>     

        </CipherData>      

        <CarriedKeyName>     

          Dr Kutter's symmetric key     

        </CarriedKeyName>     

      </EncryptedKey>      

      <ds:KeyName>     

        Dr Kutter's symmetric key     

      </ds:KeyName>     

    </ds:KeyInfo>      

    <CipherData>     

      <CipherValue>a17xj2z</CipherValue>     

    </CipherData>     

  </EncryptedData>      

  <Visit date="10pm March 10, 2002">     

    <Diagnosis>     

      Broken second metacarpal     

    </Diagnosis>     

    <lab:Diagnosis>     

      <lab:Xray>xhzhez</lab:Xray>     

    </lab:Diagnosis>     

  </Visit>      

  <Signature xmlns='https://www.w3.org/2000/09/xmldsig#'>      

    <SignedInfo>      

      <SignatureMethod     

Algorithm="https://www.w3.org/2000/07/xmldsig#rsa-sha1" />      

      <!-- signature on entire PatientRecord     

           before encryption is default interpretation -->      

      <Reference URI="">     

        <Transforms     

Algorithm="https://www.w3.org/TR/2000/WD-xml-c14n-20000710" />      

        <DigestMethod     

Algorithm="https://www.w3.org/2000/07/xmldsig#sha1" />     

        <DigestValue>kjsdf</DigestValue>     

      </Reference>     

    </SignedInfo>     

    <SignatureValue>xjksdasd</SignatureValue>     

    <KeyInfo>     

      <KeyName>Sally Smith's Integrity Key</KeyName>      

    </KeyInfo>      

  </Signature>      

</PatientRecord>

*Example 7 - Detailed XML Encryption Example*

**密钥管理:XML 密钥管理规范(XKMS)** 

*目的和益处*

XML 密钥管理规范(XKMS) [ [ XKMS ](http://www.w3c.org/TR/xkms2/) ]定义了公钥管理服务的协议。公钥管理包括公钥和私钥对的创建、该密钥对与身份和其他属性的绑定，以及该密钥对以不同格式的表示，例如通过密钥名称、数字证书或密钥参数来表示。公钥技术是 XML 数字签名、XML 加密和其他安全应用的重要组成部分。签名时，私钥用于签名，公钥用于验证签名。加密时，公钥用于加密，私钥用于解密。在任一情况下，私钥必须在所有者的控制下维护，而公钥可以与其他人共享。XKMS 旨在帮助管理公钥的共享，以便为收件人启用签名验证和加密。

公钥管理通常需要一个注册步骤，在该步骤中生成密钥对，并颁发某种令牌，以将公钥与所有者的身份和其他属性相关联。这一注册步骤通常包含某种尽职调查，以降低错误关联公钥的风险，因为人们以后会依赖该密钥对。管理还包括在情况发生变化时撤销信息与密钥对的关联的能力，例如私钥被盗或所有者的属性发生变化(例如，不再是雇员)。同样，绑定到密钥对的信息可以被更新。传统上，这种绑定是使用 X.509 数字证书、专用协议和公钥基础设施来管理的。XKMS 定义了 XML 消息格式来支持公钥管理的请求和响应，包括注册、撤销和更新。这消除了应用程序支持其他专用公钥注册和管理协议的需要。

注册完成后，公钥对可用于签名和验证或加密和解密。XML 数字签名建议中定义的<keyinfo>元素可用于向数字签名或加密数据块的接收者提供有关处理该内容所需的密钥的信息。该信息可以采取多种不同的形式，例如密钥名、包含公钥的数字证书、一组密钥参数或指示从哪里获得公钥的 URI。考虑到向接收者提供关于密钥的信息的多种选择，应用程序可能很难处理和定位密钥。除了很难预测所有格式之外，有些格式(如 X.509 证书)需要特殊的加密代码和逻辑来处理。XKMS 提供了一种 XML 消息格式，允许由服务来执行这种处理，该服务将对各种选项进行排序，确定使用的选项，并以有用的形式向接收方提供密钥信息。</keyinfo>

*特性*

XML 密钥管理规范(XKMS)定义了三种规范:

*   XML 密钥注册服务规范(XKRSS)，

*   XML 密钥信息服务规范(XKISS)

*   协议绑定

这些规范定义了注册和管理与公钥相关的信息以及确保满足安全性要求所必需的 XML 请求和响应消息。

注册服务支持信息与公钥对的绑定，公钥对可以是服务器生成的，也可以是客户端生成的。绑定将信息与密钥对相关联，创建一个`<KeyBinding>`元素。该绑定可以具有与之相关联的有效期，并且可以被重新发布或撤销。如果本地副本被破坏(当操作系统需要重新安装时)，与密钥绑定相关联的私钥也可以被备份恢复。

关键信息服务允许客户请求与`<KeyInfo>`元素相关联的信息。这可能包括:

1.  **定位**——解析< KeyInfo >元素，返回请求的密钥信息

3.  **Validate**——定位密钥信息，并提供绑定到密钥对的有效性断言。

密钥信息请求可以指定要返回的密钥信息的形式。例如，它可能要求从一个`<KeyInfo>`元素中确定一个`<KeyName>`和`<KeyValue>`。作为该过程的一部分，服务器可以执行验证以断言绑定到密钥的有效性。

协议绑定规范定义了满足安全需求的机制，包括确保消息机密性、完整性和安全性的机制。这包括以下定义:

*   避免重放攻击的两阶段协议

*   等待响应协议

*   有效载荷安全性的使用

*   使用 SSL/TLS 等传输安全性

*   Web 服务安全性的使用(见下文)

*关键概念*

关于 XKMS 规范的要点是:

1.  该规范定义了 XML 协议消息，以向信任服务器传送密钥注册和信息请求，并传送来自服务器的响应。该规范定义了这些消息与 XML 协议(SOAP)的绑定，并使用 Web 服务定义语言(WSDL)定义了消息之间的关系。

*   客户端将处理委托给信任服务，从而最小化客户端的复杂性。如何实现信任服务取决于服务，但是充当公钥基础设施(PKI)的前端是一种可能性。*   XML 密钥信息服务规范(XKISS)包括等同于传统 PKI OCSP 协议的在线状态功能，作为验证功能的一部分。*   注册支持智能卡制造的要求，包括批量处理和等待响应。*   该规范支持使用 XML 数字签名来实现消息完整性和身份验证。该规范还定义了其他认证机制、对密钥所有权证明的支持以及其他安全功能。*   定位或验证请求可以在请求中包括`<KeyInfo>`元素和`<RespondWith>`元素。`<RespondWith>`元素用于指定`<KeyInfo>`元素将被解析成什么，可能不止一个项目。例如，请求`<KeyInfo>`可能包含一个 X.509 证书，而`<RespondWith>`可能表示要返回 KeyName 和 KeyValue。规范中列出的可能性包括 KeyName、KeyValue 和证书、证书链(需要将签名追溯到可信方的证书集合)。*   一个`<KeyBinding>`元素用于将信息与一个键相关联。这是定位或验证响应中返回的内容。每个`<KeyBinding>`包括一个`<ValidityInterval>` (NotBefore，NotOnOrAfter)，还可能包括`<KeyInfo>`、`<ProcessInfo>`(不透明数据)、`<KeyUsage>`和`<UseKeyWith>`元素。

	*   密钥使用定义被有意限制为加密、签名和密钥交换。

	*   一个`<KeyBinding> <UseKeyWith>`元素定义了这个键打算用于哪个应用和应用实体。例如，密钥可能只适用于 SSL 服务器的身份验证。在这种情况下，应用程序是 HTTPS，标识符是服务器的 URL。规范中列出的应用包括 S/MIME、HTTPS、SMTP、IPSec、PKIX 等。

*例题*

客户端可能会生成一个密钥对，并希望将其注册到信任服务器，如本例所示。(XKMS 规范中提供了完整的示例):

```
<RegisterRequest      

  xmlns:ds="https://www.w3.org/2000/09/xmldsig#"      

  Service="http://test.xmltrustcenter.org/XKMS"      

  RequestId="hZMRGyATbUL4H7rYOanR6Q=="      

  >      

  <RespondWith>X509Cert</RespondWith>      

  <Prototype Id="tX4Y83grmj/eIVoeYNuTNg==">      

    <KeyInfo>      

      <ds:KeyValue>      

        <ds:RSAKeyValue>      

          <ds:Modulus>      

            zvbTdKsTprGAKJdgi7ulDR0eQBptL...      

          </ds:Modulus>       

          <ds:Exponent> AQAB </ds:Exponent>       

        </ds:RSAKeyValue>      

      </ds:KeyValue>      

    </KeyInfo>       

    <KeyUsage>Signature</KeyUsage>      

    <UseKeyWith      

      Application="urn:ietf:rfc:2633"      

      Identifier="alice@alicecorp.test" />       

  </Prototype>       

  <Authentication>      

    <ProofOfPossession>       

      <ds:Signature>      

        signing with the private key      

        demonstrates possession of it      

      </ds:Signature>       

    </ProofOfPossession>      

  </Authentication>      

</RegisterRequest> 

*Example 8 - XKMS Register Request*

服务器响应以`<RespondWith>`请求的信息:

```

```
<RegisterResult xmlns:ds="https://www.w3.org/2000/09/xmldsig#"       

                Service="http://test.xmltrustcenter.org/XKMS"       

                ResultMajor="Success"       

                RequestId="hZMRGyATbUL4H7rYOanR6Q=="       

                ResponseId="k9gyjDdhLLV1vbF7RzJjIw=="       

                >      

    <KeyBinding Id="LVrJqd26QzO9GWJD0usQwg==">      

        <KeyInfo>      

           <KeyName>Sally Smith key</KeyName>      

        </KeyInfo>      

        <KeyUsage>Signature</KeyUsage>      

        <UseKeyWith Application="urn:ietf:rfc:2633"       

        Identifier="alice@alicecorp.test" />      

    </KeyBinding>      

</RegisterResult>

*Example 9 - XKMS Register Response*

客户端可以请求身份验证:

```

```
<ValidateRequest xmlns:ds="https://www.w3.org/2000/09/xmldsig#"       

                 xmlns:xenc="https://www.w3.org/2001/04/xmlenc#"       

                 Service="http://test.xmltrustcenter.org/XKMS"       

                 RequestId="zzjmNi9YL+dnkRXzDoqPoQ=="       

                 >      

    <RespondWith>KeyName</RespondWith>      

    <RespondWith>KeyValue</RespondWith>      

    <RespondWith>Multiple</RespondWith>      

    <KeyBindingQuery Id="T/QMi7gGuKCcNWPi120A/w==">      

        <Keyoo 
```

## 分享这篇文章

```

```

```****