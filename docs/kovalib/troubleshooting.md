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

## KovaLib doesn't start: "Unknown dependency packetevents"

KovaLib needs [packetevents](https://modrinth.com/plugin/packetevents). Install it and restart.

## The text box doesn't open

The startup log says `Text input ready` when typing works. If it says `Text input is unavailable`
instead, update packetevents and KovaLib, then send the log with your server version.

## Players get a plain anvil instead of the text box

That's expected for players on **1.21.5**, for players whose resource pack hasn't finished loading, and
for everyone when `text-input.hide-anvil` or `shader.enabled` is off. Otherwise, another plugin's
resource pack may be replacing the same GUI shaders (`gui` and `position_tex_color`): only one pack's
copy can be used. Plugins that redraw the whole inventory screen often ship these.

## Parts of an anvil show through the text box

Something replaced KovaLib's GUI shaders for that player's version: see the previous answer.

## The cursor is too fast, too slow, or jittery

- Speed: `cursor.sensitivity` (default `14.0`).
- Jitter or stutter: raise `cursor.smoothing-delay` (default `1.0`) a little, to `1.25` or `1.5`. Players
  with a laggy connection need more.
- Still wrong: set `cursor.instant: false` and `cursor.smoothing-delay: 1.5`, the older way of moving
  the cursor, and please [report it](#collecting-details-for-a-bug-report).

## A replaced picture isn't used

The log names the file and the reason, then draws the original. The usual causes are a file that isn't a
real PNG, or a large image whose height doesn't divide evenly. See [Customizing](customization.md#pictures).
Remember to restart after replacing it.

## Collecting details for a bug report

Set `debug: true`, restart, reproduce the problem, and send the log with your server version and plugin list.
Turn `debug` off afterwards, because it's noisy.
