# Text input

Ask a player to type something, such as a name or a search, without chat and without mods.

```java
canvas.ask(player, TextInput.of("Name your profile").maxLength(16), new TextInputHandler() {

    @Override
    public String check(Player player, String text) {
        if (text.strip().length() < 3) {
            return "At least 3 characters";
        }
        return null; // fine
    }

    @Override
    public void onSubmit(Player player, String text) {
        createProfile(player, text.strip());
    }

    @Override
    public void onCancel(Player player) {
        // closed without an answer
    }
});
```

## What the player sees

**KovaLib's text box:** a panel with your title, a field showing what they type, and your `check`
message underneath (in red, or "Ready" in green). It's the same size at every GUI scale.

| To | The player |
|---|---|
| **Confirm** | Clicks anywhere. Ignored while your `check` returns a reason. |
| **Cancel** | Presses **Escape** |

They use their normal mouse pointer while the box is open. The world stays visible behind it, without
darkening, and their inventory isn't shown.

The box's pictures can be replaced like any other (`plugins/KovaLib/textures/kovalib/input_*.png`; see
[Customizing](../customization.md)).

**A plain anvil instead,** with your title, the text field, and two slots as the buttons (Confirm is
green dye, Cancel is a barrier), for:

- players on **1.21.5** (its client can't hide the anvil);
- players whose resource pack hasn't loaded yet;
- everyone, when the server owner turns `text-input.hide-anvil` off.

Your code is the same either way: the same handler, called the same way. Nothing in either screen can
be taken or moved.

??? note "How it works"
    Both are an anvil screen that exists only on the player's client: its rename field is the one
    vanilla text box that reports every keystroke. For the text box, KovaLib's resource pack hides that
    anvil while the box is open, and the canvas draws the box in its place. The anvil is hidden for
    that one player, and only while they type, so real anvils look normal.

## Live typing

The text box reports every keystroke, so `check` runs and its message updates as they type.
`onType` is called on every keystroke too, which is useful for a live search:

```java
@Override
public void onType(Player player, String text) {
    results.put(player.getUniqueId(), search(text));
}
```

## Good to know

- **It only exists on the player's screen.** There's no real anvil on the server: no inventory, no
  items, nothing to take or duplicate, and plugins that act on anvils never see it.
- **A canvas closes while the box is open.** `ask` closes the player's canvas
  (`CloseReason.REPLACED`). To continue the menu afterwards, open it again from `onSubmit` and
  `onCancel`.
- **`onCancel` covers every way out:** the cancel button, Escape, another screen opening, death or
  quitting. The player may be offline, so check `player.isOnline()` before reopening anything.
- **Length.** `maxLength` is 1 to 50 (the client's own limit for the box). Longer text is refused before
  `check` is asked.
- Text arrives without control characters. Anything else the player typed is kept as is.
- Asking again while a box is open replaces it, and the old box's handler hears `onCancel`.
- `isAsking(player)` and `cancelAsk(player)` check and close a box from outside.
