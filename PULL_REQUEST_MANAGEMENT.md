# Pull Request Management

## Review principles

- Keep pull requests focused and reviewable.
- Do not merge a change solely because it builds; inspect the implementation and test evidence.
- Prefer small, reversible steps for kernel, driver, boot, filesystem and build-system changes.
- Preserve author attribution and all inherited copyright/license notices.
- Keep upstream synchronization pull requests separate from product feature work.
- Do not merge broad directory moves or global renames without a migration plan and before/after validation.

## Required review checklist

Before merging, reviewers should verify:

1. The PR describes the problem, root cause and intended behavior.
2. The changed files belong to the stated modules and no unrelated files were added.
3. Public interfaces and dependencies are documented.
4. Source provenance, license compatibility and third-party notices are correct.
5. Tests cover the changed behavior, including failure paths where practical.
6. Kernel/driver changes include architecture, IRQL, locking, lifetime and memory-ordering review as applicable.
7. Compatibility changes are compared against the Windows 11 reference environment.
8. Build and runtime evidence is included.
9. No secrets, credentials, private data or unlicensed content are present.
10. The change does not silently alter feed revisions or generated outputs.

## Contributor identity

This repository permits contributor pseudonyms and GitHub noreply email addresses under CONTRIBUTING.md. Reviewers must not require an upstream LiberNT identity policy for contributions made only to this fork. This does not remove the contributor's responsibility to have rights to submit the work and preserve accurate authorship/provenance.

## Upstream synchronization

- Prepare LiberNT updates on an isolated upstream-sync branch.
- Prepare ReactOS-origin changes separately and cherry-pick only after conflict, behavioral and licensing review.
- Never merge moving upstream branch heads directly into master.
- Record the source commit(s), upstream URL and relevant local adaptations.
- Run affected build/tests before integrating.

## Merge policy

- Do not use force-push on shared branches.
- Use squash or rebase according to the repository's configured policy while preserving attribution.
- Require review for changes to boot, kernel, drivers, filesystems, cryptography, licensing, build pipelines and release infrastructure.
- Keep master in a known buildable state. Prefer staging work on integration branches until CI and runtime gates are available.
