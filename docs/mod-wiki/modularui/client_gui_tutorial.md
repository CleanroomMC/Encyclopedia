Here we want to create a UI that only exist on the client side and doesn't sync any data.

## 1. Creating the screen
First we need a new screen class which extends `ModularScreen`. Intellij immidiatly asks us to add a constructor and to override `buildUI()`.
(You can also use the `ModularScreen` constructor where you pass in a `ModularPanel`.)
```java
public class TutorialScreen extends ModularScreen {

    // these two strings are used to identify this screen. For owner you can use the mod id and 
    // for name you can use anything that fits the purpose of the screen
    public TutorialScreen(@NotNull String owner) {
        super(owner, name);
    }

    @Override
    public ModularPanel buildUI(GuiContext context) {
        return null;
    }
}
```

The `buildUI` is where the fun happens. Here you must create the main `ModularPanel` and it's widget tree. This method is called from the `ModularScreen` constructor. You should NOT call it by yourself.

Let's create a `ModularPanel`. For that you can use the normal constructor or you call `ModularPanel.default()`. You can also create a custom panel class for more customization.
The `default()` method automatically sets the size to 176 x 166 and centers it in the screen. A default background texture is given by the assigned `Theme`, but you can set your own with `.background()` if you want.
```java
public class TutorialScreen extends ModularScreen {

    // simplify constructor 
    public TutorialScreen() {
        super("tutorial_mod");
    }

    @Override
    public ModularPanel buildUI(GuiContext context) {
        ModularPanel panel = ModularPanel.defaultPanel("tutorial_panel", context);
        // create widget tree here
        return panel;
    }
}
```

From now on we will only look at the `buildUI` method.
For now lets put a title at the top of the panel. For that we'll use `.child()` and pass a widget to it. To create a text widget we can use `IKey.str().asWidget`. (Note: You can create a translated string with `IKey.lang()`).
We also want to set the postion, for that we chain `.top()` and `.left()` on ANY widegt which implements `IPositioned`. We don't need to set a size since `TextWidget` calculates it on its own. But you can limit the width for example and the text will automatically wrap, if necessary.
```java
public ModularPanel buildUI(GuiContext context) {
    ModularPanel panel = ModularPanel.defaultPanel("tutorial_panel", context);
    panel.child(IKey.str("My first screen").asWidget()
                .top(7).left(7));
    return panel;
}
```

## 2. Opening the screen
We want to open the screen when the player right clicks with a diamond. We use a forge event for that and simply check for client side and a diamond.
Then we open the screen with `ClientGUI.open()`. You need to pass a screen instance from your earlier created class.
Now let's boot up minecraft and test it.

```java
@Mod.EventBusSubscriber
public class EventHandler {

    @SubscribeEvent
    public static void onItemUse(PlayerInteractEvent.RightClickItem event) {
        if (event.getEntityPlayer().getEntityWorld().isRemote && event.getItemStack().getItem() == Items.DIAMOND) {
            ClientGUI.open(new TutorialGui());
        }
    }
}
```
Your screen should look like this, when you take a diamond and right click. The blurry background comes from the Blur mod. It is not required, but it makes things look nicer in my opinion.
You may notice the purple text in the bottom left corner or a border on widgets when you hover them. That's the debug mode. Its enabled by default in a dev environment and can be disabled via the config file or by pressing `CTRL + SHIFT + ALT + C` while in a ModularUI screen.
![grafik](https://user-images.githubusercontent.com/45517902/228584027-eaf4f49b-1967-4aa1-9cd3-416e5610f113.png)

## 3. Adding a button
Now let's add a button, that greets the player with a message. For that we will use the `ButtonWidget`. Here we do need to set a size since the text on the button is not a widget, but a `IDrawable`, which doesnt size itself. It uses the size of its widget. We don't need to set a background since its set by the current Theme. We center the button in the panel using `.align(Alignment.Center)` and set a size with `.size(60, 16)`. We could also use `.width(60).height(16)`. It's the same thing.
```java
public ModularPanel buildUI(GuiContext context) {
    ModularPanel panel = ModularPanel.defaultPanel("tutorial_panel", context);
    panel.child(IKey.str("My first screen").asWidget()
                        .top(7).left(7))
                .child(new ButtonWidget<>()
                        .align(Alignment.Center)
                        .size(60, 16)
                        .overlay(IKey.str("Say Hello"))
                        .onMousePressed(button -> {
                            EntityPlayer player = Minecraft.getMinecraft().player;
                            player.sendMessage(new TextComponentString("Hello " + player.getName()));
                            return true;
                        }));
    return panel;
}
```
Let's see what it looks like.
![grafik](https://user-images.githubusercontent.com/45517902/228590064-108ae148-acc8-45ca-9d96-e91cbe0f2e4a.png)

Pressing the button greets the player in the chat.
![grafik](https://user-images.githubusercontent.com/45517902/228590312-24f6bd17-dd05-44ee-96bd-6ae7d00e59cc.png)

Simple, right?

