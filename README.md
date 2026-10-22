# bamr87/.github — shared automation & release methodology

> Shared reusable GitHub Actions workflows, release-please setup and community health files for every bamr87 repo.

[![License](https://img.shields.io/github/license/bamr87/.github)](LICENSE)

## Overview

This is the owner-level `.github` repository for every [bamr87](https://github.com/bamr87) repository. It hosts the reusable release and publish workflows, a composite action, a starter workflow template, and the default community-health files that every repo inherits.

It is consumed by the [bamr87 dash](https://bamr87.github.io/bamr87/) — the monorepo that vendors all the projects as submodules.

Under the fleet's decision D3, shared workflows are moving to the hub: the CI gate is already the hub's `standard-ci.yml`, and the hub now carries its own `release-please.yml`. See [Reusable workflows](#reusable-workflows) for what that means for each file here.

## Features

- **Inherited defaults**: `CODE_OF_CONDUCT.md`, `CONTRIBUTING.md`, `SECURITY.md`, issue forms and a pull request template apply to every bamr87 repo that has no copy of its own.
- **Reusable release workflow**: `release-please.yml` turns Conventional Commits into a release PR, a version bump, `CHANGELOG.md` and a GitHub Release.
- **Ecosystem-aware publishing**: `publish.yml` detects the stack with the `detect-stack` action and publishes to RubyGems, npm, PyPI or GHCR.
- **Starter workflow**: `workflow-templates/release.yml` appears in the Actions tab of every bamr87 repo.

## Quick start

Nothing to install: a repo picks up the community-health defaults automatically. The block lints this repo's workflows, then adopts the release and CI standard in another repo from a [bamr87/bamr87](https://github.com/bamr87/bamr87) checkout:

```bash
# This repo's only check: lint the reusable workflows (no install, build or test step)
bash <(curl -sSfL https://raw.githubusercontent.com/rhysd/actionlint/main/scripts/download-actionlint.bash) latest /tmp
/tmp/actionlint
# Adopt the standard in a repo, from a bamr87/bamr87 checkout (dry run; add --apply to open the PR)
tools/fanout.sh --kit sdlc --target <repo>
tools/fanout.sh --kit standardize --target <repo> --artifacts ci
```

## The methodology in one picture

```
Conventional Commits  ──►  CI gate (standard-ci.yml)  ──►  merge to main
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

- **Versioning & changelog** are automated by [release-please](https://github.com/googleapis/release-please): commit messages drive the semver bump and the `CHANGELOG.md`. No manual version edits.
- **Releases are reviewable** — every release is a PR you can inspect before it ships.
- **Publishing is ecosystem-aware** — one `publish.yml` detects the stack and pushes to the right registry.
- **Quality is gated** by the shared CI gate, which lives in the dash: [`bamr87/bamr87/.github/workflows/standard-ci.yml`](https://github.com/bamr87/bamr87/blob/main/.github/workflows/standard-ci.yml) (stack-detected lint, test and build).

## Reusable workflows

| Workflow | Purpose | Call it with |
|---|---|---|
| [`release-please.yml`](.github/workflows/release-please.yml) | Version bump + CHANGELOG + GitHub Release | `uses: bamr87/.github/.github/workflows/release-please.yml@main` |
| [`publish.yml`](.github/workflows/publish.yml) | Publish to gem / npm / PyPI / GHCR + attach assets | `uses: bamr87/.github/.github/workflows/publish.yml@main` |

Composite action: [`detect-stack`](.github/actions/detect-stack/action.yml) — emits `node`/`ruby`/`gem`/`python`/`jekyll`/`mkdocs`/`docker` booleans + the primary `registry`.

**Status under decision D3.** The hub carries its own copy of the release logic, [`bamr87/bamr87/.github/workflows/release-please.yml`](https://github.com/bamr87/bamr87/blob/main/.github/workflows/release-please.yml), which the SDLC kit pins at `@v2`. The copy here keeps existing callers working until it is retired or shimmed, and the fleet standard reports a release caller of it as a UPS-REPO-21 warning during the rollout. `publish.yml` and `detect-stack` have no hub counterpart yet: a repo that publishes chains `publish.yml` off the release job's `release_created`, `tag_name` and `version` outputs.

### Retired: `ci.yml`

This repo used to host a six-job reusable gate, `ci.yml` (detect → test/lint/build/docs/commitlint/CodeQL), plus a `workflow-templates/ci.yml` caller for it. Nothing called it: on 2026-10-03 the workflow files of all bamr87, year-of-ai and ai-world-view repos were listed and none referenced it, while 19 repos call the dash's `standard-ci.yml`. It was removed to leave one shared gate. Recover it from git history if a fuller gate is wanted again.

## Adopting by hand

The hub's older rollout tool, `tools/adopt-release.sh <repo>` (or the `/adopt-release` Claude command), scaffolds callers of this repo's `release-please.yml` and `publish.yml`. To do it by hand, copy the release caller from [`workflow-templates/`](workflow-templates/) and the CI caller from the dash's [`templates/standard-ci/ci.yml`](https://github.com/bamr87/bamr87/blob/main/templates/standard-ci/ci.yml) into `.github/workflows/`, and add a release-please config:

```
.github/workflows/ci.yml         # → bamr87/bamr87 standard-ci.yml
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

Publishing only runs when the ecosystem is detected **and** its credential exists; otherwise the version bump, changelog, and GitHub Release still succeed.

| Registry | Credential | Notes |
|---|---|---|
| RubyGems | `RUBYGEMS_API_KEY` (repo secret) | |
| npm | `NPM_TOKEN` (repo secret) | published with provenance |
| PyPI | none | **trusted publishing** (OIDC) — configure the publisher on PyPI |
| GHCR | none | uses the built-in `GITHUB_TOKEN` |
| CI on the release PR | `RELEASE_PLEASE_TOKEN` (optional PAT) | so the release PR triggers CI; falls back to `GITHUB_TOKEN` |

Branch protection (require the CI checks before merge) is opt-in via the monorepo's `tools/protect-branch.sh`.

See [`docs/RELEASES.md`](https://github.com/bamr87/bamr87/blob/main/docs/RELEASES.md) in the monorepo for the full guide.

## Project structure

| Path | What it is |
|---|---|
| `.github/workflows/` | Reusable workflows called by other repos: `release-please.yml` and `publish.yml` |
| `.github/actions/detect-stack/` | Composite action `publish.yml` uses to pick the registry |
| `.github/ISSUE_TEMPLATE/`, `.github/PULL_REQUEST_TEMPLATE.md` | Default issue forms and pull request template |
| `workflow-templates/` | Starter `release.yml` offered in the Actions tab of every bamr87 repo |
| `CODE_OF_CONDUCT.md`, `CONTRIBUTING.md`, `SECURITY.md` | Default community health files inherited by repos that do not define their own |

## Development

There is no build step or test suite here; `actionlint` (in [Quick start](#quick-start)) is the only check. A change to a reusable workflow affects every repo that calls it at `@main`, so try it from one consumer repo before merging. Conventions and the rules for changing inherited files live in [AGENTS.md](AGENTS.md).

## Contributing

Contributions are welcome via pull request. Use [Conventional Commits](https://www.conventionalcommits.org/) and keep `actionlint` clean. See [CONTRIBUTING.md](CONTRIBUTING.md) for the workflow; AI agents start with [AGENTS.md](AGENTS.md).

## Changelog

Notable changes are recorded in [CHANGELOG.md](CHANGELOG.md); the full record is in the [commit history](https://github.com/bamr87/.github/commits/main). Tagged versions are on the [releases page](https://github.com/bamr87/.github/releases).

## License

Released under the [MIT License](LICENSE).

---

<!-- bamr87-portfolio-footer -->
Part of the [bamr87 portfolio](https://github.com/bamr87) — browse every project on [Amr's GitHub profile](https://github.com/bamr87).
