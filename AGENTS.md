<!-- kit: sdlc v0.1.0 · AGENTS.md is the CANONICAL agent instructions file (decision D4, UPS-AGENT-07/08). -->

# .github

> The bamr87 owner-level `.github` repo: the community-health defaults every bamr87 repo inherits, the starter release workflow template, and the reusable release and publish workflows other repos call.

## What this repo is

GitHub serves this repository's community-health files — `CODE_OF_CONDUCT.md`, `CONTRIBUTING.md`, `SECURITY.md`, and the issue forms and pull request template under `.github/` — to every bamr87 repo that has no copy of its own, and offers `workflow-templates/` in every repo's Actions tab. It also hosts the reusable `release-please.yml` and `publish.yml` workflows and the `detect-stack` composite action. There is no application, build or test suite. A change is done when `actionlint` is clean, the README says what changed, and a workflow or action change has been run from one consumer repo first, because every caller pinned to `@main` gets it the moment it merges.

## Stack & commands

```bash
# This repo's only check: lint the reusable workflows (no install, build or test step)
bash <(curl -sSfL https://raw.githubusercontent.com/rhysd/actionlint/main/scripts/download-actionlint.bash) latest /tmp
/tmp/actionlint
# Adopt the standard in a repo, from a bamr87/bamr87 checkout (dry run; add --apply to open the PR)
tools/fanout.sh --kit sdlc --target <repo>
tools/fanout.sh --kit standardize --target <repo> --artifacts ci
```

## Layout

| Path | Holds |
| --- | --- |
| `.github/workflows/` | Reusable workflows called by other repos: `release-please.yml`, `publish.yml` |
| `.github/actions/detect-stack/` | Composite action `publish.yml` uses to pick the registry |
| `.github/ISSUE_TEMPLATE/`, `.github/PULL_REQUEST_TEMPLATE.md` | Default issue forms and PR template every repo without its own inherits |
| `workflow-templates/` | Starter `release.yml` shown in every repo's Actions tab, with its `.properties.json` |
| `CODE_OF_CONDUCT.md`, `CONTRIBUTING.md`, `SECURITY.md` | Default community-health files |

## Conventions

- **Backlog of record:** GitHub Issues on bamr87/.github.
- **Definition of Done:** the checklist in `.github/PULL_REQUEST_TEMPLATE.md` — the same template every bamr87 repo without its own inherits.
- **Decisions:** no `docs/adr/` log in this repo; the fleet decisions it follows (D3, D4, …) are recorded in the hub's `specs/WORK.md`.
- **Commits:** Conventional Commits (`feat fix docs style refactor test chore perf ci build`); the PR title is the squash subject.
- **Branches:** `feature/`, `fix/`, `docs/`, `refactor/`, `test/` or `chore/` off `main`.
- **Changelog:** `CHANGELOG.md` (Keep a Changelog). No release workflow runs in this repo; the `v1.0.0` tag and the floating `v1` exist for callers that pin.
- **Never:** push to `main` directly, commit secrets, put a repo-specific rule in a file every repo inherits, or remove or rename a workflow input, output or secret that callers use.

## Shared-file rules

- A reusable workflow or action change reaches every `@main` caller on merge, and `publish.yml` itself calls `detect-stack@main`. Keep `workflow_call` inputs, outputs and secrets backward compatible.
- A repo's own community-health file replaces the inherited one wholesale; nothing merges. Keep the defaults fleet-generic.
- The fleet source for the community-health files is the hub's community kit (`templates/community/`, which carries the fleet Definition of Done block). Syncing it into this repo is a Fleet Ops change; don't fork the wording here.
- Decision D3: shared workflows live in the hub. The CI gate is the hub's `standard-ci.yml` (this repo's `ci.yml` was retired), and the hub carries its own `release-please.yml`, which the SDLC kit pins at `@v2`. This repo's `release-please.yml` keeps existing callers working until it is retired or shimmed; a release caller of it is a UPS-REPO-21 failure reported as a warning during the rollout. `publish.yml` and `detect-stack` have no hub counterpart yet.

## Fleet context

Member of the bamr87 fleet, registered in the hub's `_data/projects.yml` (slug `dot-github`) but not mounted as a submodule. Standards: <https://github.com/bamr87/bamr87/tree/main/specs>. Hub tooling acts on it: the community kit (`tools/fanout.sh --kit community`) is the source of its health files, and `tools/adopt-release.sh` still scaffolds callers of its `release-please.yml` and `publish.yml`. No scheduled loop or `@claude` handler runs in this repo.

## Standard deviations

none

## Governance

Read [`docs/constitution.md`](docs/constitution.md) before changing anything: it is the highest-precedence rule set here and wins over this file when they disagree. It changes only through a numbered record in [`docs/amendments/`](docs/amendments/), ratified by the maintainer merging the PR; agents propose, never merge. Every user-facing feature has a stable id in [`features/features.yml`](features/features.yml) with `status`, location and the PRD requirement ids it serves; the PRD's "Feature catalog" block is generated from it (`python3 <bamr87/bamr87>/tools/governance_lint.py prd-sync . --write`; check with `… check .`). Never hand-edit that block or `CHANGELOG.md`.
