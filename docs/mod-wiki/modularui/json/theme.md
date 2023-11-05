
Make sure to check out [how to register themes](../themes.md).

Here we will take a look what the theme file can look like. If you are a mod developer you can directly translate it to
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

First we have the `parent` property. This defines the parent theme. If a theme does not define a widget theme, it will
be taken from its parent. If the `parent` property is not set, it defaults to `DEFAULT`, which is just the vanilla theme.

After that we have the `color`, `background`, `hoverBackground`, `textColor` and `textShadow` properties. These are
fallback properties of [widget themes](#widget-themes). You can put any properties that any widget theme can have here.
If a widget theme does not have said property it will fall back to the top level property if defined.

For `color` and `textColor` see [color](color.md). For `background` and `hoverBackground` see [drawables](drawable.md).

Next we have objects defined with the property name `panel`, `button`, `itemSlot`, `fluidSlot`, `textField` and `toggleButton`.
These are widget themes. These are all widget themes ModularUI provides. Addons may add more.

## Widget themes

Widgets which don't use one of the existing widget themes use the fallback properties.

All widget themes have the properties `color`, `background`, `hoverBackground`, `textColor` and `textShadow`. Which are
all mostly self-explanatory. `color` is applied additionally to the background (if possible). 

The `itemSlot` and `fluidSlot` also have the `slotHoverColor`, which is just the rendered color when the slot is hovered.
Don't use full opacity here. Otherwise, you won't be able to see the item.

The `textField` theme has the `markedColor` property which is the marked text background.

The `toggleButton` has `selectedBackground`, `selectedHoverBackground` and `selectedColor` which are all self-explanatory.

!!! Note
    All widget themes are optional. You can define as many as you like. Not defined widget themes will be taken from the
    parent theme

    All properties of widget themes (and the fallback properties) are optional.