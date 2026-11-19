# External Feed Locking and Reproducible Builds

## 1. Current behavior

The inherited feeds.conf.default describes external sources by URL and branch or tag. The current scripts/feeds update command clones missing feeds and fetches the configured branch head. A later configure operation can therefore select a different external revision without any LiberNext source commit changing.

The top-level build cannot be considered fully reproducible until feed revisions and nested submodules are pinned.

## 2. Required model

Keep two concepts separate:
- **Feed manifest:** human-maintained source URL and intended upstream branch/tag.
- **Feed lock:** machine-maintained immutable commit IDs actually used for a known-good build.

The manifest expresses intent. The lock determines the exact source revision used in CI and release builds.

## 3. Proposed lock format

Use a versioned, machine-readable lock file such as feeds.lock.json:

~~~json
{
  "schema_version": 1,
  "feeds": {
    "mesa": {
      "url": "https://github.com/LiberNT/libernt-mesa.git",
      "ref": "ros-dev",
      "commit": "<40-character-commit-sha>"
    },
    "ffmpeg": {
      "url": "https://github.com/FFmpeg/FFmpeg.git",
      "ref": "n8.1.3",
      "commit": "<40-character-commit-sha>"
    }
  }
}
~~~

The example is illustrative only. Placeholder hashes must never be used for a real build.

Each nested submodule must also resolve to a recorded immutable commit. A parent feed commit is sufficient only if nested submodule gitlinks are checked out exactly as recorded.

## 4. Tool behavior

The future feed tool should support:
- list: show manifest entries and lock state;
- status: show checked-out revisions and local modifications;
- lock: resolve configured refs to commit IDs and update the lock file;
- update --locked: fetch and check out only locked commits;
- update --floating: explicitly update a branch/tag head and require a lock-file diff;
- verify: fail if a checkout does not match the lock;
- sbom: emit dependency and license inventory for the selected build profile.

CI and release behavior must default to locked revisions. Moving branch heads should only be used in an explicit upstream-update workflow.

## 5. Integrity and supply-chain rules

- Use canonical HTTPS or SSH remotes.
- Record immutable commit IDs, not only mutable branch names.
- Verify downloaded release archive SHA-256 values where source archives are used.
- Record nested submodules and generated sources.
- Do not fetch dependencies implicitly during a release build.
- Do not silently change dependency versions in a feature pull request.
- Keep local patches in reviewable patch files or documented commits.
- Record build-time-only dependencies separately from shipped runtime dependencies.
- Generate an SPDX SBOM for release artifacts and retain it with the build manifest.

## 6. Migration plan

1. Inventory all feed entries and special submodule initialization paths.
2. Resolve exact commits currently checked out in a clean local baseline.
3. Record nested submodule gitlinks and archive hashes.
4. Add the lock file without changing dependency versions.
5. Add a verification-only mode to CI.
6. Make CI use locked revisions.
7. Only then implement explicit feed update and lock-regeneration commands.
8. Review each future dependency update independently, including license and build-impact checks.

Do not change feed URLs, branches or dependency versions as part of the first documentation-only foundation change.
