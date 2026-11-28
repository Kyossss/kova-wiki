# Drawing screens

A screen implements `CanvasScreen`. KovaLib calls `render(player, painter)` whenever the frame needs
drawing again:

- the cursor moved onto or off a button;
- a button flashed pressed;
- you called `canvas.refresh(player)`.

## The canvas

Positions are **canvas pixels on a 1920×1080 canvas**, from the top-left corner. The canvas is fitted
to the player's screen and letterboxed, so a layout looks the same at every GUI scale and window
size. Later calls draw over earlier ones. The cursor is always drawn last, on top.

## Art

```java
painter.art("myplugin:panel", x, y);   // top-left corner at x, y
```

Rules for `CanvasService.registerArt(key, png, height)`:

| | |
|---|---|
| Key | `namespace:path`, lowercase letters, digits and `_ - . /`. Unique. |
| Height | On the canvas, in canvas pixels. **Even**, at most **1080**. The width follows the image's shape. |
| Large images | Over 256×256 pixels, an image is tiled automatically. Its height must then be a whole multiple of the image's pixel height (for example, a 512-pixel-tall image at height 512, 1024...). |
| When | In `onEnable`. See [Getting started](getting-started.md#3-get-the-service-and-register-your-art). |

`artWidth(key)` and `artHeight(key)` give a picture's size on the canvas, for layout.

## Boxes

Plain filled boxes, in any colour, square or rounded. Use them for panels, cards, dividers and
highlights instead of drawing a picture for each one.

```java
painter.box(40, 240, 560, 600, 0xCC1E1E2E);         // dark blue-grey at 80%, square corners
painter.box(60, 260, 240, 80, 0xFF3FA9F5, 16);      // solid blue, corners rounded by 16
painter.box(60, 400, 200, 60, 0xFFFFFFFF, 30);      // a pill: radius = half the height
```

| | |
|---|---|
| Colour | `0xAARRGGBB`: alpha first, then red, green, blue. `0xFF...` is solid, `0x80...` is half see-through. Any colour, chosen at runtime: nothing goes in the resource pack. |
| Faint colours | Below about 10% (`0x1A`) the box is not drawn at all. |
| Grid | Edges snap to the nearest multiple of **4** canvas pixels (`CanvasPainter.BOX_GRID`). Place art and text on the same grid to line them up exactly. |
| Radius | Canvas pixels, snapped to 4. At most half the box's shorter side (more is reduced to that) and 512. `0` or the 5-argument form gives square corners. |
| Overlap | See-through boxes blend like glass: red at 50% over blue at 50% shows purple. A rounded box is even all over; its pieces never overlap. |
| Off the canvas | Clipped to the 1920×1080 canvas. A box with no width or height left draws nothing. |

Boxes are drawn in call order with everything else, so draw a panel first and its contents after it.

## Buttons

```java
painter.button("play", "myplugin:play", x, y);           // enabled
painter.button("play", "myplugin:play", x, y, false);    // disabled: greyed art, ignores clicks
```

- The **id** (`"play"`) is passed to `onClick`. Keep it unique within the screen.
- The clickable area is the size of the plain picture.
- **States** are separate pictures registered under suffixed keys. KovaLib picks one each frame:

  | Registered key | Shown when | Falls back to |
  |---|---|---|
  | `myplugin:play` | normally | |
  | `myplugin:play_hover` | the cursor is over it | the plain picture |
  | `myplugin:play_pressed` | for a moment after a click | hover, then plain |
  | `myplugin:play_disabled` | `enabled` is `false` | the plain picture |

A click plays a button sound and shows the pressed state before `onClick` is called.

## Text

```java
painter.text("Hello", x, y);                                   // medium, white, left-aligned, shadowed
painter.text("Title", 960, 100, new TextStyle(Size.HUGE, Colour.YELLOW, Align.CENTER));
painter.text("Note", x, y, TextStyle.of(Size.SMALL, Colour.GRAY).shadow(false));
```

`y` is the top of the line. With `Align.CENTER` or `Align.RIGHT`, `x` is the centre or right edge.

| Sizes (line height) | Colours |
|---|---|
| `SMALL` 24, `MEDIUM` 32, `LARGE` 48, `HUGE` 64 | `WHITE`, `GRAY`, `DARK_GRAY`, `BLACK`, `RED`, `GREEN`, `YELLOW`, `AQUA` |

- **Printable ASCII only.** Anything else is drawn as `?`. A line wider than 4095 canvas pixels is cut short.
- **Shadow** is on by default, and keeps light text readable over the world. Black text never gets one.
- **Fonts:** `TextStyle.font("name")` uses a font the server owner added (see
  [Customizing](../customization.md#fonts)). Check `canvas.hasFont(name)` first, because a missing
  font draws in `default`. `canvas.fonts()` lists every font.
- **Measuring:** `painter.textWidth(text, style)` gives the width in canvas pixels, for laying things out around text.

## Input

| Player does | Your screen hears |
|---|---|
| Moves the mouse | Nothing. KovaLib moves the cursor and re-renders on hover changes. |
| Left-clicks an enabled button | `onClick(player, buttonId)` |
| Sneaks | `onSneak(player)`. Return `true` (the default) to close, `false` to stay open. |

Keep the default sneak-to-close unless the menu *must* stay up, such as a forced choice on join. It's
the player's way out if your screen ever fails to draw.

## Closing

`onClose(player, reason)` is called whenever the canvas closes, whatever the reason. The player may already be offline.

| `CloseReason` | When |
|---|---|
| `SNEAK` | The player sneaked and `onSneak` returned true |
| `PLUGIN` | `canvas.close(player)` was called |
| `REPLACED` | Another canvas was opened for the player |
| `QUIT`, `DEATH`, `WORLD_CHANGE` | The player left, died or changed world |
| `SHUTDOWN` | KovaLib is disabling |

If `render` throws, the canvas is closed and the error logged. If `onClick` throws, the error is
logged and the canvas stays open.
