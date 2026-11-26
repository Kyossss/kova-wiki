# Configuration

`plugins/KovaLib/config.yml`. Every setting is read at startup, so **restart after changing one**.
A setting missing from your file uses its default.

## `pack`

How the menu font and shader reach players. See [The resource pack](resource-pack.md) for the full picture.

| Key | Default | What it does |
|---|---|---|
| `pack.mode` | `auto` | `auto`, `nexo`, `own` or `merged`. `auto` uses Nexo when it's installed, otherwise KovaLib's own pack. |
| `pack.own.port` | `8164` | The port KovaLib's built-in web server hosts the pack on. Must be open to players. Ignored when `url` is set. |
| `pack.own.address` | `''` | The address used in the download link. Blank: the address each player typed to join (needs packetevents), else `server-ip`. Set it when behind a proxy. |
| `pack.own.url` | `''` | A direct download link to `kovalib-pack.zip` you host yourself. When set, KovaLib doesn't host the file. |
| `pack.own.prompt` | `''` | Text on the client's pack prompt. Blank uses the client's default. |
| `pack.own.required` | `false` | `true` disconnects players who decline the pack. |

## `shader`

| Key | Default | What it does |
|---|---|---|
| `shader.enabled` | `true` | Whether KovaLib's shader goes into the pack. Turned off, menus are drawn off-screen and nobody sees them. Only useful for ruling KovaLib out while troubleshooting. |

## `cursor`

| Key | Default | What it does |
|---|---|---|
| `cursor.sensitivity` | `14.0` | How far the cursor moves (in canvas pixels, out of 1920×1080) per degree the mouse turns. Higher is faster. |
| `cursor.smoothing-delay` | `1.5` | How smoothly the cursor glides, in ticks. `0` turns smoothing off; `2` is the smoothest. Above 2 gets worse. Players on 1.21.5 never get smoothing (see [Limits](index.md#limits-worth-knowing)). |

## Other settings

| Key | Default | What it does |
|---|---|---|
| `action-bar-guard` | `true` | While a menu is open, hold back other plugins' action-bar messages, which would otherwise replace the menu for up to a second. Needs packetevents. If a menu ever fails to show at all, try turning this off first. |
| `hide-betterhud` | `true` | Hide the player's BetterHud HUD while a menu is open, and bring it back after. A player who turned their own HUD off keeps it off. |
| `debug` | `false` | Log every click, what each player's client sends while a menu is open, and each step of their resource pack loading. Noisy; for troubleshooting only. |
| `test-screen` | `off` | Show KovaLib's test menu. See below. |

### `test-screen`

Shows KovaLib's built-in test menu, so you can check menus work on your server without any other
plugin. Every player gets it once their resource pack has loaded, and can reopen it with the
swap-hands key (**F**).

| Value | The menu appears |
|---|---|
| `off` | Never (normal use) |
| `backdrop` | Over a dark backdrop |
| `world` | Over the world, with the camera held still (needs packetevents) |
| `spectator` | Over the world with no hotbar, hand or crosshair (needs packetevents) |

**Turn it off before players join for real.** It takes over the F key for everyone.
