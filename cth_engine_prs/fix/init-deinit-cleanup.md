`RGFW_init` keeps a half initialized context after a failed library load, and `RGFW_deinit` never unloads Vulkan.

- Problem:
  - `RGFW_init` returns `-1` when loading OpenGL, Vulkan or EGL fails, but the context stays set and the platform stays initialized
  - the next `RGFW_init` returns `1` (already initialized), the context has no library
  - every init and deinit loads the Vulkan library one more time and never closes it
- Cause:
  - the three library failure paths in `RGFW_init_ptr` return without a cleanup, the platform failure path has one
  - `RGFW_deinit_ptr` unloads EGL and OpenGL, but not Vulkan
- Fix:
  - the library failure paths call `RGFW_deinit_ptr`
  - `RGFW_deinit_ptr` calls `RGFW_unloadVulkan`
  - `RGFW_unloadVulkan` also clears `vkGetInstanceProcAddress` and the loaded surface functions
