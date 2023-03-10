# 如何战胜 5 个常见的 JavaScript 面试挑战

> 原文：<https://www.sitepoint.com/5-common-coding-interview-challenges/>

一段时间以来，科技面试的方式一直是争议的焦点。这是一个敏感的话题，尤其是涉及到编码挑战的时候。

并不是所有的公司都使用相同的筛选过程，但在大多数情况下，预计会被要求解决一个编码挑战，要么在一个合适的平台上，要么在可怕的白板上。

通常对编码挑战的一个抱怨是，它们大多与实际工作所需的日常任务无关。尤其是涉及到前端面试问题的时候，有时候很好奇为什么面试中缺少的只是一些前端相关的问题，比如浏览器兼容性，布局方法和 DOM 事件。虽然这可能是真的，但那些支持这种方法并在这些公司负责招聘的人经常会说这样的话:

> 我宁愿雇佣一个聪明的人来教他们 X，也不愿雇佣一个对 X 了如指掌但缺乏创造力、逻辑和推理的人。— [在旧金山面试前端工程师](https://css-tricks.com/interviewing-front-end-engineer-san-francisco/)

无论我们对候选人筛选开发工作的方式有何看法，在撰写本文时，编码挑战仍然是面试过程中的一大部分。

在本文中，我将向您展示如何应对五个常见的编码挑战，这些挑战在您面试 JavaScript 或前端初级开发人员职位时可能会被问到。他们不是你在面试过程中遇到的最难的人，但是你对待他们的方式可能会决定成败。

## 应对技术面试中编码挑战的几点建议

在面对挑战之前，让我们先来看看如何应对科技面试的一些技巧。

*   **投入时间准备**。优先进行研究，学习不太熟悉的话题，大量练习。如果你没有计算机科学背景，确保你熟悉一些与**算法和数据结构**相关的基础话题。有免费和付费的在线平台，为你提供练习面试技巧的好方法。 [GeeksforGeeks](https://www.geeksforgeeks.org/) 、 [Pramp](https://www.pramp.com/#/) 、[interview . io](https://interviewing.io/)和 [CodeSignal](https://codesignal.com/) 只是我最喜欢的几个资源。
*   当你试图想出一个解决方案时，练习大声思考。事实上，在面试中谈论你的思维过程比花所有的时间在完全沉默中写下你的解决方案更好。如果你要转错弯，你的话会给面试官一个帮助你的机会。这也凸显了你的沟通技巧。
*   **了解问题再开始编码**。这很重要。否则，你可能会浪费时间去思考错误的问题。此外，它迫使你思考你可能会问面试官的问题，如边缘案例、输入/输出的数据类型等。
*   **练习手写代码**。这有助于您熟悉白板场景。白板不提供您的代码编辑器所提供的那种帮助，如快捷方式、自动完成、格式等。准备的时候，试着把你的代码写在一张纸上或者白板上，而不是在脑子里想出来。

## 常见的 JavaScript 编码挑战

很可能你已经遇到了我下面列出的一个或多个挑战，无论是在求职面试中还是在练习 JavaScript 技能时。还有什么更好的理由让你变得真正擅长解决这些问题呢？

让我们开始吧！

### #1 回文

回文是一个单词、句子或其他类型的字符序列，向后读和向前读是一样的。例如，“赛车”和“安娜”是回文。“桌子”和“约翰”不是回文，因为它们从左到右和从右到左读起来不一样。

#### 理解挑战

这个问题可以按如下方式陈述:给定一个字符串，如果该字符串是回文，则返回 true，否则返回 false。包括空格和标点符号来判断字符串是否是回文。例如:

```
palindrome('racecar')  ===  true
palindrome('table')  ===  false 
```

#### 关于挑战的推理

这个挑战围绕着反转一根弦的想法。如果反过来的字符串和原来的输入字符串相同，那么你有一个回文，你的函数应该返回 true。相反，如果反向的字符串与原始的输入字符串不同，后者就不是回文，函数将返回 false。

#### 解决办法

这里有一种方法可以解决回文挑战:

```
const palindrome = str => {
  // turn the string to lowercase
  str = str.toLowerCase()
  // reverse input string and return the result of the
  // comparisong
  return str === str.split('').reverse().join('')
} 
```

首先将输入字符串转换成小写字母。因为你知道你要将这个字符串中的每个字符与反过来的字符串中的每个对应字符进行比较，让所有的字符都是小写或大写将确保比较将忽略字符的这一方面，而只关注字符本身。

接下来，反转输入字符串。您可以通过使用字符串的[将字符串转换成数组来实现。split()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/split) 方法，然后应用数组的[。reverse()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/reverse) 方法，最后用数组的[将反转后的数组变回字符串。【join()【方法。我把上面所有的方法都链接起来，这样代码看起来更干净。](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/join)

最后，将反转后的字符串与原始输入进行比较，并返回结果—根据两者是否完全相同，结果将为真或假。

### 第二名 FizzBuzz

这是一个非常受欢迎的编码挑战——一个我不可能遗漏的问题。你可以这样陈述这个问题。

#### 理解挑战

FizzBuzz 挑战大概是这样的。编写一个执行以下操作的函数:

*   console 记录从 1 到 n 的数字，其中 n 是函数将其作为参数的整数
*   对数*嘶嘶作响*而不是数字为 3 的倍数
*   圆木*嗡鸣*而不是 5 的倍数
*   为 3 和 5 的倍数的数字记录 *fizzbuzz*

示例:

```
fizzBuzz(5) 
```

结果:

```
// 1
// 2
// fizz
// 4
// buzz 
```

#### 关于挑战的推理

关于 FizzBuzz 的一个重要观点是，你如何在 JavaScript 中找到一个数字的倍数。你可以使用[模或余数操作符](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Arithmetic_Operators#Remainder_%28%29)来实现，看起来像这样:`%`。该运算符返回两个数相除后的余数。余数为 0 表示第一个数字是第二个数字的倍数:

```
12 % 5 // 2 -> 12 is not a multiple of 5
12 % 3 // 0 -> 12 is multiple of 3 
```

如果你把 12 除以 5，结果是 2，余数是 2。如果你把 12 除以 3，结果是 4，余数是 0。在第一个例子中，12 不是 5 的倍数，而在第二个例子中，12 是 3 的倍数。

有了这些信息，破解 FizzBuzz 就是使用适当的条件逻辑来打印预期的输出。

#### 解决办法

这里有一个解决方案，你可以在 FizzBuzz 挑战赛中尝试一下:

```
const fizzBuzz = num => {
  for(let i = 1; i <= num; i++) {
    // check if the number is a multiple of 3 and 5
    if(i % 3 === 0 && i % 5 === 0) {
      console.log('fizzbuzz')
    } // check if the number is a multiple of 3
      else if(i % 3 === 0) {
      console.log('fizz')
    } // check if the number is a multiple of 5
      else if(i % 5 === 0) {
      console.log('buzz')
    } else {
      console.log(i)
    }
  }
} 
```

上面的函数只是使用条件语句进行所需的测试，并记录预期的输出。在这个挑战中，你需要注意的是`if … else`语句的顺序。先从 double 条件(`&&`)开始，到找不到倍数的情况结束。这样，你就可以面面俱到了。

### #3 字谜

如果一个单词和另一个单词使用相同数量的相同字母，但排列不同，那么这个单词就是另一个单词的变位词。

#### 理解挑战

你可以用下面的术语来陈述这个挑战:写一个函数来检查两个提供的字符串是否是彼此的变位组合；字母大小写不重要。此外，只考虑字符，不考虑空格或标点符号。例如:

```
anagram('finder', 'Friend')  --> true
anagram('hello', 'bye') --> false 
```

#### 关于挑战的推理

首先要注意的是，您需要比较两个输入字符串中的每个字母，不仅要比较它们是哪些字母，还要比较两个字符串中每个字母的数量。映射此信息的方式如下所示:

```
finder --> f: 1        friend --> f: 1
           i: 1                   r: 1
           n: 1                   i: 1
           d: 1                   e: 1
           e: 1                   n: 1
           r: 1                   d: 1 
```

保存字谜数据的合适的数据结构是 JavaScript 对象文字:关键字是字母字符，值是该字母在给定字符串中包含的次数。

接下来，请注意这些边缘情况:

*   确保字母大小写不是比较的因素；只需将两个字符串转换成小写或大写
*   把所有不是角色的东西都排除在比较之外；一个好的选择是使用一个正则表达式。

#### 解决办法

以下是你应对字谜挑战的方法:

```
// helper function that builds the
// object to store the data
const buildCharObject = str => {
  const charObj = {}
  for(let char of str.replace(/[^\w]/g).toLowerCase()) {
    // if the object has already a key value pair
    // equal to the value being looped over,
    // increase the value by 1, otherwise add
    // the letter being looped over as key and 1 as its value
    charObj[char] = charObj[char] + 1 || 1
  }
  return charObj
}

// main function
const anagram = (strA, strB) => {
  // build the object that holds strA data
  const aCharObject = buildCharObject(strA)
  // build the object that holds strB data
  const bCharObject = buildCharObject(strB)

  // compare number of keys in the two objects
  // (anagrams must have the same number of letters)
  if(Object.keys(aCharObject).length !== Object.keys(bCharObject).length) {
    return false
  }
  // if both objects have the same number of keys
  // we can be sure that at least both strings
  // have the same number of characters
  // Now we can compare the two objects to see if both
  // have the same letters in the same amount
  for(let char in aCharObject) {
    if(aCharObject[char] !== bCharObject[char]) {
      return false
    }
  }
  // if both the above checks succeed,
  // you have an anagram: return true
  return true
} 
```

注意上面代码片段中使用的 [Object.keys()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/keys) 。此方法返回一个数组，该数组包含给定对象的属性名或键，其顺序与它们在对象中出现的顺序相同。在这种情况下，该数组将如下所示:

```
['f', 'i', 'n', 'd', 'r'] 
```

这是一种获取对象属性的简便方法，无需执行繁琐的循环。在这个挑战中，您可以将它与`.length`属性一起使用来检查两个字符串是否具有相同的字符数，这是字谜的一个重要特性。

### #4 找到元音

就难度而言，这可能是挑战性较小的挑战之一(没有双关语)，但这并不影响你在求职面试中遇到它的事实。事情是这样的。

#### 理解挑战

您可以如下陈述元音挑战:编写一个函数，该函数将一个字符串作为参数，并返回该字符串中包含的元音数。

元音字母有“a”、“e”、“I”、“o”、“u”。

示例:

```
findVowels('hello') // --> 2
findVowels('why') // --> 0 
```

#### 解决办法

以下是元音挑战的简单迭代解决方案:

```
const findVowels = str => {
  let count = 0
  const vowels = ['a', 'e', 'i', 'o', 'u']
  for(let char of str.toLowerCase()) {
    if(vowels.includes(char)) {
      count++
    }
  }
  return count
} 
```

这里需要注意的一点是[的使用。包含()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/includes)方法。此方法在字符串和数组上都可用。您可以利用它来确定数组是否包含某个值。如果数组包含指定的值，则返回 true，否则返回 false。

对于这一挑战，还有一个更简洁的解决方案，您可以按如下方式设置:

```
const findVowels = str => {
  const matched = str.match(/[aeiou]/gi)
  return matched ? matches.length : 0
} 
```

上面的解决方案围绕着字符串的[。](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/match)方法匹配()。它与正则表达式结合使用，使您能够执行强大的搜索。如果在指定的字符串中找到作为参数传递给此方法的正则表达式的模式，返回值将是匹配字符的数组。如果没有找到匹配项，`.match()`将返回`null`。

### 第五名斐波那契

没有对斐波那契挑战的讨论，这篇文章是不完整的，这是一个你在工作面试或编码实践中肯定会遇到的经典问题。

斐波纳契数列是一种数字排序，其中每个数字都是前两个数字的和。例如，斐波纳契数列的前十个数字是:0，1，1，2，3，5，8，13，21，34。

#### 理解挑战

斐波那契挑战是这样的:写一个函数，返回斐波那契数列中的第 n <sup>个</sup>项，其中 n 是作为参数传递给函数的一个数字。

示例:

```
fibonacci(3)  // --> 2 
```

#### 关于挑战的推理

斐波那契挑战包括迭代函数参数指示的次数，并返回您在该位置找到的斐波那契数列中的条目。

这种陈述挑战的方式直接指向了解决问题的迭代方法。或者，你可以尝试递归解决方案，这肯定会给面试官留下深刻印象。

#### 解决办法

迭代解决方案可能是这样的:

```
const fibonacci = num => {
  // store the Fibonacci sequence you're going
  // to generate inside an array and
  // initialize the array with the first two
  // numbers of the sequence
  const result = [0, 1]

  for(let i = 2; i <= num; i++) {
    // push the sum of the two numbers
    // preceding the position of i in the result array
    // at the end of the result array
    const prevNum1 = result[i - 1]
    const prevNum2 = result[i - 2]
    result.push(prevNum1 + prevNum2)
  }
  // return the last value in the result array
  return result[num]
} 
```

上面的结果数组已经包含了数列中的前两个数字，这是因为斐波纳契数列中的每一项都是由前面两个数字的和组成的。在数列的最开始，没有两个数可以相加产生下一个数，所以你的循环不可能自动产生它们。但是，您知道前两个数字总是 0 和 1，所以您用这两个数字手动初始化您的结果数组。

让我们转到递归方法:

```
const fibonacci = num => {
  // if num is either 0 or 1 return num
  if(num < 2) {
    return num
  }
  // recursion here
  return fibonacci(num - 1) + fibonacci(num - 2)
} 
```

你一直调用`fibonacci()`把越来越小的数字加起来作为参数，直到你碰到了最上面的基本情况，传入的参数不是 0 就是 1。

## 结论

如果你刚刚参加了几轮前端或 JavaScript 开发人员的面试，尤其是初级职位，你可能会遇到至少一两个我上面列出的问题。即使您没有，您也可以使用它们来练习您的 JS 编码技能。

你能想出解决挑战的替代方法吗？让我们听听他们！

## 分享这篇文章