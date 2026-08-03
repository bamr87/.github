# bamr87/.github — shared automation & release methodology

This repository is the **single source of truth** for CI, versioning, and releases
across every [bamr87](https://github.com/bamr87) repository. It hosts reusable
GitHub Actions workflows, composite actions, workflow templates, and the default
community-health files that every repo inherits.

It is consumed by the [bamr87 dash](https://bamr87.github.io/bamr87/) — the monorepo
that vendors all the projects as submodules.

## The methodology in one picture

```
Conventional Commits  ──►  CI gate (ci.yml)  ──►  merge to main
                                                      │
                                                      ▼
                                  release-please opens a "release PR"
                                  (bumps version file + CHANGELOG.md)
                                                      │
                                          reviewer merges the release PR
                                                      │
                                                      ▼
                            tag vX.Y.Z + GitHub Release  ──►  publish.yml
                                                              (gem / npm / PyPI / GHCR)
```

- **Versioning & changelog** are automated by [release-please](https://github.com/googleapis/release-please):
  commit messages drive the semver bump and the `CHANGELOG.md`. No manual version edits.
- **Releases are reviewable** — every release is a PR you can inspect before it ships.
- **Publishing is ecosystem-aware** — one `publish.yml` detects the stack and pushes to the right registry.
- **Quality is gated** — `ci.yml` blocks merges on tests, lint, build, docs, commit-lint, and CodeQL.

## Reusable workflows

| Workflow | Purpose | Call it with |
|---|---|---|
| [`ci.yml`](.github/workflows/ci.yml) | Merge-to-main gate (detect → test/lint/build/docs/commitlint/codeql) | `uses: bamr87/.github/.github/workflows/ci.yml@main` |
| [`release-please.yml`](.github/workflows/release-please.yml) | Version bump + CHANGELOG + GitHub Release | `uses: bamr87/.github/.github/workflows/release-please.yml@main` |
| [`publish.yml`](.github/workflows/publish.yml) | Publish to gem / npm / PyPI / GHCR + attach assets | `uses: bamr87/.github/.github/workflows/publish.yml@main` |

Composite action: [`detect-stack`](.github/actions/detect-stack/action.yml) — emits
`node`/`ruby`/`gem`/`python`/`jekyll`/`mkdocs`/`docker` booleans + the primary `registry`.

### Toolchain versions come from repository variables

`ci.yml` resolves each language version as **caller input → the calling repo's
`vars.*` → a built-in default**:

| Variable | Default |
|---|---|
| `NODE_VERSION` | `20` |
| `PYTHON_VERSION` | `3.12` |
| `RUBY_VERSION` | `3.3` |

The middle rung is the point: `bamr87` is a personal account, so GitHub's
org-level variables don't exist. The dash declares the canonical values once in
`_data/fleet.yml` (in [bamr87/bamr87](https://github.com/bamr87/bamr87)) and
projects them onto every repo with `dash config sync --apply`, so a version bump
reaches the whole fleet without editing ~40 workflows. Pass an explicit input
only when one repo genuinely needs to differ.

### Two gates, deliberately

This `ci.yml` runs six jobs including a CodeQL matrix — the right gate for
release-grade repos. The dash also publishes a **one-job** gate,
`bamr87/bamr87/.github/workflows/standard-ci.yml`, for experimental and
content repos. They are not redundant: making every repo pay for the fuller gate
would multiply per-push runner count ~6× across the fleet. Pick by tier
(`_data/standards.yml`).

## Adopt the standard in a repo

The fastest path is the dash's rollout tool (from the monorepo root):

```bash
tools/adopt-release.sh <repo>     # or the /adopt-release Claude command
```

…which scaffolds the files below. To do it by hand, copy the two caller workflows
from [`workflow-templates/`](workflow-templates/) into `.github/workflows/` and add a
release-please config:

```
.github/workflows/ci.yml         # → ci.yml@main
.github/workflows/release.yml    # → release-please.yml@main, then publish.yml@main
release-please-config.json       # release-type: ruby | node | python | simple
.release-please-manifest.json    # {".": "<current version>"}
```

### `release-type` by ecosystem

| Ecosystem | `release-type` | Version source release-please maintains |
|---|---|---|
| Ruby gem | `ruby` | `version-file` (e.g. `lib/<gem>/version.rb`); other files via `extra-files` |
| Node / TS | `node` | `package.json` |
| Python | `python` | `pyproject.toml` (+ `__init__.py` via `extra-files`) |
| Jekyll / bash / docs (no package) | `simple` | a `VERSION` file → GitHub Release only |

## Required secrets / setup

Publishing only runs when the ecosystem is detected **and** its credential exists; otherwise
the version bump, changelog, and GitHub Release still succeed.

| Registry | Credential | Notes |
|---|---|---|
| RubyGems | `RUBYGEMS_API_KEY` (repo secret) | |
| npm | `NPM_TOKEN` (repo secret) | published with provenance |
| PyPI | none | **trusted publishing** (OIDC) — configure the publisher on PyPI |
| GHCR | none | uses the built-in `GITHUB_TOKEN` |
| CI on the release PR | `RELEASE_PLEASE_TOKEN` (optional PAT) | so the release PR triggers CI; falls back to `GITHUB_TOKEN` |

Branch protection (require the CI checks before merge) is opt-in via the monorepo's
`tools/protect-branch.sh`.

See [`docs/RELEASES.md`](https://github.com/bamr87/bamr87/blob/main/docs/RELEASES.md) in the
monorepo for the full guide.
