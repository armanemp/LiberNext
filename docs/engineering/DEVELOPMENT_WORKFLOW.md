# LiberNext Development Workflow

## 1. Branch model

- `master`: release-quality integration branch; it must remain buildable.
- `development`: ongoing integration branch after the baseline is established.
- `feature/<area>-<short-name>`: focused feature work.
- `fix/<area>-<short-name>`: focused bug fixes.
- `upstream/libernt-sync`: prepared upstream LiberNT updates.
- `upstream/reactos-sync`: prepared ReactOS-origin updates where relevant.

The initial foundation work is being prepared on `libernext/foundation-audit`. Do not merge it until its documentation and checks have been reviewed.

## 2. Upstream synchronization

LiberNT is the primary source baseline. ReactOS is a secondary upstream source and historical origin for much of the tree.

Upstream updates must be:
1. fetched to an isolated integration branch;
2. compared by commit and changed file;
3. reviewed for conflicts with LiberNext work;
4. checked for source and license provenance;
5. built and tested on the affected targets;
6. merged as a documented synchronization event.

Do not routinely merge ReactOS wholesale into the LiberNT-derived tree. Prefer specific, reviewed changes where the two implementations have diverged.

## 3. Commit and pull request standards

- One logical change per commit or pull request.
- Explain the problem, cause, solution and validation.
- Identify changed components and dependencies.
- Include tests and results, not just "build succeeded".
- Add source references for NT ABI/layout decisions.
- Preserve upstream commit attribution when cherry-picking.
- Do not include generated binaries or local build output unless the task explicitly requires an asset.
- Do not use a force-push on shared integration branches.

New contributions may use a public pseudonym and GitHub noreply email under the LiberNext policy. Contributors remain responsible for rights and provenance of their submissions.

## 4. Required validation

### Kernel / driver / boot changes
- Build the affected architecture.
- Run the narrowest relevant unit or API test.
- Boot in QEMU or on a dedicated test device.
- Record serial/debug output.
- Include a regression test for the defect when practical.
- Never test disk/partition changes on a disk containing important user data.

### User-mode API and service changes
- Build the affected targets.
- Run API and integration tests.
- Verify failure, timeout, restart and invalid-input behavior.
- Compare Windows 11 behavior for compatibility claims.

### Desktop and application changes
- Build the affected application independently.
- Test keyboard, mouse, high-DPI, focus, accessibility, theme and error paths.
- Verify that shell/app crashes do not crash the kernel or corrupt user data.

### Third-party imports
- Verify immutable revision, dependency licenses and local patches.
- Build with the same feature options intended for the product.
- Add tests around the adapter/integration boundary.
- Record update and security-response ownership.

## 5. CI gates

The initial inherited multi-architecture CI should be preserved while its project-specific names and toolchain references are audited. The first LiberNext-owned CI gate should prioritize:

1. source formatting / whitespace checks;
2. provenance and license-manifest checks;
3. AMD64 Clang Debug configure and build;
4. image generation;
5. QEMU smoke boot with serial log capture;
6. targeted kernel and user-mode tests;
7. dependency completeness check.

Add other architectures as separate matrix jobs. A CI job must not report a successful boot when it only compiled an image.

## 6. Issue and milestone structure

Recommended labels:
- `area:boot`, `area:kernel`, `area:mm`, `area:io`, `area:drivers`;
- `area:win32`, `area:graphics`, `area:desktop`, `area:services`;
- `area:build`, `area:licensing`, `area:documentation`;
- `type:bug`, `type:feature`, `type:compatibility`, `type:upstream-sync`;
- `priority:blocker`, `priority:high`, `priority:normal`, `priority:low`;
- `status:needs-reproduction`, `status:needs-design`, `status:ready`, `status:blocked`.

Each issue should include source commit, architecture, build profile, reproduction steps, expected behavior, observed behavior and logs when relevant.
