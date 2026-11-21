The Vulkan part of `RGFW.h` breaks after `vulkan.h`, and the surface functions of one instance are used for every instance.

- Problem:
  - `RGFW.h` with `RGFW_VULKAN` fails to compile when `vulkan.h` was included before it without the platform define: `unknown type name 'VkWaylandSurfaceCreateInfoKHR'`
  - this happens when another library includes `vulkan.h` first, for example the Vulkan Memory Allocator
  - the surface functions are loaded once and then used for every `VkInstance`, a Vulkan function pointer is only valid for its instance
- Cause:
  - the public header declares the proc typedefs of the platform surface functions, they need the platform types
  - `RGFW_LOAD_VK` stores the function in the context at the first call
- Fix:
  - the proc typedefs move to the implementation, the public header needs only the core types
  - `RGFW_LOAD_VK` loads the function of the given instance at every call, the context holds no surface functions
- Reproduction: `#include <vulkan/vulkan.h>`, then `#define RGFW_VULKAN` and `#include "RGFW.h"` in a Wayland build
