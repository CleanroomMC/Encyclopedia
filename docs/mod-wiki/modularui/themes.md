
!!! Note
    Themes can only be registered for GUIs made with ModularUI.

## Reloading themes

You can reload themes by either reloading resources (which is slow) or by running the `/reloadThemes` command.

## For mod developers

### Registering themes

First create a `JsonBuilder` instance (`com.cleanroommc.modularui.utils.JsonBuilder`). This will be your theme data.
ModularUI will automatically parse the json.
Next you need to register it. Make sure to do that before resource packs are loaded (that's when themes gets loaded and
parsed). `FMLPreInit` works fine.

```java
JsonBuilder myTheme = new JsonBuilder();
IThemeApi.get().registerTheme("mymodid:my_theme", myTheme);
```

It is not required to have the mod id in the name, but it will help identifying the theme.
Multiple themes with the same name can be registered. Themes that are added later will override all properties of all
previously registered themes.

You can edit the theme before and after you registered it. Just make sure it's before the themes are loaded.
If you are unsure you can use the `ReloadThemeEvent.Pre`.

### Applying a theme to a GUI

There are two ways to set a theme in a GUI.

1. `IThemeApi.get().registerThemeForScreen(String screenOwner, String mainPanelName, String themeName)`
   This method can be used at any point. Registering another theme with the same screen name will overwrite the old one.
2. When you create the screen: `new ModularScreen(...).useTheme(String themeName)`

If both methods are used, the first will always take priority.

## For resource packs

First create new file in your resource pack at `assets/modularui/themes.json`. You can replace `modularui` with any
loaded mod id here.
In this file you will define themes and where they are located as well as setting themes for GUIs.

Let's look at the `themes.json` file that is shipped with ModularUI.

```json
{
  "vanilla": "modularui:vanilla",
  "vanilla_dark": "modularui:vanilla_dark",
  "screens": {
    "modularui:test": "vanilla_dark"
  }
}
```

### Registering themes

The first line is `"vanilla": "modularui:vanilla"`. `vanilla` is the name of the theme and `modularui:vanilla` is the
path to the theme file.
This means the theme file for `vanilla` should be at `assets/modularui/themes/vanilla.json`.
The next line defines a new theme with the name `vanilla_dark` with the theme file located at
`assets/modularui/themes/vanilla_dark.json`.

### Applying a theme to a GUI

Let's look at the next line: `"screens": {}`

Inside the curly brackets we can set themes for screens. The format is `"screen_owner:main_panel_name": "theme_name"`.
So in the example above `modularui:test` the full screen name and `vanilla_dark` is the theme name (which we defined
before).

## Creating themes

Here we will take a look what the theme file can look like. If you are a mod developer you can directly translate to
your `JsonBuilder`.

Let's look at an example. This is what the default vanilla theme as a json file would look like.

```json
{
   "parent": "DEFAULT",
   "background": null,
   "hoverBackground": null,
   "color": "#FFFFFFFF",
   "textColor": "#FF404040",
   "textShadow": false,
   "panel": {
      "background": {
         "type": "texture",
         "id": "vanilla_background"
      }
   },
   "button": {
      "background": {
         "type": "texture",
         "id": "vanilla_button"
      },
      "textColor": "#FFFFFFFF",
      "textShadow": true
   },
   "itemSlot": {
      "background": {
         "type": "texture",
         "id": "slot_item"
      },
      "slotHoverColor": "#60FFFFFF"
   },
   "fluidSlot": {
      "background": {
         "type": "texture",
         "id": "slot_fluid"
      },
      "slotHoverColor": "#60FFFFFF"
   },
   "textField": {
      "textColor": "#FFFFFFFF",
      "markedColor": "#FF2F72A8"
   },
   "toggleButton": {
      "background": {
         "type": "texture",
         "id": "vanilla_button"
      },
      "textColor": "#FFFFFFFF",
      "textShadow": true,
      "selectedBackground": {
         "type": "texture",
         "id": "slot_item"
      },
      "selectedHoverBackground": null,
      "selectedColor": "0xFFBBBBBB"
   }
}
```
