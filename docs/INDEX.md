# Project Documentation Index

This index is the entry point for the LiberNext engineering plan. Documents in this branch are planning and audit records; they do not claim that the build, boot or hardware tests have passed.

## Project direction

- [Project charter](project/CHARTER.md) — product goals, non-goals, target architectures and decision authority.
- [Component register](project/COMPONENT_REGISTER.md) — inherited modules, project-owned components and ownership boundaries.
- [Development roadmap](roadmap/ROADMAP.md) — phased plan from the reproducible baseline to a stable release.

## Architecture and modularity

- [Target architecture](architecture/ARCHITECTURE.md) — firmware, HAL, kernel, drivers, APIs, graphics, services and desktop layers.
- [Module boundaries](architecture/MODULE_BOUNDARIES.md) — interface, dependency, CMake and test rules for each module.
- [Build-system integration](architecture/BUILD_SYSTEM_INTEGRATION.md) — safe integration point and rules for the inherited CMake build.

## Audit and quality

- [Initial baseline audit](audit/BASELINE_AUDIT.md) — repository state, build system, CI, source feeds, inherited components and audit limitations.
- [Test strategy](quality/TEST_STRATEGY.md) — unit, API, integration, QEMU, hardware and Windows 11 comparison tests.
- [Development workflow](engineering/DEVELOPMENT_WORKFLOW.md) — branch, upstream synchronization, review and CI rules.

## Reusable projects and supply chain

- [Third-party integration catalog](third-party/INTEGRATION_CATALOG.md) — existing dependencies, recommended projects, alternatives and rejected direct imports.
- [Feed locking](third-party/FEED_LOCKING.md) — immutable revisions, nested submodules and reproducible builds.
- [Source provenance and license policy](licensing/PROVENANCE_AND_LICENSE_POLICY.md) — multi-license rules, third-party notices, contributor rights and SBOM expectations.

## New product-owned source

- [libernext/README.md](../libernext/README.md) — boundary and development rules for new LiberNext-owned code.

## Status

The current branch is a documentation and foundation-planning branch. No kernel, driver, root CMake or boot source changes have been made here. The inherited master branch remains unchanged.
