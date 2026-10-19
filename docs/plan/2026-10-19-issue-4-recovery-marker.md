# Recovery marker

**Plan status:** Not implemented
**Source:** effective-flow plan
**Recommended workflow:** Feature (`effective-flow build`)
**Issue:** #4

## Requirement

Add a small static recovery marker at `src/recovery.txt` and document it in a `Recovery` section in `README.md`. The marker must contain exactly the six UTF-8 bytes `72 65 61 64 79 0a`: the word `ready` followed by exactly one LF. This is a Feature because it introduces a new repository artifact and its documented contract.

This artifact plans a later implementation only. The current smoke-fixture instructions permit planning and prohibit implementation. A later executor must obtain appropriate implementation authority and follow the then-current repository instructions before making the changes described here.

## Architecture decisions

- Keep the marker as a regular static text file; no executable recovery logic, dependencies, network calls, build tooling, generated artifacts, or additional product files are needed.
- Keep the enduring implementation scope to `src/recovery.txt` and an additive `Recovery` section in `README.md`. Creating the containing `src/` directory is necessary; add no directory placeholder file.
- Preserve the existing README bytes as an unchanged prefix. Its current heading has no terminal newline, so append two LF bytes before the new level-two heading, `## Recovery`.

## Affected files

| File | Description |
|---|---|
| `src/recovery.txt` | Create a regular file with exactly the specified six bytes. |
| `README.md` | Append a `Recovery` section describing the marker path, `ready` content, and exactly one terminal LF. |

## Implementation details

### Approach

1. Re-read `AGENTS.md` and confirm implementation is authorized. Compare the two in-scope paths with the planning baseline below. Preserve unrelated local work; stop to revise the plan if the marker already exists with a different purpose or the requested contract has changed.
2. Create `src/` if it is still absent, then create `src/recovery.txt` using a write method that produces the exact six bytes without a BOM or newline conversion.
3. Append the `Recovery` section to `README.md`, preserving all existing bytes. Explain that `src/recovery.txt` contains `ready` followed by exactly one LF. Do not imply that the file performs a recovery operation or proves runtime health.
4. Perform the focused checks in the validation plan and inspect the complete implementation change set for the two-file scope.

### Edge cases

- CRLF, a missing terminal LF, an extra LF, trailing spaces, or a UTF-8 BOM all violate the marker contract and must fail byte validation.
- The README currently ends immediately after its heading; add the separator before the new section so the headings do not merge.
- If the README changes before implementation, preserve its current content and recheck section placement. If a `Recovery` section already exists, reconcile the requested documentation there without duplicating the section; stop if this requires changing unrelated content.
- A symlink or non-regular object at `src/recovery.txt`, or a non-directory `src`, requires stopping before overwriting it.

## Acceptance criteria

The implementation is complete only when all criteria hold together:

- [ ] `src/recovery.txt` is a regular non-symlink file and contains exactly six bytes, with hexadecimal sequence `72 65 61 64 79 0a`.
- [ ] `README.md` has one `## Recovery` section documenting the marker path, the word `ready`, and its single terminal LF, without suggesting executable recovery behavior.
- [ ] The pre-existing README content is preserved byte for byte, with only the necessary separator and requested section appended against the verified baseline.
- [ ] The implementation changes only `src/recovery.txt` and `README.md`; it introduces no dependencies, network calls, build tooling, or other files and preserves unrelated local work.

## Validation plan

Run these checks only in a separately authorized implementation run, from the repository root:

- Check file type without following symlinks, then compare the marker's raw bytes with the six-byte expected value. `wc -c < src/recovery.txt` must report `6`; `od -An -tx1 src/recovery.txt` must report `72 65 61 64 79 0a` after whitespace normalization. Byte equality is decisive rather than a text-only comparison.
- Inspect the Markdown heading and section text to confirm the path and exact content contract are documented once. Inspect the rendered or plain Markdown to ensure the original heading and new section are separated.
- Compare `README.md` with the pre-implementation version: all prior bytes must be an unchanged prefix and only the separator and requested section may follow.
- Compare the full working-tree and untracked-file inventory before and after implementation. Attribute only the two named product paths to this task; retain all pre-existing operator and runtime files. The planning artifact is a pre-existing handoff artifact, outside the product implementation delta.
- No repository-native test, lint, format, or build command is configured in this fixture. Use the focused file and byte checks; add no test files or tooling for this change.

## Assumptions and open points

Verified on 2026-10-19 at HEAD `d843990`: `README.md` is the only tracked file and contains exactly the 14 ASCII bytes for `# sf-test-repo`, without a trailing newline. `src/` is absent. The tracked tree has no local changes. The untracked `.gitignore`, `AGENTS.md`, and setup ADR are existing operator files and must be preserved. `docs/plan/` was empty before this plan. There are no manifests, validation scripts, or test/build/formatter configurations.

The issue requirement defines the six-byte contract and two-file product scope. No product behavior beyond this static marker is assumed. Revalidate the baseline at implementation time; harmless unrelated changes do not require a new plan, while conflicting in-scope changes require reconciliation before editing.

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

- No findings. The independent deep plan review checked requirement consistency, the two-file scope, byte-exact acceptance and verification, README preservation, drift handling, error cases, feasibility, maintainability, security, and data protection. The planning-only restriction and setup ADR are respected. Prior review history was excluded from the judgment; no unresolved decision or specialist boundary was identified.

## Open points

- No open points.
