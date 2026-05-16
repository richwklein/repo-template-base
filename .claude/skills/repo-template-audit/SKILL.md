---
name: repo-template-audit
description: Compare the current repo against richwklein/repo-template-base (and repo-template-astro if applicable) plus the canonical GitHub repo settings manifest, then surface drift. Use when the user runs /repo-template-audit, asks to check template drift, or wants to verify GitHub repo settings against the canonical baseline.
---

# repo-template-audit

> **Status: scaffold — full implementation pending Phase 8 of the repo standardization plan.**

This skill audits a target repo against canonical sources:

1. **File drift**: tracked files (issue templates, workflows, configs, docs) compared against `richwklein/repo-template-base` and, if `package.json` + `astro.config.*` are present, `richwklein/repo-template-astro`.
2. **Settings drift**: GitHub repo-level configuration (Actions permissions, advanced security, general settings) compared against `richwklein/repo-template-base/docs/REPO_SETTINGS.yaml`.

## Invocation

```
/repo-template-audit [path]
```

If `path` is omitted, audit the current working directory.

## Behavior (target — to implement in Phase 8)

### Part A — File drift

1. Detect template flavor from the target repo:
   - `astro.config.*` + `package.json` → `astro` flavor (consume both base + astro manifests)
   - Otherwise → `base` flavor
2. Fetch canonical files via `gh api repos/richwklein/repo-template-{base,astro}/contents/<path>` so audits always reflect current template state, not a local cache.
3. Compare against the file lists in `manifest.json`:
   - **Exact-match** files must be byte-identical.
   - **Schema-match** files must contain the listed keys/scripts; content beyond that is free.
4. `CODE_OF_CONDUCT.md` is verified by presence only (canonical Contributor Covenant content is not regenerated — diffing would be noisy and the file is downloaded from an external canonical URL).

### Part B — Settings drift

1. Fetch `docs/REPO_SETTINGS.yaml` from `richwklein/repo-template-base`.
2. Fetch optional `.github/repo-settings-override.yaml` from the target repo. Merge overrides over the canonical values; treat the result as the effective expected state.
3. Read current settings via `gh api`:
   - `GET /repos/{o}/{r}/actions/permissions`
   - `GET /repos/{o}/{r}/actions/permissions/selected-actions`
   - `GET /repos/{o}/{r}/actions/permissions/workflow`
   - `GET /repos/{o}/{r}` (security_and_analysis + general fields)
   - `GET /repos/{o}/{r}/private-vulnerability-reporting`
4. Diff effective expected vs actual.

### Part C — Report

- **Missing files** — present in template, absent in repo.
- **Drifted files** — diff snippet (first ~30 lines) per file.
- **Schema gaps** — required `package.json` scripts / config keys missing.
- **Settings drift** — table: `section.key | canonical | actual | suggested action`.
- **Honored overrides** — drift the override file explicitly permits.
- **Extra files** — informational only, not flagged as wrong.

### Part D — Remediation (explicit per-item confirmation)

- Files: offer to open a PR with proposed changes. Group by area (e.g., issue templates, workflows).
- Settings: emit `gh api` patch commands for the user to review and run. If invoked with `--apply`, run them after confirmation.

## Self-audit

The skill's own files (`SKILL.md`, `manifest.json`, `lib/*`) are tracked in `manifest.json`. Running the audit against a repo whose copy of the skill is older than canonical will flag the skill itself as drifted.

## Implementation notes (for Phase 8)

- Use `gh api ... --jq` for setting reads to keep the parser thin.
- For file fetches: `gh api repos/richwklein/repo-template-base/contents/<path> --jq .content | base64 -d`.
- The `code_scanning_default_setup` value is read via `GET /repos/{o}/{r}/code-scanning/default-setup` — not the main repo endpoint.
- Keep all canonical state remote; never cache to disk. Audit must reflect the *current* template.
