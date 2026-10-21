# PRD — .github

> Product requirements for [`bamr87/.github`](https://github.com/bamr87/.github).
> Derived from the README, agent docs, config and code in this repo. Anything not
> stated in those sources is marked **TBD**; success metrics are proposals.

## 1. Overview / problem

The single source of truth for versioning and releases across bamr87 repositories: reusable GitHub Actions workflows (`release-please.yml`, `publish.yml`), composite actions, workflow templates, and default community-health files every repo inherits.

## 2. Target users

- Every bamr87 repository (and its maintainers) consuming release workflows
- Contributors who see the default CONTRIBUTING / CODE_OF_CONDUCT / SECURITY files

## 3. Goals and non-goals

**Goals**

- One consistent release-please based release process
- Simple adoption via the starter `release.yml` template
- Default community-health files

**Non-goals**

- Per-repo CI (the old shared `ci.yml` is retired)

## 4. Core features (current)

- Reusable workflows: `release-please.yml`, `publish.yml`
- Composite actions under `.github/actions/`
- Starter workflow `workflow-templates/release.yml`
- Issue/PR templates, CODE_OF_CONDUCT, CONTRIBUTING, SECURITY

## 5. Requirements

**Functional**

- Consumers select `release-type` by ecosystem
- Required secrets documented in README

**Non-functional**

- Backward-compatible workflow inputs for consumers: TBD (versioning policy)

## 6. Success metrics (proposed — not defined in sources)

- Repos adopted the standard
- Release workflow failure rate

## 7. Roadmap / open questions

- Workflow version pinning policy for consumers: TBD

## 8. Links

- [README.md](README.md) · [CONTRIBUTING.md](CONTRIBUTING.md) · [SECURITY.md](SECURITY.md) · [CHANGELOG.md](CHANGELOG.md)
