# Third-Party Project Integration Catalog

**Purpose:** Identify mature projects that can strengthen LiberNext without duplicating what the current source tree already contains.

This is an engineering shortlist, not blanket approval to import code. Every candidate needs a revision-specific license review, dependency review, platform-portability check and test plan before integration.

## 1. Already present — do not add a second copy

| Project / component | Current evidence in LiberNext | License / condition | Action |
|---|---|---|---|
| Wine | Extensive modules listed in `media/doc/WINESYNC.txt` | LGPL-2.1-or-later; component-level notices apply | Maintain targeted syncs and local patches; do not import a second Wine tree |
| Mesa | `mesa` feed in `feeds.conf.default`; existing OpenGL and Gallium integration | File-level Mesa license inventory; inspect the actual fork and files | Stabilize current fork, record exact commit and graphics test results |
| FEX | `fex-arm64ec` feed, ARM64EC build option | Upstream FEX is MIT; nested submodules have their own licenses | Keep as the ARM64 x86 emulation path; validate NT-specific integration |
| felix86 | Feed and `dll/win32/amd64emu` integration | MIT, with separately licensed vendored dependencies | Keep optional and isolated to RISC-V64 |
| FFmpeg | `ffmpeg` feed, build option and Media Foundation codec integration | LGPL-2.1-or-later by default; enabling GPL parts changes the result to GPL | Keep the build explicitly LGPL-only unless a separately reviewed decision is made |
| DirectX Shader Compiler | `dxc` feed; current config builds dxilconv | University of Illinois Open Source License; includes third-party notices | Retain the existing narrow use; do not add a second DXC copy |
| Windows Driver Frameworks | `sdk/lib/drivers/wdf` | MIT in the current third-party inventory | Audit what is implemented and tested before adding any WDF fork |
| DirectX-Headers / DXCore | Present under `sdk/include/psdk` | MIT | Maintain header provenance and version |
| mbed TLS | `dll/3rdparty/mbedtls` | Apache-2.0 OR GPL-2.0-or-later | Prefer the Apache-2.0 option when all relevant files permit it; verify each file |
| FreeType | `sdk/lib/3rdparty/freetype` | FTL | Reuse existing implementation |
| libpng / libjpeg / libtiff | Existing third-party directories | Multiple licenses; libjpeg is custom/BSD-like | Reuse current copies and update individually |
| libxml2 / libxslt / Little CMS | Existing third-party directories | MIT in current inventory | Reuse current copies |
| zlib / zstd | Existing third-party directories | zlib; zstd BSD-3-Clause OR GPL-2.0-only | Reuse current code; keep license selection explicit |
| lwIP | `drivers/network/tcpip/lwip` | Modified BSD | Extend current network path rather than importing another stack |
| VirtIO | `sdk/lib/drivers/virtio`, NetKVM | BSD-3-Clause | Retain, test and document supported devices |
| Microsoft storage / filesystem samples | CDFS, FastFAT, CD-ROM, ClassPnP and Disk components | MS-PL | Preserve notices and validate the exact source revisions |

The full inherited inventory is `media/doc/3rd Party Files.txt`. The list above is intentionally selective.

## 2. Strong candidates for a future integration

| Project | Repository | License | Proposed role | Priority | Main caveat |
|---|---|---|---|---|---|
| SQLite | https://github.com/sqlite/sqlite | Public domain for core code | Package metadata, search indexes, settings/catalog databases | P1 | Keep databases out of kernel mode; review build scripts and optional extensions separately |
| libarchive | https://github.com/libarchive/libarchive | BSD-style | Archive reading/writing for file manager, updater and recovery tools | P1 | Enable only required formats and dependencies; validate malformed archives and path traversal |
| libcurl | https://github.com/curl/curl | curl MIT-like license | HTTPS downloads, repository metadata, update service and diagnostics | P1 | Requires a working TLS backend, DNS and socket layer; license is SPDX `curl`, not literally MIT |
| Scintilla + Lexilla | https://github.com/ScintillaOrg/scintilla and https://github.com/ScintillaOrg/lexilla | MIT | Editing component for a first-party text editor and script editor | P2 | Requires a working Win32 window/message, clipboard, font and accessibility path |
| HarfBuzz | https://github.com/harfbuzz/harfbuzz | MIT | Complex-script shaping for the font/text stack | P2 | First check the current GDI/Uniscribe path and existing font stack; integrate only if a measurable gap remains |
| SDL3 | https://github.com/libsdl-org/SDL | zlib | Multimedia/game/application API for optional user-mode apps | P2 | It is not a replacement for NT input, audio or graphics drivers; requires a tested platform backend |
| Windows Terminal | https://github.com/microsoft/terminal | MIT | Reference and possible future terminal application | P3 | Depends on modern console/ConPTY and Windows UI facilities; not drop-in for the current shell |
| PowerShell | https://github.com/PowerShell/PowerShell | MIT | Optional advanced command shell and automation layer | P3 | Depends on a compatible .NET runtime and many system APIs; large integration effort |
| Windows Driver Frameworks upstream | https://github.com/microsoft/Windows-Driver-Frameworks | MIT | Source reference for WDF behavior and future compatibility work | P2 | WDF is already present; compare revisions and port only missing functionality |
| DXVK | https://github.com/doitsujin/dxvk | zlib | Optional Direct3D 8/9/10/11-to-Vulkan compatibility layer | P3 | Needs a functional Vulkan user-mode stack and compatible memory/synchronization primitives; not a native WDDM driver |

### Candidate selection notes

- SQLite, libarchive and libcurl are the strongest near-term additions because they support system utilities and product services without becoming part of the kernel.
- Scintilla/Lexilla can provide a mature editing component without forcing a large UI framework into the OS.
- HarfBuzz is a typography capability, not a complete UI toolkit.
- SDL3 should be offered as an application/developer runtime after the input, audio and graphics paths are testable.
- Windows Terminal and PowerShell are later-stage applications, not initial platform dependencies.
- WDF must be audited in-place before any upstream code is imported.
- DXVK is a compatibility layer for applications; it cannot replace the graphics kernel, display miniport or WDDM implementation.

## 3. Reference-only or not suitable for direct import

| Project class | Example | Why not a direct integration |
|---|---|---|
| Linux kernel GPU drivers | amdgpu, i915, nouveau | Linux kernel APIs and DRM/KMS driver contracts are not NT WDDM contracts. Reuse only components with a clearly compatible license and a separately engineered NT interface |
| Linux userland services | systemd, udev, NetworkManager | Built around Linux kernel APIs and service assumptions; not suitable as native NT services without a major compatibility layer |
| Full Linux desktop environments | KDE Plasma, GNOME, XFCE | Depend on Linux/Wayland/X11 ecosystems and large stacks; the chosen product direction is a native NT desktop |
| Qt/GTK application stacks | Qt, GTK | Possible application frameworks, but bring large dependency and licensing/porting costs; do not select before the native Win32 shell baseline |
| Windows App SDK / WinUI 3 apps | Windows Terminal UI components, modern Settings examples | Depend on Windows App SDK and APIs not established in this source baseline |
| Arbitrary Windows driver binaries | Vendor WDM/KMDF drivers | Binary compatibility depends on kernel, WDF, PnP, power, signing and undocumented behavior; evaluate per driver and hardware |

## 4. Import acceptance checklist

A project is accepted only when all answers are recorded:

1. Does the project solve a requirement that LiberNext actually has?
2. Is a similar implementation already present?
3. Is the exact source revision known and immutable?
4. Are the license and every included dependency compatible with the intended distribution?
5. Can it build with the current RosBE/CMake/Ninja environment?
6. Does it require Linux, Windows App SDK, .NET, Qt, Vulkan or other facilities that are not yet available?
7. Can it be isolated behind a stable component interface?
8. What tests prove correct behavior and security?
9. Who owns updates, local patches and vulnerability handling?
10. Can the component be disabled or replaced without breaking the core OS?

## 5. Licensing references

- FFmpeg legal guidance: https://ffmpeg.org/legal.html
- Wine license: https://github.com/wine-mirror/wine/blob/master/LICENSE
- FEX source: https://github.com/FEX-Emu/FEX
- DirectX Shader Compiler: https://github.com/microsoft/DirectXShaderCompiler
- SDL: https://github.com/libsdl-org/SDL
- libarchive: https://github.com/libarchive/libarchive
- curl: https://github.com/curl/curl
- SQLite public-domain statement: https://www.sqlite.org/copyright.html
- Windows Driver Frameworks: https://github.com/microsoft/Windows-Driver-Frameworks

A repository-level license badge is not a substitute for file-level review or third-party notices.
