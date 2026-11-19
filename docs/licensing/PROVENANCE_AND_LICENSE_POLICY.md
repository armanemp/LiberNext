# Source Provenance and License Policy

## 1. Baseline rule

LiberNext is a multi-license operating-system source tree. The inherited code includes ReactOS-origin files, LiberNT changes, Wine-synchronized files, Microsoft-licensed samples, permissively licensed libraries, firmware and other third-party material.

There is no blanket relicensing of the repository. Existing copyright notices, SPDX headers, license texts and third-party acknowledgements must be preserved.

## 2. Required provenance record

Every newly imported external component must be recorded with:

- component name and purpose;
- canonical upstream URL;
- exact immutable commit or release tag;
- source archive checksum, where applicable;
- file-level SPDX expressions or an explicit unresolved-license report;
- upstream copyright and license files;
- full dependency inventory, including nested dependencies;
- local patches, with a short explanation for each;
- build configuration and disabled optional features;
- whether the code is shipped, build-only, test-only or reference-only;
- responsible module owner and planned update policy.

The repository should gradually generate an SPDX SBOM for each release. Unknown or conflicting license metadata blocks release packaging until resolved.

## 3. No destructive license cleanup

Never:
- remove inherited copyright lines;
- replace every file header with one project-wide license;
- delete license files because a top-level README mentions a different license;
- copy code from a repository without identifying its exact license;
- infer a license from a GitHub badge alone;
- mix optional GPL components into a build described as LGPL-only;
- use firmware or binary blobs without checking their redistribution terms.

## 4. Hybrid distribution model

The kernel and other derivative components remain under their applicable inherited copyleft licenses. New LiberNext-owned modules may use a separate license only where the module is legally independent and all combined-work obligations are satisfied.

Separating files into directories, executables or DLLs does not automatically establish legal independence. Static linking, intimate data exchange, shared private structures and runtime control flow may matter. The exact distribution model must be reviewed by qualified counsel before proprietary distribution is attempted.

A permissive license such as MIT, BSD or zlib is a candidate for a genuinely independent library or application, not a mechanism to relicense inherited GPL code.

## 5. GPL and inherited code

- Follow each file's exact license expression.
- GPL-2.0-only and GPL-2.0-or-later are not interchangeable.
- GPL-2.0-only code cannot simply be combined with GPL-3.0-only code as one combined derivative work without additional permission or a compatibility basis.
- Where upstream gives an "or later" option, document the selected version for the relevant component.
- Preserve LGPL requirements for modifications and combined works.
- Preserve the notices and conditions for BSD, MIT, MS-PL, University of Illinois, FTL, zlib, public-domain and firmware-licensed files.
- The license of a dependency does not automatically determine the license of the whole operating system.

## 6. Contributor identity and copyright

LiberNext will use its own contribution policy. Contributors may use a public pseudonym and a GitHub noreply address for commits unless a particular contribution or legal process requires otherwise. The project must still have a reliable means to record authorship, confirm that the contributor has rights to submit the material, and obtain any necessary license grant.

No contributor is required to submit an upstream LiberNT pull request. The inherited upstream requirement to place a full legal name and real email in every code commit is not automatically adopted for independent LiberNext contributions.

A Contributor License Agreement or copyright assignment is not introduced by this document. Any future change requires a separate maintainer decision and explicit contributor consent.

## 7. Clean-room and third-party code

Prohibited sources:
- leaked Microsoft source code;
- proprietary code without redistribution and modification rights;
- decompiled/disassembled source copied into implementation;
- code with unknown provenance;
- unreviewed Linux GPL code copied into a module whose license cannot accommodate it.

Acceptable engineering sources include published specifications, public SDK/WDK headers, official documentation, public symbols, open-source code under a compatible license, and lawful black-box behavioral tests. When the applicable law or source permissions are unclear, stop and seek legal review rather than guessing.

## 8. Branding and artwork

Source-code permissions do not grant trademark rights or copyright permission for logos, banners, wallpapers and icon artwork. Use original LiberNext artwork or assets whose exact licenses allow the intended use. Keep an asset attribution and license inventory.

The inherited LiberNT trademark policy must be treated as active until written permission or legal review resolves the proposed product name. Do not ship the LiberNT logo or inherited brand assets as LiberNext artwork.
