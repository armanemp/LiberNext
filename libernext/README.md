# LiberNext-Owned Components

This directory is reserved for new product-owned components developed specifically for LiberNext.

It is intentionally separate from the inherited ReactOS/LiberNT source tree. Existing upstream components remain in their established directories until a reviewed migration proves that moving them is necessary and safe.

## Rules for new components

Every component must:
- have one clearly stated responsibility;
- be a separate CMake target where practical;
- expose a small, documented public interface;
- keep private implementation details internal;
- declare all build and runtime dependencies;
- avoid circular dependencies;
- include appropriate tests;
- include a README with purpose, ownership, interface, build options and test instructions;
- record source provenance and license information for imported code;
- be disableable or replaceable without breaking unrelated components.

## Planned top-level layout

~~~text
libernext/
  shared/          # Small shared utilities; no dependency on the desktop
  services/        # Product-owned user-mode services
  desktop/         # Session shell, taskbar, start/search, notifications
  applications/    # First-party desktop and system applications
  sdk/             # LiberNext-owned public headers and developer interfaces
  tests/           # Tests for LiberNext-owned components
  cmake/           # Reusable CMake helpers for this subtree only
~~~

## Initial implementation order

1. Establish the existing AMD64 build and QEMU boot baseline.
2. Add a minimal shared diagnostics/logging library only if the inherited logging APIs are insufficient.
3. Define and test the session/desktop process boundary.
4. Add the shell as a user-mode executable, not a kernel extension.
5. Add taskbar, start/search, notifications and settings as separate targets.
6. Add file manager and system utilities after the shell contracts stabilize.

## Current status

This directory is a documented ownership boundary. No new component is linked into the root build yet. That integration is deliberately postponed until a clean local build baseline is captured.
