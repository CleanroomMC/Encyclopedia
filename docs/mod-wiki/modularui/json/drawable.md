This page is about defining drawables in json files. These are used in backgrounds on themes for example.

In all examples we will use `background`.

Drawables have a type (for example images). Each type has different properties.
For each type each property will be listed and explained.

## Empty Drawable

Type name: `empty` or `null`

Empty Drawables are special since they don't need to be an object. The can juts be `null`.

Empty Drawables don't have any properties.

Example:

Both definitions are equivalent.

```json
{
  "background1": null,
  "background2": {
    "type": "empty"
  }
}
```

## Images

Type name: `texture`

You can get an existing image with `name` or `id`. In this case all other properties will be ignored.

```json
{
  "background": {
    "type": "texture",
    "id": "vanilla_background"
  }
}
```

Or you can define a new image. An image has multiple subtypes.

1. Full image: Uses the whole image at the given path (will be stretched to fit the render size)
2. Sub image: Uses a rectangular part of an image (will be stretched to fit the render size)
3. Adaptable image: has a border and a plain center. The border is drawn separately, so it won't be stretched no matter
   how large the texture is drawn. The center should be plain because it will be stretched to fit the render size.
4. Tiled image: The image will be drawn multiples times at its original size like tiles to fit the render size. This can
   be combined with adaptable image.

For all image types you need the `location` property. It defines where the image file is. The value is a resource
location.
Check out
the [GroovyScript docs](https://groovyscript-docs.readthedocs.io/en/latest/groovyscript/rl/#converting-to-file-path) to
find out more.

### Full image

Full images only need the `location property`, which is explained above.

### Sub image

Here you want to define a rectangle inside the image. You can do that using `x`, `y`, `w`, `h`, `iw` and `ih`. All these
properties are in pixel. `w` is also `width`, `h` is also `height`, `iw` is also `imageWidth` and `ih` is
also `imageHeight`.

Or you can define a rectangle with `u0`, `v0`, `u1` and `v1`. These are relative values from 0.0 to 1.0. `u` is the
x-axis and `v` the y-axis. 0 means start and 1 means end.

!!! Warning
You can NOT mix both types!

### Adaptable image

Can be a full image or a sub image. To make an image adaptable you need `borderX` and/or `borderY` or just `border` (
sets both axis).
`borderX` is the border size on the x-axis. All properties are in pixels.

### Tiled image

Can be a full image or a sub image. Can also have border properties for adaptable image.
For tiled images you need `"tiled": true` and `imageWidth` (or `iw`) and `imageHeigth` (or `ih`).

### Example

This is an example for an adaptable sub image (we set the values to still be the full image).

```json
{
  "background": {
    "type": "texture",
    "location": "modularui:gui/background/background",
    "imageWidth": 195,
    "imageHeight": 136,
    "x": 0,
    "y": 0,
    "width": 195,
    "height": 136,
    "border": 4
  }
}
```

## Colored rectangle

Type name: `color` or `rectangle`

Properties:

- `cornerRadius` is the corner radius. Default is 0
- `cornerSegments` is the amount of segments the corner has if the corner radius is not 0. Default is 6. Lower means
  more pointy but faster drawing, higher means smoother but slower drawing.
- `color` is the color of the whole rectangle (see [colors](color.md))

We can also set colors for each corner individually. This way we can create beautiful gradients.

- `colorTop` sets the color of the top left and top right corner. `colorBottom`, `colorLeft` and `colorRight` work
  analogue
- `colorTopLeft` or `colorTL` sets the color of the top left corner. `colorTopRight`, `colorBottomLeft`
  and `colorBottomRight` work analogue

Of course, you can combine multiple colors. For example, you can set `color` and then `colorBL`. This will make all
corners
have the color of `color` except the bottom left corner which will have the `colorBL` color.

## Ellipse / Circle

Type name: `ellipse`

The ellipse will always stretch to its render size. It will only be a circle if render width and render height are the
same.

Properties:

- `color` sets the color of the whole circle
- `colorInner` sets the center color, creating a gradient
- `colorOuter` sets the outer color, creating a gradient
- `segments` is the amount of edges the circle will have. Default is 40. Lower means more pointer and, but faster
  drawing and higher means smoother and slower drawing. With low segment count you can also draw polygons

## Text

Type name: `text`

Properties:

- `text`, `string` or `key`: The text (Required)
- `lang` or `translate`: If the text is a lang key and should be translated (Default is false)
- `color`: Text color (see [color](color.md)) (Uses color of the widget theme if applicable by default)
- `shadow`: True if the text should have a shadow. (Uses shadow of the widget theme if applicable by default)
- `align` or `alignment`: The alignment of the text in the render size. (see [alignment](alignment.md)) (Default is center) 
- `scale`: The scale to draw text at. (Default is 1.0)

!!! Example
    ```json
    {
      "type": "text",
      "text": "Hello World",
      "lang": false
    }
    ```

The text can also be an array. In this case the `lang` property is ignored.
Each of the elements in the array can either be a string or an object with the `text` and `lang` property. Those objects
can also have any style property like `color` and `shadow`.

!!! Example
    ```json
    {
      "type": "text",
      "text": [
        "Hello ",
        {
          "text": "this.part.is.translated",
          "lang": true
        },
        "I18n:this.part.is.also.translated"
      ]
    }
    ```

As you can see in the example above you can also start a string with `I18n:` to make it translated.

## Item

Type name: `item`

- `item`: The format is `mod:item_id:meta` where meta is optional
- `nbt`: nbt data (untested)

!!! Example
    ```json
    {
      "background": {
        "type": "item",
        "item": "minecraft:diamond"
      }
    }
    ```

## Icon

An icon is a drawable wrapper which can draw a drawable at a fixed size.

Type name: `icon`

Properties:

- `drawable` or `icon`: The wrapped drawable. (Required)
- `width` or `w`: The width to render the drawable at.
- `height` or `h`: The height to render the drawable at.
- `autoWidth` and `autoHeight`: True if the width/height should match the render size (how normal drawables work).
- `autoSize`: True if the width and height should match the render size (how normal drawables work). (Default is true)
- `alignment` or `align`: The alignment of the drawable in the render size. (see [alignment](alignment.md)) (default is center)

You can also set margins. But they only work if the width (for left and right) or the height (for top and bottom) is set to auto.
Multiple margins can be combined.

- `margin`: The margin on all edges
- `marginHorizontal` and `marginVertical`: The margin on horizontal (left and right) or vertical (top and bottom) edges
- `marginTop`, `marginBottom`, `marginLeft`, `marginRight`; The margin of each edge

!!! Example
    ```json
    {
      "background": {
        "type": "icon",
        "drawable": {
          "type": "...",
          "...": "..."
        },
        "width": 18,
        "height": 18
      }
    }
    ```

## Drawable array

A drawable can also be multiple drawable. You can do that by using square brackets `[]` instead of curly brackets `{}`.
And inside there you just define multiple drawable objects

!!! Example
    ```json
    {
      "background": [
        {
          "type": "...",
          "...": "..."
        },
        {
          "type": "...",
          "...": "..."
        },
        {
          "type": "...",
          "...": "..."
        }
        
      ]
    }
    ```
