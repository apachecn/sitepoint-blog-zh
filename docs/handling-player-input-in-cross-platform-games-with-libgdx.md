# 用 LibGDX 处理跨平台游戏中的玩家输入

> 原文：<https://www.sitepoint.com/handling-player-input-in-cross-platform-games-with-libgdx/>

LibGDX 是一个用于创建跨平台游戏和应用程序的开源 Java 库。在我的上一篇教程中，我介绍了如何设置库并为开发做好准备。本教程将介绍如何用 LibGDX 处理玩家输入，为你的跨平台游戏带来交互性。

## 不同类型的输入状态。

处理输入是一项简单的任务。如果一个键被按下*，那么该键应该被记录为`true`，同样，当一个键被按下*时也是如此。这种简单会导致很多问题，尤其是游戏。您需要的是一种简单的方法来询问 libGDX 特定的键是被按下、按下还是释放。**

 **但是这三个不同的关键状态有什么意义呢？它们描述了用户如何与键盘互动。

*   **按下**:一个键被激活，只触发一帧。
*   **按下**:当前有一个键被按下。
*   **释放**:一个键被释放，只触发一帧。

如果渲染函数中有逻辑，例如:

```
if (key.pressed())
    print("pressed")

if (key.down())
    print("down")

if (key.released())
    print("released") 
```

您可能会看到类似这样的内容:

```
..."pressed" //triggered one frame ..."down" //constant amongst all frames ..."down" ..."down" ..."released" //triggered one from 
```

同样的逻辑也适用于触摸事件。

## 输入管理器类

有许多不同的方法可以实现这个类，但是我建议你尽可能的“面向对象”,这意味着每个按键和触摸事件都是它自己的对象。

第一个要声明的类是输入管理器本身(它将实现 libGDX 的`InputProcessor`类)。

```
public class InputManager implements InputProcessor {
} 
```

如果您遵循上一教程，并且正在使用 IntelliJ，在这个类中右键单击并单击*generate->override methods*。(由于代码膨胀，我现在将这些生成的方法从这些代码片段中去掉。)

接下来，创建一个名为`InputState`的内部类。

**注意**:下面三个类都是`InputManager`的内部类。

```
public class InputState {
    public boolean pressed = false;
    public boolean down = false;
    public boolean released = false;
} 
```

现在创建扩展`InputState`的`KeyState`和`TouchState`类

```
public class KeyState extends InputState{
    //the keyboard key of this object represented as an integer.
    public int key;

    public KeyState(int key){
        this.key = key;
    }
}

public class TouchState extends InputState{
    //keep track of which finger this object belongs to
    public int pointer;
    //coordinates of this finger/mouse
    public Vector2 coordinates;
    //mouse button
    public int button;
    //track the displacement of this finger/mouse
    private Vector2 lastPosition;
    public Vector2 displacement;

    public TouchState(int coord_x, int coord_y, int pointer, int button){
        this.pointer = pointer;
        coordinates = new Vector2(coord_x, coord_y);
        this.button = button;

        lastPosition = new Vector2(0, 0);
        displacement = new Vector2(lastPosition.x, lastPosition.y);
    }
} 
```

`TouchState`类更复杂，因为它不仅有`Pressed`、`Down`和`Released`事件，还存储哪个手指控制着这个对象，并存储手势移动的坐标和位移向量。

下面是整个类的基本结构(不包括存根重写方法):

```
public class InputManager implements InputProcessor {
    public class InputState {
        public boolean pressed = false;
        public boolean down = false;
        public boolean released = false;
    }

    public class KeyState extends InputState{
    //the keyboard key of this object represented as an integer.
        public int key;

        public KeyState(int key){
            this.key = key;
        }
    }

    public class TouchState extends InputState{
        //keep track of which finger this object belongs to
        public int pointer;
        //coordinates of this finger/mouse
        public Vector2 coordinates;        
        //mouse button
        public int button;
        //track the displacement of this finger/mouse
        private Vector2 lastPosition;
        public Vector2 displacement;

        public TouchState(int coord_x, int coord_y, int pointer, int button){
            this.pointer = pointer;
            coordinates = new Vector2(coord_x, coord_y);
            this.button = button;

            lastPosition = new Vector2(0, 0);
            displacement = new Vector2(lastPosition.x,lastPosition.y);
        }
    }
} 
```

因为每个按键/触摸事件都是它自己的对象，所以你需要将它们存储在一个数组中。向`InputManager`添加两个新的数组字段。

```
public Array<KeyState> keyStates = new Array<KeyState>();
public Array<TouchState> touchStates = new Array<TouchState>(); 
```

并为`InputManager`添加一个构造函数来初始化这两个对象。

```
public InputManager() {
    //create the initial state of every key on the keyboard.
    //There are 256 keys available which are all represented as integers.
    for (int i = 0; i < 256; i++) {
        keyStates.add(new KeyState(i));
    }

    //this may not make much sense right now, but I need to create
    //atleast one TouchState object due to Desktop users who utilize
    //a mouse rather than touch.
    touchStates.add(new TouchState(0, 0, 0, 0));
} 
```

现在，您可以利用生成的覆盖方法来控制这些对象的逻辑了。从`public boolean keyDown(int keycode)`开始。

```
@Override
public boolean keyDown(int keycode) {
    //this function only gets called once when an event is fired. (even if this key is being held down)

    //I need to store the state of the key being held down as well as pressed
    keyStates.get(keycode).pressed = true;
    keyStates.get(keycode).down = true;

    //every overridden method needs a return value. I won't be utilizing this but it can be used for error handling.
    return false;
} 
```

接下来是`public boolean keyUp(int keycode)`。

```
@Override
public boolean keyUp(int keycode) {
    //the key was released, I need to set it's down state to false and released state to true
    keyStates.get(keycode).down = false;
    keyStates.get(keycode).released = true;
    return false;
} 
```

现在您已经处理了键状态的逻辑，您需要一种方法来访问这些键以检查它们的状态。将这三种方法添加到`InputManager`:

```
//check states of supplied key
public boolean isKeyPressed(int key){
    return keyStates.get(key).pressed;
}
public boolean isKeyDown(int key){
    return keyStates.get(key).down;
}
public boolean isKeyReleased(int key){
    return keyStates.get(key).released;
} 
```

一切都在成形，所以现在是尝试解释一切是如何组合在一起的好时机。

libGDX 将每个键表示为一个整数，用于从返回单个`keyState`对象的`keyStates`数组中获取一个元素。您可以检查该键的状态(`Pressed`、`Down`或`Released`)，这是一个布尔值。

你几乎已经准备好试驾`InputManager`了，但是还有一些东西需要设置。现在，唯一真正起作用的状态是`Down`状态。

当一个键是`Down`时。`Down`设置为真。而当一个键是`Up`时，`Down`被设置为假。另外两个状态，`Pressed`和`Released`还不能正常工作。

这些是`Trigger`键，应该只触发一帧，然后保持为假。现在，一旦它们被激活，它们将继续保持`True`。

您需要为`InputManager`实现一个名为`update`的新方法，它将正确处理`Pressed`和`Released`的状态。

```
public void update(){
  //for every keystate, set pressed and released to false.
  for (int i = 0; i < 256; i++) {
      KeyState k = keyStates.get(i);
      k.pressed = false;
      k.released = false;
  }
} 
```

`Update`方法允许您调整`InputManager`立柱框架。任何需要重置的内容都在这个方法中。

要使用`InputManager`，您需要在项目的`core`名称空间中实例化它。在`MyGdxGame`内，添加`InputManager`作为新字段。

```
public class MyGdxGame extends ApplicationAdapter {
    InputManager inputManager;
} 
```

在`MyGdxGame`构造函数中，实例化`InputManager`。为了使`InputManager`有用，您需要将它传递给 libGDX 的`InputProcessor`，libGDX 将使用这个新对象来处理输入事件。用下面的方法替换当前的`create`方法:

```
@Override
  public void create () {
      inputManager = new InputManager();
      Gdx.input.setInputProcessor(inputManager);
  } 
```

现在您需要在`MyGdxGame`的 render 方法的末尾调用`InputManager`的 update 方法。

你应该在所有涉及输入的游戏逻辑都处理完之后再调用`update()`。用以下方法替换当前的`create`方法:

```
@Override
public void render () {
    inputManager.update();
} 
```

以下是`MyGdxGame`目前的样子:

```
public class MyGdxGame extends ApplicationAdapter {
    SpriteBatch batch;
    Texture img;
    OrthographicCamera camera;
    InputManager inputManager;

    @Override
    public void create () {
        batch = new SpriteBatch();
        img = new Texture("badlogic.jpg");
        camera = new OrthographicCamera(Gdx.graphics.getWidth(), Gdx.graphics.getHeight());
        inputManager = new InputManager();
        Gdx.input.setInputProcessor(inputManager);
    }

    @Override
    public void render () {
        camera.update();
        batch.setProjectionMatrix(camera.combined);

        Gdx.gl.glClearColor(1, 0, 0, 1);
        Gdx.gl.glClear(GL20.GL_COLOR_BUFFER_BIT);
        batch.begin();
        batch.draw(img, 0, 0);
        batch.end();

        inputManager.update();
    }
} 
```

测试这段代码，确保一切正常。

```
@Override
    public void render () {

        //testing key states...
        if (inputManager.isKeyPressed(Input.Keys.A)) {
            System.out.println("A Pressed");
        }
        if (inputManager.isKeyDown(Input.Keys.A)) {
            System.out.println("A Down");
        }
        if (inputManager.isKeyReleased(Input.Keys.A)) {
            System.out.println("A Released");
        }

        camera.update();
        batch.setProjectionMatrix(camera.combined);

        Gdx.gl.glClearColor(1, 0, 0, 1);
        Gdx.gl.glClear(GL20.GL_COLOR_BUFFER_BIT);
        batch.begin();
        batch.draw(img, 0, 0);
        batch.end();

        inputManager.update();
    } 
```

您现在应该有更准确的输入事件了。

`InputManager`几乎完成了，剩下的就是实现处理触摸事件的逻辑。幸运的是，`KeyStates`和`TouchStates`功能相同。现在，您将利用生成的触摸事件方法。

**注意**:这个方法被大量评论，所以我可能重复我自己。

```
@Override
    public boolean touchDown(int screenX, int screenY, int pointer, int button) {
        //There is always at least one touch event initialized (mouse).
        //However, Android can handle multiple touch events (multiple fingers touching the screen at once).

        //Due to this difference, the input manager will add touch events on the fly if more than one
        //finger is touching the screen.

        //check for existing pointer (touch)
        boolean pointerFound = false;

        //get altered coordinates
        int coord_x = coordinateX(screenX);
        int coord_y = coordinateY(screenY);

        //set the state of all touch state events
        for (int i = 0; i < touchStates.size; i++) {
            TouchState t = touchStates.get(i);
            if (t.pointer == pointer) {
                t.down = true;
                t.pressed = true;

                //store the coordinates of this touch event
                t.coordinates.x = coord_x;
                t.coordinates.y = coord_y;
                t.button = button;

                //recording last position for displacement values
                t.lastPosition.x = coord_x;
                t.lastPosition.y = coord_y;

                //this pointer exists, don't add a new one.
                pointerFound = true;
            }
        }

        //this pointer doesn't exist yet, add it to touchStates and initialize it.
        if (!pointerFound) {
            touchStates.add(new TouchState(coord_x, coord_y, pointer, button));
            TouchState t = touchStates.get(pointer);

            t.down = true;
            t.pressed = true;

            t.lastPosition.x = coord_x;
            t.lastPosition.y = coord_y;
        }
        return false;
    } 
```

`KeyStates`和`TouchStates`的主要区别之一是，由于键盘是一个物理设备，所有的`KeyStates`都在`InputManager`的构造函数中初始化。

您知道所有可用的键，但是触摸事件是跨平台的事件。在桌面上触摸意味着用户点击了鼠标，但在 Android 上触摸意味着用户用手指触摸了屏幕。最重要的是，桌面上只能有一个触摸事件(鼠标)，而 Android 上可以有多个触摸事件(手指)。

为了解决这个问题，根据用户的操作动态添加新的`TouchStates`。

当用户触发一个触摸事件时，你首先要把它的`screenX`和`screenY`值转换成更有用的值。坐标`0,0`在屏幕的左上角，Y 轴翻转。为了适应这一点，给`InputManager`添加两个简单的方法来转换这些坐标，使`0,0`在屏幕的中心，Y 轴右侧向上，这将更好地处理`SpriteBatch`对象。

```
private int coordinateX (int screenX) {
        return screenX - Gdx.graphics.getWidth()/2;
    }
private int coordinateY (int screenY) {
        return Gdx.graphics.getHeight()/2 - screenY;
    } 
```

现在，您需要一种方法来检查这是一个新的触摸事件，还是`InputManager`已经发现了这个事件并将其添加到了`TouchStates`的列表中。为了澄清，用户正拿着他们的设备并用右手拇指触摸屏幕，这将是`touchStates`内的第一个触摸事件，并且`InputManager`知道它可以处理至少一个触摸事件。如果用户决定用左手和右手拇指触摸屏幕，第二次触摸事件将实例化一个新的`TouchState`，并将其动态添加到`touchStates`。`InputManager`现在知道它可以处理两个触摸事件。

所有这些都是从传递给这个方法的`pointer`变量开始的。`pointer`变量是一个整数，允许您区分同时发生的触摸事件。

每当用户触发一个触摸事件时，使用`pointer`来检查是否创建一个新的`TouchState`。

```
boolean pointerFound = false; 
```

遍历所有可用的`TouchState`对象，检查该指针是否已经存在，并设置该`TouchState`的状态。

```
//set the state of all touch state events
        for (int i = 0; i < touchStates.size; i++) {
            TouchState t = touchStates.get(i);
            if (t.pointer == pointer) {
                t.down = true;
                t.pressed = true;

                //store the coordinates of this touch event
                t.coordinates.x = coord_x;
                t.coordinates.y = coord_y;
                t.button = button;

                //recording last position for displacement values
                t.lastPosition.x = coord_x;
                t.lastPosition.y = coord_y;

                //this pointer exists, don't add a new one.
                pointerFound = true;
            }
        } 
```

注意如果没有找到一个`TouchState`，你如何创建一个新的并把它附加到`touchStates`数组中。还要注意如何访问`TouchState`。

```
touchStates.add(new TouchState(coord_x, coord_y, pointer, button));
TouchState t = touchStates.get(pointer); 
```

如果一个手指触摸屏幕，`pointer`就是代表这个手指的`0`。将这个新的`TouchState`对象添加到数组中会自动设置正确的索引值。它在数组中的位置是`0`，与它的`pointer`值相同。

如果第二个手指触摸屏幕，它的`pointer`将是`1`。当新的`TouchState`被添加到数组中时，它的索引值是`1`。如果需要找出哪个`TouchSate`是哪个，使用给定的`pointer`值从`touchStates`中找出正确的`TouchState`。

现在处理手指不再接触屏幕时的逻辑。

```
@Override
    public boolean touchUp(int screenX, int screenY, int pointer, int button) {
        TouchState t = touchStates.get(pointer);
        t.down = false;
        t.released = true;

        return false;
    } 
```

**注意**:我选择不移除`TouchState`对象，即使它们不再被使用。如果发现了一个新的手指计数，我认为它应该被发现并准备好被重用。如果你对此有不同的看法，在`touchUp`方法中，你可以实现移除`TouchState`的逻辑。

现在计算一个`TouchState`的位移向量来处理手指手势。

```
@Override
    public boolean touchDragged(int screenX, int screenY, int pointer) {
        //get altered coordinates
        int coord_x = coordinateX(screenX);
        int coord_y = coordinateY(screenY);

        TouchState t = touchStates.get(pointer);
        //set coordinates of this touchstate
        t.coordinates.x = coord_x;
        t.coordinates.y = coord_y;
        //calculate the displacement of this touchstate based on
        //the information from the last frame's position 
        t.displacement.x = coord_x - t.lastPosition.x;
        t.displacement.y = coord_y - t.lastPosition.y;
        //store the current position into last position for next frame.
        t.lastPosition.x = coord_x;
        t.lastPosition.y = coord_y;

        return false;
    } 
```

与之前的按键状态一样，您需要添加三个方法来访问这些触摸状态，再加上另外两个方法来获取`TouchState`的坐标和位移。

```
//check states of supplied touch
    public boolean isTouchPressed(int pointer){
        return touchStates.get(pointer).pressed;
    }
    public boolean isTouchDown(int pointer){
        return touchStates.get(pointer).down;
    }
    public boolean isTouchReleased(int pointer){
        return touchStates.get(pointer).released;
    }

    public Vector2 touchCoordinates(int pointer){
        return touchStates.get(pointer).coordinates;
    }
    public Vector2 touchDisplacement(int pointer){
        return touchStates.get(pointer).displacement;
    } 
```

您现在遇到了与之前`KeyStates`相同的问题，当时只有`Down`状态正常工作。但是这很有道理，你从来没有添加逻辑来重置`trigger`事件。

返回到`update`方法，添加以下代码:

```
for (int i = 0; i < touchStates.size; i++) {
            TouchState t = touchStates.get(i);

            t.pressed = false;
            t.released = false;

            t.displacement.x = 0;
            t.displacement.y = 0;
        } 
```

下面是完整的`update`方法:

```
public void update(){
        for (int i = 0; i < 256; i++) {
            KeyState k = keyStates.get(i);
            k.pressed = false;
            k.released = false;
        }
        for (int i = 0; i < touchStates.size; i++) {
            TouchState t = touchStates.get(i);

            t.pressed = false;
            t.released = false;

            t.displacement.x = 0;
            t.displacement.y = 0;
        }
    } 
```

**注意**:每一帧的位移矢量都被重置回`(0, 0)`。

## 使用输入管理器

您已经看到了使用`InputManager`检查`KeyStates`的例子。现在我将使用`TouchStates`来解释。

如果应用程序在桌面上运行，并且仅使用鼠标，则使用唯一可用的`touchStates`中的`TouchState`。

```
if (inputManager.isTouchPressed(0)) {
    System.out.println("PRESSED");
 }
 if (inputManager.isTouchDown(0)) {
     System.out.println("DOWN");
     System.out.println("Touch coordinates: " + inputManager.touchCoordinates(0));
     System.out.println("Touch displacement" + inputManager.touchDisplacement(0));
 }
 if (inputManager.isTouchReleased(0)) {
     System.out.println("RELEASED");
 } 
```

如果应用程序运行在 Android 上，你需要遍历所有可用的`TouchStates`并单独处理每一个，但是直接检查一个`TouchState`容易出错。

```
inputManager.touchStates.get(0); 
```

用添加到`InputManager`中的新方法覆盖这一点:

```
public TouchState getTouchState(int pointer){
        if (touchStates.size > pointer) {
            return touchStates.get(pointer);
        } else {
            return null;
        }
    } 
```

现在你有了一个更干净的方法，通过简单的错误检查来访问一个`TouchState`。

```
inputManager.getTouchState(0); 
```

如果这是在一个 for 循环中开始，那么 for 循环的边界检查将基本上没有错误检查。

```
for (int i = 0; i < inputManager.touchStates.size; i++) {
            TouchState t = inputManager.getTouchState(i);

            System.out.println("Touch State: " + t.pointer + " : coordinates : " + t.coordinates);
        } 
```

## (赛场局势发生变化后表明)胜负难料

这个输入管理器是为控制游戏环境中的实体而设计的。我最初设计这个职业的基础是为了让我的游戏角色有“可变”的跳跃高度。

后来我扩展了这个类来处理 Android 多点触摸事件的输入，这个类目前被用在一个叫做 [Vortel](#) 的游戏中。我需要一种方法来允许用户用他们的手指控制一个实体，不管他们的手指在屏幕上的什么位置，也不管同时有多少手指在屏幕上。我通过累加每个`TouchState`的所有位移向量，然后将这个新向量应用到实体上，实现了这个效果。如果你有空，请去看看。

我在另一款名为[软泥](#)的游戏中使用了可变跳跃高度功能。我使用了`TouchState` ( `Pressed,` `Down`和`Released`)的不同状态，根据用户触摸屏幕的时间来精确控制角色跳多高。

*我希望这篇教程对你的游戏想法有用，期待你在下面*的提问和评论。

## 分享这篇文章**