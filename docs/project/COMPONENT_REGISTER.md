# LiberNext Component Register

This register tracks where each subsystem lives, who owns its roadmap, and what kind of work is allowed. It does not claim that all components are complete.

## 1. Component ownership classes

- **Inherited core:** Existing ReactOS/LiberNT code; keep original paths, notices and build targets.
- **Inherited integration:** Existing third-party code or source feeds; preserve upstream boundaries and record local patches.
- **LiberNext-owned:** New product code created for this project; place under libernext/.
- **Reference-only:** External source used to study behavior or APIs; not copied into the product.
- **Candidate:** Proposed dependency pending an integration decision.

## 2. Initial register

| Component | Current location / source | Ownership class | Initial action |
|---|---|---|---|
| Boot and FreeLoader | boot/, bootloader/ | Inherited core | Preserve and baseline |
| HAL | hal/ | Inherited core | AMD64 validation first |
| NT kernel and executive | ntos/ | Inherited core | Stabilize; no broad rewrite |
| NVS memory manager | ntos/mm/ | Inherited core / LiberNT work | Preserve; focused correctness and parity tests |
| Cache manager | ntos/cc/ | Inherited core / LiberNT work | Preserve; focused tests |
| Driver stack | drivers/ | Inherited core and integration | Inventory hardware coverage |
| WDF | sdk/lib/drivers/wdf/ | Inherited integration | Audit before upstream update |
| Filesystems | drivers/filesystems/ | Inherited core / integration | Audit NTFS variants and recovery behavior |
| Win32 / NT APIs | dll/, subsystems/, base/ | Inherited core and Wine integration | Maintain per-component API coverage |
| Mesa / graphics | Existing tree and mesa feed | Inherited integration | Establish software-rendering baseline |
| DXC | dxc feed | Inherited integration | Retain narrow DXIL conversion role |
| FFmpeg | ffmpeg feed | Inherited integration | Preserve explicit LGPL-only build configuration |
| FEX | fex-arm64ec feed | Inherited integration | Optional ARM64-only emulation path |
| felix86 | felix86 feed | Inherited integration | Optional RISC-V64 emulation path |
| First-party shell | libernext/desktop/shell/ (planned) | LiberNext-owned | Design user-mode boundary |
| Taskbar / Start / notifications | libernext/desktop/ (planned) | LiberNext-owned | Separate build targets |
| File manager | libernext/applications/file-manager/ (planned) | LiberNext-owned / candidate components | Evaluate UI and archive libraries |
| Settings application | libernext/applications/settings/ (planned) | LiberNext-owned | Use public user-mode system APIs |
| Package manager | libernext/services/package/ (planned) | LiberNext-owned | Define package format and trust model first |
| Update service | libernext/services/update/ (planned) | LiberNext-owned | Reuse current TLS/network libraries after testing |
| Terminal | libernext/applications/terminal/ (planned) | LiberNext-owned / later candidate | Validate console and PTY prerequisites |
| Text editor | libernext/applications/text-editor/ (planned) | LiberNext-owned / candidate component | Evaluate Scintilla/Lexilla |
| Archive manager | libernext/applications/archive-manager/ (planned) | LiberNext-owned / candidate component | Evaluate libarchive |
| Test harness | libernext/tests/ (planned) | LiberNext-owned | Share contracts, not private implementation |

## 3. Candidate integration order

### P1 — Add after baseline and API prerequisites exist
- SQLite for package metadata, caches, settings catalogs and search indexes.
- libarchive for archive management and package/recovery tooling.
- libcurl for update and repository downloads, once TLS/socket backends are selected.

### P2 — Add when user-facing applications need them
- Scintilla + Lexilla for a native text-editor editing surface.
- HarfBuzz for text shaping only if the current font/Uniscribe path proves insufficient.
- SDL3 as an optional application/developer runtime, not a replacement for the native desktop or kernel input/audio/graphics stacks.

### P3 — Research after core platform compatibility
- Windows Terminal as a reference or later application after console/ConPTY and UI dependencies exist.
- PowerShell after a sufficiently compatible .NET runtime and required Win32/NT APIs exist.
- DXVK only after a sufficiently mature Vulkan user-mode and driver stack exists.

## 4. Module admission rule

A candidate becomes an owned dependency only after:
1. A named feature requires it.
2. A source-tree search proves there is no adequate existing implementation.
3. Exact revision and license are audited.
4. Its dependency closure is recorded.
5. A build proof-of-concept succeeds.
6. The component is isolated behind a stable interface.
7. Security and malformed-input tests are defined.
8. An owner and update policy are assigned.

No candidate is imported merely because it is popular or has a permissive license.
