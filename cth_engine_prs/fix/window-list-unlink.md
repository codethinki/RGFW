The window list keeps closed windows and loses open ones.

- Problem:
  - closing a window that is not the root leaves it in the list, the list then holds a freed window
  - a third window replaces the second one in the list: `root->next` always holds the newest window
  - after closing the root window, `_RGFW->cur` is `NULL`, the next `RGFW_createWindow` dereferences it
  - `RGFW_getCurrentWindow_OpenGL` and `RGFW_getCurrentWindow_EGL` skip the last window and crash without windows
- Cause:
  - `RGFW_createWindowPtr` links a new window after `_RGFW->cur`, but does not move `_RGFW->cur` to it
  - `RGFW_window_closePtr` unlinks only the root, for another window it only moves `_RGFW->cur`
  - the two loops stop at `win->next == NULL`
- Fix:
  - `_RGFW->cur` is the last window: a new window links after it and becomes the last
  - `RGFW_window_closePtr` unlinks a window from the window before it
  - the loops run until `win == NULL`
