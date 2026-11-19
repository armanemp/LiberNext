# LiberNext Project Charter

**Status:** Initial architecture baseline  
**Working product name:** LiberNext (provisional; trademark review required)  
**Reference platform:** Windows 11 / NT 10.x  
**Initial target architecture:** AMD64

## 1. Project definition

LiberNext is an independent NT-based operating-system project. Its initial source baseline is the public LiberNT repository, which itself derives from ReactOS and includes substantial independent work. LiberNext will preserve all upstream copyright and licensing notices while establishing its own governance, product architecture, release process, desktop environment, tools and roadmap.

LiberNext is not intended to remain a branded rebuild of LiberNT. Upstream projects are engineering sources, not the product identity or the authority for LiberNext's future direction.

## 2. Product goals

1. Build a reliable, bootable NT-family operating system with Windows 11 as the behavioral compatibility reference.
2. Prioritize AMD64, then bring up ARM64; retain other inherited architectures where they remain maintainable.
3. Build an original LiberNext desktop shell and first-party user experience.
4. Improve application, system API, storage, graphics, networking and device-driver compatibility incrementally.
5. Keep the implementation modular, testable, documented and reproducible.
6. Reuse mature open-source projects when they fit the target environment and their licenses permit the intended use.
7. Preserve the ability to distribute independent applications and tools under separate compatible licenses where they are genuinely separate works.

## 3. Non-goals

- Do not claim full Windows 11 compatibility before test evidence supports that claim.
- Do not bulk-import unrelated Linux kernel code or treat Linux drivers as drop-in NT drivers.
- Do not rewrite the kernel or move the inherited source tree merely to make the directory names look new.
- Do not copy proprietary Microsoft source code, leaked code, or code with unclear provenance.
- Do not remove, rewrite or homogenize inherited copyright and license notices.
- Do not ship inherited LiberNT logos, artwork or branding without the necessary permission.

## 4. Engineering priorities

The order of priority is:

1. Reproducible AMD64 build and repeatable boot.
2. Kernel, executive, memory manager, I/O manager and HAL correctness.
3. Storage, ACPI, PCI, USB, network and input reliability.
4. NT10 user-mode and Win32 compatibility.
5. Graphics, display, audio and multimedia.
6. Independent desktop shell and system applications.
7. Installer, package/update services, recovery and release engineering.

A polished desktop is not a substitute for a stable kernel and device stack.

## 5. Architecture policy

The inherited ReactOS/LiberNT source layout will be retained initially. New product-owned components will live under a clearly marked `libernext/` subtree and be introduced through explicit CMake targets. Existing source directories will only be reorganized when a measured engineering benefit justifies the risk and a migration plan preserves build and test coverage.

Each new component must have:
- one clear responsibility and owner;
- a documented public interface;
- explicit build dependencies;
- unit, integration or compatibility tests appropriate to its role;
- a license/provenance record;
- no hidden dependency on another component's private implementation.

## 6. Licensing and contributor identity

The repository is a multi-license source tree. No single license label should be applied indiscriminately to all files. Inherited file headers, third-party notices and corresponding license texts remain authoritative unless a rights holder grants a different license.

LiberNext will maintain its own contribution policy. Public pseudonyms and GitHub no-reply addresses may be accepted for new contributions, subject to preserving accurate authorship and copyright information and any applicable legal obligations. The upstream LiberNT contribution identity policy does not automatically govern work submitted only to this independent fork.

## 7. Name and brand status

"LiberNext" is a working name only. The inherited `TRADEMARK.md` states that the LiberNT project restricts names and marks that may be confused with LiberNT. Before public product branding, domain registration, release distribution or logo publication, obtain written permission or complete a legal review and select a sufficiently distinct name if needed.

## 8. Initial source baseline

The repository was inspected on 2026-11-19. Its `master` branch pointed to:

`8840dcee8a916ee752bb3f4490f8e047a67c5f71`

The same commit was observed as the latest commit on `LiberNT/LiberNT` at inspection time. This establishes the initial technical baseline; it does not mean all inherited code has been independently audited or tested by LiberNext.

## 9. Decision authority

LiberNext maintainers decide which upstream changes are accepted, which components are replaced, and which product features are developed. Upstream synchronization is selective and evidence-based. A clean merge is not, by itself, proof of correctness.
