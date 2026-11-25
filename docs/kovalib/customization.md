# Customizing pictures and fonts

## Pictures

Every picture a menu uses, from KovaLib and from every plugin built on it, is copied to
`plugins/KovaLib/textures/` the first time the server starts. Each one sits in a folder named after the
plugin that owns it:

```
plugins/KovaLib/textures/
  kovalib/          KovaLib's own: cursor.png, backdrop.png, the test menu's pictures
  kovaprofile/      KovaProfile's menu pictures (when installed)
```

**To change a picture,** replace its file with your own PNG, keeping the file name, and **restart**.
KovaLib reads pictures only at startup, so `/nexo reload pack` alone resends the old ones.
**To get the original back,** delete the file and restart.

Rules for a replacement:

- **It must be a PNG.** A file KovaLib can't use is reported in the log, and the original is drawn instead,
  so a bad file never blanks a menu.
- **The height on screen stays the same.** Each picture has a fixed height on the 1920×1080 canvas; your
  image is scaled to it. The width follows your image's shape, so keep the same proportions to keep the
  same layout.
- **Big pictures:** an image wider or taller than 256 pixels is cut into tiles. Its height on screen must
  then be an exact multiple of the image's height. If a replacement is rejected for this, make the
  image's height divide the original's on-screen height.
- **Buttons have states,** each its own file: `name.png` (normal), `name_hover.png`, `name_pressed.png`
  and `name_disabled.png`. Only the files already in the folder are used. Adding a state the plugin
  doesn't have, such as a `_hover` file for a button without one, does nothing.

KovaLib never overwrites a file you've put there, even when it updates.

## Fonts

Menu text uses KovaLib's built-in lettering, called `default`. To add your own:

1. Put a `.ttf` or `.otf` file in `plugins/KovaLib/fonts/`, for example `fonts/Pixel Art.ttf`.
2. Restart. The log says `Font 'pixel_art' loaded`.
3. Use that name (the file name in lowercase, with spaces and symbols turned into `_`) wherever a plugin
   offers a font setting.

Up to **13 fonts** besides `default`. Only plain English letters, digits and punctuation are drawn;
a character your font lacks shows blank.

### Tuning a font

A `.yml` file with the same name, next to the font, adjusts how it's set. Every value is a fraction of
the line height:

```yaml
# fonts/Pixel Art.yml
scale: 0.72       # the size of the letters
baseline: 0.76    # where the baseline sits, from the top of the line
tracking: 0.035   # the gap after each letter
space: 0.24       # how wide a space is
antialias: true   # false for pixel fonts, to keep them crisp
```

These are the defaults. Fonts are read at startup, so restart after any change.
