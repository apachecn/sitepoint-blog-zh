# 使用 Swift 创建一个俄罗斯方块益智游戏–最终步骤

> 原文：<https://www.sitepoint.com/create-tetromino-puzzle-game-using-swift-final-steps/>

我希望到目前为止你喜欢这个教程。在这最后一部分，我们将在游戏中加入一些最终的元素来创造一个最终的，完美的游戏。我们开始吧！

### 旋转

与 move 方法类似，我们用`Tetromino` class' `rotate`方法准备了旋转行为:

```
func rotate(rotation: Rotation = .Counterclockwise) {
   switch rotation {
   case .Counterclockwise:
       let bitmapSet = bitmaps[shape]!
       if rotationalState + 1 == bitmapSet.count {
           rotationalState = 0
       } else {
           ++rotationalState
       }
   case .Clockwise:
       let bitmapSet = bitmaps[shape]!
       if (rotationalState == 0) {
           rotationalState = bitmapSet.count - 1
       } else {
           --rotationalState
       }
   }
}
```

现在我们需要从`GameScene`类中调用这个方法。让我们现在就实现它。

1.  在`moveTetrominoTo`方法之后添加一个名为`rotateTetromino`的新方法。

    ```
    func moveTetrominoTo(direction: Direction) {...}

    func rotateTetromino() {
        activeTetromino.rotate()

        if collidedWith(.None) {
            activeTetromino.rotate(rotation: .Clockwise)
        } else {
            refresh()
        }
    }
    ```

    我们立即旋转活动的四分图，并在其静止位置检查碰撞。如果检测到碰撞，我们立即以相反的方向旋转砖块，使其回到先前的状态。如果没有碰撞，我们刷新游戏场景。

2.  像横向移动一样，我们需要更多的触摸事件处理来执行砖块的旋转。最直接的方法是跟踪游戏板上的触摸。我们现在就开始吧。

在`touchesBegan`事件处理程序中添加第三个条件，检查游戏板内部的触摸，然后执行旋转。

```
if location.x < gameBoardFrame.origin.x {
    moveTetrominoTo(.Left)
} else if location.x > gameBoardFrame.origin.x + gameBoardFrame.width {
    moveTetrominoTo(.Right)
} else if CGRectContainsPoint(gameBoardFrame, location) {
    rotateTetromino()
}
```

保存然后运行( **⌘ + R** )我们的游戏。这个游戏现在看起来和感觉起来都像真的一样。如果你试图在游戏当前的状态下玩游戏，并试图*清除线*，你会失望地发现，这一个关键规则仍然不见了。如果你不熟悉这条规则，不要担心。我将在下一节及其实现中简要讨论它。

### 清算行

清除线路是我们游戏的主要目标。要清除一行，必须用块填充一行的所有空间，同时避免产生间隙。创建的*线*从游戏中移除或清除。线清除发生在砖块落地之后，这是你得分的主要方式。现在就来执行这个规则吧。

1.  在`collidedWith`方法之后添加一个名为`clearLines`的新方法。

    ```
    func collidedWith(direction: Direction) -> Bool {...}

    func clearLines() {
        var linesToClear = [Int]()
        for row in 0..<gameBitmapDynamic.count - 1 {
            var isLine = true
            for col in 0..<gameBitmapDynamic[0].count {
                if gameBitmapDynamic[row][col] == 0 {
                    isLine = false
                }
            }

            if isLine {
                linesToClear.append(row)
            }
        }

        if linesToClear.count > 0 {
            for line in linesToClear {
                gameBitmapDynamic.removeAtIndex(line)
                gameBitmapDynamic.insert([8, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 8], atIndex: 1)
            }
        }
    }
    ```

我们遍历游戏位图中包含活动的俄罗斯方块的所有行(除了地板),并检查零值。零值意味着在该特定位置没有块。如果至少有一个零值，那么我们可以有把握地假设这个特定的行没有完整的行。我们跳过这一行，继续下一行，直到找到完整的一行。

我们将找到完整行的特定行号添加到名为`linesToClear`的数组中。然后，我们遍历这个变量中包含的每个值，删除每一行，同时在顶部添加一个空行以保留游戏板的大小。

1.  让我们在处理着陆的部分的`moveTetrominoTo`方法中调用这个新方法:

    ```
    if direction == .Down {
        clearLines()

        gameBitmapStatic.removeAll(keepCapacity: true)
        gameBitmapStatic = gameBitmapDynamic

        ...
    }
    ```

保存并运行( **⌘ + R** )游戏。如果不想等砖块落地太久，可以通过降低`defaultSpeed`变量的值来临时调整下落速度。或者我们可以添加一个即时投放功能。这是我们下一步的目标。

### Instadrop

instadrop 功能使游戏更快更刺激，但也增加了失误的风险。让我们实现这个特性。

1.  创建一个名为`instaDrop`的新方法，你猜对了。您可以在我们的`clearLines`方法后添加它。

    ```
    func clearLines() {...}

    func instaDrop() {
        while collidedWith(.Down) == false {
            activeTetromino.moveTo(.Down)
            updateGameBitmap()

        }
        clearLines()

        gameBitmapStatic.removeAll(keepCapacity: true)
        gameBitmapStatic = gameBitmapDynamic

        activeTetromino = Tetromino()
        centerActiveTetromino()

        refresh()
        lastUpdate = NSDate()
    }
    ```

只要路上没有障碍物，我们就不断地向下移动活跃的方块。我们使用`collidedWith`方法来检查碰撞。我们还更新了游戏位图，以反映活跃的俄罗斯方块的新位置。

在循环之后，我们在砖块落地之后执行与`moveTetrominoTo`中相同的落地程序。为了方便起见，我们可以将它放在一个单独的方法中，以减少代码的重复。我们现在就开始吧。创建一个名为`didLand`的函数，并使用复制的代码更新它的主体。

```
func instaDrop() {...}

func didLand() {
    clearLines()

    gameBitmapStatic.removeAll(keepCapacity: true)
    gameBitmapStatic = gameBitmapDynamic

    activeTetromino = Tetromino()
    centerActiveTetromino()

    refresh()
    lastUpdate = NSDate()
}
```

现在更新 instaDrop 和 moveTetrominoTo 来重用这个常用函数。

```
func moveTetrominoTo(direction: Direction) {
    if collidedWith(direction) == false {
        ...
    } else {
        if direction == .Down {
            didLand()
            return
        }
    }

    refresh()
}

...

func instaDrop() {
    while collidedWith(.Down) == false {...}
    didLand()
}
```

1.  让我们为这个行为实现另一个触摸事件处理程序。触摸游戏板的底部区域将是显而易见的选择，但是玩家应该小心不要无意中触摸它。

在`touchesBegan`事件处理程序中添加第四个条件来检查游戏板下的触摸，然后执行放下。

```
if location.x < gameBoardFrame.origin.x {
    moveTetrominoTo(.Left)
} else if location.x > gameBoardFrame.origin.x + gameBoardFrame.width {
    moveTetrominoTo(.Right)
} else if CGRectContainsPoint(gameBoardFrame, location) {
    rotateTetromino()
} else if location.y < gameBoardFrame.origin.y {
    instaDrop()
}
```

保存并运行( **⌘ + R** )我们的游戏。现在每块砖落地都不用等太久，可以用力。这几乎是我们最终游戏的所有关键特征。你已经可以玩并享受这个版本了。但是仍然缺少一些重要的元素。现在就来补充吧。

### 下一块砖

我们的游戏虽然简单，但仍然需要很多策略和快速思考，尤其是在高水平的游戏中。除了决定砖块应该落在哪里和以什么样的配置落下，你还可以通过观察下一个可用的砖块来仔细计划你的行动。

1.  让我们从添加两个值开始，这两个值将保存排队的 tetromino 的视觉和结构表示。

    ```
    ...

    class GameScene: SKScene {
        let gameBoard = SKSpriteNode()
        let nextTetrominoDisplay = SKSpriteNode()

        var activeTetromino = Tetromino()
        var nextTetromino = Tetromino()

        ...
    }
    ```

2.  在`GameScene.swift`中创建一个名为`showNextTetromino`的新方法。

    ```
    func showNextTetromino() {
        nextTetrominoDisplay.removeAllChildren()

        for row in 0..<nextTetromino.bitmap.count {
            for col in 0..<nextTetromino.bitmap[row].count {
                if nextTetromino.bitmap[row][col] > 0 {
                    let bit = nextTetromino.bitmap[row][col]
                    let square = SKSpriteNode(color: colors[bit], size: CGSize(width: blockSize, height: blockSize))
                    square.anchorPoint = CGPoint(x: 0, y: 1.0)
                    square.position = CGPoint(x: col * Int(blockSize) + col, y: -row * Int(blockSize) + -row)
                    nextTetrominoDisplay.addChild(square)
                }
            }
        }

        let nextTetrominoDisplayFrame = nextTetrominoDisplay.calculateAccumulatedFrame()
        let gameBoardFrame = gameBoard.calculateAccumulatedFrame()
        nextTetrominoDisplay.position = CGPoint(x: gameBoardFrame.origin.x + gameBoardFrame.width - nextTetrominoDisplayFrame.width, y: -30)

        if nextTetrominoDisplay.parent == nil {
            self.addChild(nextTetrominoDisplay)
        }
    }
    ```

    我们使用`removeAllChildren`清除先前显示的所有精灵节点，然后用新的精灵替换它。我们使用我们在其他方法中看到的相同的嵌套`for in`循环技术来绘制 tetromino。我们借助`calculateAccumulatedFrame`方法将`nextTetrominoDisplay`定位到场景的右上区域。最后，我们将它添加为场景的子节点，但要确保我们只做一次。

3.  让我们给`didMoveToView`方法添加几行新代码。

    ```
    override func didMoveToView(view: SKView) {
        /* Setup your scene here */
        ...

        nextTetrominoDisplay.anchorPoint = CGPoint(x: 0, y: 1.0)
        showNextTetromino()
    }
    ```

我们将 sprite 节点的锚点设置在左上角。然后我们调用刚刚创建的新方法。

1.  我们还修改了`didLand`方法来处理这个更新。

    ```
    func didLand() {
        clearLines()

        gameBitmapStatic.removeAll(keepCapacity: true)
        gameBitmapStatic = gameBitmapDynamic

        activeTetromino = nextTetromino
        centerActiveTetromino()

        nextTetromino = Tetromino()
        showNextTetromino()

        refresh()
        lastUpdate = NSDate()
    }
    ```

我们没有创建一个新的`Tetromino`对象来设置为活动对象，而是将下一个 tetromino 指定为当前活动对象。最后，我们创建一个新的 tetromino 来添加到队列中，然后调用`showNextTetromino`来显示它。

保存并运行( **⌘ + R** )游戏。享受制定战略和计划下一步行动的乐趣。仍然缺少一些元素。不去任何地方玩游戏有什么意义。我们需要一种进步感。这就是我们接下来要实现的。

### 得分和拉平

在我们的游戏中，赢得分数的主要动作是清除线。玩家还可以在执行 instadrop 时获得额外的点数，作为执行冒险行动的奖励。分数也可以随着砖块的掉落而自动增加。当玩家超过关卡的目标分数时，他就会升级，同时掉落的速度也会增加。

1.  让我们从添加保存当前分数、级别和目标的值开始。我们还将定义显示这些值的文本元素。

    ```
    class GameScene: SKScene {
        ...

        let scoreLabel = SKLabelNode()
        let levelLabel = SKLabelNode()

        var score = 0
        var level = 1
        var nextLevel = 3000

        override func didMoveToView(view: SKView) {...}
    }
    ```

我们从 0 分和 1 级开始。为了升级，我们需要超过一个特定的分数。例如，要达到 2 级，我们需要超过 3000 分。

1.  接下来，我们创建一个名为`updateScoreWith`的新方法，它将分数作为参数添加到当前分数中。

    ```
    func updateScoreWith(points: Int = 1) {
        if scoreLabel.parent == nil &amp;&amp; levelLabel.parent == nil {
            let gameBoardFrame = gameBoard.calculateAccumulatedFrame()

            scoreLabel.text = &quot;Score: \(score)&quot;
            scoreLabel.fontSize = 20.0
            scoreLabel.fontColor = SKColor.whiteColor()
            scoreLabel.horizontalAlignmentMode = .Left
            scoreLabel.position = CGPoint(x: gameBoardFrame.origin.x, y: -scoreLabel.frame.height - 50)
            self.addChild(scoreLabel)

            levelLabel.text = &quot;Level: \(level)&quot;
            levelLabel.fontSize = 20.0
            levelLabel.fontColor = SKColor.whiteColor()
            levelLabel.horizontalAlignmentMode = .Left
            levelLabel.position = CGPoint(x: scoreLabel.frame.origin.x, y: -levelLabel.frame.height - scoreLabel.frame.height - 50 - 10)
            self.addChild(levelLabel)
        }

        score += points * level * level
        scoreLabel.text = &quot;Score: \(score)&quot;

        if score > nextLevel {
            levelLabel.text = &quot;Level: \(++level)&quot;
            nextLevel = Int(2.5 * Double(nextLevel))

            if dropTime - 150 <= 0 {
                // Maximum speed
                dropTime = 100
            } else {
                dropTime -= 150
            }
        }
    }
    ```

我们准备显示我们当前分数和级别的标签节点。设置字体大小、颜色、对齐和位置。接下来，我们使用当前级别作为乘数来更新分数。级别越高，得分越高。然后我们检查我们是否超过了目标分数。如果有，那么我们将级别增加 1，速度增加 150 毫秒。我们还将下一个级别的目标分数提高了 150%。我们还会更新场景中显示的分数。我们设定了速度上限，但实际上，有人达到上限的可能性非常小。

1.  现在让我们在所有生成点的操作中调用这个方法。首先让我们在`didMoveToView`方法中做一个初始调用来显示起始值。

    ```
    override func didMoveToView(view: SKView) {
        ...

        updateScoreWith(points: 0)
    }
    ```

    接下来，我们在`moveTetrominoTo`方法中调用它来添加下落时的自动点。

    ```
    func moveTetrominoTo(direction: Direction) {
        if collidedWith(direction) == false {
            activeTetromino.moveTo(direction)

            if direction == .Down {
                updateScoreWith()
                lastUpdate = NSDate()
            }
        } else {
            ...
        }
        ...
    }
    ```

接下来，我们在`clearLines`方法中调用它，这是最重要的点生成动作。如果您一次最多清除 4 行，我们会传入一个清除行数的立方，再加上一个奖金乘数。

```
func clearLines() {
    ...

    if linesToClear.count > 0 {
        ...

        var multiplier = linesToClear.count == 4 ? 10 : 1
        updateScoreWith(points: linesToClear.count * linesToClear.count * linesToClear.count)
    }
}
```

最后，我们在进行瞬间下降时，得分是平时的两倍。

```
func instaDrop() {
    while collidedWith(.Down) == false {
        updateScoreWith(points: 2)
        activeTetromino.moveTo(.Down)
        updateGameBitmap()
    }
    didLand()
}
```

就是这样！保存并运行( **⌘ + R** )游戏，并尝试打破我的最高分 406，858 分。在这个阶段，你可以随意定制形状、游戏板和其他元素的大小和颜色。玩转评分规则，水平目标，触摸处理和其他机制。让这个游戏真正成为你自己的。

![High Score](img/d5507c39c4e92bcb95885c8fc0212579.png)

## 结论

游戏本身现在实际上已经完成了，除了一些缺少的铃声和口哨声。我会让你负责添加声音效果，自定义应用程序图标，启动屏幕图形和处理游戏状态作为一个练习。但是请注意，我们不建议你将它作为你自己的游戏出售，因为这样会有遭遇版权纠纷的风险。现在，给自己一个鼓励，因为你坚持到底了。

我们只是触及了 Swift 最重要的一些特性的表面，包括类型推断、常量、枚举和选项。但是你的学习不止于此。继续通读[苹果的 Swift 编程语言书籍](https://developer.apple.com/library/ios/documentation/Swift/Conceptual/Swift_Programming_Language/)以获得对该语言更深入的理解和欣赏。

谢谢你，祝你的应用或游戏开发事业一切顺利！

## 分享这篇文章