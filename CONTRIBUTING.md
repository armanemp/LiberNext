# Contributing

This repository is an independent development fork with its own roadmap and contribution process. The product name currently used in development is provisional; see the project charter and trademark review note before public branding or distribution.

## Contributor identity and privacy

- A public pseudonym or alias is accepted for Git author and committer names.
- A GitHub noreply address or another valid public-facing address is accepted.
- Do not impersonate another person or use an address you do not control.
- Contributors must have the legal right to submit the material and must not knowingly submit third-party code without permission.
- Do not publish passwords, private keys, access tokens, personal data or other secrets in commits or issues.
- No Contributor License Agreement or copyright assignment is currently required by this repository. Any future change must be announced separately and require explicit agreement.

This policy applies to contributions submitted only to this repository. It does not change the rules of LiberNT, ReactOS, Wine or any other upstream project.

## Source and licensing rules

- Preserve every inherited copyright, SPDX identifier and license notice.
- Do not change the license of inherited files unless all relevant rights holders have granted that right.
- Identify the upstream URL and immutable commit/tag for imported code.
- Record local modifications and dependencies in the relevant provenance document.
- Do not submit leaked Microsoft source code, proprietary source code obtained without permission, or code transcribed from decompiled/disassembled proprietary binaries.
- Do not copy GPL-licensed Linux implementation code into this source tree.
- Use published specifications, public SDK/WDK headers, public symbols, official documentation, compatible open-source code and lawful black-box testing.
- Every new third-party dependency must pass the checklist in docs/third-party/INTEGRATION_CATALOG.md.

## Engineering requirements

Every change should:
1. Solve one clearly stated problem.
2. Identify the affected module, architecture and compatibility contract.
3. Include a focused test or explain why a test is not yet possible.
4. Build the affected target and relevant architecture.
5. Include runtime/boot evidence for kernel, boot and driver changes.
6. Compare observable Windows compatibility behavior against the documented Windows 11 reference when applicable.
7. Document any ABI, structure-layout, synchronization, IRQL, locking or memory-ordering decision.
8. Avoid unrelated refactoring and broad mechanical renames.

Kernel and driver changes require particular care. Follow the inherited engineering guidance in AGENTS.md, especially its clean-room rules, ABI evidence requirements, architecture-neutrality rules and memory-manager constraints.

## Pull requests

Use focused pull requests with:
- a concise title and problem statement;
- cause and implementation summary;
- affected components and architectures;
- exact build and test commands;
- results and logs for relevant runtime tests;
- third-party source/license information where applicable.

Do not claim a feature is Windows 11 compatible merely because it compiles or boots. State the tested behavior and known limitations.

## Security reports

Do not publish exploitable vulnerabilities, credentials or private security-sensitive details in public issues. Use the repository's configured private security reporting channel where available; otherwise contact the maintainers privately through a verified project contact.
