Adds `RGFW_physicalToKeyChar`: the character of a physical key in the current keyboard layout.

- Problem:
  - an application that shows key bindings needs the character of a key in the layout of the user
  - `RGFW_physicalToMappedKey` returns an `RGFW_key`, so keys outside ASCII (`ß`, `ö`, dead keys) have no character
- Changes:
  - `u32 RGFW_physicalToKeyChar(RGFW_key)`: the codepoint of the key without modifiers, `0` if the key gives none
  - Wayland: the keysym at level 0 of the effective xkb layout, then `xkb_keysym_to_utf32`
  - Win32: `ToUnicodeEx` with an empty key state and flag `0x4`, which keeps the keyboard state
  - the result is the same on every platform:
    - letters are lowercase
    - a dead key gives its spacing character, for example `^`, `´`, `~`: Win32 gives it, the Wayland code maps the dead keysyms
    - Enter, Tab, Backspace, Escape and Delete give `\r`, `\t`, `\b`, `\x1B` and `\x7F`: Win32 gives nothing for Delete, so the function returns `\x7F`
    - keypad keys give no character, NumLock is a modifier: only `/`, `*`, `-`, `+` and Enter give one
- Open:
  - Escape, Backspace, Tab and Enter need the reverse keycode table of the keycode mapping fix
  - X11, macOS and WASM return `0` for now
  - custom backends must define the new function
