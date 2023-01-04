# Python 机器学习入门

> 原文：<https://www.sitepoint.com/machine-learning-python-primer/>

在过去的十年里，机器学习已经从科学研究实验室进入了日常的网络和移动应用程序。机器学习使您的应用程序能够执行以前非常难以编程的任务，例如检测图像中的对象和人脸，检测垃圾邮件和仇恨言论，以及为电子邮件和消息应用程序生成智能回复。

但是执行机器学习与经典编程有着本质的不同。在本文中，您将学习机器学习的基础知识，并创建一个可以根据测量结果预测花卉种类的基本模型。

## 机器学习是如何工作的？

经典编程依赖于定义明确的问题，这些问题可以分解成不同的类、函数和 if–else 命令。另一方面，机器学习依赖于根据经验发展其行为。你不是给机器学习模型提供规则，而是通过例子来训练它们。

机器学习算法有不同的类别，每一种都可以解决特定的问题。

### 监督学习

[**监督学习**](https://bdtechtalks.com/2020/02/10/unsupervised-learning-vs-supervised-learning/) 适用于你想从输入数据到结果的问题。所有监督学习问题的共同特点是，有一个你可以用来测试你的模型的基础事实，比如标签图像或历史销售数据。

监督学习模型可以解决回归或分类问题。回归模型预测数量(如售出的商品数量或股票价格)，而分类问题则试图确定输入数据的类别(如猫/狗/鱼/鸟，欺诈/非欺诈)。

图像分类、人脸检测、股票价格预测和销售预测是监督学习可以解决的问题的例子。

一些流行的监督学习算法包括线性和逻辑回归、支持向量机、决策树和人工神经网络。

### 无监督学习

无监督学习适用于有数据但不是结果，而是寻找模式的问题。例如，您可能希望根据客户的相似性对他们进行分类。这在无监督学习中称为**聚类**。或者，您可能希望检测企业中偏离正常活动的恶意网络流量。这被称为**异常检测**，另一个无监督学习任务。无监督学习对于**降维**也很有用，这是一种通过移除不相关的特征来简化机器学习任务的技巧。

一些流行的无监督学习算法包括 K-均值聚类和主成分分析(PCA)。

### 强化学习

[**强化学习**](https://bdtechtalks.com/2019/05/28/what-is-reinforcement-learning/) 是机器学习的一个分支，其中智能主体试图通过与其环境的交互来实现一个目标。强化学习包括行动、状态和奖励。未经训练的 RL 代理从随机采取行动开始。每个动作都会改变环境的状态。如果代理发现自己处于期望的状态，它会收到奖励。代理人试图找到产生最大回报的动作序列和状态。

强化学习用于推荐系统、机器人和游戏机器人，如谷歌的 AlphaGo 和 AlphaStar。

## 设置 Python 环境

在这篇文章中，我们将关注监督学习，因为它是机器学习中最受欢迎的分支，其结果更容易评估。我们将使用 Python，因为它有许多支持机器学习应用的特性和库。但是一般概念可以应用于任何具有类似库的编程语言。

(如果你是 Python 的新手，freeCodeCamp 有一个很棒的[速成班](https://www.freecodecamp.org/news/python-crash-course/)，可以让你从基础开始。)

经常用于数据科学和机器学习的 Python 库之一是 [Scikit-learn](https://sklearn.org/) ，它提供了流行的机器学习算法的实现。Scikit-learn 不是 Python 基础安装的一部分，您必须手动安装它。

macOS 和 Linux 预装了 Python。要安装 Scikit-learn 库，请在终端窗口中键入以下命令:

```
pip install scikit-learn 
```

或者对于 Python 3:

```
python3 -m pip install scikit-learn 
```

在 Microsoft Windows 上，必须先安装 Python。你可以从[官网](https://www.python.org/downloads/windows/)获取最新版本的 Python 3 for Windows 的安装程序。安装 Python 后，在命令行窗口中键入以下命令:

```
python -m pip install scikit-learn 
```

或者，您可以安装 Anaconda 框架，它包括 Python 3 的独立安装以及 Scikit-learn 和许多其他用于数据科学和机器学习的库，如 *Numpy* 、 *Scipy* 和 *Matplotlib* 。你可以在[它的官网](https://www.anaconda.com/products/individual)找到《Anaconda》免费个人版的安装说明。

## 第一步:定义问题

每个机器学习项目的第一步是知道你想解决什么问题。定义问题将帮助您确定需要收集的数据类型，并让您了解需要使用的机器学习算法类型。

在我们的例子中，我们想要创建一个模型，基于花瓣和[萼片](https://en.wikipedia.org/wiki/Sepal)的长度和宽度的测量来预测花的种类。

这是一个监督分类问题。我们需要收集一份不同花卉标本及其相应物种的测量清单。然后，我们将使用这些数据来训练和测试一个机器学习模型，该模型可以将测量值映射到物种。

## 第二步:收集数据

机器学习最棘手的部分之一是收集数据来训练你的模型。您必须找到一个可以收集训练模型所需数量的数据的来源。您还需要验证数据的质量，确保它代表您的模型将处理的不同情况，并避免收集包含隐藏偏见的数据。

幸运的是，Scikit-learn 包含了几个玩具数据集来尝试不同的机器学习算法。其中之一是“[鸢尾花数据集](https://en.wikipedia.org/wiki/Iris_flower_data_set)”，它恰好包含我们解决问题所需的精确数据。我们需要做的就是从库中加载它。

以下代码加载住房数据集:

```
from sklearn.datasets import load_iris

iris = load_iris() 
```

Iris 数据集包含 150 个观测值，每个观测值包含四个测量值(`iris.data`)和目标花卉种类(`iris.target`)。数据列的名称可以在`iris.feature_names`中看到:

```
print(iris.feature_names)
'''
['sepal length (cm)',
 'sepal width (cm)',
 'petal length (cm)',
 'petal width (cm)']
''' 
```

`iris.target`包含数据集中注册的三种花卉之一的数值指数(0–2)。花卉种类的名称可在`iris.target_names`中找到:

```
print(iris.target_names)
'''['setosa' 'versicolor' 'virginica']''' 
```

## 步骤 3:分割数据集

在开始训练之前，必须将数据分成训练集和测试集。您将使用训练集来训练您的机器学习模型，并使用测试集来验证其准确性。

这是为了确保您的模型没有过度拟合训练数据。当你的机器学习模型在训练样本上表现良好，但在看不见的数据上表现不佳时，就会发生过度拟合。过度拟合可能是由于选择错误的机器学习算法、在模型上进行错误的配置、训练数据差或训练样本太少而导致的。

根据您正在解决的问题的类型和您拥有的数据量，您必须决定将多少数据分配给测试集。通常，当你有大量的数据时(大约成千上万的例子)，即使是 1%的小样本也足以测试你的模型。对于总共包含 150 条记录的 Iris 数据集，我们将选择 75–25 分割。

Scikit-learn 有一个`train_test_split`函数，可以将数据集分成训练和测试数据集:

```
from sklearn.model_selection import train_test_split

X_train, X_test, y_train, y_test = train_test_split(iris.data, iris.target, test_size=0.25, stratify=iris.target, random_state=42) 
```

`train_test_split`获取数据和目标数据集，并返回两对数据集用于训练(`X_train`和`y_train`)和测试(`X_test`和`y_test`)。`test_size`参数决定分配给测试的数据百分比(0 到 1 之间)。`stratify`参数确保训练和测试阵列包含来自每个类的平衡数量的样本。`random_state`变量存在于 Scikit-learn 的许多功能中，用于控制随机数发生器和再现性。

## 步骤 4:构建模型

现在我们的数据已经准备好了，我们可以创建一个机器学习模型，并在训练集上训练它。有许多不同的机器学习算法可以解决像我们正在处理的分类问题。在我们的例子中，我们将使用"[逻辑回归](https://towardsdatascience.com/understanding-logistic-regression-9b02c2aec102)"算法，它非常快，适合于简单且不包含太多维度的分类问题。

Scikit-learn 的`LogisticRegression`类实现了这个算法。在实例化它之后，我们通过调用`fit`函数在我们的火车集合(`X_train`和`y_train`)上训练它。这将调整模型的参数，以找到测量值和花卉种类之间的映射。

```
from sklearn.linear_model import LogisticRegression

lr = LogisticRegression()
lr.fit(X_train, y_train) 
```

## 步骤 5:评估模型

既然我们已经训练了模型，我们想要测量它的准确性。`LogisticRegression`类有一个`score`方法，返回模型的精度。首先，我们将测量模型对训练数据的准确性:

```
print(lr.score(X_train, y_train)) 
```

这将返回大约 0.97，这意味着该模型准确地预测了 97%的训练样本的类别，这是非常好的，因为我们每个物种只有大约 37 个训练样本。

接下来，我们将在测试集上检查模型的准确性:

```
print(lr.score(X_test, y_test)) 
```

这将为我们提供大约 95%的准确率，略低于训练精度，这是很自然的，因为这些是模型从未见过的例子。通过创建更大的数据集或尝试另一种机器学习算法(如支持向量机)，我们可能能够进一步提高模型的准确性，并弥合训练和测试性能之间的差距。

最后，我们想看看如何在新的例子上使用我们训练过的模型。`LogisticRegression`类有一个`predict`函数，它将一组观察值作为输入，并返回预测的类。在我们的花分类器模型的情况下，我们需要为它提供四个测量值的数组(萼片长度、萼片宽度、花瓣长度、花瓣宽度),它将返回一个表示花的类别的整数:

```
output = lr.predict([[4.4, 3.2, 1.3, 0.2]])
print(iris.target_names[output[0]])

'''setosa 
```

恭喜你！你已经创建了你的第一个机器学习模型。我们现在可以将它整合到一个应用程序中，该应用程序从用户处获取测量值并返回花卉种类:

```
sepal_l = float(input("Sepal length (cm):"))
sepal_w = float(input("Sepal width (cm):"))
petal_l = float(input("Petal length (cm):"))
petal_w = float(input("Petal width (cm):"))

measurements = [[sepal_l, sepal_w, petal_l, petal_w]]
output = lr.predict(measurements)
print(f"Your flower is {iris.target_names[output[0]]}") 
```

希望这将是你成为机器学习大师的第一步。从这里，你可以继续学习其他机器学习算法，了解更多机器学习的基本概念，并继续学习更高级的主题，如[神经网络和深度学习](https://bdtechtalks.com/2021/01/28/deep-learning-explainer/)。通过一点学习和实践，您将能够创建出色的应用程序，可以检测图像中的对象，处理语音命令，并与用户进行对话。

## 分享这篇文章