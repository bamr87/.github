# Contributing

Default contributing guide inherited by every bamr87 repo. Individual repos may
override it with their own `CONTRIBUTING.md`.

## Workflow

1. Branch off `main` (`feature/…`, `fix/…`, `docs/…`).
2. Make focused changes; keep the README current (README-first, README-last).
3. Open a PR. **The PR title must be a [Conventional Commit](https://www.conventionalcommits.org/)** —
   it determines the next version.
4. CI (the reusable [`ci.yml`](https://github.com/bamr87/.github/blob/main/.github/workflows/ci.yml) gate)
   must pass: tests, lint, build, docs, commit-lint, CodeQL.
5. Squash-merge. [release-please](https://github.com/bamr87/.github#readme) opens a **release PR** that
   bumps the version and updates `CHANGELOG.md`; merging it tags and publishes the release.

## Conventional Commits → version bump

| Prefix | Example | Bump |
|---|---|---|
| `fix:` | `fix: handle empty input` | patch |
| `feat:` | `feat: add CSV export` | minor |
| `feat!:` / `BREAKING CHANGE:` | `feat!: drop Node 16` | major |
| `docs:` `chore:` `refactor:` `test:` `ci:` `perf:` `style:` | — | none (unless `!`) |

## Local checks

Run the repo's own tests/lint before pushing (`npm test`, `bundle exec rake test`,
`pytest`, etc.). When in doubt, the CI gate is the source of truth.
