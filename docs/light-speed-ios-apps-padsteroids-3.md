# 光速 iOS 应用程序:Padsteroids 3

> 原文：<https://www.sitepoint.com/light-speed-ios-apps-padsteroids-3/>

在这三部分的教程中，我们一直在为 iPad 制作一个太空游戏。在前两部分中，我们创建了应用程序，添加了飞船的绘图，添加了飞行和发射控制，并将它们连接起来，这样飞船就可以移动并发射激光。现在是时候加入一些小行星让游戏变得真正有趣了。

在本教程的最后阶段，您将看到一些更高级的绘图，包括线性渐变填充，以及动态和从显示中移除视图的能力。我们还将介绍如何构建简单的碰撞检测代码来计算激光是否会拦截小行星。

第一步，创建 AsteroidView 类。

### 创建小行星视图

构建 AsteroidView 类的方式与本教程第二部分中构建 CircleControlView 类的方式相同。一旦向导完成，您应该在项目文件列表中看到如图 1 所示的内容。

[![Padsteroids 3 Figure 1](img/ea92dd181255289adf4102498f8f57f5.png)](https://www.sitepoint.com/wp-content/uploads/2011/06/figure17.png)

图 1

为了产生小行星的外观，我们将画一个圆，但在不同的点上随机缩进，这将使它看起来崎岖不平。我们把缩进的数量设置为 20，但是你可以把它改成你认为最好的小行星的样子。

从这里我们需要修改`AsteroidView.h`文件来添加缩进列表以及旋转的开始角度。我们还需要一个渐变参考变量来保存我们将用于小行星的渐变填充。

清单 1 显示了第一个版本的`AsteroidView.h`。

**清单 1。AsteroidView.h**

```
#import <UIKit/UIKit.h>

@interface AsteroidView : UIView {
    NSArray *indents;   
    float startDegree;

    CGGradientRef gradient;
}

@end
```

现在我们需要在清单 2 所示的`AsteroidView.m`文件中实现 AsteroidView 类。

**清单 2。AsteroidView.m**

```
#import "AsteroidView.h"

@implementation AsteroidView

- (void)setupIndents {
    NSMutableArray *indentList = [[[NSMutableArray alloc] init] autorelease];
    for( int i = 0; i < 20; i++ ) {
        float indent = ( ( (float)rand()/(float)RAND_MAX ) * 0.6 ) + 0.4;
        [indentList addObject:[NSNumber numberWithFloat:indent]];
    }
    indents = [[NSArray arrayWithArray:indentList] retain];
    startDegree = 0;

    CGFloat locations[2] = { 0.0, 1.0 };
    CGFloat components[8] = { 1.0, 0.9, 0.9, 1.0, 1.0, 0.0, 0.0, 1.0 };

    CGColorSpaceRef colorSpace = CGColorSpaceCreateDeviceRGB();
    gradient = CGGradientCreateWithColorComponents (colorSpace, components,
                                                    locations, 2);

    self.opaque = FALSE;
    [self setBackgroundColor:[[UIColor alloc] initWithRed:0 green:0 blue:0 alpha:0.0]];

    CGColorSpaceRelease(colorSpace);
}

- (id)initWithFrame:(CGRect)frame {
    self = [super initWithFrame:frame];
    if (self) {
        [self setupIndents];
    }
    return self;
}

- (void)drawRect:(CGRect)rect {
    if ( indents == NULL ) {
        [self setupIndents];
    }

    CGPoint center;
    center.x = self.bounds.size.width / 2;
    center.y = self.bounds.size.height / 2;

    float degree = startDegree;
    float indentDegree = 360.0 / (float)[indents count];
    float radius = (float)( self.bounds.size.width / 2 ) * 0.9;

    BOOL firstPointDrawn = NO;
    CGPoint firstPoint;

    CGMutablePathRef path = CGPathCreateMutable();

    for( NSNumber *number in indents ) {
        CGPoint newPoint;

        newPoint.x = center.x + ( ( radius * [number floatValue] ) * sin( degree * 0.0174532925 ) );
        newPoint.y = center.y + ( ( radius * [number floatValue] ) * cos( degree * 0.0174532925 ) );

        if ( firstPointDrawn == NO ) { 
            CGPathMoveToPoint(path, NULL, newPoint.x, newPoint.y);
            firstPoint.x = newPoint.x;
            firstPoint.y = newPoint.y;
        } else {
            CGPathAddLineToPoint(path, NULL, newPoint.x, newPoint.y);
        }

        degree += indentDegree;
        if ( degree > 360.0 )
            degree -= 360.0;
        firstPointDrawn = YES;
    }

    CGPathAddLineToPoint(path, NULL, firstPoint.x, firstPoint.y);

    CGContextRef context = UIGraphicsGetCurrentContext();

    CGContextAddPath(context, path);
    CGContextClosePath(context);
    CGContextClip(context);

    CGContextDrawLinearGradient(context, gradient, 
                                CGPointMake(self.bounds.size.width/2,0), 
                                CGPointMake(self.bounds.size.width/2,self.bounds.size.height),
                                kCGGradientDrawsBeforeStartLocation | kCGGradientDrawsAfterEndLocation);

    CFRelease(path);
}

- (void)dealloc {
    [super dealloc];
    CGGradientRelease(gradient);
}

@end
```

这个类中有趣的工作在于两个方法，第一个是创建缩进列表的`setupIndents`方法，第二个是绘制图形的`drawRect`方法。

`setupIndents`方法创建一个 NSMutableArray 对象，它是一个数组，您可以动态地向其中添加元素。然后，该方法用一组 20 个随机缩进值填充该数组。然后，它通过用 NSMutableArray 的内容初始化 NSArray 来为对象创建`indentList`数组。

接下来`setupIndents`要做的是创建一个渐变来填充小行星。该梯度用于`drawRect`方法。`drawRect`方法首先使用缩进列表创建一个剪辑路径。然后在填充为渐变填充的矩形绘图上使用此剪辑路径。我们必须解决这个问题，因为对于多边形来说没有渐变填充函数。因此，您需要将裁剪设置为多边形，然后在其中绘制一个填充矩形。

### 测试小行星观点

现在我们有了小行星的第一个代码版本，是时候测试它了。最简单的方法是回到 XIB 文件，在游戏界面视图中添加一个 AsteroidView。您可以在图 2 中看到指定了 AsteroidView 的 Indentity 检查器。

[![Padsteroids 3 Figure 2](img/25daa907458bfad760d33a8e88634c8f.png)](https://www.sitepoint.com/wp-content/uploads/2011/06/figure21.png)

图 2

您需要确保这是游戏界面的子界面，如图 3 所示。

[![Padsteroids 3 Figure 3](img/c2d883237a32aa3d752676566eb82faf.png)](https://www.sitepoint.com/wp-content/uploads/2011/06/figure32.png)

图 3

这是小行星处理系统架构的重要组成部分。所有的小行星都将是游戏表面的孩子。游戏界面通过添加 AsteroidView 类型的子视图来创建小行星，观察它渲染的激光与其子视图之间的碰撞，如果检测到碰撞，则销毁子视图，并且一旦所有子视图都被销毁，则生成新的小行星。

一旦我们确定 AsteroidView 在正确的位置，就需要正确设置它的属性，如图 4 所示。

[![Padsteroids 3 Figure 4](img/961791f281948486e68ee459f4ad623b.png)](https://www.sitepoint.com/wp-content/uploads/2011/06/figure41.png)

图 4

该视图需要设置为具有清晰颜色背景的重绘模式。它还需要有用户交互和多点触摸禁用。

您可以随意调整它的大小，但是图 5 显示了一些测试视图的合理设置。

[![Padsteroids 3 Figure 5](img/6970f1dc02e4d712a0a07dd30dbb2c33.png)](https://www.sitepoint.com/wp-content/uploads/2011/06/figure51.png)

图 5

现在所有的属性都设置好了，是时候启动应用程序了，看看我们的小行星的渐变填充光彩。它应该类似于图 6。

[![Padsteroids 3 Figure 6](img/3553a115cbdd0af1a11c6bbd0e0571c7.png)](https://www.sitepoint.com/wp-content/uploads/2011/06/figure62.png)

图 6

当然，缩进的设置是随机的，所以你的结果会有所不同。

一旦你确定小行星被正确渲染，从游戏界面中删除它，如图 7 所示。

[![Padsteroids 3 Figure 7](img/b438da84ce03606dc0d72547f09e48b1.png)](https://www.sitepoint.com/wp-content/uploads/2011/06/figure71.png)

图 7

删除小行星后，我们现在可以在游戏界面视图中添加代码，动态添加它们。

### 动态添加小行星

清单 3 展示了需要添加到 GameSurface 定义中的 asteroidsAdded 布尔值。

**清单 3。向 GameSurface.h 添加小行星**

```
#import <UIKit/UIKit.h>

@interface GameSurface : UIView {
...

BOOL asteroidsAdded;
}

...

@end
```

然后我们需要在`GameSurface.m`上使用一些新方法，如清单 4 所示。

**清单 4。给 GameSurface.m 添加小行星**

```
#import "AsteroidView.h"
...

- (void)buildAsteroid {
    CGRect safeZone = CGRectMake((self.bounds.size.width/2)-50, 
                                 (self.bounds.size.height/2)-50, 100, 100);

    CGRect aFrame;
    while( true ) {
        aFrame.size.width = aFrame.size.height = ( ( (float)rand() / (float)RAND_MAX ) * 100.0 ) + 50;
        aFrame.origin.x = ( (float)rand() / (float)RAND_MAX ) * ( self.bounds.size.width - aFrame.size.width );
        aFrame.origin.y = ( (float)rand() / (float)RAND_MAX ) * ( self.bounds.size.height - aFrame.size.height );
        if ( CGRectIntersectsRect(aFrame, safeZone) == FALSE )
            break;
    }

    AsteroidView *av = [[AsteroidView alloc] initWithFrame:aFrame];
    [self addSubview:av];
    asteroidsAdded = YES;
}

- (void)createAsteroids {
    for( int i = 0; i < 20; i++ ) {
        [self buildAsteroid];
    }
}

- (void)drawRect:(CGRect)rect
{
    if ( asteroidsAdded == NO ) {
        [self createAsteroids];
    }
    ...
}
```

重要的第一步是在文件类型中添加导入，以引入 AsteroidView 定义。从那里我们添加了两个新方法，第一个是`buildAsteroid`，第二个是`createAsteroids`。`buildAsteroid`方法创建一个小行星，并将其添加为视图的子视图。`createAsteroid`功能只是调用`buildAsteroids`二十次来创建一个杂乱的小行星场。

`buildAsteroid`方法很有趣，因为它使用了`CGRectIntersectsRect`函数来确保小行星的位置不会太靠近屏幕的中心点。这确保了飞船启动时没有小行星。

对`drawRect`方法进行了扩充，增加了对`createAsteroids`的调用，以构建初始的小行星区域。

如果这样做了，结果应该如图 8 所示。

[![Padsteroids 3 Figure 8](img/1aee563fee8b3050029234fae08a1437.png)](https://www.sitepoint.com/wp-content/uploads/2011/06/figure82.png)

图 8

现在有一个小行星场是好的，但理想情况下，它需要四处移动，这样它才真正具有威胁性。所以下一步是给小行星做动画。

### 制作小行星动画

我们希望小行星既运动又旋转。旋转部分将通过缓慢调整对象中的`startDegree`值来处理。游戏表面将使用与小行星相关联的方向和速度属性来改变位置。清单 5 显示了对`AsteroidView.h`文件的更新。

**清单 5。向 AsteroidView.h 添加循环和位置**

```
#import <UIKit/UIKit.h>

@interface AsteroidView : UIView {
    NSArray *indents;   
    float startDegree;

    CGGradientRef gradient;

    float direction;
    float speed;
}

- (void)cycle;

@property (readwrite,assign) float direction;
@property (readwrite,assign) float speed;

@end
```

循环法将被用来旋转小行星。并且`@property`关键字指示方向和速度值是可以使用点符号设置的对象的属性(例如 asteroid1.direction = 50)。

对 AsteroidView.m 的更新如清单 6 所示。

**清单 6。向 AsteroidView.m 添加循环和位置**

```
#import "AsteroidView.h"

@implementation AsteroidView

@synthesize direction, speed;

...

- (void)cycle {
    startDegree += 0.2;
    if ( startDegree > 360 )
        startDegree -= 360.0;
    [self setNeedsDisplay];
}

@end
```

`@synthesize`关键字是自动生成方向和速度属性的访问器的一种简单方法。还显示了新的循环方法，该方法只需调整`startDegree`并刷新显示。

游戏界面也需要用新的 cycleAsteroids 方法进行更新，如清单 7 所示。

**清单 7。给游戏 Surface.h 添加小行星循环**

```
#import <UIKit/UIKit.h>

    @interface GameSurface : UIView {
        ...
    }

    ...

    - (void)cycleAsteroids;

    @end
```

清单 8 显示了新的`cycleAsteroids`方法和对`buildAsteroid`的更新。

**清单 8。给 GameSurface.m 添加小行星循环**

```
- (void)buildAsteroid {
    ...

    AsteroidView *av = [[AsteroidView alloc] initWithFrame:aFrame];
    av.direction = ( (float)rand() / (float)RAND_MAX ) * 360.0;
    av.speed = ( ( (float)rand() / (float)RAND_MAX ) * 1.0 ) + 0.2;
    [self addSubview:av];
    asteroidsAdded = YES;
}

- (void)cycleAsteroids {
    for( AsteroidView *v in [self subviews] ) {
        if ( v ) {
            CGPoint vorigin;
            vorigin.x = v.frame.origin.x + ( v.speed * sin( v.direction * 0.0174532925 ) );
            vorigin.y = v.frame.origin.y + ( v.speed * cos( v.direction * 0.0174532925 ) );

            if ( vorigin.x < 0 || vorigin.y < 0 ||
                vorigin.x + v.bounds.size.width > self.bounds.size.width ||
                vorigin.y + v.bounds.size.height > self.bounds.size.height ) {
                v.direction = v.direction + ( ( ( (float)rand() / (float)RAND_MAX ) * 180.0 ) + 180.0 );
                if ( v.direction > 360.0 )
                    v.direction -= 360.0;
            }
            else {
                [v setFrame:CGRectMake(vorigin.x, vorigin.y, v.bounds.size.width, v.bounds.size.height)];
            }

            [v cycle];
        }
    }
}
```

需要更新`buildAsteroid`方法，为速度和方向添加随机值。新的`cycleAsteroid`方法需要首先移动小行星，如果它撞到墙上，重置它的速度和方向，然后给小行星发送循环方法，让它旋转。

最后一个修改是对`PadsteroidsViewController.m`的修改，让它调用`cycleAsteroids`方法，如清单 9 所示。

**清单 9。从 PadsteroidsViewController.m 调用 cycleAsteroids】**

```
- (void)gameUpdate:(NSTimer*)theTimer {
    [gameSurface cycleAsteroids];
    ...
}
```

当所有这些都工作时，您应该会看到如图 9 所示的内容。

[![Padsteroids 3 Figure 9](img/f1749450805a23b2f2cc39f6a7dc9909.png)](https://www.sitepoint.com/wp-content/uploads/2011/06/figure91.png)

图 9

这很好，但是现在激光只是从小行星下面通过，如图 9 所示。下一步是计算出激光何时穿过小行星并摧毁它。

### 添加碰撞检测

`GameSurface.m`的`lineIntersects`方法使用一些 trig 来计算出飞船穿过小行星矩形的向量。该方法如清单 10 所示。

**清单 10。为 GameSurface.m 添加激光碰撞检测**

```
-(BOOL)lineIntersects:(CGRect)rect start:(CGPoint)start end:(CGPoint)end {
    double minX = start.x;
    double maxX = end.x;

    if(start.x > end.x) {
        minX = end.x;
        maxX = start.x;
    }

    if(maxX > rect.origin.x+rect.size.width)
        maxX = rect.origin.x+rect.size.width;

    if(minX < rect.origin.x)
        minX = rect.origin.x;

    if(minX > maxX)
        return NO;

    double minY = start.y;
    double maxY = end.y;

    double dx = end.x - start.x;

    if(abs(dx) > 0.0000001) {
        double a = (end.y - start.y) / dx;
        double b = start.y - a * start.x;
        minY = a * minX + b;
        maxY = a * maxX + b;
    }

    if(minY > maxY) {
        double tmp = maxY;
        maxY = minY;
        minY = tmp;
    }

    if(maxY > rect.origin.y + rect.size.height)
        maxY = rect.origin.y + rect.size.height;

    if(minY < rect.origin.y)
        minY = rect.origin.y;

    if(minY > maxY)
        return NO;

    return YES;
}

- (void)enableGun:(float)distance angle:(float)angle {
    gunEnabled = YES;
    gunDirection = angle;

    CGPoint laserStart, laserEnd;
    laserStart.x = ( self.bounds.size.width / 2 ) + shipLocation.x;
    laserStart.y = ( self.bounds.size.height / 2 ) + shipLocation.y;
    laserEnd.x = laserStart.x + ( 1000.0 * cos((gunDirection+90.0) * 0.0174532925) ) * -1;
    laserEnd.y = laserStart.y + ( 1000.0 * sin((gunDirection+90.0) * 0.0174532925) ) * -1;

    for( AsteroidView *v in [self subviews] ) {
        if ( [self lineIntersects:v.frame start:laserStart end:laserEnd] ) {
            [v removeFromSuperview];
        }
    }

    [self setNeedsDisplay];
}

- (void)disableGun {
    gunEnabled = NO;

    if ( [[self subviews] count] == 0 ) {
        [self createAsteroids];
    }

    [self setNeedsDisplay];
}
```

`enableGun`和`disableGun`方法现在升级为命中的代码测试，在`disbleGun`的情况下，如果它们都被摧毁了，就会产生新的小行星。`enableGun`中的碰撞代码遍历视图的所有子视图，并使用`lineIntersects`方法检查激光矢量是否与小行星视图的框架相交。

正如你在图 10 中看到的，你现在可以用激光扫射并摧毁小行星。

[![Padsteroids 3 Figure 10](img/d28af873d253cef9bf0c6911de62f879.png)](https://www.sitepoint.com/wp-content/uploads/2011/06/figure101.png)

图 10

在这里，我用激光扫过屏幕底部，摧毁了下半部分的所有小行星。

### 结论

这不是一个完整的游戏。没有多重等级，没有得分，甚至没有失败的可能。但是所有的基本工具都在那里，可以建造你喜欢的任何东西。而且你可以用你在这里学到的技能；构建应用程序、使用界面构建器、创建自定义视图、处理用户交互事件、进行复杂的绘图以及进行冲突检测。

您可以在自己的应用程序中随意使用这个示例中的所有代码。如果你有什么有趣的东西，请告诉我。我一定会下载下来试一试。

## 分享这篇文章