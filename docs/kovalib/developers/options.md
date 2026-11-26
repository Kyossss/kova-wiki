# Display options

`canvas.open(player, screen, options)` takes a `CanvasOptions`, which says how the canvas is shown.
Opening a canvas replaces any the player already had open.

## The four styles

```java
CanvasOptions.overBackdrop()                          // over a dark backdrop, the view left alone
CanvasOptions.overWorld(eye)                          // over the world, the camera held at eye
CanvasOptions.overWorld(eye).mode(Mode.SPECTATOR)     // the same, with no hotbar, hand or crosshair
CanvasOptions.overlay()                               // drawn over gameplay, takes no input
```

| | Backdrop | Over the world | Spectator | Overlay |
|---|---|---|---|---|
| World visible | No | Yes | Yes | Yes |
| Camera | Free (hidden) | Held at `eye` | Held | Free, player keeps playing |
| Cursor and clicks | Yes | Yes | Yes | No |
| Player held still | Yes | Yes | Yes | No |
| Vanilla HUD | Shown | Shown | Hidden | Shown |

### Over the world

`eye` is where the camera sits and which way it looks: the location's yaw and pitch. **Keep it near the
player**, because their client only has the world around them loaded. The player stays where they
are, invisible to themselves. Other players see them standing still.

### Spectator

Only the player's own client is told they're in spectator. Their real game mode never changes, so
nothing needs undoing after a crash, and other players see them as before. Spectator always holds the
view (at the player's own eyes, level, if you pass no `eye`).

### Overlay

For dialogue lines, subtitles and notices: drawn over the world while the player walks and plays on.
`onClick` and `onSneak` are never called. If you want a click to continue, listen for it yourself. An
overlay can't hold the view.

## Fine-tuning

```java
CanvasOptions.overWorld(eye).hideHud(false)                   // keep the BetterHud HUD up
new CanvasOptions(true, eye, true, Mode.ADVENTURE, Input.CURSOR) // every field
```

| Field | Meaning |
|---|---|
| `backdrop` | Draw KovaLib's full-screen backdrop behind the screen |
| `viewFrom` | Hold the camera here, or `null` to leave the view alone |
| `hideHud` | Hide the BetterHud HUD while open (if BetterHud is installed and the owner allows it) |
| `mode` | `ADVENTURE` or `SPECTATOR`, as told to the player's own client only |
| `input` | `CURSOR` (a menu) or `NONE` (only shown) |

## Fades

```java
canvas.fadeToBlack(player, 10);    // fade the whole screen to black over 10 ticks, and hold it
// ... teleport, switch worlds, load the player's data ...
canvas.fadeFromBlack(player, 10);  // clear it over 10 ticks
```

The fade covers everything, menus included, and works under shaderpacks and at every GUI scale. It
clears by itself after `CanvasService.FADE_HOLD_TICKS` (200 ticks), so a caller that fails half-way
can't leave anyone blind. It's sent as a title, so a title another plugin sends meanwhile replaces it.
Only call `fadeFromBlack` on a faded screen.
