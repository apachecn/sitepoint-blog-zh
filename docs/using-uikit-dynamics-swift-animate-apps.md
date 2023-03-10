# 在 Swift 中使用 UIKit Dynamics 制作应用动画

> 原文：<https://www.sitepoint.com/using-uikit-dynamics-swift-animate-apps/>

在 iOS 7 中，苹果引入了一些类来帮助开发人员构建用户界面，这些界面将“感觉真实”,并在用户交互中表现得“逼真”。用来实现这个的工具主要是 UIKit 动态和运动效果。

以前，要实现这样的交互，开发人员必须精通数学、物理和核心动画库。有了新的库，建立和实现这样的现实行为和动画变得更加容易。

Motion Effects 允许您根据手机的方向以及用户握持和移动手机的方式来调整应用程序 UI 的外观。这方面的一个例子可以在主屏幕上看到，它有视差效果，对手机的移动做出反应。这给原本平坦的界面带来了深度感。

UIKit Dynamics 允许您将现实生活中的行为添加到您的视图中，这样当它们被激活时，效果就是您对现实世界对象的预期。这些行为包括重力、碰撞、依附等。

为用户界面元素提供不同行为的类有:

*   `UIGravityBehavior`–为您的视图提供引力行为。
*   `UICollisionBehavior`–提供碰撞检测。
*   `UISnapBehavior`–使视图移动并捕捉到界面上的特定点，就像连接到该点一样。
*   `UIAttachmentBehavior`–指定两个动态项目之间或一个动态项目和一个锚点之间的动态连接。
*   `UIPushBehavior`–对一个或多个动态项目施加连续或瞬时力，使这些项目相应地改变位置。

要向视图添加上述任何行为，我们初始化行为，并向其传递我们想要展示行为的视图。然后，如果我们希望更改默认设置，我们可以在上面设置配置(我们将在后面讨论)。然后我们将它添加到`UIDynamicAnimator`中，它为您定义的各种行为和动画提供了一个上下文。`UIDynamicAnimator`用一个参考视图初始化，它用这个参考视图定义它的坐标系，并计算各种行为的输出。

我们将看看如何实施上述 5 种行为。我已经建立了一个开始项目，你可以[下载](https://github.com/sitepoint-examples/UIKit-Starter)和最后的例子[在这里](https://github.com/sitepoint-examples/UIKit-Final)。它提供了一个简单的主视图类型的应用程序，其中主视图显示我们将查看的行为列表，详细视图将显示行为的演示。这是为了让我们可以专注于 UIKit 动态，而不必解释如何设置项目。在这个例子中，我们将使用 Swift，因此您将需要 Xcode 6 来跟进。

## 重力和碰撞

我们将从给视图添加重力开始。这是一个常见的概念，我们已经开始期待在我们的世界中的对象。当某物没有力量支撑它时，它就会落到地上。我们可以使用`UIGravityBehavior`类来模拟这种行为。

在这个项目的`GravityAndCollisionViewController`类中，我们首先添加了一个重力行为将作用的视图。

将下面的实例变量添加到类中。

```
var squareView: UIView!
```

在`viewDidLoad()`中，在方法末尾添加以下内容。

```
squareView = UIView(frame: CGRect(x: 100, y: 100, width: 100, height: 100))
squareView.backgroundColor = UIColor.blueColor()
view.addSubview(squareView)
```

运行该应用程序，您应该会在屏幕顶部看到一个蓝色方块。

要将重力行为添加到视图中，请添加以下实例变量。

```
var gravity: UIGravityBehavior!
var animator: UIDynamicAnimator!
```

然后在`viewDidLoad()`的末尾添加以下内容

```
animator = UIDynamicAnimator(referenceView: view)
gravity = UIGravityBehavior(items: [squareView])
animator.addBehavior(gravity)
```

上面用当前视图初始化了动画，创建了一个重力行为，并添加了一个你想要展示该行为的视图。在运行应用程序时，方形视图会从屏幕上消失。默认情况下，重力向下作用，但是您可以通过修改`UIGravityBehavior`的`angle`属性来改变这一点。你也可以通过设置不同的`magnitude`来改变物体下落的速度。就像真实重力一样，视图被拉低，直到碰到边界。由于我们还没有定义一个，视图不断下降，甚至消失在视图之外。

![Starter project overview](img/61377115e667f9931bb146212bb0c03f.png)

我们通过设置边界将正方形视图保持在屏幕的边界内。要设置边界，可以将参考视图的边界设置为碰撞检测的边界，也可以将自定义线定义为碰撞的边界。

要添加边界，我们必须创建一个`UICollisionBehavior`类的实例。将下面的实例变量添加到类中。

```
var collision: UICollisionBehavior!
```

然后在`viewDidLoad()`方法的末尾添加以下内容。

```
collision = UICollisionBehavior(items: [squareView])
collision.translatesReferenceBoundsIntoBoundary = true
animator.addBehavior(collision)
```

上面的代码用我们想要添加碰撞行为的对象数组初始化了一个`UICollisionBehavior`对象。这个类以及其他行为类接受一个符合`UIDynamicItem`协议的对象数组。所有`UIView`的实例都符合这个协议，所以我们的 square 非常适合。

然后，我们将碰撞行为实例的`translatesReferenceBoundsIntoBoundary`属性设置为`true`，这样我们的参考视图的边界(在初始化`UIDynamicAnimator`时设置)就被设置为碰撞检测行为的边界。

运行该应用程序时，方块会下落，并在稳定下来之前反弹几下，击中屏幕底部。

![Gravity with bounce example](img/656771f9b4f4643f74f86cb7bd6cd3ff.png)

如果您想定义自己的边界，可以使用`addBoundaryWithIdentifier`方法来设置边界。例如具有如下定义的边界，将设置一个将方块停止在定义的位置的屏障。

```
collision.addBoundaryWithIdentifier("barrier", fromPoint: CGPointMake(self.view.frame.origin.x, 350), toPoint: CGPointMake(self.view.frame.origin.x + self.view.frame.width, 350))
```

![Stopping Gravity](img/1d63bbf4d706fc008b6f9d9c94c77099.png)

下面显示了`GravityAndCollisionViewController`类的完整代码。

```
//
// GravityAndCollisionViewController.swift
// Dynamics Demo
//
// Created by Joyce Echessa on 8/26/14.
// Copyright (c) 2014 Joyce Echessa. All rights reserved.
//

import UIKit

class GravityAndCollisionViewController: UIViewController {

var squareView: UIView!
var gravity: UIGravityBehavior!
var animator: UIDynamicAnimator!
var collision: UICollisionBehavior!

override func viewDidLoad() {
super.viewDidLoad()

// Do any additional setup after loading the view.

squareView = UIView(frame: CGRect(x: 100, y: 100, width: 100, height: 100))
squareView.backgroundColor = UIColor.blueColor()
view.addSubview(squareView)

animator = UIDynamicAnimator(referenceView: view)
gravity = UIGravityBehavior(items: [squareView])
animator.addBehavior(gravity)

collision = UICollisionBehavior(items: [squareView])
collision.translatesReferenceBoundsIntoBoundary = true
// collision.addBoundaryWithIdentifier("barrier", fromPoint: CGPointMake(self.view.frame.origin.x, 350), toPoint: CGPointMake(self.view.frame.origin.x + self.view.frame.width, 350))
animator.addBehavior(collision)
}

override func didReceiveMemoryWarning() {
super.didReceiveMemoryWarning()
// Dispose of any resources that can be recreated.
}

}
```

## 突然的

接下来我们将看看`UISnapBehavior`类。这将视图捕捉到屏幕上的特定点，具有真实世界捕捉效果的弹性。我们将在视图控制器上使用点击手势识别器，使我们能够检测用户何时触摸屏幕和接触点，以便我们可以将方形视图捕捉到该点。

打开`Main.storyboard`文件，找到`Snap`视图控制器。将点击手势识别器从对象库拖到视图控制器。接下来，通过按住 Control 键从手势识别器拖动到`SnapViewController.swift`文件，为手势识别器创建一个动作。将动作命名为`handleTap`，并选择动作类型为`UITapGestureRecognizer`而不是`AnyObject`。

如图所示修改`SnapViewController.swift`文件。

```
//
// SnapViewController.swift
// Dynamics Demo
//
// Created by Joyce Echessa on 8/26/14.
// Copyright (c) 2014 Joyce Echessa. All rights reserved.
//

import UIKit

class SnapViewController: UIViewController {

var squareView: UIView!
var snap: UISnapBehavior!
var animator: UIDynamicAnimator!

@IBAction func handleTap(sender: UITapGestureRecognizer) {

let tapPoint: CGPoint = sender.locationInView(view)

if (snap != nil) {
animator.removeBehavior(snap)
}

snap = UISnapBehavior(item: squareView, snapToPoint: tapPoint)
animator.addBehavior(snap)

}

override func viewDidLoad() {
super.viewDidLoad()

// Do any additional setup after loading the view.

squareView = UIView(frame: CGRect(x: 100, y: 100, width: 100, height: 100))
squareView.backgroundColor = UIColor.blueColor()
view.addSubview(squareView)

animator = UIDynamicAnimator(referenceView: view)

}

override func didReceiveMemoryWarning() {
super.didReceiveMemoryWarning()
// Dispose of any resources that can be recreated.
}

}
```

这里我们首先声明`UIView`、`UISnapBehavior`和`UIDynamicAnimator`变量。在`viewDidLoad()`中，我们实例化了方形视图，并将其添加到我们的主视图中。我们还实例化了动画师。在`handleTap()`动作方法中，我们获取被点击的位置，并实例化捕捉行为以捕捉到该位置。您可以通过修改吸附行为的`damping`属性来改变振荡的级别。

![Snap Example](img/1c875474bce635892b076ac352f21faf.png)

您应该注意到，每次用户触摸屏幕时，我们都会实例化`UISnapBehavior`实例。在给动画制作人添加一个新的动作之前，我们还会移除任何先前的捕捉行为。一个动画应该只有一个捕捉行为附加到一个特定的项目。当同一项目的多个捕捉行为被添加到同一个 animator 时，捕捉行为将不会被执行，因为它不知道先执行哪个。你可以通过移除动画师的`if`块来测试这一点。在运行程序时，第一次点击将按预期工作，第二次点击将很少移动方块(如果有的话),当您继续点击屏幕，并实质上向动画师添加捕捉行为时，方块将根本不会移动。

## 附上

`UIAttachmentBehavior`类可以用来创建两个项目之间或者一个项目和一个点之间的连接。我们将通过将一个视图附加到一个锚点来看看如何使用它。然后，我们将添加一个平移手势来识别屏幕上的拖动，并将附加视图的锚点更新到用户拖动到的位置。

打开`Main.storyboard`文件，找到`Attach`视图控制器。将平移手势识别器从对象库拖到视图控制器。接下来，通过按住 Control 键从手势识别器拖动到`AttachViewController.swift`文件，为手势识别器创建一个动作。将动作命名为`handlePan`，并选择动作类型为`UIPanGestureRecognizer`而不是`AnyObject`。

然后如图所示修改`SnapViewController.swift`文件。

```
//
// AttachViewController.swift
// Dynamics Demo
//
// Created by Joyce Echessa on 8/26/14.
// Copyright (c) 2014 Joyce Echessa. All rights reserved.
//

import UIKit

class AttachViewController: UIViewController {

var squareView: UIView!
var anchorView: UIView!
var attachment: UIAttachmentBehavior!
var animator: UIDynamicAnimator!
var gravity: UIGravityBehavior!

@IBAction func handlePan(sender: UIPanGestureRecognizer) {

attachment.anchorPoint = sender.locationInView(view)
anchorView.center = sender.locationInView(view)

}

override func viewDidLoad() {
super.viewDidLoad()

// Do any additional setup after loading the view.

squareView = UIView(frame: CGRect(x: 100, y: 100, width: 100, height: 100))
squareView.backgroundColor = UIColor.blueColor()
view.addSubview(squareView)

anchorView = UIView(frame: CGRect(x: view.center.x, y: view.center.y, width: 20, height: 20))
anchorView.backgroundColor = UIColor.redColor()
view.addSubview(anchorView)

attachment = UIAttachmentBehavior(item: squareView, attachedToAnchor: CGPointMake(anchorView.center.x, anchorView.center.y))

animator = UIDynamicAnimator(referenceView: view)
animator.addBehavior(attachment)

gravity = UIGravityBehavior(items: [squareView])
animator.addBehavior(gravity)

}

override func didReceiveMemoryWarning() {
super.didReceiveMemoryWarning()
// Dispose of any resources that can be recreated.
}

}
```

在上面的代码中，我们创建了两个视图。一个大的蓝色正方形视图和一个较小的红色视图。红色方块只是作为视觉辅助，以便我们可以看到连接点。我们不会把蓝色的方块连接到红色的方块上，而是把它连接到一个点上。

创建两个视图后，我们用`squareView`项实例化`UIAttachmentBehavior`实例，并将其锚点设置为红色视图的中心位置。然后我们将行为添加到动画中。

在`handlePan()`函数中，我们将附件行为的锚点设置为用户触摸的点，然后我们将红色方块的位置更新到那个位置，这样我们就可以看到锚点的新位置。

当你运行应用程序时，蓝色方块会因为重力而向下坠落，但由于连接，它不会完全脱落，而是开始围绕锚点振荡。在屏幕上拖动时，红色方块会随着拖动而移动，蓝色方块的锚点也会移动。

![Attach Example](img/9d4eaa1b580edc45dd8824fb53920ee0.png)

## 推

`UIPushBehavior`有两种创建推送行为的模式——持续推送行为和瞬时推送行为。

连续推送将在推送期间对视图应用一个量级。这意味着视图将会加速，因为更多的能量被不断地加入其中。使用瞬时推动时，推动只发生一次，因此视图在继续移动时不会加速。

我们将通过创建两个视图并对每个视图应用不同的行为来比较两者的行为。

如图所示修改`PushViewController.swift`文件。

```
//
// PushViewController.swift
// Dynamics Demo
//
// Created by Joyce Echessa on 8/26/14.
// Copyright (c) 2014 Joyce Echessa. All rights reserved.
//

import UIKit

class PushViewController: UIViewController {

var redSquareView: UIView!
var blueSquareView: UIView!
var animator: UIDynamicAnimator!

override func viewDidLoad() {
super.viewDidLoad()

// Do any additional setup after loading the view.

redSquareView = UIView(frame: CGRect(x: 40, y: 100, width: 50, height: 50))
redSquareView.backgroundColor = UIColor.redColor()
view.addSubview(redSquareView)

blueSquareView = UIView(frame: CGRect(x: 220, y: 100, width: 50, height: 50))
blueSquareView.backgroundColor = UIColor.blueColor()
view.addSubview(blueSquareView)

let continuousPush: UIPushBehavior = UIPushBehavior(items: [redSquareView], mode: UIPushBehaviorMode.Continuous)
let instantaneousPush: UIPushBehavior = UIPushBehavior(items: [blueSquareView], mode: UIPushBehaviorMode.Instantaneous)

continuousPush.setAngle( CGFloat(M_PI_2) , magnitude: 0.3);
instantaneousPush.setAngle( CGFloat(M_PI_2) , magnitude: 0.3);

animator = UIDynamicAnimator(referenceView: view)
animator.addBehavior(continuousPush)
animator.addBehavior(instantaneousPush)

}

override func didReceiveMemoryWarning() {
super.didReceiveMemoryWarning()
// Dispose of any resources that can be recreated.
}

}
```

要使推动工作，您需要设置推动的幅度和角度属性。幅度是推动行为的力向量。它用于计算视图移动的量和速度。angle 属性是以弧度表示的浮点值。它将决定推动的方向。在我们的例子中，推的角度是 90 度(M_PI_2 是 pi/2)。这将视图向下推，如果您希望视图向上推，那么您可以使用负值。

当你运行应用程序时，你会注意到应用了瞬时推力的蓝色方块以比红色视图更大的速度开始，但是红色视图获得了更大的加速度，最终比蓝色视图移动得更快。

![Push Example](img/5dcf32d816ddf0ca66110e1796bc4eb1.png)

## 配置视图的动态行为

UIKit 的动态行为中内置的默认物理特性可能对您试图在视图上实现的任何效果都没问题，但是您可能还想对此进行调整，并更改项目对这些行为的反应。使用`UIDynamicItemBehaviour`类，你可以给你的动态项目分配各种特性，这些特性会影响它们对动态效果的反应。

通过实例化`UIDynamicItemBehaviour`类并设置想要更改的实例的属性，可以做到这一点。然后你把它添加到动画中，就像你添加其他行为一样。

您可以在项目的行为类上设置的属性有:

*   摩擦力–一个介于 0 和 1 之间的浮点值，指定当项目在其他项目上滑动或其他项目在其边缘滑动时，项目移动的阻力有多大。
*   弹性–介于 0 和 1 之间的浮点值，指定项目的碰撞弹性。
*   密度——动画师用它来计算物体的质量，这会影响物体对施加在物体上的力的反应。它是一个介于 0 和 1 之间的浮点值，默认值为 1。
*   allows rotation–一个布尔值，它决定动画制作者是否可以旋转项目。
*   阻力——这是物品对运动的阻力。可以从 0 到`CGFLOAT_MAX`。设置的值越大，物品对施加于其上的力的抵抗力越强。
*   抗角性——这决定了物品对旋转运动的抵抗力。

作为一个例子，我们将使用来自`GravityAndCollisionViewController`类的第一个例子，并添加一个项目行为，这将使方形视图更有弹性。

将以下变量添加到`GravityAndCollisionViewController`类中。

```
var itemBehaviour: UIDynamicItemBehavior!
```

然后在`viewDidLoad()`的末尾添加以下内容

```
itemBehaviour = UIDynamicItemBehavior(items: [squareView])
itemBehaviour.elasticity = 0.7
animator.addBehavior(itemBehaviour)
```

在运行应用程序时，由于其更高的弹性，方形视图将比之前看到的反弹更多次。

![Gravity with Bounce](img/f6837d2cb3e495ee7953dc9886d20c36.png)

## 示例应用程序

现在，我们将使用我们看到的各种行为来创建一个视图，该视图在启动时会迅速进入视图，用户可以通过点击按钮或抓取视图并将其移出屏幕来关闭它。只有当用户将视图移得太靠南时，视图才会移出屏幕，否则，视图会迅速回到原来的位置。在这两种情况下，视图都会脱离屏幕。

首先，向`Main.storyboard`中的示例应用程序场景添加一个按钮。然后通过按住 Control 键从按钮拖动到`ExampleApplicationViewController`类来添加一个动作。命名行动`showAlertView`。完成后，在你的`ExampleApplicationViewController.swift`文件中应该有下面的函数。

```
@IBAction func showAlertView(sender: UIButton) {
}
```

接下来，修改`ExampleApplicationViewController.swift`文件，如下所示。我不会解释所有的事情，因为我们已经讨论了我在前面的例子中使用的行为。对于没有涉及到的内容，我已经添加了注释来解释代码。

```
//
// ExampleApplicationViewController.swift
// Dynamics Demo
//
// Created by Joyce Echessa on 8/26/14.
// Copyright (c) 2014 Joyce Echessa. All rights reserved.
//

import UIKit

class ExampleApplicationViewController: UIViewController {

var overlayView: UIView!
var alertView: UIView!
var animator: UIDynamicAnimator!
var attachmentBehavior : UIAttachmentBehavior!
var snapBehavior : UISnapBehavior!

override func viewDidLoad() {
super.viewDidLoad()

// Do any additional setup after loading the view.

// Initialize the animator
animator = UIDynamicAnimator(referenceView: view)

// Create the dark background view and the alert view
createOverlay()
createAlert()
}

func createOverlay() {
// Create a gray view and set its alpha to 0 so it isn't visible
overlayView = UIView(frame: view.bounds)
overlayView.backgroundColor = UIColor.grayColor()
overlayView.alpha = 0.0
view.addSubview(overlayView)
}

func createAlert() {
// Here the red alert view is created. It is created with rounded corners and given a shadow around it
let alertWidth: CGFloat = 250
let alertHeight: CGFloat = 150
let alertViewFrame: CGRect = CGRectMake(0, 0, alertWidth, alertHeight)
alertView = UIView(frame: alertViewFrame)
alertView.backgroundColor = UIColor.redColor()
alertView.alpha = 0.0
alertView.layer.cornerRadius = 10;
alertView.layer.shadowColor = UIColor.blackColor().CGColor;
alertView.layer.shadowOffset = CGSizeMake(0, 5);
alertView.layer.shadowOpacity = 0.3;
alertView.layer.shadowRadius = 10.0;

// Create a button and set a listener on it for when it is tapped. Then the button is added to the alert view
let button = UIButton.buttonWithType(UIButtonType.System) as UIButton
button.setTitle("Dismiss", forState: UIControlState.Normal)
button.backgroundColor = UIColor.whiteColor()
button.frame = CGRectMake(0, 0, alertWidth, 40.0)

button.addTarget(self, action: Selector("dismissAlert"), forControlEvents: UIControlEvents.TouchUpInside)

alertView.addSubview(button)
view.addSubview(alertView)
}

func showAlert() {
// When the alert view is dismissed, I destroy it, so I check for this condition here
// since if the Show Alert button is tapped again after dismissing, alertView will be nil
// and so should be created again
if (alertView == nil) {
createAlert()
}

// I create the pan gesture recognizer here and not in ViewDidLoad() to
// prevent the user moving the alert view on the screen before it is shown.
// Remember, on load, the alert view is created but invisible to user, so you
// don't want the user moving it around when they swipe or drag on the screen.
createGestureRecognizer()

animator.removeAllBehaviors()

// Animate in the overlay
UIView.animateWithDuration(0.4) {
self.overlayView.alpha = 1.0
}

// Animate the alert view using UIKit Dynamics.
alertView.alpha = 1.0

var snapBehaviour: UISnapBehavior = UISnapBehavior(item: alertView, snapToPoint: view.center)
animator.addBehavior(snapBehaviour)
}

func dismissAlert() {

animator.removeAllBehaviors()

var gravityBehaviour: UIGravityBehavior = UIGravityBehavior(items: [alertView])
gravityBehaviour.gravityDirection = CGVectorMake(0.0, 10.0);
animator.addBehavior(gravityBehaviour)

// This behaviour is included so that the alert view tilts when it falls, otherwise it will go straight down
var itemBehaviour: UIDynamicItemBehavior = UIDynamicItemBehavior(items: [alertView])
itemBehaviour.addAngularVelocity(CGFloat(-M_PI_2), forItem: alertView)
animator.addBehavior(itemBehaviour)

// Animate out the overlay, remove the alert view from its superview and set it to nil
// If you don't set it to nil, it keeps falling off the screen and when Show Alert button is
// tapped again, it will snap into view from below. It won't have the location settings we defined in createAlert()
// And the more it 'falls' off the screen, the longer it takes to come back into view, so when the Show Alert button
// is tapped again after a considerable time passes, the app seems unresponsive for a bit of time as the alert view
// comes back up to the screen
UIView.animateWithDuration(0.4, animations: {
self.overlayView.alpha = 0.0
}, completion: {
(value: Bool) in
self.alertView.removeFromSuperview()
self.alertView = nil
})

}

@IBAction func showAlertView(sender: UIButton) {
showAlert()
}

func createGestureRecognizer() {
let panGestureRecognizer: UIPanGestureRecognizer = UIPanGestureRecognizer(target: self, action: Selector("handlePan:"))
view.addGestureRecognizer(panGestureRecognizer)
}

// This gets called when a pan gesture is recognized. It was set as the selector for the UIPanGestureRecognizer in the
// createGestureRecognizer() function
// We check for different states of the pan and do something different in each state
// In Began, we create an attachment behaviour. We add an offset from the center to make the alert view twist in the
// the direction of the pan
// In Changed we set the attachment behaviour's anchor point to the location of the user's touch
// When the user stops dragging (In Ended), we snap the alert view back to the view's center (which is where it was originally located)
// When the user drags the view too far down, we dismiss the view
// I check whether the alert view is not nil before taking action. This ensures that when the user dismisses the alert view
// and drags on the screen, the app will not crash as it tries to move a view that hasn't been initialized.
func handlePan(sender: UIPanGestureRecognizer) {

if (alertView != nil) {
let panLocationInView = sender.locationInView(view)
let panLocationInAlertView = sender.locationInView(alertView)

if sender.state == UIGestureRecognizerState.Began {
animator.removeAllBehaviors()

let offset = UIOffsetMake(panLocationInAlertView.x - CGRectGetMidX(alertView.bounds), panLocationInAlertView.y - CGRectGetMidY(alertView.bounds));
attachmentBehavior = UIAttachmentBehavior(item: alertView, offsetFromCenter: offset, attachedToAnchor: panLocationInView)

animator.addBehavior(attachmentBehavior)
}
else if sender.state == UIGestureRecognizerState.Changed {
attachmentBehavior.anchorPoint = panLocationInView
}
else if sender.state == UIGestureRecognizerState.Ended {
animator.removeAllBehaviors()

snapBehavior = UISnapBehavior(item: alertView, snapToPoint: view.center)
animator.addBehavior(snapBehavior)

if sender.translationInView(view).y > 100 {
dismissAlert()
}
}
}

}

override func didReceiveMemoryWarning() {
super.didReceiveMemoryWarning()
// Dispose of any resources that can be recreated.
}

}
```

当你运行应用程序时，点击“显示提醒”按钮，一个红色的视图将被捕捉到屏幕上，背景将变成灰色。用户将能够通过点击消除按钮来消除视图。这样做时，视图将从屏幕上消失，背景将变回白色。用户也可以拖动视图，但是在释放视图时，它会迅速恢复原位。当用户将视图向下拖得太远并放开它时，它会脱离屏幕。

![Example Application](img/1c24b8fdc3f3283dbe30ea92dc2c9b7b.png)

## 结论

使用 UIKit Dynamics 库，您可以自由地创建像真实世界对象一样的视图。如何将它应用到你的应用程序中，有无数种可能性。然而，你可能会被诱惑在你的应用程序中添加很多很酷的效果，记住——仅仅因为你可以，并不意味着你应该这样做。

无论你选择用这些工具做什么，请确保你遵守了[苹果的 iOS 人机界面指南](https://developer.apple.com/library/ios/documentation/userexperience/conceptual/mobilehig/)，并且你没有用你应用程序上所有的“酷眼糖果”来淹没你的用户。在 [iOS HIG 文档](https://developer.apple.com/library/ios/documentation/userexperience/conceptual/mobilehig/)上，你首先会读到的是“尊重”。用户界面帮助用户理解内容并与之互动，但永远不会与之竞争。”所以明智地使用你的能力。

## 分享这篇文章