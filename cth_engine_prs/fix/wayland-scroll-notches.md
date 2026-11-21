On Wayland, one wheel notch scrolls about 1.5 instead of 1 like on Win32.

- Problem:
  - Win32 gives `1` per wheel notch, Wayland gives the axis value / 10, about `1.5` per notch on common compositors
  - the same application scrolls faster on Wayland
- Cause:
  - RGFW binds `wl_seat` version 4 and uses only `wl_pointer.axis`, a distance in surface pixels
- Fix:
  - bind `wl_seat` up to version 8 (version 7 with older wayland headers)
  - `wl_pointer.axis_value120` gives the wheel notches (`value120 / 120`), version 5 to 7 use `axis_discrete`
  - the axis events of one `wl_pointer.frame` give one scroll event, so a diagonal scroll gives one event
  - continuous scroll (touchpad) has no notches and keeps the axis value / 10
  - without frames (version 4) every axis event is a scroll event, as before
