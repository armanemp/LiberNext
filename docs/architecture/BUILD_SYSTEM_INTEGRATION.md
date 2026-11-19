# Build-System Integration Plan

**Status:** Design only; no CMake source has been changed by this document.

## 1. Facts from the current build

The inherited root CMakeLists.txt:
- calls project(REACTOS);
- sets C to C99 and C++ to C++11 globally;
- imports the ReactOS version machinery and LiberNT build-stamp logic;
- defines the inherited __REACTOS__ compatibility macro globally;
- configures host tools in the non-cross-compiling branch;
- configures the OS target in the cross-compiling branch;
- adds boot, base, dll, submodules, drivers, HAL, SDK libraries, media, modules, ntos, subsystems and WPP targets in a specific order;
- includes the CI target definitions after the OS components are created.

The top-level configure script selects AMD64, Clang, Debug and Ninja by default, requires an installed RosBE toolchain, initializes the glmark2 submodule, and updates configured feeds unless --no-feeds-update is supplied.

The NTOS CMake file requires NVS=ON. The legacy memory/cache-manager build path is explicitly retired and fails configuration when NVS is disabled.

## 2. Integration principle

Do not rewrite the inherited top-level CMake system as a foundation task. Retain the current source and binary directory variables and target conventions while establishing a separate build namespace for new product-owned components.

In particular:
- do not globally rename REACTOS_SOURCE_DIR, REACTOS_BINARY_DIR, __REACTOS__, module macros or artifact paths before a dependency and consumer audit;
- do not change the global C/C++ language standard to satisfy one optional UI library;
- do not enable all optional feeds by default;
- do not add user-mode targets in the host-tools branch.

The existing names are technical compatibility machinery, not a requirement that the product keep the upstream brand.

## 3. Proposed single integration point

After a local baseline is recorded, add one reviewed call to add_subdirectory(libernext) in the target-side section of the root CMakeLists.txt, after the inherited OS components and subsystems have declared their targets and before CI/package-finalization targets are generated.

The first integration should be guarded by a project option such as LIBERNEXT_BUILD_COMPONENTS, default OFF until at least one new component is independently buildable and tested.

The first libernext/CMakeLists.txt should only add subdirectories for completed components. It must not create placeholder targets, silently fetch dependencies or change global compiler settings.

## 4. Target construction rules

For a new user-mode executable:
- use add_executable with explicit sources;
- use the existing set_module_type helper with the correct NT/Win32 subsystem;
- declare imported system DLL dependencies explicitly;
- use target-scoped include directories and compile definitions;
- package it using the existing add_cd_file mechanism only after its destination and startup role are defined;
- add a focused test or smoke-run target.

For a new library:
- use a separate STATIC or SHARED target as appropriate;
- expose only stable public headers;
- keep implementation headers private;
- declare dependencies with target_link_libraries;
- avoid global include_directories and add_definitions in new modules;
- document ABI and runtime initialization expectations.

Existing examples:
- base/applications/notepad shows the current Win32 GUI executable pattern;
- base/applications/taskmgr shows a larger GUI application target;
- base/shell/explorer shows an inherited shell executable and its current import-library dependencies.

These are integration examples, not a decision to reuse their code for the LiberNext desktop.

## 5. Proposed component layout

~~~text
libernext/
  CMakeLists.txt
  shared/
    diagnostics/
    settings/
  services/
    session/
    notifications/
    update/
    package/
  desktop/
    shell/
    taskbar/
    start-menu/
    window-integration/
  applications/
    file-manager/
    settings/
    terminal/
    text-editor/
    archive-manager/
  sdk/
    include/
  tests/
~~~

Create a directory only when a real module starts. Do not check in empty placeholder directories or a large scaffold that cannot yet build.

## 6. Build profiles

Initial profiles:
- amd64-clang-debug: mandatory local and PR baseline.
- amd64-clang-release: scheduled after debug build is stable.
- arm64-clang: scheduled architecture validation.
- riscv64-clang: scheduled/experimental validation.
- i386 and MSVC profiles: retain existing upstream coverage while tracking known limitations.

New desktop and applications must be optional during early kernel stabilization. A missing optional desktop component must not prevent the base kernel/image build.

## 7. First implementation sequence

1. Record exact RosBE, CMake, Ninja, LLVM and host package versions.
2. Run a clean AMD64 configure and build without source changes.
3. Produce and hash a bootable image.
4. Boot under QEMU UEFI and save serial output.
5. Record the external feed and nested-submodule revisions actually used.
6. Add only the build-manifest and feed-lock validation tooling.
7. Add the first harmless LiberNext user-mode target using the existing application pattern.
8. Integrate the shell only after session, window and composition prerequisites are demonstrated.

## 8. Acceptance criteria

The build integration is accepted when:
- a clean checkout still builds the inherited baseline with the new option disabled;
- enabling the new option builds only the selected LiberNext targets;
- no new module changes global compile options or definitions;
- the target graph has no dependency cycles;
- host-tool builds remain unaffected;
- dependency completeness checks pass;
- the generated boot image and existing tests remain unchanged when no LiberNext component is enabled.
