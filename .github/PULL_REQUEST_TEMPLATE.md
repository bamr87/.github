<!-- Default PR template inherited by every bamr87 repo without its own. -->

## What & why

<!-- One or two sentences. Link the issue: Closes #123 -->

## Conventional Commit

This repo releases via [release-please](https://github.com/bamr87/.github/blob/main/README.md).
The **PR title** (squash-merge subject) must be a Conventional Commit — it drives the next version:

- `fix: …` → patch · `feat: …` → minor · `feat!: …` / `BREAKING CHANGE:` → major
- `docs:` `chore:` `refactor:` `test:` `ci:` `perf:` → no release on their own

## Checklist

- [ ] PR title is a Conventional Commit
- [ ] Tests added/updated and passing (CI green)
- [ ] Docs/README updated (README-first, README-last)
- [ ] No secrets, generated artifacts, or unrelated changes
