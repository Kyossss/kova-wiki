# Troubleshooting

Start with the startup log. KovaLib names anything it's missing.

## Players see random symbols instead of a menu

Their client doesn't have KovaLib's resource pack, or doesn't have it **yet**.

- They may have declined the pack. Ask them to rejoin and accept it. In the server list, edit the
  server and set **Server Resource Packs: Enabled**.
- If you don't use Nexo, check that `pack.own.port` (default **8164**) is open to players. Test it by
  opening `http://<your-server-address>:8164/kovalib/` in a browser. A "404" page means the web server
  is reachable. A timeout means the port is blocked.
- Behind a proxy, set `pack.own.address`. See [the resource pack](resource-pack.md#kovalibs-own-pack).

## The menu doesn't show at all, but nothing looks broken

- **Check the player's Minecraft version.** KovaLib supports 1.21.4 to 26.3. On a newer version, menus
  don't show until KovaLib is updated for it.
- **Look for the overlay warning.** `The pack has other text shaders in overlays...` means another plugin's
  shader is replacing KovaLib's. Most often that's a Nexo animated GIF glyph. See
  [Other plugins' shaders](resource-pack.md#other-plugins-shaders).
- **Try `action-bar-guard: false`.** If the menu appears with it off, tell the plugin developer and
  include your plugin list.
- **Check that `shader.enabled` is `true`.**

## A menu that should show the world says it needs packetevents

Menus that hold the camera over the world, and menus in spectator style, need
[packetevents](https://modrinth.com/plugin/packetevents). Install it and restart.

## The cursor is too fast, too slow, or jittery

- Speed: `cursor.sensitivity` (default `14.0`).
- Jitter: raise `cursor.smoothing-delay` towards `2`.

## A replaced picture isn't used

The log names the file and the reason, then draws the original. The usual causes are a file that isn't a
real PNG, or a large image whose height doesn't divide evenly. See [Customizing](customization.md#pictures).
Remember to restart after replacing it.

## Collecting details for a bug report

Set `debug: true`, restart, reproduce the problem, and send the log with your server version and plugin list.
Turn `debug` off afterwards, because it's noisy.
