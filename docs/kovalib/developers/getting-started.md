# Getting started for developers

KovaLib draws cursor-driven menus in the GUI layer, where shaderpacks can't move them. Your plugin
describes a screen; KovaLib runs the cursor, clicks, frames and resource pack.

## 1. Add the API

The API is `dev.kova:kovalib-api` (packages `dev.kova.lib.api` and `dev.kova.lib.api.event`). Use
`provided` / `compileOnly` scope: the KovaLib plugin supplies it at runtime, so never shade it into
your jar.

> **Maven repository: coming with the first public release.** Until then, compile against the KovaLib
> jar itself as a local `compileOnly` / `system` dependency.

```xml
<dependency>
    <groupId>dev.kova</groupId>
    <artifactId>kovalib-api</artifactId>
    <version>0.3.0</version>
    <scope>provided</scope>
</dependency>
```

Target **Java 21** and compile against the Paper API of the oldest version you support. KovaLib
supports Paper 1.21.4 and newer.

## 2. Depend on KovaLib

In your `plugin.yml`, so KovaLib enables before you:

```yaml
depend: [KovaLib]        # or softdepend, if menus are optional for you
```

## 3. Get the service and register your art

Pictures are glyphs in a font, and the font reaches players inside the resource pack. That pack is
built on the first tick after every plugin has enabled, so **register all art in `onEnable`**. Art
registered later isn't in the pack, and players see nothing for it until the pack is rebuilt.

```java
public final class MyPlugin extends JavaPlugin {

    private CanvasService canvas;

    @Override
    public void onEnable() {
        canvas = getServer().getServicesManager().load(CanvasService.class);
        if (canvas == null) {
            getLogger().severe("KovaLib is missing");
            return;
        }
        // Key: namespace:path, lowercase. Height: on the 1920x1080 canvas, even.
        canvas.registerArt("myplugin:panel", readPng("menu/panel.png"), 600);
        canvas.registerArt("myplugin:ok", readPng("menu/ok.png"), 80);
        canvas.registerArt("myplugin:ok_hover", readPng("menu/ok_hover.png"), 80);

        getServer().getPluginManager().registerEvents(new JoinMenu(canvas), this);
    }

    private byte[] readPng(String path) {
        try (InputStream in = getResource(path)) {
            return in.readAllBytes();
        } catch (IOException | NullPointerException e) {
            throw new IllegalStateException("missing " + path, e);
        }
    }
}
```

Server owners can replace any picture you register, by putting a file at
`plugins/KovaLib/textures/<namespace>/<path>.png`. Use your plugin's name as the namespace.

## 4. Write a screen

```java
final class WelcomeScreen implements CanvasScreen {

    private final CanvasService canvas;
    private int clicks;

    WelcomeScreen(CanvasService canvas) {
        this.canvas = canvas;
    }

    @Override
    public void render(Player player, CanvasPainter painter) {
        painter.art("myplugin:panel", 660, 240)
               .text("Welcome, " + player.getName(), 960, 300,
                       new TextStyle(TextStyle.Size.LARGE, TextStyle.Colour.WHITE, TextStyle.Align.CENTER))
               .text("Clicked " + clicks + " times", 960, 400, TextStyle.of(TextStyle.Size.SMALL, TextStyle.Colour.GRAY)
                       .align(TextStyle.Align.CENTER))
               .button("ok", "myplugin:ok", 860, 700);
    }

    @Override
    public void onClick(Player player, String buttonId) {
        if (buttonId.equals("ok")) {
            clicks++;
            canvas.refresh(player); // draw again with the new count
        }
    }
}
```

`render` must draw the **whole** screen every time, from the screen's own state. Nothing from the last
frame is kept. See [Drawing screens](screens.md).

## 5. Open it once the player is ready

A canvas opened before the player's resource pack has loaded shows as stray characters. Wait for
`CanvasReadyEvent`:

```java
final class JoinMenu implements Listener {

    private final CanvasService canvas;

    JoinMenu(CanvasService canvas) {
        this.canvas = canvas;
    }

    @EventHandler
    public void onReady(CanvasReadyEvent event) {
        canvas.open(event.getPlayer(), new WelcomeScreen(canvas), CanvasOptions.overBackdrop());
    }

    @EventHandler
    public void onUnavailable(CanvasUnavailableEvent event) {
        // They declined the pack, or it failed. Offer a fallback, such as a chest menu.
        event.getPlayer().sendMessage("Accept the server resource pack to see this menu.");
    }
}
```

See [Knowing when menus can show](readiness.md).

## Threading

Call every `CanvasService` method on the **main thread**. KovaLib calls your screen on the main
thread too.

## Next

- [Drawing screens](screens.md): art, buttons, text, layout
- [Display options](options.md): backdrop, over the world, spectator, overlay, fades
- [Knowing when menus can show](readiness.md)
- [API reference](reference.md)
