On Wayland, a key repeat sends only a keyChar event, no key press.

- Problem:
  - a held key gives repeated `RGFW_keyChar` events, but no `RGFW_keyPressed` with `repeat = RGFW_TRUE`
  - Win32 and X11 send the repeated key press, so code that reads key events works differently on Wayland
  - after the window loses the keyboard focus with a held key, the repeat continues, `RGFW_pollEvents` then uses a `NULL` window
- Cause:
  - the repeat in `RGFW_pollEvents` calls only `RGFW_wl_send_key_event`, which sends the character
  - the keyboard leave handler clears `kbOwner`, but not `last_key`
- Fix:
  - the repeat sends the key press with `repeat = RGFW_TRUE`, then the character
  - the keyboard leave handler stops the repeat: no release reaches the window after the leave
