# PR texts for upstream

- one text per branch of this fork that waits for an upstream PR
- the branch name is the file name
- opened PRs leave this folder

## Declined upstream, only in this fork

- `fix/wayland-no-forced-debug`: no forced `RGFW_DEBUG` on Wayland
  - #610 closed without merge, the maintainer wants Wayland in debug mode
  - do not open it again, keep it when upstream is merged in
