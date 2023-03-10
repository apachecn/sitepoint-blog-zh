# 遗传算法导论

> 原文：<https://www.sitepoint.com/genetic-algorithms-introduction/>

遗传算法是一种程序，它使用模拟进化中自然过程的操作来搜索问题的最佳解决方案，例如“适者生存”、染色体交叉和突变。本文简要介绍了如何编写遗传算法，讨论了在编写自己的算法时需要考虑的一些重要问题，并给出了几个遗传算法的实例。

## 猜测未知

这是 2369 年，人类已经分散在各个星球上。你是一个年轻聪明的医生，驻扎在外太空的星际基地，那里挤满了星际旅行者、商人和偶尔出现的失败者。几乎就在你到达之后，电视台的一个店主对你产生了兴趣。他声称自己只不过是一个普通的裁缝，但有传言说他是为一个特别肮脏的政权工作的秘密特工。

你们两个开始享受每周的午餐，讨论从政治到诗歌的一切。即使过了几个月，你仍然不确定他是在做出浪漫的姿态，还是在探听秘密(你一点也不知道)。也许两者都有一点。

一天午餐时，他向你提出了这个挑战:“我有一个消息要告诉你，亲爱的医生！我当然不能说是什么。但是我会告诉你它有 12 个字符长。这些字符可以是字母表中的任何字母、空格或标点符号。我会告诉你你的猜测有多离谱。你很聪明；你觉得你能搞清楚吗？”

你回到医疗室的办公室，仍然在想他说的话。突然，作为实验的一部分，你留在附近电脑上运行的基因测序模拟给了你一个想法。你不是一个密码破译者，但也许你可以利用你在遗传学方面的专业知识来理解他的信息！

## 一点理论

正如我在开始时提到的，遗传算法是一种使用模拟推动进化的过程的操作来搜索解决方案的过程。经过多次迭代，该算法从一组可能的解决方案中选择最佳候选(猜测)，重新组合它们，并检查哪些组合使其更接近解决方案。不太有利的候选被丢弃。

在上面的场景中，秘密消息中的任何字符都可以是 A–Z、空格或基本的标点符号。假设这给了我们以下 32 个字符的“字母表”来处理:`ABCDEFGHIJKLMNOPQRSTUVWXYZ -.,!?`这意味着有 32 个 <sup>12 个</sup>(大约 1.15×10 <sup>18 个</sup>)可能的消息，但是这些可能性中只有一个是正确的。检查每一种可能性都要花很长时间。相反，遗传算法将随机选择 12 个字符，并要求裁缝/间谍对结果与他的信息的接近程度进行评分。这比蛮力搜索更有效，因为分数让我们微调未来的候选人。反馈让我们能够衡量每个猜测的适合度，并有望避免在死胡同里浪费时间。

假设我们做三个猜测:`HOMLK?WSRZDJ`、`BGK KA!QTPXC`和`XELPOCV.XLF!`。第一名候选人获得 248.2 分，第二名获得 632.5 分，第三名获得 219.5 分。分数如何计算取决于具体情况，我们将在后面讨论，但现在让我们假设它是基于候选人和目标信息之间的偏差:完美的分数是 0(也就是说，没有偏差；候选人和目标是相同的)，更大的分数意味着有更大的偏差。得分为 248.2 和 219.5 的猜测比得分为 635.5 的猜测更接近秘密信息。

未来的猜测是通过结合最好的尝试做出的。有许多方法可以组合候选字，但现在我们将考虑一个简单的交叉方法:新猜测中的每个字符都有 50–50 的机会从第一个或第二个父候选字中复制。如果我们取两个猜测`HOMLK?WSRZDJ`和`XELPOCV.XLF!`，我们后代候选人的第一个角色有 50%的几率是`H`，50%的几率是`X`，第二个角色要么是`O`，要么是`E`，以此类推。后代可能是`HELLO?W.RLD!`。

![Generating new candidates through crossover](img/f9affe290f4c2b8886567880ce3b0f5f.png)

通过交叉产生新的候选

然而，如果我们只使用来自父候选项的值，那么在多次迭代中会出现一个问题:缺乏多样性。如果我们有一个由所有`A`和所有`B`组成的候选者，那么任何由它们单独通过交叉产生的后代将只由`A`和`B`组成。如果解决方案包含一个`C`，我们就倒霉了。

为了降低这种风险并保持多样性，同时仍然缩小解决方案的范围，我们可以引入一些小的变化。我们提供了一个很小的机会，从字母表中选择一个任意的值，而不是直接的 50-50 分割。有了这种突变，后代可能会变成`HELLO WORLD!`。

![Mutation keeps things fresh!](img/e021660b5aa9735a492b887f857904a8.png)

突变让事物保持新鲜感！

不出所料，遗传算法借用了遗传科学的大量词汇。因此，在我们深入探讨之前，让我们先来提炼一些术语:

*   **等位基因**:遗传字母表中的一员。如何定义等位基因取决于算法。例如，`0`和`1`可能是处理二进制数据的遗传算法的等位基因，处理代码的算法可能使用函数指针，等等。在我们的秘密信息场景中，等位基因是字母表中的字母、空格和各种标点符号。

*   **染色体**:给定的等位基因序列；候选解决方案；一个“猜想”。在我们的场景中，`HOMLK?WSRZDJ`、`XELPOCV.XLF!`和`HELLO WORLD!`都是染色体。

*   **基因**:染色体上特定位置的等位基因。对于染色体`HOMLK?WSRZDJ`，第一个基因是`H`，第二个基因是`O`，第三个是`M`，以此类推。

*   **群体**:作为问题解决方案提出的一个或多个候选染色体的集合。

*   **世代**:算法特定迭代期间的群体。一代中的候选人提供基因来产生下一代的种群。

*   适合度(Fitness):评估候选人与期望解决方案接近程度的一种方法。更适合的染色体更有可能将它们的基因传递给未来的候选者，而不太适合的染色体更有可能被丢弃。

*   **选择**:选择一些候选染色体进行繁殖(用于创建新的候选染色体)并丢弃其他候选染色体的过程。存在多种选择策略，它们对选择较弱候选人的容忍度各不相同。

*   **繁殖**:将一个或多个候选基因组合起来产生新候选基因的过程。供体染色体称为**亲本**，产生的染色体称为**后代**。

*   **突变**:在后代中随机引入异常基因，以防止多代遗传多样性的丧失。

## 给我看看代码！

我猜想，鉴于高层次的概述和术语列表，您现在可能渴望看到一些代码。因此，让我们来看一些解决我们的秘密消息问题的 JavaScript。当您通读时，我邀请您思考哪些方法可能被视为“样板代码”，哪些方法的实现与我们试图解决的问题更紧密地联系在一起:

```
class Candidate {
    constructor(chromosome, fitness) {
        this.chromosome = chromosome;
        this.fitness = fitness;
    }

    /**
     * Convenience method to sort an array of Candidate
     * objects.
     */
    static sort(candidates, asc) {
        candidates.sort((a, b) => (asc)
            ? (a.fitness - b.fitness)
            : (b.fitness - a.fitness)
        );
    }
}

class GeneticAlgorithm {
    constructor(params) {
        this.alphabet = params.alphabet;
        this.target = params.target;
        this.chromosomeLength = params.target.length;
        this.populationSize = params.populationSize;
        this.selectionSize = params.selectionSize;
        this.mutationRate = params.mutationRate;
        this.mutateGeneCount = params.mutateGeneCount;
        this.maxGenerations = params.maxGenerations;
    }

    /**
     * Convenience method to return a random integer [0-max).
     */
    randomInt(max) {
        return Math.floor(Math.random() * max);
    }

    /**
     * Create a new chromosome from random alleles.
     */
    createChromosome() {
        const chrom = [];
        for (let i = 0; i < this.chromosomeLength; i++) {
            chrom.push(this.alphabet[
                this.randomInt(this.alphabet.length)
            ]);
        }
        return chrom;
    }

    /**
     * Create the initial population with random chromosomes
     * and assign each a fitness score for later evaluation.
     */
    init() {
        this.generation = 0;
        this.population = [];

        for (let i = 0; i < this.populationSize; i++) {
            const chrom = this.createChromosome();
            const score = this.calcFitness(chrom);
            this.population.push(new Candidate(chrom, score));
        }
    }

    /**
     * Measure a chromosome’s fitness based on how close its
     * genes match those of the target; uses mean squared
     * error.
     */
    calcFitness(chrom) {
        let error = 0;
        for (let i = 0; i < chrom.length; i++) {
            error += Math.pow(
                this.target[i].charCodeAt() - chrom[i].charCodeAt(),
                2
            );
        }
        return error / chrom.length;
    }

    /**
     * Reduce the population to only the fittest candidates;
     * elitist selection strategy.
     */
    select() {
        // lower MSE is better
        Candidate.sort(this.population, true);
        this.population.splice(this.selectionSize);
    }

    /**
     * Apply crossover and mutation to create new offspring
     * chromosomes and increase the population.
     */
    reproduce() {
        const offspring = [];
        const numOffspring = this.populationSize /
            this.population.length * 2;

        for (let i = 0; i < this.population.length; i += 2) {
            for (let j = 0; j < numOffspring; j++) {
                let chrom = this.crossover(
                    this.population[i].chromosome,
                    this.population[i + 1].chromosome,
                );
                chrom = this.mutate(chrom);

                const score = this.calcFitness(chrom);
                offspring.push(new Candidate(chrom, score));
            }
        }

        this.population = offspring;
    }

    /**
     * Create a new chromosome through uniform crossover.
     */
    crossover(chromA, chromB) {
        const chromosome = [];
        for (let i = 0; i < this.chromosomeLength; i++) {
            chromosome.push(
                this.randomInt(2) ? chromA[i] : chromB[i]
            );
        }
        return chromosome;
    }

    /**
     * (Possibly) introduce mutations to a chromosome.
     */
    mutate(chrom) {
        if (this.mutationRate < this.randomInt(1000) / 1000) {
            return chrom;
        }

        for (let i = 0; i < this.mutateGeneCount; i++) {
            chrom[this.randomInt(this.chromosomeLength)] =
                this.alphabet[
                    this.randomInt(this.alphabet.length)
                ];
        }
        return chrom;
    }

    /**
     * Return whether execution should continue processing
     * the next generation or should stop.
     */
    stop() {
        if (this.generation > this.maxGenerations) {
            return true;
        }

        for (let i = 0; i < this.population.length; i++) {
            if (this.population[i].fitness == 0) {
                return true;
            }
        }
        return false;
    }

    /**
     * Repeatedly perform genetic operations on the
     * population of candidate chromosomes in an attempt to
     * converge on the fittest solution.
     */
    evolve() {
        this.init();
        do {
            this.generation++;
            this.select();
            this.reproduce();
        } while (!this.stop());

        return {
            generation: this.generation,
            population: this.population
        };
    }
}

const result = new GeneticAlgorithm({
    alphabet: Array.from('ABCDEFGHIJKLMNOPQRSTUVWXYZ !'),
    target: Array.from('HELLO WORLD!'),
    populationSize: 100,
    selectionSize: 40,
    mutationRate: 0.03,
    mutateGeneCount: 2,
    maxGenerations: 1000000
}).evolve();

console.log('Generation', result.generation);
Candidate.sort(result.population, true);
console.log('Fittest candidate', result.population[0]); 
```

我们从定义一个`Candidate`数据对象开始，简单地将染色体与它们的适应值配对。为了方便起见，还附加了一个静态排序方法；当我们需要找到或输出最合适的染色体时，它就派上了用场。

接下来我们有一个实现遗传算法本身的`GeneticAlgorithm`类。

构造函数接受模拟所需的各种参数的对象。它提供了一种指定遗传字母表、目标消息和其他参数的方法，这些参数用于定义模拟运行的约束条件。在上面的例子中，我们期望每一代有 100 个候选人。从这些染色体中，只有 40 条染色体将被选择用于繁殖。我们提供 3%的机会引入突变，当它发生时，我们将突变两个基因。`maxGenerations`值作为保护措施；如果在一百万代之后我们没有收敛到一个解决方案，无论如何我们都会终止这个脚本。

值得一提的一点是，运行算法时提供的种群、选择规模和最大代数都很小。更复杂的问题可能需要更大的搜索空间，这反过来增加了算法的内存使用和运行时间。然而，强烈鼓励小的突变参数。如果它们变得太大，我们就失去了基于适应度复制候选的任何好处，并且模拟开始变成随机搜索。

像`randomInt()`、`init()`和`run()`这样的方法可能被认为是样板文件。但是仅仅因为有样板文件并不意味着它对模拟没有真正的意义。例如，遗传算法大量使用随机性。虽然内置的`Math.random()`函数对于我们的目的来说很好，但是对于其他问题，您需要一个更精确的随机生成器。 [Crypto.getRandomValues()](https://developer.mozilla.org/en-US/docs/Web/API/Crypto/getRandomValues) 提供更强的加密随机值。

性能也是一个考虑因素。我在这篇文章中努力保持易读性，但是请记住，操作会一遍又一遍地重复。您可能会发现自己需要对循环内的代码进行微优化，使用更节省内存的数据结构，以及内联代码而不是将其分成函数/方法，所有这些都与您的实现语言无关。

像`calcFitness()`、`select()`、`reproduce()`甚至`stop()`这些方法的实现都是针对我们要解决的问题的。

`calcFitness()`返回一个值，该值衡量染色体对某些期望标准的适应性——在我们的例子中，它与秘密消息的匹配程度。计算适合度几乎总是依赖于情境；我们的实现使用每个基因的 ASCII 值来计算均方误差，但其他指标可能更适合。例如，我可以计算出两个值之间的[海明](https://en.wikipedia.org/wiki/Hamming_distance)或[莱文斯坦](https://en.wikipedia.org/wiki/Levenshtein_distance)距离，甚至可以合并多个测量值。最终，对于适应度函数来说，重要的是返回一个关于手头问题的有用度量，而不是简单的布尔“is-fit”/“is-fit”。

`select()`方法展示了一种精英选择策略——在整个种群中只选择最适合的候选者进行繁殖。正如我前面提到的，还有其他策略，比如锦标赛选择，它从群体中的个体候选人中选择最合适的候选人，以及波尔兹曼选择，它施加越来越大的压力来选择候选人。这些不同方法的目的是确保染色体有机会传递后来证明有益的基因，即使这可能不会立即显现出来。对这些和其他选择策略的深入描述，以及示例实现，可以很容易地在网上找到。

![Various selection strategies illustrated](img/8627860ef673a2ceff2139a9b21cb497.png)

说明了各种选择策略

组合基因的方法也有很多。我们的代码使用统一的交叉创造后代，其中每个基因都有平等的机会从父母中选择。其他策略可能偏爱父母的基因。另一种流行的策略是 k 点交叉，在这种策略中，染色体在`k`点分裂，产生 <nobr>`k` + 1</nobr> 片段，这些片段组合起来产生后代。交叉点可以是固定的或随机选择的。

![k-point crossover strategies illustrated](img/2b244d1d892985b2080adafa4cdd8886.png)

图示的 k 点交叉策略

我们也不局限于两条亲代染色体。我们将三个或更多候选人的基因结合起来，甚至构建一个候选人。考虑一个通过绘制随机多边形来演化图像的算法。在这种情况下，我们的染色体被实现为图像数据。在每一代中，从群体中选择最适合的图像作为父代，并且通过将它们自己的多边形绘制到父代的副本来生成所有子代候选。父染色体/图像作为基础，子染色体/图像是父染色体上的独特突变/图画。

## 遗传算法在起作用

遗传算法既可以用于娱乐，也可以用于盈利。也许遗传算法最流行的两个例子是 BoxCar 2D 和美国宇航局的 X 波段天线。

BoxCar 2D 是一种模拟，它使用遗传算法来进化出能够穿越模拟地形的最佳“汽车”。这辆车是由八个随机向量组成的，它们创建了一个多边形，并将轮子连接到随机的点上。该项目的网站可以在[boxcar2d.com](http://boxcar2d.com/)找到，它在其“关于”页面上提供了该算法的简要介绍，以及展示一些最佳设计的排行榜。不幸的是，该网站使用 Flash，这可能使许多人现在无法访问它——在这种情况下，如果你好奇，你可以在 YouTube 上找到各种屏幕记录。你可能也想看看 Rafael Matsunaga 使用 HTML5 技术在 rednuht.org/genetic_cars_2[写的一个类似的(优秀的)模拟。](https://rednuht.org/genetic_cars_2/)

![A car evolved in BoxCar 2D, image from the BoxCar 2D leaderboard](img/d413111a031ab33eb7fed113aedbb7be.png)

一辆由 BoxCar 2D 演变而来的汽车，图片来自 BoxCar 2D 排行榜

2006 年，美国宇航局的太空技术 5 号任务测试了太空中的各种新技术。其中一项技术是使用遗传算法设计的新天线。设计新天线可能是一个非常昂贵和耗时的过程。它需要特殊的专业知识，当需求改变或者原型没有按预期执行时，经常会发生挫折。进化后的天线制造时间更短，增益更高，功耗更低。讨论设计过程的论文全文可在网上免费获得([采用进化算法的自动天线设计](https://ti.arc.nasa.gov/m/pub-archive/1244h/1244%20%28Hornby%29.pdf))。遗传算法也被用来优化现有的天线设计，以获得更好的性能。

![Best evolved antennas for their class of requirements, image taken from the Automated Antenna Design paper](img/ea69b551a75b0050002fdb4543db9943.png)

针对其需求级别的最佳改进天线，图片摘自自动化天线设计论文

遗传算法甚至被用于网页设计！Elijah Mensch 的一个高级项目([通过应用交互式遗传算法](https://digitalcommons.bard.edu/cgi/viewcontent.cgi?article=1339&context=senproj_s2016)来优化网站设计)使用它们来优化新闻文章轮播，方法是操纵 CSS 规则并通过 A/B 测试来评分适应性。

![Best layouts from generations 1 and 9, images taken from Optimizing Website Design paper](img/ddb3ca8f612315138a2be6581e7f9a1e.png)

第 1 代和第 9 代最佳布局，图片取自优化网站设计论文

## 结论

到目前为止，你应该对什么是遗传算法有一个基本的了解，并对它们的词汇足够熟悉，能够破译你在自己的研究中可能遇到的任何资源。但是理解理论和术语只是工作的一半。如果你计划编写自己的遗传算法，你也必须理解你的特殊问题。在你开始之前，这里有一些重要的问题要问你自己:

*   我如何将我的问题表示为染色体？我的有效等位基因是什么？

*   我知道目标是什么吗？也就是说，我在寻找什么？它是一个特定的值还是任何一个适应度超过某个阈值的解？

*   如何量化候选人的适合度？

*   如何组合和变异候选项以产生新的候选解？

我希望我也帮助你发现了程序如何从自然中获取灵感的价值——不仅仅是形式上的，还有过程和功能上的。欢迎在论坛中分享你自己的想法[。](https://www.sitepoint.com/community/)

## 分享这篇文章