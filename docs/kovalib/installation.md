# Installing KovaLib

1. **Check the requirements.** You need a Paper (or Purpur) server on Minecraft 1.21.4 to 26.3.
   See [Requirements](index.md#requirements).
2. **Install packetevents** (required, free). Download it from
   [Modrinth](https://modrinth.com/plugin/packetevents) and put the jar in `plugins/`. KovaLib won't
   start without it.
3. **Put `KovaLib-<version>.jar` in `plugins/`**, along with the plugin that uses it (for example
   KovaProfile).
4. **Start the server.** KovaLib creates `plugins/KovaLib/` with:

   | File or folder | What it is |
   |---|---|
   | `config.yml` | Settings. See [Configuration](configuration.md). |
   | `textures/` | Every menu picture, ready to replace. See [Customizing](customization.md). |
   | `fonts/` | Drop `.ttf` / `.otf` fonts here. See [Customizing](customization.md#fonts). |
   | `pack/kovalib-pack.zip` | KovaLib's resource-pack files, for owners who host or merge them themselves. |

5. **Read the startup log.** KovaLib says how its pack reaches players, for example
   `Pack: KovaLib's files go into Nexo's pack.` or `Hosting KovaLib's pack on port 8164`.
   It also warns if anything is missing.
6. **If you don't use Nexo,** make sure port **8164** (TCP) is open to players, the same way your game
   port is. That's where they download KovaLib's pack. Your host's panel usually calls this
   "additional ports" or "allocations". If you can't open a port, see
   [Hosting the pack yourself](resource-pack.md#hosting-the-pack-yourself).

## Checking it works

Set `test-screen: backdrop` in `config.yml` and restart. Every player who joins, once their
resource pack has loaded, gets KovaLib's test menu. They can reopen it with the swap-hands key
(**F**). If players see the menu and the cursor moves, everything is working. Set it back to
`test-screen: off` afterwards. See [Configuration](configuration.md#test-screen).

## Updating

Replace the jar and restart. Your `config.yml`, `textures/` and `fonts/` are kept: KovaLib never
overwrites a file you have. If you host or merge the pack yourself, re-upload or re-merge
`pack/kovalib-pack.zip` after every update.

When a new Minecraft version comes out, menus won't show for players on it until KovaLib is updated
for it. Nothing breaks in the meantime; the menu simply doesn't appear.
