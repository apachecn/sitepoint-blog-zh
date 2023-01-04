# MongoDB 3.6 中的 JSON 模式验证和表达性查询语法

> 原文：<https://www.sitepoint.com/json-schema-validation-expressive-query-syntax-in-mongodb-3-6/>

*本文最初发表于 [MongoDB](https://www.mongodb.com/blog/post/mongodb-36-json-schema-validation-expressive-query-syntax?utm_medium=sp-synd&utm_source=sitepoint&utm_content=expressive-query&jmp=sp-ref) 。感谢您对使 SitePoint 成为可能的合作伙伴的支持。*

MongoDB 的主要优势之一一直是增强开发人员的能力:通过依赖灵活的模式架构，MongoDB 使应用程序能够更容易、更快地从概念验证阶段过渡到生产阶段，并随着需求的发展迭代更新周期。

但是，随着应用程序的成熟和扩展，它们往往会达到一个稳定的阶段，在这个阶段，频繁的模式更改不再重要，或者必须以更受控制的方式推出，以防止不需要的数据被插入到数据库中。当多个应用程序写入同一个数据库时，或者当分析过程依赖于预定义的数据结构来保证准确性和有用性时，这些控制尤其重要。

MongoDB 3.2 是第一个引入[文档验证](https://docs.mongodb.com/manual/core/document-validation?utm_medium=sp-synd&utm_source=sitepoint&utm_content=expressive-query&jmp=sp-ref)的版本，这是习惯于关系数据库的开发人员和 DBA 一直要求的特性之一。正如 MongoDB 的 CTO Eliot Horowitz 在[中强调的文档验证和动态模式意味着什么](http://www.eliothorowitz.com/blog/2015/09/11/document-validation-and-what-dynamic-schema-means/):

> 与 3.2“需要时使用模式”特性一起，文档验证为 MongoDB 提供了一种新的、强大的保持数据整洁的方式。这些肯定不是我们将提供的最终工具集，而是 MongoDB 处理模式的重要一步。

## 宣布支持 JSON 模式验证

在 MongoDB 3.2 的文档验证功能的基础上， [MongoDB 3.6](https://www.mongodb.com/mongodb-3.6?utm_medium=sp-synd&utm_source=sitepoint&utm_content=expressive-query&jmp=sp-ref) 引入了一种更强大的在数据库中实施模式的方法，它支持 JSON 模式验证，这是 IETF 新兴的 [JSON 模式](http://json-schema.org/)标准的一部分。

JSON 模式验证以许多不同的方式扩展了文档验证，包括在数组中实施模式和防止添加未经批准的属性的能力。这些是我们将在这篇博文中关注的新特性，以及构建业务验证规则的能力。

从 MongoDB 3.6 开始，JSON 模式是实施[模式验证](https://docs.mongodb.com/master/core/schema-validation?utm_medium=sp-synd&utm_source=sitepoint&utm_content=expressive-query&jmp=sp-ref)的推荐方式。下一节重点介绍使用 JSON 模式验证的特性和好处。

## 从文档验证切换到 JSON 模式验证

我们将首先创建一个*订单*集合(基于我们在[文档验证教程博文](https://www.mongodb.com/blog/post/document-validation-part-2-putting-it-all-together-a-tutorial?utm_medium=sp-synd&utm_source=sitepoint&utm_content=expressive-query&jmp=sp-ref)中发布的一个例子):

```
db.createCollection("orders", {
  validator: {
    item: { $type: "string" },
    price: { $type: "decimal" }
  }
}); 
```

有了这个文档验证配置，我们不仅要确保*项目*和*价格*属性出现在任何*订单*文档中，还要确保*项目*是一个字符串，而*价格*是一个小数(这是所有货币和百分比值的推荐类型)。因此，不能插入以下元素(因为" rogue" *price* 属性):

```
db.orders.insert({
    "_id": 6666, 
    "item": "jkl", 
    "price": "rogue",
    "quantity": 1 }); 
```

但是，可以插入以下文档(注意拼写错误的“pryce”属性):

```
db.orders.insert({
    "_id": 6667, 
    "item": "jkl", 
    "price": NumberDecimal("15.5"),
    "pryce": "rogue" }); 
```

在 MongoDB 3.6 之前，您无法防止添加拼写错误或未经授权的属性。让我们看看 JSON 模式验证如何防止这种行为。为此，我们将使用一个新的操作符， [$jsonSchema](https://docs.mongodb.com/master/reference/operator/query/jsonSchema?utm_medium=sp-synd&utm_source=sitepoint&utm_content=expressive-query&jmp=sp-ref#op._S_jsonSchema) :

```
db.runCommand({
  collMod: "orders",
  validator: {
    $jsonSchema: {
      bsonType: "object",
      required: ["item", "price"],
      properties: {

       item: {
            bsonType: "string"
       },
       price: {
          bsonType: "decimal"
        }
      }
    }
  }
}); 
```

上面的 JSON 模式完全等同于我们之前在上面的 *orders* 集合中设置的文档验证规则。让我们通过使用 Mongo shell 中的 [db.getCollectionInfos()](https://docs.mongodb.com/manual/reference/method/db.getCollectionInfos?utm_medium=sp-synd&utm_source=sitepoint&utm_content=expressive-query&jmp=sp-ref) 方法，检查我们的模式是否已经更新为使用新的 *$jsonSchema* 操作符:

```
db.getCollectionInfos({name:"orders"}) 
```

这个命令打印出关于*订单*集合的大量信息。为了便于阅读，下面是包含 JSON 模式的部分:

```
...
"options" : {
    "validator" : {
        "$jsonSchema" : {
            "bsonType" : "object",
            "required" : [
                "item",
                "price"
            ],
            "properties" : {
                "item" : {
                    "bsonType" : "string"
                },
                "price" : {
                    "bsonType" : "decimal"
                }
            }
        }
    },
    "validationLevel" : "strict",
    "validationAction" : "error"
}
... 
```

现在，让我们稍微丰富一下我们的 JSON 模式，以便更好地利用它的强大功能:

```
db.runCommand({
  collMod: "orders",
  validator: {
    $jsonSchema: {
      bsonType: "object",
      <strong>additionalProperties: false</strong>,
      required: ["item", "price"],
      properties: {
       <strong>_id: {}</strong>,
       item: {
            bsonType: "string",
            description: "'item' must be a string and is required"
        },
        price: {
          bsonType: "decimal",
          description: "'price' must be a decimal and is required"
        },
        quantity: {
          <strong>bsonType: ["int", "long"]</strong>,
          minimum: 1,
          maximum: 100,
          exclusiveMaximum: true,
          description:
            "'quantity' must be short or long integer between 1 and 99"
        }
      }
    }
  }
}); 
```

让我们回顾一下我们对模式所做的添加:

*   首先，注意**additional properties:false**属性的使用:它阻止我们添加除了那些在*属性*部分中提到的属性之外的任何属性。例如，不再可能插入包含拼写错误的*普莱斯*属性的数据。因此，在文档的根级别使用**additional properties:false**也使得对 *_id* 属性的声明成为强制性的:无论我们的插入代码是否显式设置它，它都是 MongoDB 需要的字段，如果不存在，它将自动创建。因此，我们必须在模式的*属性*部分显式包含它。
*   第二，我们选择将*数量*属性声明为一个介于 1 和 99 之间的短整数或长整数(使用*最小值*、*最大值*和*排他最大值*属性)。当然，因为我们的模式只允许小于 100 的整数，我们可以简单地将 bsonType 属性设置为 *int* 。但是添加 *long* 作为有效类型使得应用程序代码更加灵活，特别是如果可能有计划取消最大*限制的话。*
*   最后，注意*描述*属性(出现在*项目*、*价格*和*数量*属性声明中)完全是可选的，除了为读者记录模式之外，对模式没有任何影响。

使用上面的模式，可以将以下文档插入到我们的订单集合中:

```
db.orders.insert({ 
    "item": "jkl", 
    "price": NumberDecimal(15.50),
    "quantity": NumberInt(99)
  });

  db.orders.insert({ 
    "item": "jklm", 
    "price": NumberDecimal(15.50),
    "quantity": NumberLong(99)
  }); 
```

但是，以下文件不再被视为有效:

```
db.orders.insert({ 
    "item": "jkl", 
    "price": NumberDecimal(15.50),
    <strong>"quantity": NumberInt(100)</strong>
  });
  db.orders.insert({ 
    "item": "jkl", 
    "price": NumberDecimal(15.50),
    <strong>"quantity": "98"</strong>
  });
  db.orders.insert({ 
    "item": "jkl", 
    <strong>"pryce": NumberDecimal(15.50),</strong>
    "quantity": NumberInt(99)
  }); 
```

你可能注意到我们上面的订单看起来很奇怪:他们只包含一个单一的项目。更实际的情况是，一个订单由多个项目组成，一个可能的 JSON 结构如下:

```
{
    _id: 10000,
    total: NumberDecimal(141),
    VAT: 0.20,
    totalWithVAT: NumberDecimal(169),
    lineitems: [
        {
            sku: "MDBTS001",
            name: "MongoDB Stitch T-shirt",
            quantity: NumberInt(10),
            unit_price:NumberDecimal(9)
        },
        {
            sku: "MDBTS002",
            quantity: NumberInt(5),
            unit_price: NumberDecimal(10)
        }
    ]
} 
```

使用 MongoDB 3.6，我们现在可以控制`lineitems`数组的结构，例如使用下面的 JSON 模式:

```
db.runCommand({
    collMod: "orders",
    validator: {
      $jsonSchema: {
        bsonType: "object",       
        required: ["lineitems"],
        properties: {
        lineitems: {
              <strong>bsonType: ["array"],</strong>
              minItems: 1,
              maxItems:10,
              items: {
                  required: ["unit_price", "sku", "quantity"],
                  bsonType: "object",
                  additionalProperties: false,
                  properties: {
                      sku: {
                        bsonType: "string",
                        description: "'sku' must be a string and is required"
                      },
                      name: {
                        bsonType: "string",
                        description: "'name' must be a string"
                      },
                      unit_price: {
                        bsonType: "decimal",
                        description: "'unit_price' must be a decimal and is required"
                      },
                      quantity: {
                        bsonType: ["int", "long"],
                        minimum: 0,
                        maximum: 100,
                        exclusiveMaximum: true,
                        description:
                          "'quantity' must be a short or long integer in [0, 100)"
                      },
                  }                    
              }
          }
        }
      }
    }
  }); 
```

使用上面的模式，我们强制在 orders 集合中插入或更新的任何订单都包含一个由 1 到 10 个文档组成的`lineitems`数组，这些文档都具有`sku`、`unit_price`和`quantity`属性(数量必须是整数)。

该架构将阻止插入以下格式错误的文档:

```
db.orders.insert({
        total: NumberDecimal(141),
        VAT: NumberDecimal(0.20),
        totalWithVAT: NumberDecimal(169),
        lineitems: [
            {
                sku: "MDBTS001",
                name: "MongoDB Stitch T-shirt",
                quantity: NumberInt(10),
                price: NumberDecimal(9) //this should be 'unit_price'
            },
            {
                name: "MDBTS002", //missing a 'sku' property
                quantity: NumberInt(5),
                unit_price: NumberDecimal(10)
            }
        ]
}) 
```

但是它允许插入以下符合模式的文档:

```
db.orders.insert({
        total: NumberDecimal(141),
        VAT: NumberDecimal(0.20),
        totalWithVAT: NumberDecimal(169),
        lineitems: [
            {
                sku: "MDBTS001",
                name: "MongoDB Stitch T-shirt",
                quantity: NumberInt(10),
                unit_price: NumberDecimal(9)
            },
            {
                sku: "MDBTS002",
                quantity: NumberInt(5),
                unit_price: NumberDecimal(10)
            }
        ]
}) 
```

但是，如果您密切注意上面的顺序，您可能会注意到它包含一些错误:

1.  `totalWithVAT`属性值不正确(应该等于 141*1.20=169.2)
2.  `total`属性值不正确(应等于各行项目小计之和，(即 10*9+10*5=140)

有没有什么方法可以使用数据库验证规则强制保证`total`和`totalWithVAT`值是正确的，而不仅仅依赖于应用程序逻辑？

## 介绍 MongoDB 表达式查询语法

由于表达式查询语法(MongoDB 3.6 的一个新特性)，现在可以添加更复杂的业务验证规则。

表达式查询语法的目标之一是将 MongoDB 的[聚合表达式](https://docs.mongodb.com/manual/core/aggregation-pipeline/#pipeline-expressions?utm_medium=sp-synd&utm_source=sitepoint&utm_content=expressive-query&jmp=sp-ref)的能力引入 MongoDB 的[查询语言](https://docs.mongodb.com/manual/tutorial/query-documents?utm_medium=sp-synd&utm_source=sitepoint&utm_content=expressive-query&jmp=sp-ref)。一个有趣的用例是编写动态验证规则的能力，这些规则在运行时计算和比较多个属性值。使用新的 [$expr](https://docs.mongodb.com/manual/reference/operator/query/expr?utm_medium=sp-synd&utm_source=sitepoint&utm_content=expressive-query&jmp=sp-ref#op._S_expr) 操作符，可以使用下面的验证表达式来验证 *totalWithVAT* 属性的值:

```
$expr: {
   $eq: [
     "$totalWithVAT",
     {$multiply: [
       "$total", 
       {$sum: [1, "$VAT"]}
     ]}
   ]
} 
```

上面的表达式检查了`totalWithVAT`属性值是否等于`total * (1+VAT)`。以其简洁的形式，下面是我们如何使用它作为验证规则，以及我们的 JSON 模式验证:

```
db.runCommand({
    collMod: "orders",
    validator: {
 <strong>$expr:{$eq:[
           "$totalWithVAT",
           {$multiply:["$total", {$sum:[1,"$VAT"]}]}
             ]}</strong>,
      $jsonSchema: {
        bsonType: "object",       
        required: ["lineitems"],
        properties: {
          lineitems: {
              bsonType: ["array"],
              minItems: 1,
              maxItems:10,
              items: {
                  required: ["unit_price", "sku", "quantity"],
                  bsonType: "object",
                  additionalProperties: false,
                  properties: {
                      sku: {
                        bsonType: "string",
                        description: "'sku' must be a string and is required"
                      },
                      name: {
                        bsonType: "string",
                        description: "'name' must be a string"
                      },
                      unit_price: {
                        bsonType: "decimal",
                        description: "'unit_price' must be a decimal and is required"
                      },
                      quantity: {
                        bsonType: ["int", "long"],
                        minimum: 0,
                        maximum: 100,
                        exclusiveMaximum: true,
                        description:
                          "'quantity' must be a short or long integer in [0, 100)"
                      },
                  }                    
              }
          }
        }
      }
    }
  }); 
```

有了上面的验证器，下面的插入操作就不再可能了:

```
db.orders.insert({
        total: NumberDecimal(141),
        VAT: NumberDecimal(0.20),
        totalWithVAT: NumberDecimal(169),
        lineitems: [
            {
                sku: "MDBTS001",
                name: "MongoDB Stitch T-shirt",
                quantity: NumberInt(10),
                Unit_price: NumberDecimal(9)
            },
            {
                sku: "MDBTS002",
                quantity: NumberInt(5),
                unit_price: NumberDecimal(10)
            }
        ]
}) 
```

相反，`totalWithVAT`值必须根据我们新的增值税验证规则进行调整:

```
db.orders.insert({
    total: NumberDecimal(141),
    VAT: NumberDecimal(0.20),
    <strong>totalWithVAT: NumberDecimal(169.2)</strong>,
    lineitems: [
            {
                sku: "MDBTS001",
                name: "MongoDB Stitch T-shirt",
                quantity: NumberInt(10),
                unit_price: NumberDecimal(9)
            },
            {
                sku: "MDBTS002",
                quantity: NumberInt(5),
                unit_price: NumberDecimal(10)
            }
        ]
}) 
```

如果我们还想确保*总计*值是每个订单行项目值的总和(即*数量*单价*)，则应使用以下表达式:

```
$expr: { 
    $eq: [
       "$total", 
       {$sum: {
          $map: {
             "input": "$lineitems",
             "as": "item",
             "in": { 
                "$multiply": [
                   "$$item.quantity", 
                   "$$item.unit_price"
                ]
             } 
          }
       }}
    ]
  } 
```

上面的表达式使用 [$map](https://docs.mongodb.com/manual/reference/operator/aggregation/map?utm_medium=sp-synd&utm_source=sitepoint&utm_content=expressive-query&jmp=sp-ref) 运算符计算每个行项目的小计，然后[对所有这些小计求和](https://docs.mongodb.com/manual/reference/operator/aggregation/sum?utm_medium=sp-synd&utm_source=sitepoint&utm_content=expressive-query&jmp=sp-ref)，最后[将其与*总计*值进行比较。为了确保 Total 和 VAT 验证规则都被检查，我们必须使用](https://docs.mongodb.com/manual/reference/operator/aggregation/eq?utm_medium=sp-synd&utm_source=sitepoint&utm_content=expressive-query&jmp=sp-ref)[$和](https://docs.mongodb.com/manual/reference/operator/aggregation/and?utm_medium=sp-synd&utm_source=sitepoint&utm_content=expressive-query&jmp=sp-ref)操作符将它们组合起来。最后，我们的集合验证器可以用下面的命令更新:

```
db.runCommand({
    collMod: "orders",
    validator: {
      $expr:{ $and:[
          {$eq:[ 
            "$totalWithVAT",
                   {$multiply:["$total", {$sum:[1,"$VAT"]}]}
          ]}, 
          {$eq: [
                   "$total", 
                {$sum: {$map: {
                    "input": "$lineitems",
                    "as": "item",
                    "in":{"$multiply":["$$item.quantity","$$item.unit_price"]}
                   }}}
             ]}
        ]},
      $jsonSchema: {
        bsonType: "object",       
        required: ["lineitems", "total", "VAT", "totalWithVAT"],
        properties: {
          total: { bsonType: "decimal" },
          VAT: { bsonType: "decimal" },
          totalWithVAT: { bsonType: "decimal" },
          lineitems: {
              bsonType: ["array"],
              minItems: 1,
              maxItems:10,
              items: {
                  required: ["unit_price", "sku", "quantity"],
                  bsonType: "object",
                  additionalProperties: false,
                  properties: {
                      sku: {bsonType: "string"},
                      name: {bsonType: "string"},
                      unit_price: {bsonType: "decimal"},
                      quantity: {
                        bsonType: ["int", "long"],
                        minimum: 0,
                        maximum: 100,
                        exclusiveMaximum: true

                      },
                  }                    
              }
          }
        }
      }
    }
  }); 
```

因此，我们必须更新`total`和`totalWithVAT`属性，以符合我们更新的模式和业务验证规则(不改变`lineitems`数组):

```
db.orders.insert({
      total: NumberDecimal(140),
      VAT: NumberDecimal(0.20),
      totalWithVAT: NumberDecimal(168),
      lineitems: [
          {
              sku: "MDBTS001",
              name: "MongoDB Stitch T-shirt",
              quantity: NumberInt(10),
              unit_price: NumberDecimal(9)
          },
          {
              sku: "MDBTS002",
              quantity: NumberInt(5),
              unit_price: NumberDecimal(10)
          }
      ]
  }) 
```

## 后续步骤

随着 MongoDB 3.6 中 JSON 模式验证的引入，数据库管理员现在能够更好地满足来自合规官或监管者的数据治理需求，同时仍然受益于 MongoDB 灵活的模式架构。

此外，开发人员会发现新的表达式查询语法非常有用，通过将业务逻辑从应用程序层移到数据库层，可以使他们的应用程序代码更加简单。

如果您想了解更多关于 MongoDB 3.6 的新内容，请下载我们的[新内容指南](https://www.mongodb.com/collateral/mongodb-36-whats-new?utm_medium=sp-synd&utm_source=sitepoint&utm_content=expressive-query&jmp=sp-ref)。

如果你想深入了解技术方面，请访问我们官方文档中的[模式验证](https://docs.mongodb.com/master/core/schema-validation?utm_medium=sp-synd&utm_source=sitepoint&utm_content=expressive-query&jmp=sp-ref)和[表达查询语法](https://docs.mongodb.com/manual/reference/operator/query/expr?utm_medium=sp-synd&utm_source=sitepoint&utm_content=expressive-query&jmp=sp-ref)页面。

如果您想获得更实际的实践经验，可以看看这个 [JSON 模式验证实践实验室](https://github.com/rlondner/mongodb-3.6-hols/tree/master/json-schemas)。你可以马上在 [MongoDB Atlas 数据库服务](https://www.mongodb.com/cloud/atlas?utm_medium=sp-synd&utm_source=sitepoint&utm_content=expressive-query&jmp=sp-ref)上试用，该服务自 MongoDB 3.6 正式发布之日起就支持它。

最后但同样重要的是，报名参加我们来自 MongoDB 大学的[免费 MongoDB 3.6 培训](https://university.mongodb.com/courses/M036/about?utm_medium=sp-synd&utm_source=sitepoint&utm_content=expressive-query&jmp=sp-ref)。

## 分享这篇文章