# Knowing when menus can show

Menus are drawn with a font from KovaLib's resource pack. Until a player's client has loaded that pack,
a canvas shows as stray characters. KovaLib tracks this for every player.

## Events

| Event | Fired |
|---|---|
| `CanvasReadyEvent` | Once per session, when the player's pack has loaded. Always at least one tick after they joined, so it's the right place to open a join screen. |
| `CanvasUnavailableEvent` | At most once per session, when canvases won't show for this player: they declined the pack, or it failed. `event.state()` says which. |

Both are normal Bukkit events, fired on the main thread.

```java
@EventHandler
public void onReady(CanvasReadyEvent event) {
    canvas.open(event.getPlayer(), new JoinScreen(canvas), CanvasOptions.overBackdrop());
}

@EventHandler
public void onUnavailable(CanvasUnavailableEvent event) {
    if (event.state() == PackState.DECLINED) {
        // e.g. open a chest-GUI fallback instead
    }
}
```

## Checking at any time

```java
if (canvas.isReady(player)) { ... }
PackState state = canvas.packState(player);   // WAITING, READY, DECLINED or FAILED
```

| `PackState` | Meaning |
|---|---|
| `WAITING` | Joined, pack not loaded yet (also returned for a player who isn't online) |
| `READY` | Canvases show |
| `DECLINED` | They declined the pack. Canvases won't show this session. |
| `FAILED` | The pack couldn't be delivered or loaded. Canvases won't show this session. |

`state.unavailable()` is `true` for `DECLINED` and `FAILED`.

## How KovaLib knows

Depending on how the pack is delivered (see [The resource pack](../resource-pack.md)):

- **KovaLib's own pack** is sent after join, and its status is reported by the client.
- **Nexo, or a pack merged into the server's own pack,** is usually sent *before* the player joins.
  Plugins can't hear that pack's status, so a player who gets through joining is counted as ready,
  unless the server recorded a failure for them.
- **Nexo set to send after join** is judged by the pack status the client reports.

`canvas.open` works before a player is ready, but there's no reason to call it then.
