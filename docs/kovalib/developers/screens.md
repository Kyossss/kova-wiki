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
