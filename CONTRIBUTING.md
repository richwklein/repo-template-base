# Contributing

## Commit messages

This repo uses [Conventional Commits](https://www.conventionalcommits.org/). The release workflow (release-please) parses commit messages to generate changelogs and version bumps.

Allowed types: `feat`, `fix`, `chore`, `docs`, `refactor`, `test`, `build`, `ci`, `perf`, `style`.

Breaking changes: append `!` (e.g., `feat!: rename public API`) or include a `BREAKING CHANGE:` footer.

Examples:

```
feat(auth): add SSO sign-in
fix: stop crashing on empty search results
chore(deps): bump astro to 6.2.0
docs: clarify deployment steps
```

## Branching and PRs

- `main` is protected by a ruleset (see [`docs/BRANCH_PROTECTION.md`](docs/BRANCH_PROTECTION.md)).
- Work happens on feature branches. PRs only — no direct pushes to `main`.
- Merges must be squash or rebase. No merge commits.
- Branch must be up to date with `main` before merging (strict status checks).
- Commits must be signed (SSH or GPG signing required by ruleset).

## Local environment — multi-account GitHub

If you push to repos owned by different GitHub accounts from the same machine, use SSH host aliases plus a conditional `~/.gitconfig` include so the right identity and signing key are used per repo.

### `~/.ssh/config`

```
Host github.com
  HostName github.com
  User git
  AddKeysToAgent yes
  UseKeychain yes
  IdentityFile ~/.ssh/id_ed25519_github
  IdentitiesOnly yes

# Example: a second account
Host github.com-hh
  HostName github.com
  User git
  AddKeysToAgent yes
  UseKeychain yes
  IdentityFile ~/.ssh/id_ed25519_hh
  IdentitiesOnly yes
```

Clone the secondary account's repos using the aliased host:

```
git clone git@github.com-hh:helpinghandwc/website.git
```

### Scoped git identity

Create a per-account gitconfig with the right name, email, and signing key:

```ini
# ~/.gitconfig-hh
[user]
  name = Helping Hand Warren County
  email = rich.klein@helpinghandwc.org
  signingkey = ~/.ssh/id_ed25519_hh.pub
[gpg]
  format = ssh
[commit]
  gpgsign = true
```

Wire it into the main `~/.gitconfig` so it applies automatically to any remote that uses the aliased host:

```ini
[includeIf "hasconfig:remote.*.url:*github.com-hh*/**"]
  path = ~/.gitconfig-hh
```

`hasconfig:remote.*.url` is preferred over `gitdir:` — it survives moving the repo directory.

## Verify commits are signed

```
git log --show-signature -1
```

If the signature line is missing, the commit will be rejected at merge by the ruleset.
