# 如何做一个简单的 JavaScript 测试

> 原文：<https://www.sitepoint.com/simple-javascript-quiz/>

“我如何做一个 JavaScript 测试？”是学习 web 开发的人最常问的问题之一，这是有道理的。小测验很有趣！它们是学习新主题的好方法，并且可以让你用有趣的东西吸引你的观众。

编写自己的 JavaScript 测验也是一个很好的学习练习。它教你如何处理事件、操纵 DOM、处理用户输入、给用户反馈以及跟踪他们的分数(例如，使用客户端存储)。当您启动并运行一个基本的测验时，有一大堆增加更高级功能的可能性，比如分页。我在文章的结尾[对此进行了探讨。](#whatsnext)

在本教程中，我将指导您创建一个多步骤的 JavaScript 测验，您可以根据自己的需要进行调整并添加到自己的站点中。如果你想知道我们最终会得到什么，你可以跳过这一步去看[工作测验](#demo)。

*要获得更深入的 JavaScript 知识，请阅读我们著名的 [JavaScript:新手到忍者，第二版](https://www.sitepoint.com/premium/books/javascript-novice-to-ninja-2nd-edition/)。*

## 开始前需要注意的事项

开始前需要知道的几件事:

*   这是一个前端教程，意味着任何知道如何浏览页面源代码的人都可以找到答案。对于严肃的测验，数据需要通过后端处理，这超出了本教程的范围。
*   本文中的代码使用了现代 JavaScript 语法(ES6+)，这意味着它不会与任何版本的 Internet Explorer 兼容。然而，它可以在现代浏览器上正常工作，包括微软 Edge。
*   如果你需要支持旧的浏览器，我写了一个兼容 IE8 的 JavaScript 测试教程。或者，如果你想复习 ES6，可以在 SitePoint Premium 上查看 Darin Haener 的[课程。](https://www.sitepoint.com/premium/courses/diving-into-es2015-2924)
*   您需要熟悉 HTML、CSS 和 JavaScript，但是每一行代码都将被单独解释。

## JavaScript 测验的基本结构

理想情况下，我们希望测验的问题和答案在我们的 JavaScript 代码中，并让我们的脚本自动生成测验。这样，我们就不需要写很多重复的标记，并且我们可以很容易地添加和删除问题。

要设置 JavaScript 测验的结构，我们需要从下面的 HTML 开始:

*   举行小测验
*   A `<button>`提交测验
*   显示结果的一个`<div>`

这看起来是这样的:

```
<div id="quiz"></div>
<button id="submit">Submit Quiz</button>
<div id="results"></div> 
```

然后，我们可以选择这些 HTML 元素，并将对它们的引用存储在变量中，如下所示:

```
const quizContainer = document.getElementById('quiz');
const resultsContainer = document.getElementById('results');
const submitButton = document.getElementById('submit'); 
```

接下来，我们需要一种方法来建立一个测验，显示结果，并把它们放在一起。我们可以从设计我们的功能开始，我们将在设计过程中填充这些功能:

```
function buildQuiz(){}

function showResults(){}

// display quiz right away
buildQuiz();

// on submit, show results
submitButton.addEventListener('click', showResults); 
```

这里，我们有一些函数来构建测验并显示结果。我们将立即运行我们的`buildQuiz`函数，当用户单击提交按钮时，我们将运行我们的`showResults`函数。

## 显示测验问题

我们测验需要的下一件事是一些要显示的问题。我们将使用对象文字来表示单个问题，并使用一个数组来保存构成测验的所有问题。使用数组将使问题易于迭代:

```
const myQuestions = [
  {
    question: "Who invented JavaScript?",
    answers: {
      a: "Douglas Crockford",
      b: "Sheryl Sandberg",
      c: "Brendan Eich"
    },
    correctAnswer: "c"
  },
  {
    question: "Which one of these is a JavaScript package manager?",
    answers: {
      a: "Node.js",
      b: "TypeScript",
      c: "npm"
    },
    correctAnswer: "c"
  },
  {
    question: "Which tool can you use to ensure code quality?",
    answers: {
      a: "Angular",
      b: "jQuery",
      c: "RequireJS",
      d: "ESLint"
    },
    correctAnswer: "d"
  }
]; 
```

请随意输入您想要的问题或答案。

*注意:由于这是一个数组，问题将按照它们列出的顺序出现。如果您想在将问题呈现给用户之前对其进行排序，请查看我们关于在 JavaScript 中对一组对象进行排序的快速技巧。*

现在我们有了问题列表，我们可以在页面上显示它们。我们将一行一行地浏览下面的 JavaScript，看看它是如何工作的:

```
function buildQuiz(){
  // variable to store the HTML output
  const output = [];

  // for each question...
  myQuestions.forEach(
    (currentQuestion, questionNumber) => {

      // variable to store the list of possible answers
      const answers = [];

      // and for each available answer...
      for(letter in currentQuestion.answers){

        // ...add an HTML radio button
        answers.push(
          `<label>
            <input type="radio" name="question${questionNumber}" value="${letter}"> ${letter} : ${currentQuestion.answers[letter]} </label>`
        );
      }

      // add this question and its answers to the output
      output.push(
        `<div class="question"> ${currentQuestion.question} </div>
        <div class="answers"> ${answers.join('')} </div>`
      );
    }
  );

  // finally combine our output list into one string of HTML and put it on the page
  quizContainer.innerHTML = output.join('');
} 
```

首先，我们创建一个`output`变量来包含所有的 HTML 输出，包括问题和答案选项。

接下来，我们可以开始为每个问题构建 HTML。我们需要像这样循环每个问题:

```
myQuestions.forEach( (currentQuestion, questionNumber) => {
  // the code we want to run for each question goes here
}); 
```

为了简洁起见，我们使用一个[箭头函数](https://www.sitepoint.com/arrow-functions-javascript/)来对每个问题执行操作。因为这是在一个 [forEach](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/forEach) 循环中，所以我们得到当前值、索引(当前项在数组中的位置号)和数组本身作为参数。我们只需要当前值和索引，出于我们的目的，我们将分别命名为`currentQuestion`和`questionNumber`。

现在让我们看看循环内部的代码:

```
// we'll want to store the list of answer choices
const answers = [];

// and for each available answer...
for(letter in currentQuestion.answers){

  // ...add an html radio button
  answers.push(
    `<label>
      <input type="radio" name="question${questionNumber}" value="${letter}"> ${letter} : ${currentQuestion.answers[letter]} </label>`
  );
}

// add this question and its answers to the output
output.push(
  `<div class="question"> ${currentQuestion.question} </div>
  <div class="answers"> ${answers.join('')} </div>`
); 
```

对于每个问题，我们都希望生成正确的 HTML，所以我们的第一步是创建一个数组来保存可能答案的列表。

接下来，我们将使用一个循环来填充当前问题的可能答案。对于每一个选择，我们都创建了一个 HTML 单选按钮，并将其封装在一个`<label>`元素中。这是为了让用户能够单击答案文本上的任意位置来选择答案。如果省略了标签，那么用户将不得不点击单选按钮本身，这是不太容易访问的。

注意，我们使用的是[模板文字](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals)，它们是字符串，但是功能更强大。我们将利用以下功能:

*   多线能力
*   不再需要对引号中的引号进行转义，因为模板文字使用反斜杠
*   字符串插值，所以你可以像这样把 JavaScript 表达式嵌入到你的字符串中:`${code_goes_here}`。

一旦我们有了答案按钮列表，我们就可以将问题 HTML 和答案 HTML 推送到我们的整体输出列表中。

注意，我们使用一个模板文本和一些嵌入式表达式来首先创建问题 div，然后创建答案 div。`join`表达式获取我们的答案列表，并将它们放在一个字符串中，我们可以将它输出到我们的`answers` div 中。

现在我们已经为每个问题生成了 HTML，我们可以将它们连接在一起并显示在页面上:

```
quizContainer.innerHTML = output.join(''); 
```

现在我们的`buildQuiz`函数完成了。

此时，您应该能够运行测验，并看到显示的问题。但是，请注意，代码的结构很重要。由于被称为[时间死区](https://wesbos.com/temporal-dead-zone/)的东西，你不能在你的问题数组被定义之前引用它。

概括地说，这是正确的结构:

```
// Functions
function buildQuiz(){ ... }
function showResults(){ ... }

// Variables
const quizContainer = document.getElementById('quiz');
const resultsContainer = document.getElementById('results');
const submitButton = document.getElementById('submit');
const myQuestions = [ ... ];

// Kick things off
buildQuiz();

// Event listeners
submitButton.addEventListener('click', showResults); 
```

## 显示测验结果

在这一点上，我们想要构建我们的`showResults`函数来循环这些答案，检查它们，并显示结果。

下面是这个函数，我们接下来将详细介绍它:

```
function showResults(){

  // gather answer containers from our quiz
  const answerContainers = quizContainer.querySelectorAll('.answers');

  // keep track of user's answers
  let numCorrect = 0;

  // for each question...
  myQuestions.forEach( (currentQuestion, questionNumber) => {

    // find selected answer
    const answerContainer = answerContainers[questionNumber];
    const selector = `input[name=question${questionNumber}]:checked`;
    const userAnswer = (answerContainer.querySelector(selector) || {}).value;

    // if answer is correct
    if(userAnswer === currentQuestion.correctAnswer){
      // add to the number of correct answers
      numCorrect++;

      // color the answers green
      answerContainers[questionNumber].style.color = 'lightgreen';
    }
    // if answer is wrong or blank
    else{
      // color the answers red
      answerContainers[questionNumber].style.color = 'red';
    }
  });

  // show number of correct answers out of total
  resultsContainer.innerHTML = `${numCorrect} out of ${myQuestions.length}`;
} 
```

首先，我们选择测验的 HTML 中的所有答案容器。然后，我们将创建变量来跟踪用户的当前答案和正确答案的总数。

```
// gather answer containers from our quiz
const answerContainers = quizContainer.querySelectorAll('.answers');

// keep track of user's answers
let numCorrect = 0; 
```

现在我们可以循环每个问题并检查答案。

```
// for each question...
myQuestions.forEach( (currentQuestion, questionNumber) => {

  // find selected answer
  const answerContainer = answerContainers[questionNumber];
  const selector = `input[name=question${questionNumber}]:checked`;
  const userAnswer = (answerContainer.querySelector(selector) || {}).value;

  // if answer is correct
  if(userAnswer === currentQuestion.correctAnswer){
    // add to the number of correct answers
    numCorrect++;

    // color the answers green
    answerContainers[questionNumber].style.color = 'lightgreen';
  }
  // if answer is wrong or blank
  else{
    // color the answers red
    answerContainers[questionNumber].style.color = 'red';
  }
}); 
```

这部法典的主旨是:

*   在 HTML 中查找所选答案
*   如果答案是正确的，处理发生的事情
*   如果答案是错的，处理发生的事情。

让我们更仔细地看看我们是如何在 HTML 中找到所选答案的:

```
// find selected answer
const answerContainer = answerContainers[questionNumber];
const selector = `input[name=question${questionNumber}]:checked`;
const userAnswer = (answerContainer.querySelector(selector) || {}).value; 
```

首先，我们要确保在当前问题的答案容器中寻找答案。

在下一行中，我们将定义一个 CSS 选择器，它将让我们找到哪个单选按钮被选中。

然后我们使用 JavaScript 的`querySelector`在之前定义的`answerContainer`中搜索 CSS 选择器。本质上，这意味着我们将找到哪个答案的单选按钮被选中。

最后，我们可以通过使用`.value`得到那个答案的值。

### 处理不完整的用户输入

但是如果用户留下了一个空白答案呢？在这种情况下，使用`.value`会导致一个错误，因为你不能得到不存在的东西的值。为了解决这个问题，我们添加了表示“或”的`||`和空对象`{}`。现在总的声明说:

*   获取对我们选择的答案元素的引用，如果不存在，则使用空对象。
*   获取第一条语句中的值。

因此，该值要么是用户的答案，要么是`undefined`，这意味着用户可以跳过一个问题，而不会破坏我们的测验。

### 评估答案并显示结果

我们的答案检查循环中的下一个语句将让我们处理正确和错误的答案。

```
// if answer is correct
if(userAnswer === currentQuestion.correctAnswer){
  // add to the number of correct answers
  numCorrect++;

  // color the answers green
  answerContainers[questionNumber].style.color = 'lightgreen';
}
// if answer is wrong or blank
else{
  // color the answers red
  answerContainers[questionNumber].style.color = 'red';
} 
```

如果用户的答案与正确的选项相匹配，则将正确答案的数量增加一，并将选项集涂成绿色(可选)。如果答案是错误的或空白的，将答案选项涂成红色(也是可选的)。

一旦答案检查循环完成，我们可以显示用户答对了多少个问题:

```
// show number of correct answers out of total
resultsContainer.innerHTML = `${numCorrect} out of ${myQuestions.length}`; 
```

现在我们有了一个可行的 JavaScript 测验！

如果您愿意，您可以将整个测验包装在一个[life(立即调用的函数表达式)](https://developer.mozilla.org/en-US/docs/Glossary/IIFE)中，这是一个一旦您定义它就运行的函数。这将使您的变量不在全局范围内，并确保您的测验不会干扰页面上运行的任何其他脚本。

```
(function(){
  // put the rest of your code here
})(); 
```

现在你都准备好了！请随意添加或删除问题和答案，并按照您喜欢的方式设计测验。

此时，您的测验可能看起来像这样(带有一点点样式):

参见 [CodePen](https://codepen.io) 上 SitePoint([@ SitePoint](https://codepen.io/SitePoint))
的 Pen [简单 JavaScript 问答(无分页)](https://codepen.io/SitePoint/pen/rmoLbW)。