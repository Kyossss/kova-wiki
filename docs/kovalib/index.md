# KovaLib

KovaLib draws menus on the player's screen that keep working when they use **shaderpacks** (Iris,
OptiFine). It's a free library: on its own it shows nothing to players. Plugins built on it, such as
KovaProfile, draw their menus with it.

## Why it exists

Many "custom UI" plugins place their menus in the world and use the game's shaders to move them onto
the screen. Shaderpacks replace those shaders, so for any player running one, the menu ends up far
away and never shows. KovaLib draws in the game's interface layer instead, which shaderpacks leave
alone.

## What players get

- **A free-moving cursor.** Players move it by moving the mouse, click with **left click**, and
  usually leave with **sneak** (a menu can choose to stay open).
- **The same layout on every screen.** Menus are drawn on a fixed 1920×1080 canvas, scaled to fit and
  letterboxed, so they look the same at any GUI scale and window size.
- **Menus over the world or over a backdrop.** A menu can hold the camera still on a scene, or cover
  the screen with a dark backdrop.
- **Safe while open.** While a menu takes input, the player is held where they stand and can't break,
  place, attack, drop or swap items.

## What server owners get

- **Works with or without Nexo.** If Nexo is installed, KovaLib adds its files to Nexo's resource pack.
  If not, KovaLib sends its own small pack. See [The resource pack](resource-pack.md).
- **Works alongside BetterHud.** KovaLib shares BetterHud's text shader instead of fighting it, and
  hides the BetterHud HUD while a menu is open.
- **Re-skinnable.** Every menu picture, from KovaLib and from plugins built on it, can be replaced with
  your own PNG, and you can add your own fonts. See [Customizing](customization.md).
- **Typing without chat.** Plugins can ask players to type, such as a name or a search, in a text box
  that updates as they type. See [Text input](developers/text-input.md).
- **No commands, no permissions.** Everything is in `config.yml`.

## Requirements

| | |
|---|---|
| Server software | **Paper**, or a Paper fork such as Purpur. Spigot and Folia are not supported. |
| Minecraft | **1.21.4 to 26.3**, players on any of those versions (with ViaVersion, too). A newer Minecraft version needs a KovaLib update before menus show on it. |
| Java | 21 or newer (the version your Minecraft server already needs) |
| Required | [packetevents](https://modrinth.com/plugin/packetevents) (free) |
| Optional | Nexo (carries the pack), BetterHud (shares its shader, HUD hidden under menus) |

!!! note "Tested versions"
    KovaLib has been tested in game on **Minecraft 26.1.2**, with and without Nexo. Its menus for
    1.21.4 to 26.3 are built from each version's own game files, but haven't been tested in game on
    those versions yet. If menus don't show on your version, please
    [report it](troubleshooting.md#collecting-details-for-a-bug-report).

## Limits worth knowing

- Menu text is **plain English letters, digits and punctuation** (printable ASCII). Other characters show as `?`.
- Text comes in **4 sizes** and **8 colours** (see [the developer docs](developers/screens.md#text)).
- Menu pictures are loaded at startup. Changing one needs a **restart**.
- On **1.21.5** clients the cursor moves in steps instead of gliding: that version gives the menu shader no clock to smooth it with. Everything else works the same.
- A menu is shown through the action bar, so while one is open, other plugins' action-bar messages are held back.

## Next

- [Installing KovaLib](installation.md)
- [The resource pack](resource-pack.md)
- [Configuration](configuration.md)
- [Customizing pictures and fonts](customization.md)
- [Troubleshooting](troubleshooting.md)
- [For developers](developers/getting-started.md)
