# LiberNext Test and Validation Strategy

**First target:** AMD64  
**Compatibility reference:** Windows 11  
**Status:** Initial policy; test execution has not yet been performed by this document.

## 1. Test pyramid

### Level 1 — Static and source checks
- Compiler diagnostics and warning policy.
- Formatting and whitespace.
- Include and dependency hygiene.
- License/provenance manifest validation.
- Generated-file consistency.
- CMake configure and target graph checks.
- Dependency completeness using sdk/tools/depcheck.py.

### Level 2 — Unit tests
Test deterministic logic in isolation: parsers, serializers, path handling, settings, package metadata, IPC validation, state machines, timeouts, and host-testable memory-management helpers.

### Level 3 — API and compatibility tests
Cover NT native APIs, NTDLL and Win32 APIs, Registry, process/thread behavior, synchronization, filesystem semantics, services, RPC, COM, networking and security primitives.

For Windows compatibility, run the same test binary and cases against the documented Windows 11 reference environment. Compare individual checks, not only aggregate pass counts. Record Windows build, architecture, test-binary hash and environment configuration.

### Level 4 — Integration tests
- Boot and session startup.
- Storage stack and filesystem.
- Plug and Play enumeration.
- USB input and storage.
- Network stack and services.
- Graphics, display and desktop composition.
- Shell/service IPC and restart behavior.
- Installer, update and recovery flows.

### Level 5 — System and hardware tests
Use QEMU UEFI/BIOS boots where supported, followed by real SATA/NVMe media, USB devices, display modes, audio, networking and a dedicated physical hardware matrix. A VM pass is not proof that a physical driver works.

## 2. Initial AMD64 baseline matrix

| Area | Initial test | Evidence |
|---|---|---|
| Build | Clean RosBE Clang Debug configure and build | Full log, toolchain version, build manifest |
| Image | Create bootable image | Artifact hash and image metadata |
| UEFI | Boot QEMU with UEFI firmware | Serial log and console capture |
| BIOS | Boot QEMU BIOS path if supported | Serial log |
| Kernel | Early initialization, scheduler, DPC and interrupt smoke tests | Debug log and test result |
| Memory | Allocation, mapping, protection and pressure tests | Test output and crash-dump review |
| Storage | Enumerate virtual disk and mount supported filesystem | Device tree and filesystem logs |
| Filesystem | Create, read, rename, delete, flush and remount files | Test report and integrity hashes |
| PnP | Enumerate and rescan virtual PCI/USB devices | Device tree before/after |
| User mode | Start system processes and essential Win32 tools | Process list and exit statuses |
| Desktop | Start session and first-party shell when integrated | Screenshot, event log and restart test |

## 3. Windows 11 reference environment

Maintain a controlled reference VM:
- Record exact Windows 11 build and update revision.
- Use matching virtual CPU count, RAM, firmware and storage layout where possible.
- Keep test binaries identical between systems.
- Store per-test expected/observed results.
- Rerun the reference only when the test binary or environment changes in a result-affecting way.
- Separate undocumented observations from documented API requirements.

Do not treat undocumented observations as universal guarantees without test conditions.

## 4. QEMU smoke-test profile

The automated smoke test should:
1. Create or obtain the AMD64 boot image.
2. Start QEMU with a fixed machine type and UEFI firmware.
3. Attach a serial console.
4. Wait for a defined boot milestone.
5. Collect serial output and emulator exit status.
6. Terminate on a bounded timeout.
7. Preserve logs and the exact image hash.

"QEMU process started" is not a boot pass. The milestone must prove that the kernel reached a known initialization point; later it can be upgraded to user-session or shell-ready.

## 5. Test ownership by module

| Module class | Required tests |
|---|---|
| Boot / HAL / Kernel | Build, static assertions, unit tests where possible, QEMU boot, runtime stress |
| Memory / Cache / I/O | Invariants, API tests, stress, fault injection, boot validation |
| Drivers / PnP | Build, INF/package validation, device enumeration, start/stop/remove, physical test |
| Filesystems / Storage | Read/write, crash/recovery, metadata integrity, disposable-media tests |
| NT / Win32 APIs | API tests, Windows 11 per-check comparison, application smoke tests |
| Graphics | Display modes, software rendering, composition, API conformance, physical GPU tests |
| Services | Start/stop, restart, timeout, permission, malformed IPC and resource-exhaustion tests |
| Desktop / Apps | UI automation, keyboard/mouse, high-DPI, accessibility, crash/restart and profile integrity |
| Installer / Updater | Disposable disk images, interrupted transaction, rollback, signature and tamper tests |

## 6. CI gates

### Pull request baseline
- Static source and license checks.
- AMD64 Clang Debug configure and build.
- Image generation.
- Dependency completeness check.
- Focused tests selected by changed paths.
- QEMU smoke boot once automation is available.

### Nightly / scheduled
- AMD64 extended tests.
- i386 compatibility build where maintained.
- ARM64 build.
- RISC-V64 build.
- MSVC build matrix where supported.
- Longer filesystem, memory and driver tests.

### Release
- Clean pinned-source checkout.
- Complete release matrix.
- Reproducible artifact and checksum generation.
- SBOM and license package.
- Installation, update and recovery tests.
- Manual review of hardware support claims.

Keep the fast, reliable AMD64 gate mandatory. Do not make every pull request wait for every experimental architecture before the wider matrix is stable.

## 7. Failure classification

Classify each failure as build/toolchain regression, boot failure, kernel invariant/crash, missing API, incorrect API semantics, driver/hardware gap, filesystem/data-integrity issue, graphics/audio/media limitation, service/IPC failure, or test/reference-environment defect.

A test bug must not be "fixed" by weakening the expected behavior without evidence.

## 8. Test data safety

All partition, filesystem, installer and recovery tests use disposable virtual disks or dedicated spare media. Never run destructive tests against the host operating-system disk or a disk containing user data.
