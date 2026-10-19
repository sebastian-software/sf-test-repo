# Add repository health marker

**Plan status:** Not implemented
**Source:** effective-flow plan
**Recommended workflow:** Feature (`effective-flow build`)
**Issue:** #2

## Requirement

In a separately authorized future implementation, add `src/health.txt` as a repository health marker containing exactly the three bytes `6f 6b 0a` (`ok` followed by one LF). Add a `Health marker` section to `README.md` explaining the file's purpose and path while retaining the existing heading and content. This is a Feature because it introduces a new repository artifact; the README change documents that artifact.

This planning pass changes only this plan. Future implementation is limited to the two affected paths and adds no dependencies, configuration, or third-party content.

## Architecture decisions

- Use a static plain-text marker, with no executable endpoint, runtime integration, or generated output. Its exact bytes are the entire marker contract.
- Append the README section after the existing content. Preserve the original heading and text; add the newline and blank line needed to separate the new section because the current README has no final newline.
- Deliver the marker and its explanation as one bounded change with no external prerequisites. Keep verification read-only and require no test framework or dependency.

## Affected files

| File | Description |
|---|---|
| `src/health.txt` | Create a regular file containing exactly `ok` and one LF; create `src/` if still absent. |
| `README.md` | Append a `Health marker` section naming `src/health.txt` and identifying it as the repository health marker. Preserve existing content. |

## Implementation details

### Approach

1. Re-read `README.md`, inspect `src/health.txt`, and compare in-scope working state with the planning baseline below. Preserve any unrelated work. If either target has incompatible new content or is a symlink, stop for reconciliation rather than overwriting it.
2. In the future authorized implementation, create `src/` if absent and write `src/health.txt` with exactly the required three bytes. Do not add a BOM, CRLF, spaces, or an extra newline.
3. Append the level-two `Health marker` heading and a concise English explanation that `src/health.txt` is the repository health marker. Retain every existing README character in its original order, including the existing heading.
4. Run the focused validation below and inspect the complete change set against the implementation start state. The implementation package is complete only when all acceptance criteria pass together.

### Edge cases

- The current README has no trailing newline. Add separation before the new heading without joining it onto the existing heading or rewriting existing text.
- An absent `src/` is expected and requires only directory creation; an existing incompatible marker or an unsafe target path requires reconciliation before implementation.
- Extra bytes, a BOM, CRLF, missing LF, or additional LF in the marker fail acceptance even if a text viewer displays `ok`.
- A pre-existing equivalent README section should be reconciled rather than duplicated. No existing section was found at planning time.

## Acceptance criteria

- [ ] `src/health.txt` is a regular file whose complete byte sequence is exactly `6f 6b 0a`, proven by the byte and length checks below.
- [ ] `README.md` retains its existing heading and content and includes a level-two `Health marker` section explaining that `src/health.txt` is the repository health marker, proven by README inspection and its diff.
- [ ] The implementation delta contains exactly the new marker and the README update; no other tracked file changes and no dependency, configuration, or third-party content is introduced. Verify against the implementation start state, preserving pre-existing untracked files and this planning artifact.

## Validation plan

All checks run from the repository root during the future implementation; none of these implementation checks has been run as evidence of implemented behavior in this planning pass.

- Run `test -f src/health.txt` and `test ! -L src/health.txt`; both must exit zero.
- Run `od -An -tx1 src/health.txt`; its complete output, ignoring display whitespace, must be `6f 6b 0a`. Run `wc -c < src/health.txt`; the length must be `3`.
- Read `README.md` and run `git diff -- README.md`; confirm the original `# sf-test-repo` heading remains and the new section has the required path and explanation. Account for the previously missing final newline when inspecting the diff.
- Record the implementation start change set, then compare it with `git diff --name-status`, `git diff --cached --name-status`, and `git ls-files --others --exclude-standard` after implementation. The combined delta attributable to this package must consist only of `README.md` and `src/health.txt`; the untracked marker must be checked even before staging. Preserve unrelated pre-existing entries.
- Inspect the final diff and marker bytes together. No additional automated test suite or formatter is configured in this fixture, and no dependency or test scaffold is needed for this static contract.

## Assumptions and open points

- Verified on 2026-10-19 at HEAD `d84399025307c00afaa53497284ec5db0d25f7d5`, branch `main`: `README.md` contains exactly `# sf-test-repo` without a trailing newline; `src/` is absent and there are no existing plan files.
- Verified working state before plan creation: no tracked modifications; `.gitignore`, `AGENTS.md`, and `docs/adr/effective-flow-project-setup.md` are pre-existing untracked files and must be preserved.
- The issue defines a repository marker only. No consumer, API, health-monitoring service, or runtime behavior is specified or required.
- Re-read changed in-scope files before implementation. Stop or revise if their current state, the byte contract, required scope, or validation tools no longer match this plan.

## Plan review

**Result:** Approved

### Summary

| Area | Critical | Important | Note |
|---|---:|---:|---:|
| Architecture | 0 | 0 | 0 |
| Security | 0 | 0 | 0 |
| Data protection | 0 | 0 | 0 |
| Error cases | 0 | 0 | 0 |
| Testability | 0 | 0 | 0 |
| Scope | 0 | 0 | 0 |
| Maintainability | 0 | 0 | 0 |

### Findings

- No findings. Independent gap analysis and plan-level review checked architecture, security, data protection, error cases, testability, scope, and maintainability against the persisted plan and verified fixture context on 2026-10-19.

## Open points

- No open points.
