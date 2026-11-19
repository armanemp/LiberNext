# LiberNext Baseline Audit

**Audit date:** 2026-11-19  
**Audit type:** Static repository and build-metadata review through GitHub  
**Audited ref:** `master`  
**Audit commit:** `8840dcee8a916ee752bb3f4490f8e047a67c5f71`  
**Status:** Initial pass; local build and boot validation are still required.

## 1. Repository identity and state

| Item | Observation |
|---|---|
| Repository | `armanemp/LiberNext` |
| Visibility | Public |
| Default branch | `master` |
| Reported repository size | 963,806 KB (GitHub repository-size metadata) |
| Access available during audit | Admin / push |
| Branches visible in initial branch search | `master` |
| Latest inspected commit | 2026-11-19, 17:19:02 UTC |
| Latest upstream LiberNT commit | Same SHA observed at the same time |

The inspected head is exactly the same commit as the latest commit returned by the LiberNT upstream query. No LiberNext-specific source commit was visible before this foundation branch was created. The source history is therefore an upstream baseline, not yet a mature independent development history.

## 2. Existing project identity is still inherited

The repository has not yet completed its product-identity transition:

- `README.md` still identifies the product as LiberNT, embeds the LiberNT banner and desktop screenshot, links to LiberNT releases and community channels, and names build artifacts `LiberNT.img`, `LiberNT.vhd` and `LiberNT.vhdx`.
- `AGENTS.md` still introduces LiberNT and contains LiberNT engineering instructions.
- `CONTRIBUTING.md` and `PULL_REQUEST_MANAGEMENT.md` still contain the upstream full-legal-name and real-email contribution requirements.
- `TRADEMARK.md` is the inherited LiberNT branding policy. It says that modified distributed builds must use another name and restricts confusingly similar marks.
- Top-level `CMakeLists.txt` still uses `project(REACTOS)`, includes ReactOS version machinery and defines `__REACTOS__`.
- `configure.sh` still uses inherited ReactOS variable names and LiberNT build-stamp naming.
- `.github/workflows/build.yml` still uses the LiberNT toolchain bootstrap, LiberNT artifact names and inherited build settings.

These are identity and integration tasks, not reasons to rename every inherited symbol immediately. In particular, `__REACTOS__` is used throughout the inherited tree as a source/build compatibility macro and must not be globally renamed in a cosmetic pass.

## 3. Build system and CI found

The top-level build uses CMake and Ninja through `configure.sh` / `configure.cmd`. The documented default is an AMD64 Clang Debug configuration. The build expects the RosBE cross-toolchain and host tools including CMake, Ninja, Flex, Bison, Python modules and graphics-shader tooling.

The inspected GitHub Actions workflow includes:
- Linux GCC / i386;
- Linux Clang / AMD64;
- Linux Clang / ARM64 with a Raspberry Pi profile;
- Linux Clang / RISC-V64 with a SpacemiT profile;
- Linux Clang / PowerPC;
- Windows/MSVC builds for AMD64, i386 and ARM64.

The workflow configures builds, builds external submodules, produces boot/install images, and runs the dependency checker. This is a useful inherited CI foundation. The workflow file was inspected, but this audit did not execute the workflow or verify that the current run is green. The commit-status query returned no status records; that response alone does not prove that no GitHub Actions run exists.

## 4. External source feeds

`feeds.conf.default` and `scripts/feeds` define a feed system which checks external source repositories out below `submodules/`. These are cloned source feeds rather than a single monolithic source tree.

| Feed | Source and ref in current configuration | Purpose / note |
|---|---|---|
| `fex-arm64ec` | `LiberNT/FEX`, branch `main-ros` | ARM64EC x86 emulation and nested dependencies including Zydis, Zycore, VIXL and others |
| `mesa` | `LiberNT/libernt-mesa`, branch `ros-dev` | Mesa graphics integration |
| `ffmpeg` | FFmpeg tag `n8.1.3` | Multimedia; current build configuration documents an LGPL-only build path |
| `felix86` | OFFTKP/felix86 tag `26.10` | RISC-V64 x86/x86-64 user-mode emulation work |
| `dxc` | Microsoft DirectXShaderCompiler tag `v1.10.2605.37` | Only the DXIL converter component is built by the current configuration |

The FEX and Mesa feeds track branch heads. `scripts/feeds` explicitly fetches and fast-forwards to the configured branch tip. That is convenient for development but not fully reproducible. LiberNext should add an immutable feed lock containing commit IDs and checksums before declaring release builds reproducible.

`configure.sh` also contains special handling for the glmark2 Git submodule. The exact submodule declaration and checkout state must be verified in a local recursive checkout.

## 5. Important components already present

The inherited source tree already contains substantially more reusable code than a minimal ReactOS checkout. The current third-party inventory identifies, among other items:

- Wine-synchronized user-mode API implementations, listed in `media/doc/WINESYNC.txt`;
- Microsoft Windows Driver Frameworks (WDF), MIT, under `sdk/lib/drivers/wdf`;
- DirectX-Headers / DXCore headers, MIT;
- DXC as an external feed;
- Mesa as an external feed and existing OpenGL integration;
- FEX and felix86 emulation integration;
- FFmpeg as an external multimedia feed;
- mbed TLS 3.6.7, Apache-2.0 OR GPL-2.0-or-later;
- FreeType, FTL;
- libpng, libjpeg, libtiff, zlib and zstd;
- libxml2 and libxslt;
- Little CMS;
- lwIP;
- lwext4, with a mixed file-level license inventory;
- VirtIO guest libraries and NetKVM components;
- Microsoft CDFS, FastFAT, CD-ROM, ClassPnP and Disk driver source under MS-PL;
- firmware and board-specific code with their own redistribution terms.

The authoritative per-file and component inventory currently lives in `media/doc/3rd Party Files.txt`, together with file headers and third-party license texts. That inventory must be retained and extended rather than replaced by a short top-level license badge.

## 6. Initial technical conclusions

1. The codebase is a viable starting point for an NT10/Windows 11 compatibility project; it already contains modern work beyond the historical ReactOS baseline.
2. AMD64 is the correct first validation target. ARM64 and RISC-V64 should remain visible in the architecture strategy, but should not block the first independent, repeatable AMD64 build.
3. The existing external feed mechanism is useful, but moving branch heads should be pinned to immutable revisions for reproducible releases.
4. Wine, Mesa, FEX, FFmpeg, DXC and WDF are already represented. Do not add duplicate copies.
5. New LiberNext desktop code should be isolated under a project-owned subtree and introduced as individual build targets.
6. The inherited tree has many license expressions. Hybrid licensing requires per-component and per-file provenance, not blanket relicensing.
7. Branding, contributor policy, build artifact names and project metadata still identify the upstream projects.

## 7. Work not completed by this audit

This static audit did not:
- clone the approximately 964 MB repository into a local build environment;
- initialize all source feeds or nested submodules;
- run RosBE bootstrap or CMake configuration;
- run Ninja for AMD64;
- boot an image in QEMU or real hardware;
- inspect all source files or all SPDX headers;
- generate a complete SBOM / license report;
- verify every CI run or release artifact;
- validate hardware support for the user's Intel i9-9900K and AMD RX 580.

These items are explicit follow-up tasks. No build or boot success is claimed.
