# repo-template-base

Language-agnostic GitHub repository template for [@richwklein](https://github.com/richwklein) projects.

Universal conventions live here: GitHub issue/PR templates, CODEOWNERS, dependabot config for GitHub Actions, CodeQL workflow, VSCode recommendations, editorconfig, license, security policy, and code of conduct.

Framework-specific templates extend this one. The Astro/TypeScript variant lives at [richwklein/repo-template-astro](https://github.com/richwklein/repo-template-astro).

## Use this template

Click **Use this template → Create a new repository** on GitHub, then run through the checklist below in the new repo.

### Post-template checklist

- [ ] **Update `README.md`** with your project description (replace this content).
- [ ] **Update `.vscode/settings.json`** — add project-specific entries to `cSpell.words` if needed.
- [ ] **Adjust `.github/workflows/code-codeql.yaml`** — set the `language` matrix to match your project (e.g., `python`, `swift`). For Bash-only repos, delete this workflow and use `actionlint` instead.
- [ ] **Apply repo settings and branch protection** — run `/repo-template-audit richwklein/repo-template-base` from a Claude Code session; the audit skill compares this repo's live GitHub settings and reports any drift. Add the branch ruleset **after** the first PR has run so the status check names are registered.
- [ ] **If the repo cuts releases**: add `release-please.yaml` workflow + `release-please-config.json` + `.release-please-manifest.json`. See the astro template for the canonical setup.
- [ ] **Verify CODEOWNERS** — the default `@richwklein` may need to change for repos under a different owner (e.g., `@helpinghandwc/maintainers`).

## Audit drift

Install the audit skill (`npx skills add richwklein/skills`), then run `/repo-template-audit richwklein/repo-template-base` from an agent coding session to check whether files and GitHub settings still match this template.

## Conventions

- Commits: [Conventional Commits](https://www.conventionalcommits.org/). See [`AGENTS.md`](AGENTS.md).
- Branching: `main` is protected; squash or rebase merges only; PRs required.
- Signed commits: required by ruleset.

## License

[MIT](LICENSE) © Richard Klein
