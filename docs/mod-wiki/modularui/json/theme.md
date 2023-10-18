
Make sure to check out [how to register themes](../themes.md).

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
