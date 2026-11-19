# Module Boundaries and Build Rules

## 1. What a module means

A module is a component with a defined responsibility, build target, interface, dependency set and validation strategy. A folder alone is not a module.

For new LiberNext-owned code, each module should contain:

```text
<module>/
  CMakeLists.txt
  README.md
  include/
  src/
  tests/
  LICENSES/       # Only when this module imports third-party material
  THIRD_PARTY.md  # Provenance and exact revision information
```

Small modules may omit unused directories, but every module must document its interface and dependencies.

## 2. Dependency rules

1. Dependencies flow down the architecture layers; lower layers must not depend on applications or desktop code.
2. Shared utility modules must not depend on the shell.
3. Desktop applications must not reach into kernel-private structures.
4. Kernel modules must not depend on user-mode libraries.
5. A component may depend only on declared public interfaces of another component.
6. Cyclic CMake target dependencies are rejected.
7. Optional features must be represented by explicit CMake options or profiles.
8. No module may silently fetch an unpinned source tree during a release build.
9. Third-party sources must not be copied into the inherited tree without license/provenance review.
10. Existing upstream modules must be extended at their established integration points unless an approved replacement plan exists.

## 3. Component types

| Type | Examples | Expected interface |
|---|---|---|
| Kernel component | Memory manager, I/O manager | NT kernel contracts |
| Driver / framework | USB, storage, WDF | DriverEntry, IRP, PnP, power, WDF contracts |
| Runtime / API | NTDLL, Win32 DLL | NT / Win32 ABI |
| System service | Session, update, package service | Service control + RPC / named pipes |
| Desktop component | Shell, taskbar, notifications | User-mode API + IPC |
| Application | File manager, terminal | Public Win32 / NT APIs |
| Third-party library | SQLite, libarchive | Narrow wrapper / stable library API |
| Build/test tooling | RosBE, tests, image generation | Build targets and CI artifacts |

## 4. CMake integration

- Each new module receives a distinct CMake target.
- The target owns its include paths, compile definitions and linked dependencies.
- Avoid directory-global compile definitions for new code.
- Prefer target-scoped `target_include_directories`, `target_compile_definitions` and `target_link_libraries`.
- Do not introduce a large top-level refactor to modernize the inherited build all at once.
- New product code should enter the build through one reviewed `add_subdirectory(libernext/...)` integration point.
- Build options must have clear defaults and documented dependencies.
- A disabled optional component must not break the core build.

## 5. Interface and versioning policy

- Public interfaces belong in a module's `include/` directory or in the documented NT/Win32 SDK surface.
- Private implementation headers remain under `src/`.
- Changes to a public interface require a compatibility note and affected-module tests.
- IPC protocols must have explicit message formats, version fields and bounded input validation.
- Avoid exposing compiler-specific C++ ABI across module boundaries; use C ABI or documented NT/COM interfaces where appropriate.
- Do not export internal kernel structures as product APIs.

## 6. Testing policy

Every module needs the smallest useful test suite:
- unit tests for deterministic logic;
- API tests for externally observable contracts;
- integration tests for cross-module behavior;
- boot/runtime tests for kernel, driver and boot changes;
- Windows 11 comparison tests when Windows compatibility is the goal.

Test names must identify the contract being checked. Tests must not silently encode LiberNext behavior as the expected Windows behavior.

## 7. Dependency management

Third-party projects are managed as separate, versioned dependencies:
- immutable commit or release tag;
- source URL and upstream project;
- license expression and included license files;
- local patch series;
- build options and disabled optional components;
- checksums for release archives;
- owner and update cadence.

Do not add duplicate implementations of libraries already present in the source tree. Before adding a new library, search the existing tree and its third-party inventory.

## 8. Refactoring policy

Large-scale directory movement, global renaming and build-system replacement are not foundation tasks. Each refactor requires:
- a concrete problem statement;
- an inventory of affected targets and consumers;
- a staged migration;
- before/after build comparison;
- tests proving no behavior regression;
- a rollback path.
