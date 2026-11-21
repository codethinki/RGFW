Some keys have no keycode mapping: Escape, Backspace, Tab and Enter in `RGFW_rgfwToApiKey`, and the ISO key on X11 and Wayland.

- Problem:
  - `RGFW_rgfwToApiKey` returns `0` for `RGFW_keyEscape`, `RGFW_keyBackSpace`, `RGFW_keyTab` and `RGFW_keyReturn`
  - so `RGFW_physicalToMappedKey` returns `RGFW_keyNULL` for these keys on X11 and Wayland
  - the ISO key between the left Shift and Z (`<>` on German keyboards) gives `RGFW_keyNULL` on X11 and Wayland, Win32 gives `RGFW_keyWorld1`
- Cause:
  - `RGFW_initKeycodes` fills the reverse table only from `RGFW_keySpace` up, these four keys have smaller values
  - the X11 and Wayland keycode table has no entry for keycode 94 (evdev `KEY_102ND`)
- Fix:
  - the reverse table starts at `RGFW_keyNULL + 1`
  - keycode 94 maps to `RGFW_keyWorld1`, like scan code `0x56` on Win32
