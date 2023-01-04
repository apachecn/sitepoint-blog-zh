# 泛型字典与 XmlSerializer

> 原文：<https://www.sitepoint.com/generic-dictionaries-vs-the-xmlserializer/>

最简洁的新功能之一。NET 2.0 是泛型的概念。这个特性最常见的用途之一是在新的泛型集合中。任何花了大量时间在 1.1 中扩展 CollectionBase 和 DictionaryBase(或者只是使用 CodeSmith 的 collection gen 作弊)的人都会理解这些集合有多么有用。

的另一个非常方便的特性。NET 是内置的、近乎自动化的 XmlSerialization。假设遵循一些简单的规则——主要是拥有一个空白的默认构造函数，并确保所有属性都实现 Get 和 Set 访问器——就可以立即将任何对象序列化为 Xml。不幸的是，当试图使用新的泛型字典作为希望 XmlSerialize 的对象的成员时，这种策略就失败了。您得到的不是一点 xml，而是一个漂亮的小异常，声明“在此反映类型‘您的类型名’时出错”。

有许多方法可以解决这个问题。大多数涉及实现 IXmlSerializable，这往往会导致编写大量重复的代码。如果您正在写入和读取一个非常特定的模式，这些技术是一个很好的选择。然而，很多时候，人们只需要以一种快速、肮脏但有效的方式获得一些序列化的东西，以便与一些内部 Web 服务或某种临时存储一起使用。

在这种情况下，可以使用以下技术:

1.  首先，用 XmlIgnore 属性修饰 Dictionary <tkey tvalue="">属性。这告诉 XmlSerializer 忽略该属性，从而消除反射错误。</tkey>
2.  创建一个新的公共属性，该属性接受并返回 DictionaryEntry 对象的数组。我倾向于将它们特别命名为(_x_DictionaryName)，以便清楚地表明通常不应该使用该属性。
3.  连载吧。

如果上面的内容有点晦涩，请看下面的例子:

```
 using System;
using System.Collections;
using System.Collections.Generic;
using System.Text;
using System.Xml.Serialization;

namespace SPBlog.DictionarySerialization
{
    [XmlRoot("StuffContainer")]
    public class Serializable
    {
        public Serializable() { }

        private Dictionary<string, int> stuff=new Dictionary<string,int>();

        /// <summary>
        /// Public stuff dictionary.
        /// </summary>
        /// <remarks>
        /// Note the XmlIgnore attribute.
        /// </remarks>
        [XmlIgnore()]
        public Dictionary<string, int> Stuff
        {
            set { stuff = value; }
            get { return stuff; }
        }

        /// <summary>
        /// Property created expressly for the XmlSerializer
        /// </summary>
        /// <remarks>
        /// Note the XML Serialiazation attributes; they control what elements are named when this object is serialized.
        /// </remarks>
        [XmlArray("Stuff")]
        [XmlArrayItem("StuffLine", Type=typeof(DictionaryEntry))]
        public DictionaryEntry[] _x_Stuff
        {
            get
            {
                //Make an array of DictionaryEntries to return
                DictionaryEntry[] ret=new DictionaryEntry[Stuff.Count];
                int i=0;
                DictionaryEntry de;
                //Iterate through Stuff to load items into the array.
                foreach (KeyValuePair<string, int> stuffLine in Stuff)
                {
                    de = new DictionaryEntry();
                    de.Key = stuffLine.Key;
                    de.Value = stuffLine.Value;
                    ret[i]=de;
                    i++;
                }
                return ret;
            }
            set
            {
                Stuff.Clear();
                for (int i=0; i<value.Length; i++)
                {
                    Stuff.Add((string)value[i].Key, (int)value[i].Value);
                }
            }
        }
    }
} 

```

这将创建以下 Xml:

```
 <?xml version="1.0" encoding="utf-8"?>
<StuffContainer xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="https://www.w3.org/2001/XMLSchema">
  <Stuff>
    <StuffLine>
      <Key xsi:type="xsd:string">One</Key>
      <Value xsi:type="xsd:int">1</Value>
    </StuffLine>
    <StuffLine>
      <Key xsi:type="xsd:string">Two</Key>
      <Value xsi:type="xsd:int">2</Value>
    </StuffLine>
    <StuffLine>
      <Key xsi:type="xsd:string">Three</Key>
      <Value xsi:type="xsd:int">3</Value>
    </StuffLine>
    <StuffLine>
      <Key xsi:type="xsd:string">Four</Key>
      <Value xsi:type="xsd:int">4</Value>
    </StuffLine>
  </Stuff>
</StuffContainer> 

```

现在，正如您在上面看到的，DictionaryEntry 以一种有趣的方式反序列化——它向键和值节点添加了一个类型属性。如果需要对输出进行更有限的控制，可以很容易地修改这种技术，使用序列化为特定类型的自定义对象。

## 分享这篇文章