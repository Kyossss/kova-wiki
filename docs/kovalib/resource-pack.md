# The resource pack

KovaLib's menus are made of a custom font and a small text shader. Players need both, which means they
need a resource pack. KovaLib can deliver it in three ways, chosen by `pack.mode` in `config.yml`.

| `pack.mode` | Who sends the pack | Use it when |
|---|---|---|
| `auto` *(default)* | Nexo if it's installed, otherwise KovaLib | Almost always |
| `nexo` | Nexo: KovaLib adds its files to Nexo's pack | You run Nexo |
| `own` | KovaLib sends its own small pack | You don't run Nexo |
| `merged` | You: KovaLib's files are merged into your server's own pack | You already send a pack and want one download |

## With Nexo

Nothing to set up. Each time Nexo builds its pack, KovaLib adds its files, and Nexo sends the pack as
usual. After changing KovaLib's pictures or fonts, **restart**. KovaLib reads them only at startup,
so `/nexo reload pack` alone resends the old ones.

If Nexo is set to send the pack before players join (`Pack.dispatch.send_pre_join`, Nexo's default),
menus can open as soon as a player is in the world.

## KovaLib's own pack

Without Nexo, KovaLib sends players its own small pack (about 120 KB). It's sent **on top of** any
pack your server already sends, so your existing pack is never replaced. The client shows its usual
prompt when the player joins.

KovaLib hosts the file itself on a small built-in web server:

```yaml
pack:
  mode: auto        # or own
  own:
    port: 8164      # must be open to players, like your game port
    address: ''     # blank = automatic, see below
    url: ''         # set this to host the pack yourself instead
    prompt: ''      # text on the client's pack prompt
    required: false # true = kick players who decline
```

**The address in the download link.** By default KovaLib uses the address each player typed to join
(this needs packetevents), or your `server-ip` from `server.properties`. Set `pack.own.address` to your
server's address if:

- you're **behind a proxy** (Velocity, BungeeCord). The address players type is the proxy's, not this
  server's, so set it to an address that reaches *this* server's port 8164.
- you don't have packetevents and `server-ip` is blank.

The pack is built the same way every time, so players who come back don't download it again unless
KovaLib or your pictures changed.

### Hosting the pack yourself

If you can't open a port, upload `plugins/KovaLib/pack/kovalib-pack.zip` to any host that gives a
**direct download link** (a link that downloads the zip itself, not a web page). Put the link in
`pack.own.url`. KovaLib then sends that link instead of hosting the file.

After **every KovaLib update** (and after changing pictures or fonts), upload the new zip again.
Clients check the file against the one KovaLib expects, and refuse an outdated upload.

## Merging into your own pack

If you already send a pack through `resource-pack` in `server.properties`, you can put KovaLib's files
into it, so players download one pack:

1. Set `pack.mode: merged` and restart.
2. Copy everything from `plugins/KovaLib/pack/kovalib-pack.zip` **except `pack.mcmeta`** into your pack.
   That includes the `kovalib_*` folders: KovaLib's shader for each Minecraft version lives in one of them.
3. Open the zip's `pack.mcmeta` and copy every entry of its `"overlays"` list into the `"overlays"` list
   of your own `pack.mcmeta` (create the list if you have none). Without these entries the shaders are
   never used, and menus don't show.
4. Re-merge after every KovaLib update.

If your pack already has its own `assets/minecraft/shaders/core/rendertype_text.vsh` or `text.vsh`,
those clash with KovaLib's. A pack can only use one of each per Minecraft version.

## Players who decline the pack

With `required: false` (the default), a player who declines can keep playing, but menus won't show for
them. KovaLib tells the plugins built on it, and each one decides what to do. A plugin might show a
normal chest menu instead, or ask the player to accept the pack.

With `required: true`, players who decline are disconnected by the game client, as with any required
server pack.

## Other plugins' shaders

A pack can only contain one copy of each text shader, so other plugins' shaders can get in the way:

- **BetterHud.** Handled. KovaLib adds its part to BetterHud's shader instead of shipping its own.
- **Nexo animated GIF glyphs.** If a Nexo glyph is a shader-animated GIF (a `gif:` glyph with a
  `height` or `ascent`), Nexo adds its own text shader, which beats KovaLib's and hides the menus.
  KovaLib warns about this in the startup log once Nexo has built its pack. To fix it, give the glyph
  no `height` or `ascent`, or set it to `type: SPRITE`.
- **Other plugins that ship a text shader** in a pack overlay can do the same. They're named in the
  same warning.
