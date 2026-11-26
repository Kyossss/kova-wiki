# API reference

Package `dev.kova.lib.api` unless noted. Every method is for the **main thread**.

## `CanvasService`

Get it with `getServer().getServicesManager().load(CanvasService.class)`.

| Method | What it does |
|---|---|
| `registerArt(String key, byte[] png, int height)` | Add a picture. In `onEnable` only. Throws `IllegalArgumentException` for a bad key or image, or a key that's taken. |
| `hasArt(String key)` | Whether a picture is registered |
| `artWidth(String key)`, `artHeight(String key)` | Its size on the canvas |
| `textWidth(String text, TextStyle.Size size)` | Width of text in the default font |
| `textWidth(String text, TextStyle style)` | Width in the style's size and font |
| `hasFont(String name)`, `fonts()` | The server's fonts; `default` is always first |
| `isReady(Player)`, `packState(Player)` | Whether canvases show for the player. See [readiness](readiness.md). |
| `open(Player, CanvasScreen, CanvasOptions)` | Open a screen, replacing any open canvas |
| `close(Player)` | Close it; the screen hears `CloseReason.PLUGIN` |
| `isOpen(Player)`, `screen(Player)` | Whether a canvas is open, and which screen |
| `refresh(Player)` | Draw the player's screen again on the next tick |
| `fadeToBlack(Player, int ticks)` | Fade the screen to black and hold it (at most `FADE_HOLD_TICKS`, 200) |
| `fadeFromBlack(Player, int ticks)` | Clear a fade |
| `ask(Player, TextInput, TextInputHandler)` | Open a text box. See [Text input](text-input.md). |
| `isAsking(Player)`, `cancelAsk(Player)` | Whether a text box is open; close it (the handler hears `onCancel`) |

## `CanvasScreen`

| Method | Called when |
|---|---|
| `render(Player, CanvasPainter)` | The frame needs drawing. Draw everything. |
| `onClick(Player, String buttonId)` | An enabled button was clicked |
| `onSneak(Player)` *(default: return true)* | The player sneaked; return true to close |
| `onClose(Player, CloseReason)` *(default: nothing)* | The canvas closed, for any reason |

## `CanvasPainter`

Constants `WIDTH = 1920`, `HEIGHT = 1080`. Every draw method returns the painter, so calls chain.

| Method | |
|---|---|
| `art(String key, int x, int y)` | Draw a picture, top-left at x, y |
| `button(String id, String artKey, int x, int y[, boolean enabled])` | Draw a clickable button |
| `text(String text, int x, int y[, TextStyle style])` | One line of text, top of the line at y |
| `textWidth(String text, TextStyle.Size size)`, `textWidth(String, TextStyle)` | Measure text |

## `CanvasOptions`

Record `(boolean backdrop, Location viewFrom, boolean hideHud, Mode mode, Input input)`.

| Factory | |
|---|---|
| `overBackdrop()` | Over the backdrop, view left alone |
| `overWorld(Location eye)` | Over the world, camera held at `eye` |
| `overlay()` | Shown over gameplay, no input, HUD left up |

Copy-with methods: `hideHud(boolean)`, `mode(Mode)`, `input(Input)`. `takesInput()` is true for `Input.CURSOR`.
Enums: `Mode { ADVENTURE, SPECTATOR }`, `Input { CURSOR, NONE }`. See [Display options](options.md).

## `TextStyle`

Record `(Size size, Colour colour, Align align, boolean shadow, String font)`.

| | |
|---|---|
| `TextStyle.DEFAULT` | Medium, white, left, shadowed, `default` font |
| `TextStyle.of(Size, Colour)` | Left-aligned, shadowed |
| `new TextStyle(Size, Colour, Align)` | Shadowed, default font |
| `align(Align)`, `shadow(boolean)`, `font(String)` | Copies with one field changed |

`Size { SMALL(24), MEDIUM(32), LARGE(48), HUGE(64) }` with `pixels()`;
`Colour { WHITE, GRAY, DARK_GRAY, BLACK, RED, GREEN, YELLOW, AQUA }`; `Align { LEFT, CENTER, RIGHT }`.

## `TextInput` and `TextInputHandler`

Record `TextInput(String title, String initial, int maxLength)`: `TextInput.of(title)`, then `initial(String)`
and `maxLength(int)` (1 to `CLIENT_LIMIT`, 50).

| `TextInputHandler` method | Called |
|---|---|
| `check(Player, String)` *(default: null)* | On every keystroke; return null if the text can be submitted, else a short reason |
| `onType(Player, String)` *(default: nothing)* | On every keystroke |
| `onSubmit(Player, String)` | The player confirmed valid text |
| `onCancel(Player)` *(default: nothing)* | Closed without an answer |

## `CloseReason`

`SNEAK`, `PLUGIN`, `REPLACED`, `QUIT`, `DEATH`, `WORLD_CHANGE`, `SHUTDOWN`. See [Closing](screens.md#closing).

## `PackState`

`WAITING`, `READY`, `DECLINED`, `FAILED`; `unavailable()`. See [Readiness](readiness.md).

## Events, `dev.kova.lib.api.event`

| Event | Extra |
|---|---|
| `CanvasReadyEvent` | |
| `CanvasUnavailableEvent` | `state()`: `DECLINED` or `FAILED` |
