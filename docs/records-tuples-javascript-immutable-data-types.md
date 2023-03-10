# 记录和元组:JavaScript 新的不可变数据类型

> 原文：<https://www.sitepoint.com/records-tuples-javascript-immutable-data-types/>

**记录和元组是新的 JavaScript 不可变数据类型，目前处于 TC39 标准批准过程的[阶段 2。它们可能会发生变化，目前在任何浏览器或运行时都不可用，但明年应该会有可用的实现。它们有助于解决程序员面临的一些令人困惑的难题……](https://github.com/tc39/proposal-record-tuple)**

## 不断的变化

专业的 JavaScripters 人员会告诉你，在可能的情况下，用`const`给变量赋值是最佳实践。它使得变量*不可变*。价值观是不能改变的，所以你要处理的问题更少。

不幸的是，`const`只让原始值不可变(字符串、数字、BigInt、布尔、符号和`undefined`)。你不能重新分配一个数组或一个对象，但是它们包含的值和属性*可以*修改。例如:

```
// array constant
const myArray = [1, 2, 3];

// change array values
myArray[0] = 99;
myArray.push(42);

console.log(myArray); // [ 99, 2, 3, 42 ]

myArray = 'change'; // ERROR! 
```

类似地，对于对象:

```
// object constant
const myObj = { a: 1, b: 2, c: 3 }

// change object properties
myObj.a = 99;
myObj.d = 42;

console.log(myObj); // { a:99 ,b:2, ,c:3, ,d:42 }

myObj = 'change'; // ERROR! 
```

[`Object.freeze()`](https://developer.mozilla.org/Web/JavaScript/Reference/Global_Objects/Object/freeze) 方法会有所帮助，但是仅浅冻结应用于对象的直接子属性:

```
const myObj = { a: 1, b: 2, c: { v: 3 } }
Object.freeze(myObj);

myObj.a = 99; // silently ignored
myObj.c.v = 99; // works fine

console.log(myObj); // { a: 1, b: 2, c: { v: 99 } } 
```

因此，很难保证函数不会有意或无意地改变数组或对象中的值。开发人员必须要么抱最好的希望，要么传递一个变量的克隆版本— *(它有自己的挑战)*。

## 等价不等式

当开发人员试图进行看似合理的对象或数组比较时，会出现进一步的混乱:

```
const str = 'my string';
console.log( str === 'mystring' );  // true

const num = 123;
console.log( num === 123 );         // true

const arr = [1, 2, 3];
console.log( arr === [1, 2, 3] );   // false

const obj = { a: 1 };
console.log( obj === { a: 1 } );    // false 
```

只有基元类型可以通过值进行比较。对象和数组通过引用进行传递和比较*。只有当两个变量指向内存中的同一项时，它们才是等价的:*

```
const a = [1, 2];

const b = a;
b.push(3);

console.log( a === b ); // true

// original array has changed
console.log( a ); // [1, 2, 3] 
```

深入比较两个对象或数组需要一个递归比较函数来依次评估每个值。即使这样，您也可能会遇到日期或函数等类型的问题，这些类型可能会以不同的方式存储。

## 元组:不可变的类似数组的数据结构

元组是非常不可变的类似数组的数据结构。它们实际上是复合的基本类型，用普通数组语法前面的一个`#`修饰符来标识:

```
// new tuples
const t1 = #[1, 2, 3];
const t2 = #[1, 2, #[3, 4]]; 
```

或者，一个新的`Tuple.from()`方法可以从数组中创建一个元组:

```
// new tuple from an array
const t3 = Tuple.from( [1, 2, 3] ); 
```

与标准数组不同，元组必须满足以下要求:

1.  它们不能有未设定值的*孔*。例如，`#[1,,,4]`无效。
2.  它们必须只设置原语、其他元组或记录。不允许数组、对象或函数等类型:

```
 const t4 = #[ new Date() ]; // ERROR (sets an object)
  const t5 = #[1, 2, [3, 4]]; // ERROR (sets an array) 
```

因为元组是原语，所以它们可以通过值与其他元组进行深度比较:

```
const t6 = #[1, 2];

console.log( t6 === #[1, 2] ); // true 
```

注意，如果元组保存单个值，使用不太严格的`==`操作符进行比较是可能的。例如:

```
const t7 = #[99];

console.log( t7 == #[99] ); // true
console.log( t7 == 99 );    // true
console.log( t7 == '99' );  // true

// tuple cannot be compared to an array
console.log( t7 == [99] );  // false 
```

## 记录:不可变的类对象数据结构

记录是非常不可变的类似对象的数据结构。同样，它们是复合的基本类型，用普通对象语法前面的一个`#`修饰符来标识:

```
// new records
const r1 = #{ a: 1, b: 2 };
const r2 = #{
  a: 1,
  b: #{ c: 2 }, // child record
  d: #[ 3, 4 ]  // child tuple
}; 
```

或者，新的`Record()`构造函数可以从一个对象创建一个记录:

```
// new record from an object
// #{ a: 1, b: 2 }
const r3 = Record({ a: 1, b: 2 }); 
```

或者，`Record.fromEntries()`方法可以从一系列数组或元组值对中创建一条记录:

```
// new record from array of name-values
// #{ a: 1, b: 2 }
const r4 = Record.fromEntries([
  ['a', 1],
  ['b', 2]
]); 
```

与标准对象不同，记录必须满足以下要求:

1.  它们必须使用字符串属性名。例如，`#{ Symbol(): 1 }`无效。
2.  它们只能使用原语、其他元组或记录来设置值。不允许数组、对象或函数等类型:

```
 const r5 = #{ 'd': new Date() };   // ERROR (sets an object)
  const r6 = #{ a: 1, b: { c: 2 } }; // ERROR (sets an object) 
```

记录可以与其他记录进行深度比较，并且属性顺序无关紧要:

```
const r7 = #{ a: 1, b: 2 };

console.log( r7 === #{ b: 2, a: 1 } ); // true 
```

记录只能与其他记录进行比较，所以使用`==`或`===`操作符没有区别。但是，可以提取对象 [`keys()`](https://developer.mozilla.org/Web/JavaScript/Reference/Global_Objects/Object/keys) 和 [`values()`](https://developer.mozilla.org/Web/JavaScript/Reference/Global_Objects/Object/values) 进行具体比较。例如:

```
const r8 = #{ a: 99 };

console.log( Object.values(r8) == 99 ); // true 
```

## 不可变更新

元组和记录可能听起来像复杂的计算机科学术语，但它们最终将允许 JavaScript 中健壮的不可变数据存储和比较。你可以今天就在[这个游乐场](https://rickbutton.github.io/record-tuple-playground/)或[这个聚合填充物](https://github.com/bloomberg/record-tuple-polyfill)试用它们，但请注意，拟议的实施可能会在未来几个月内发生变化。

## 分享这篇文章