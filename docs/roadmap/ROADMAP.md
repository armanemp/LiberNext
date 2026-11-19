# LiberNext Development Roadmap

**Planning basis:** Initial LiberNT source baseline  
**First release goal:** A repeatable AMD64 development image, not a consumer-ready OS  
**Release numbering:** 0.x for development milestones; 1.0 only after release criteria are satisfied.

## Phase 0 — Governance and reproducibility

### Work
- Confirm product name / trademark clearance.
- Replace upstream project identity documentation with LiberNext-owned policies where appropriate.
- Record baseline commit and upstream source mapping.
- Create source-provenance and component/license inventory.
- Pin moving external feeds to immutable commit IDs.
- Record toolchain versions and checksums.
- Define branch protections, review requirements and security reporting.
- Add a build manifest and generate an SPDX SBOM.

### Exit criteria
- A clean checkout can identify every external source revision.
- No unknown feed revision is silently pulled in release builds.
- Project policies no longer direct LiberNext contributors to LiberNT channels or rules.
- The license/provenance audit has no unresolved high-risk component in the release path.

## Phase 1 — AMD64 build and boot baseline

### Work
- Install and verify RosBE and host dependencies.
- Configure default AMD64 Clang Debug build.
- Build the minimum bootable image and installer image.
- Record build logs and artifact hashes.
- Boot from UEFI in QEMU; record serial and graphical output.
- Boot from BIOS where supported by the current image.
- Save a golden baseline for later regression comparison.

### Exit criteria
- Clean checkout builds without undocumented manual steps.
- QEMU boot is repeatable.
- Kernel initialization and essential storage paths are stable enough for subsequent testing.
- Build and boot regressions are visible in CI.

## Phase 2 — Core stability and system diagnostics

### Work
- Kernel and executive smoke tests.
- NVS / memory manager invariants and stress tests.
- Cache manager and file I/O stress tests.
- Object, process, thread and synchronization tests.
- ACPI, PCI/PCIe and Plug and Play enumeration.
- USB xHCI, keyboard and storage validation.
- NTFS read/write, dirty volume and recovery tests.
- Kernel debugger and crash dump workflow.
- Basic system information and log collection.

### Exit criteria
- Critical boot, memory, storage and device-enumeration failures are reproducible and tracked.
- Tests cover known regressions.
- No destructive disk operation is tested against user data.

## Phase 3 — Application and driver compatibility baseline

### Work
- Define a Windows 11 test reference machine and VM image.
- Establish per-API and per-application compatibility matrices.
- Test NTDLL, Win32, Registry, COM, RPC, Services and common runtimes.
- Audit WDF/KMDF coverage using the source already in the repository.
- Test WDM/WDF sample drivers only where prerequisites are implemented.
- Expand WoW64 validation on AMD64.
- Prioritize common APIs by real application demand.

### Exit criteria
- Each supported compatibility claim maps to a test.
- Known failures are classified as missing API, incorrect behavior, missing driver, graphics limitation or unsupported installer/runtime.
- No broad "Windows 11 compatible" claim is made from a small sample.

## Phase 4 — Graphics and multimedia baseline

### Work
- Establish a software-rendering baseline using the already integrated Mesa path.
- Validate display modes, framebuffer, WDDM interactions and DWM composition.
- Run OpenGL and DirectX API tests where the existing stack supports them.
- Verify DXC/DXIL conversion and shader validation paths.
- Validate audio endpoint enumeration and the current audio stack.
- Test FFmpeg-enabled media paths under the configured LGPL-only profile.
- Profile RX 580 separately; do not assume a vendor WDDM driver will load.

### Exit criteria
- A known-good software graphics path exists.
- Desktop composition is stable at basic resolutions.
- Hardware acceleration support is stated per adapter and driver, not generically.

## Phase 5 — LiberNext services and desktop alpha

### Work
- Implement a session/service boundary for the new desktop.
- Implement a settings store and theme service.
- Build shell core, taskbar and start/search surface as separate targets.
- Add notifications and system tray integration.
- Add first-party file manager and settings app.
- Add terminal and system monitor.
- Use existing Win32/USER/GDI and composition infrastructure initially.
- Introduce shared UI controls only after the first application demonstrates a reusable need.

### Exit criteria
- A user can boot, log in, launch applications, manage windows, browse files and change basic system settings.
- Shell restart does not require a kernel reboot.
- Shell failure does not corrupt the user profile or kernel state.
- Every desktop component can be built and tested separately.

## Phase 6 — Product services and applications

### Work
- Package metadata format and package manager.
- Update service with signed metadata and rollback strategy.
- Archive manager using a reviewed archive library.
- Text editor using a reviewed editor component or an independent implementation.
- Network download service and TLS integration using existing mbed TLS or a separately reviewed client library.
- Recovery environment and safe-mode startup.
- Installer UX and disk/partition safety checks.
- Accessibility, high-DPI and localization.

### Exit criteria
- Packages can be installed, updated, removed and rolled back safely.
- Updates are authenticated and interrupted updates are recoverable.
- Installer destructive actions require explicit user confirmation and operate only on the selected disk.

## Phase 7 — Hardware expansion

### Work
- Intel and AMD chipset/ACPI coverage.
- NVMe and SATA paths, power state handling and device removal.
- Audio, Wi-Fi, Ethernet, Bluetooth and input devices.
- WDDM hardware adapters, beginning with devices for which a compatible driver path is actually available.
- ARM64 bring-up and board-specific hardware profiles.
- Continue RISC-V64 only when its maintenance cost does not impair AMD64 stability.

### Exit criteria
- Per-device support matrix based on real hardware tests.
- No driver is marked supported solely because it compiles or loads.

## Phase 8 — Stabilization and 1.0 release

### Work
- Full regression and compatibility suite.
- Security review of kernel, services, updater, package manager and installer.
- Reproducible release builds and SBOM.
- License and trademark review.
- Signed release artifacts and hashes.
- Documentation, recovery guide and release notes.
- Public issue triage and supported-hardware list.

### Exit criteria for 1.0
- Repeatable build, installation, boot, update and recovery.
- No known blocker in core boot, storage, profile or update flows.
- Supported hardware list backed by test evidence.
- All shipped code and assets have resolved license/provenance records.
- Compatibility claims are specific and evidence-based.

## Work ordering rule

Do not run all phases in parallel. The desktop can be prototyped in user mode while core work continues, but product integration should follow stable contracts. A feature is not complete until its tests, documentation, license record and failure behavior are reviewed.
