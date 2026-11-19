# LiberNext Architecture

**Status:** Target architecture proposal  
**Initial implementation baseline:** Existing LiberNT / ReactOS-derived tree  
**First validation target:** AMD64

## 1. Architecture principles

LiberNext will evolve as a layered NT operating system. The inherited kernel and compatibility layers remain the foundation; new product-owned functionality must be separated into explicit components.

The architecture is modular in its build graph and interfaces, not merely in its directory names.

### Required principles

- Keep kernel-mode and user-mode responsibilities separate.
- Prefer existing NT interfaces and documented contracts over private cross-component calls.
- Keep architecture-specific code in architecture-specific files or HAL/architecture hooks.
- Make new components independently configurable and testable.
- Keep third-party dependencies behind a small adapter or stable interface where practical.
- Avoid cyclic dependencies.
- Pin external source versions for reproducible builds.
- Preserve inherited source layout until a measured refactor is justified.
- Never merge a feature merely because it compiles; require tests and runtime evidence.

## 2. Target layers

### L0 — Firmware and boot

Responsibilities:
- UEFI / BIOS handoff;
- boot manager and loader;
- boot configuration and early boot diagnostics;
- kernel image loading and early memory map handling.

Initial strategy: retain and harden the inherited FreeLoader and boot infrastructure. Do not replace the boot chain during the first foundation milestone.

### L1 — Hardware abstraction and platform

Responsibilities:
- HAL and interrupt routing;
- ACPI and power interfaces;
- PCI / PCIe and bus enumeration;
- USB host controllers and device buses;
- timers, DMA, IOMMU and platform-specific initialization.

Initial strategy: AMD64 first, using the existing HAL and drivers. Keep common paths architecture-neutral.

### L2 — NT kernel and executive

Responsibilities:
- scheduler, dispatcher and synchronization;
- process and thread management;
- virtual memory and cache management;
- object manager, I/O manager and security;
- executive services and system calls.

Initial strategy: preserve the existing LiberNT NVS and cache-manager work. Kernel changes require a focused test, documented evidence and boot/runtime validation.

### L3 — Driver frameworks and device stack

Responsibilities:
- WDM and NT driver interfaces;
- WDF / KMDF / UMDF compatibility;
- Plug and Play and power management;
- storage, network, display, audio, input and bus drivers.

Initial strategy: audit the already-present WDF source and driver coverage before importing any additional driver framework or device driver.

### L4 — User-mode NT and Win32 compatibility

Responsibilities:
- NTDLL and native runtime;
- Win32 subsystem and API DLLs;
- COM, RPC, registry and services;
- WOW64 / ARM64EC and application loader paths;
- runtime libraries and system APIs.

Initial strategy: treat Wine-synchronized components as managed upstream integrations. Track each synchronization boundary and LiberNext-specific patch separately.

### L5 — Graphics, display and multimedia

Responsibilities:
- display miniport and WDDM model;
- graphics kernel and DWM integration;
- OpenGL / Vulkan user-mode support where implemented;
- DirectX shader translation and validation;
- fonts, text shaping, image, audio and video codecs.

Initial strategy: reuse the current Mesa, DXC, FFmpeg and DirectX-Headers integration. Do not introduce a second graphics stack before the current path is characterized.

### L6 — System services and session infrastructure

Responsibilities:
- logon/session management;
- service lifecycle and service configuration;
- device notification and system event routing;
- settings storage;
- update, package, recovery and diagnostic services.

New product-owned services should use documented IPC and stable user-mode contracts.

### L7 — LiberNext desktop and applications

Responsibilities:
- original shell and desktop session;
- taskbar, start/search surface, notification center and system tray;
- file manager and settings application;
- terminal, text editor, archive manager and system utilities.

Initial strategy: implement as user-mode components. The desktop must not require private kernel interfaces.

## 3. Project-owned source area

New product code should be organized under:

```text
libernext/
  CMakeLists.txt
  cmake/
  shared/
    base/
    diagnostics/
    settings/
  services/
    session/
    update/
    package/
    notifications/
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
    system-monitor/
  sdk/
    include/
  tests/
```

The layout is a target for new LiberNext-owned code. Existing upstream components must not be moved here just for visual consistency.

## 4. Runtime architecture

The intended user-mode dependency direction is:

```text
Applications
    |
Desktop Shell / Session
    |
LiberNext Services
    |
Win32 / NT User-mode APIs
    |
NT Executive and Drivers
    |
HAL and Hardware
```

System applications consume public system APIs. The shell talks to services over explicit IPC. Services do not call directly into application-private code. Kernel drivers expose their functionality through normal NT device and I/O contracts.

## 5. Graphics policy

The first desktop implementation should use the existing Win32, USER, GDI and current composition infrastructure. Avoid creating a new compositor or graphics API abstraction until the current WDDM/DWM state is measured.

A future LiberNext UI toolkit can provide shared controls, layout, theme tokens, accessibility, high-DPI behavior and animation. It should not be coupled to the kernel or to a single application.

## 6. Architecture milestones

- **Foundation:** boot and build AMD64 repeatably.
- **Core stability:** kernel, storage, ACPI and device enumeration test coverage.
- **User-mode baseline:** reliable logon/session and essential Win32 APIs.
- **Desktop alpha:** shell, taskbar, start/search, file manager and settings.
- **Application compatibility:** broader API coverage and application test matrix.
- **Release engineering:** installer, package/update, recovery and signed build artifacts.
