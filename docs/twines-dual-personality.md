# Twine 的双重人格

> 原文：<https://www.sitepoint.com/twines-dual-personality/>

有了[绳](http://www.twine.com)，你在屏幕上看到的只是故事的一半。拉开窗帘，它展现了一个全新的一面，渴望和街区里的其他孩子一起玩耍。

当我登录并查看我的个人资料页面时，我看到以下内容:

![Twine screenshot](img/05950876242c4da5f4906ee7ba5883bf.png)

语义 Web 客户端看到了这一点:

```
 <rdf:RDF>
<app:User rdf:about="http://www.twine.com/user/davidseth">
<web:views rdf:datatype="https://www.w3.org/2001/XMLSchema#nonNegativeInteger">17</web:views>
<radar:createdDate rdf:datatype="https://www.w3.org/2001/XMLSchema#dateTime">2008-03-11T19:28:23.722-0500</radar:createdDate>
<radar:lastModifiedDate rdf:datatype="https://www.w3.org/2001/XMLSchema#dateTime">2008-03-14T18:47:51.546-0500</radar:lastModifiedDate>
<basic:url rdf:resource="http://davidseth.net"/>
<basic:url rdf:resource="https://www.sitepoint.com/articlelist/497"/>
<basic:url rdf:resource="http://boab.info"/>
<basic:url rdf:resource="http://davidseth.net/foaf.rdf"/>
<radar:isPerson rdf:resource="http://www.twine.com/item/1tx4lphj-g5"/>
<app:status rdf:datatype="https://www.w3.org/2001/XMLSchema#string" xml:space="preserve">linkinging it all together in the tropics</app:status>
<app:location rdf:datatype="https://www.w3.org/2001/XMLSchema#string" xml:space="preserve">Townsville, QLD Australia</app:location>
</app:User>
</rdf:RDF> 
```

现在图形视图:

![twine-graph-davidseth](img/4b4163ce43c0031b25740ee1ee530320.png)
*图 1——麻线(黄点是我)*

虽然上图不会成为卢浮宫展出的下一件作品，但它至少包括了一件美丽的东西。

**不起眼的网址**

那个漂亮的东西就是网址。这代表了*me*作为<http:>的概念。从技术上来说，这是一个 URI，但数据网络的全部意义在于将事物联系起来，并且可以被发现。这意味着我应该能够去<http:>并且实际上**得到**(你看到 HTTP/REST 连接了吗)的意思。</http:></http:>

事情越来越有趣了

这就是关联数据的魔力所在。我可以用我的 [FOAF](http://data.boab.info/david/foaf.rdf) 文件(这只是一个文本文件)得到这个图形:

![twine-graph-foaf-final](img/9f76800dfec5812987030624d27a4297.png)
*图 2——FOAF(黄点是我，红点是汤斯维尔)*

最后，我可以**得到**我住的地方【http://dbpedia.org/resource/Townsville】<>，这是图表:

![twine-graph-townsville](img/f0385d7b0d6c5625cfcc81222ee572cb.png)
*图 3——Dbpedia(红点为汤斯维尔)*

**把点连起来**

你可能已经注意到了上面图表中的彩色点。

*   **图 1** 有一个黄点，代表绳线上的我。
*   **图 2** 上也有一个代表我 FOAF 的黄点，还有一个红点，因为我住在汤斯维尔。
*   **图 3** 有一个红点代表汤斯维尔。

因为我们处理的是 RDF 图，所以可以很容易地将这些独立的数据集合并，以创建一个更大、更完整的图。创建了一个路径，可以很容易地从我的 Twine 个人资料->到我的 FOAF 个人资料->到 Dbpedia Townsville 条目。

![twine linked data](img/8fbd1872c43f35ec4627b74a45562f54.png)

这是语义网真正酷的部分，有人真的想过这个东西:)我可以用一个 SPARQL 查询来查询一个数据服务(Twine)、一个文本文件(我的 FOAF 文件)和一个网站(Dbpedia)。

**巨型全球图**

![Giant Global Graph](img/160b61da02a11c251f4296564345d9e8.png)

这只是一个巨大的全球图表，准备被有创造力的人挖掘出来，创造下一个伟大的混搭。

*http://www.flickr.com/people/hotmeteor/——大虾，布鲁塞尔自由大学认知科学研究中心——图片*

## 分享这篇文章