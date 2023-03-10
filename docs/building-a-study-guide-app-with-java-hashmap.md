# 用 Java Hashmap 构建学习指南应用程序

> 原文：<https://www.sitepoint.com/building-a-study-guide-app-with-java-hashmap/>

如今，实体词典的概念已经相当过时了。当一个简单的谷歌搜索就能给你所有你需要的信息时，查阅实体书来了解一个单词或短语的意思是没有意义的。

但是对于一个重要的编程概念:键值对，字典是一个非常有用的隐喻。字典通过将单词(一个“键”)与其含义(一个“值”)联系起来来运作。这种键值对是编程中的基本数据模型。

大多数编程语言使用哈希来确保键的唯一性，并使值的存储和检索更加高效。Java 中的`HashMap`将所有这些放在一起，允许程序员在插入和检索时通过散列键来存储键值对。

在本教程中，我们将学习如何通过将 Java 概念及其对应的含义存储在一个散列表中来构建一个学习指南程序，以便在学习时检索。本教程的源代码可以在[这里](https://github.com/sitepoint-editors/sitepoint-hashmap-study-guide)找到

### 散列表

Java 映射通过将键值对存储在单个数据结构中，使程序更加高效。或者，可以用两个手动同步的数据结构来完成同样的工作——一个保存键，另一个保存值——但是这不是将键映射到值的最佳方法；这就是一个`Map`的作用。`HashMap`可以说是其中最强大的一款。映射的所有键必须是同一类型，其值也必须是同一类型。

在后台，Java 的 HashMap 类将这些对存储在数组中，但是我们不必太担心实现，因为大部分繁重的工作都被封装了。

`Map`接口提供了将对放入映射、替换对的值、通过键移除对、通过键检查对的存在以及清除映射的方法。今天，我们将利用几乎所有这些功能。

## 应用程序

我们的应用程序将是一个简单的学习指南应用程序。我们将构建我们的`HashMap`来存储我们的概念，并使用 Java 控制流语句进行循环，直到我们为每个概念提供了正确的含义。我们来建吧！

### 班级

我们需要两个类来运行我们的学习指南应用程序。第一个类将是我们运行程序的主类，第二个类将是另一个类，用于模拟 Java 概念的含义，并封装一些字段和方法来简化学习指南界面。

我们需要做的第一件事是用一个主类创建一个新的 Java 项目。一旦我们设置好了，让我们继续构建将在我们的`Main`中使用的`Meaning`类。

![study](img/06196d0a8db013da53e52e3a7ff238a1.png)

## 意义类

创建一个名为“意义”的新类。我们不会深入讨论这门课，但让我们回顾一下它的重要性。这个类将通过持有 Java 概念的含义和该含义的标记来模拟要研究的 Java 概念的含义。以下是它的重要部分:

```
public class Meaning {
    /*if a user's guess at the meaning matches at least 65% of the 
    actual meaning of a Meaning, the user is correct.*/
    private static final double CORRECTNESS_THRESHOLD_PERCENTAGE = .65;

    //the string representation of this meaning
    private String meaning;
    //whether or not the user's input matches this meaning
    private boolean gotCorrect;
    //holds the tokens of the meaning
    private String[] meaningTokens;

    //the constructor
    public Meaning(String meaning) {
        this.meaning = meaning;
        tokenizeMeaning();
    }

    //omitted ...

    /**
     * This is a naive lexical analyzer
     * that counts how many words match
     * between the user input and this meaning.
     *
     * There are many ways to improve this.
     * @param userInput the user's guess at what this concept means
     * @return true if the user's input matches 
     * this meaning.
     */
    public boolean userInputMatches(String userInput) {
        //make sure the user's input is not empty
        if(userInput != null && !userInput.isEmpty()) {
            int numMatchedTokens = 0;
            userInput = removeAllPunctuation(userInput);
            String[] userInputTokens = userInput.split(" ");

            //total tokens. The greater of the two
            int totalTokens = Math.max(userInputTokens.length, 
            meaningTokens.length);

            /*get the number of matched tokens 
            between the two sets of tokens.*/
            numMatchedTokens = getNumMatchedTokens(userInputTokens);

            //cast to a double to get floating point result
            double matchPercentage = ((double)numMatchedTokens / totalTokens);
            //return whether or not the matched percentage meets the threshold
            return matchPercentage >= CORRECTNESS_THRESHOLD_PERCENTAGE;
        }

        //return false because the user's input is empty 
        return false;
    }

    //omitted ...
} 
```

为了构造一个新的`Meaning`实例，我们将概念的含义传递给构造函数。从那里，含义将被标记化并存储在一个数组中，供我们在检查用户的输入是否与概念的含义匹配时使用。

当程序运行时，用户将被要求输入对一个概念的含义的猜测，我们将通过调用`Meaning`的`userInputMatches()`实例方法来使用该输入来检查用户是否正确。该方法将对用户的输入进行标记化，并遍历含义的标记和用户的输入标记，按顺序计算它们的匹配项。如果 65%的含义符号与用户输入的符号匹配，我们将返回 true 这意味着用户的答案是正确的，所以他们得到一分。

这就是我们需要的全部内容，但是你可以在本教程的支持代码中查看完整的类。

## 主类

我们的学习指导将在主课堂上进行。随便你怎么称呼这个类，但我会把我的叫做“学习指南”。

```
public class StudyGuide {
    //omitted ...

    //initialize a HashMap to hold String-Meaning (key-value) pairs.
    private static final HashMap<String, Meaning> studyGuide = new HashMap<>();
    /*The total possible points the user can obtain. 
    Equal to the number of concepts in the study guide.*/
    private static int possiblePoints;

    //our main method to start our app
    public static void main(String[] args) {
        populateStudyGuide();
        printInstructions();       
        study();
    }

    //omitted ...
} 
```

我们的课开始得相当谦逊。我们首先初始化一个名为`studyGuide`的`final static HashMap`作为我们的学习指南。代表我们的概念的`studyGuide`的所有键将是`String`类型，而我们的概念的值和含义将是我们自定义的`Meaning`类型。

我们通过调用`populateStudyGuide`用概念-意义对填充`studyGuide`来开始我们的应用程序:

```
/**
 * Populates the study guide with Java concepts.
 */
private static void populateStudyGuide() {
    //add a bunch of 
    studyGuide.put("Variable", new Meaning(
            "Used to store a single value for later use."));
    studyGuide.put("String", new Meaning(
            "A class for representing character strings."));
    studyGuide.put("double", new Meaning(
            "A primitive datatype for representing floating point numbers."));
    studyGuide.put("Double", new Meaning(
            "A class for wrapping a double in an Object with convenient methods."));
    studyGuide.put("zero", new Meaning(
            "The number zero. The first index in arrays and the first position of lists."));

    //set the possible points
    updatePossiblePoints();
} 
```

在那里，我们五次调用我们的`HashMap`的`put()`实例方法，每次将一个概念及其含义放在`studyGuide`中。该方法通过调用`updatePossiblePoints()`来设置用户可获得的可能点`static possiblePoints`来结束:

```
private static void updatePossiblePoints() {
        possiblePoints = studyGuide.size();
        System.out.println("There are " 
        + possiblePoints + " concepts to study.");
} 
```

注意，`possiblePoints`应该总是等于`studyGuide`中的元素数量，我们可以通过调用它的`size()`实例方法来收集这些元素。

接下来，我们调用`printInstructions()`为用户将我们的应用程序的指令打印到控制台。之后，是时候学习了:

```
/**
 * Starts the study session
 */
private static void study() {
    //for scanning the user's keyboard for input
    Scanner userInputScanner = new Scanner(System.in);
    //to store the user's input
    String userInput;

    /*the points obtained by the user 
    for getting the meanings of concepts correct.*/
    int userPoints = 0;
    /*how many rounds the user takes 
    to get all of the concept meanings correct*/
    int rounds = 1;

    //set the default user input because the while loop needs to check it
    userInput = "startInConsole";

    /*We'll let the user quit at any point in the app,
    so we must make sure they haven't quit yet*/
    while(userPoints < possiblePoints && !userQuit(userInput, userPoints)) {
        //store the keys of the study guide in a immutable Set
        final Set<String> concepts = studyGuide.keySet();

        for (String concept : concepts) {
            Meaning currentConceptMeaning = studyGuide.get(concept);
            //make sure currentConceptMeaning is not null
            if(currentConceptMeaning == null) {
                studyGuide.remove(concept);
                //move to next iteration
                continue;
            }

            if(!currentConceptMeaning.userGotCorrect()) {
                System.out.printf("\n\t\t" + OUTPUT_SEPERATOR_LINE 
                        + "\n\n> What is %s?\n\n\n", concept);

                if(userInputScanner.hasNextLine()) {
                    userInput = userInputScanner.nextLine();
                }

                if (!userQuit(userInput, userPoints)) {
                    if (currentConceptMeaning.userInputMatches(userInput)) {
                        currentConceptMeaning.markUserGotCorrect();
                        userPoints++;
                        System.out.printf("\n> CORRECT! %s means: %s",
                                concept, currentConceptMeaning.getMeaning());
                    } else {
                        System.out.printf("\n> WRONG! %s means: %s",
                                concept, currentConceptMeaning.getMeaning());
                    }
                }
            }
        }

        System.out.println(OUTPUT_SEPERATOR_LINE);
        System.out.printf("\n> You have %d of %d possible points at the end of round %d. ", 
                userPoints, possiblePoints, rounds);

        //make sure the user hasn't scored all of the possible points
        if(userPoints < possiblePoints) {
            System.out.println("\n> Type anything to continue "
                    + "OR remove to remove a concept \"x\" or \"quit\" to quit?");
            if(userInputScanner.hasNextLine()) {
                userInput = userInputScanner.nextLine();
                if(userInput.toLowerCase().equals("remove")) {
                    System.out.println("\n> Remove which concept?");
                    if(userInputScanner.hasNextLine()) {
                        removeConcept(userInputScanner.nextLine());
                    }
                }
            }
        } else break; //break out of the loop because the user is done
    }

    System.out.println(OUTPUT_SEPERATOR_LINE);
    System.out.println("Congrats! You got all the meanings correct.");
} 
```

我们通过调用`study()`方法开始学习。这个方法声明了几个变量来保存关于用户学习会话的信息。我们将一个`Scanner`初始化为`userInputScanner`来捕获用户的输入，并将其存储在`userInput`中。用户累积的积分将被存储在`userPoints`中，并在用户每次为一个概念提供正确的含义时增加 1。

我们使用一个`while`循环来继续提示用户概念，只要他们没有获得可能的分数，并且他们没有输入“x”或“quit”来退出。

在`while`循环的顶部，我们必须通过调用其`keySet()`实例方法将`studyGuide`中键的`Set`存储在一个常量中，这样我们就可以迭代它。对于每个键，我们通过调用`studyGuide`的`get()`实例方法并向其传递键来获取其值。返回并存储在`currentConceptMeaning`中的值是一个代表概念含义的`Meaning`实例。只要`currentConceptMeaning`不是`null`，我们就让用户猜测他们认为是什么。如果`currentConceptMeaning`的`userInputMatches()`实例方法返回`true`，我们调用它的`markUserGotCorrect()`实例方法来设置它的`gotCorrect`实例字段为真，并递增`userPoints`，因为用户为概念提供了正确的含义。

在每一轮结束时，我们打印出用户已经积累了多少分数，如果他们没有完成学习指南，我们允许他们删除他们放弃学习的概念；我们通过调用我们的`removeConcept()`方法来做到这一点:

```
/**
  * This allows the user to remove a concept they give up on from the study guide. 
  * Removes the pair from the HashMap with a key that matches the concept.
  * @param concept 
  */
 private static void removeConcept(String concept) {
     Meaning conceptMeaning = studyGuide.get(concept);
     if(conceptMeaning != null) {
         //make sure the user hasn't already gotten the meaning correct
         if(!conceptMeaning.userGotCorrect()){
             //remove the concept's key-value pair from the study guide
             studyGuide.remove(concept);
             System.out.println("Removed \"" + concept + "\" from your study guide.");

             /*update the possible points so that it matches 
             the number of concepts left in the study guide*/
             updatePossiblePoints();
         } else {
             //don't let the user remove a concept they already know
             System.out.println("You know \"" + concept + "\", silly. Can't remove.");
         }
     } else {
         System.out.println("\"" + concept + "\" isn't in your study guide.");
     }
 } 
```

当我们调用`removeConcept()`时，它得到与`studyGuide`中的概念对应的`Meaning`值为`conceptMeaning`。如果找到了这个概念，我们调用`conceptMeaning`的`userGotCorrect()`实例方法来检查用户是否正确理解了这个概念的含义，如果返回了`false`，我们调用`studyGuide()`的`remove()`实例方法通过这个概念作为键从`HashMap`中删除键-值对。我们不会让用户删除一个他们已经正确定义的概念，因为他们已经得到了分数。

我们的`userQuit()`方法检查用户是否在任何时候要求退出以结束程序并打印出用户的点数。另一方面，如果用户看完了学习指南，我们调用`break`来跳出`while`循环。在这一点上，应用程序将祝贺用户学习成功，程序将结束。

## 结论

看看，我们都完成了！我们现在有了一个学习指南，用一个`HashMap`来帮助我们学习 Java 概念。我们现在还知道如何使用`HashMap`在 Java 中有效地存储和操作键值对。你已经在成为地图大师的路上了！摆弄源代码，看看如何能使学习指南更好。有问题吗？留下评论，我会尽力回答。

参考资料:
[地图上的甲骨文文档](https://docs.oracle.com/javase/7/docs/api/java/util/Map.html)
[哈希表上的甲骨文文档](https://docs.oracle.com/javase/7/docs/api/java/util/HashMap.html)

## 分享这篇文章