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

An anvil-style screen with your title. Its text box is where they type, and two slots beside it act as
buttons:

| Slot | Does |
|---|---|
| **Confirm** (green dye) | Submits the text. Shows your `check` message in red instead while the text isn't acceptable. |
| **Cancel** (barrier) | Closes without an answer |

**Escape** also cancels. Their own inventory shows below as usual, but nothing in the screen can be
taken or moved.

## Live typing

The text box reports every keystroke, so `check` runs and the confirm button updates as they type.
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
