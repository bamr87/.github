# Changelog

All notable changes to this project are documented in this file. The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and versions follow [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Added

- `CHANGELOG.md`, in Keep a Changelog format.
- `release-please.yml`: the release is made by the fleet's release app, a GitHub App. When the caller holds `RELEASE_APP_CLIENT_ID` and `RELEASE_APP_PRIVATE_KEY`, each job mints an installation token for its own repo (contents and pull-requests write, revoked when the job ends) and uses it to tag, to open the release PR and to push the lockfile commit. The order is the app, then `RELEASE_PLEASE_TOKEN`, then `GITHUB_TOKEN`, so a caller that holds only the PAT releases as before. A key without the client ID, or an app that cannot mint a token, fails the run and says why. Both secrets are declared, so a caller under another owner can pass them by name.

### Deprecated

- `release-please.yml`: the `RELEASE_PLEASE_TOKEN` secret, still read where the release app is not provisioned.

### Changed

Merged to `main` since v1.0.0:

- 2026-10-17: docs(readme): align README with the portfolio standard ([#6](https://github.com/bamr87/.github/pull/6))

## [1.0.0] - 2026-10-16

First tagged release. Pin with @v1 (floating major) or @v1.0.0.

- 2026-10-04: chore(ci)!: retire the unused reusable ci.yml gate ([#5](https://github.com/bamr87/.github/pull/5))
- 2026-10-04: docs(contributing): point the CI step at the real gate (standard-ci.yml) ([#4](https://github.com/bamr87/.github/pull/4))
- 2026-10-04: fix(issue-forms): label feature requests `feature` instead of `enhancement` ([#3](https://github.com/bamr87/.github/pull/3))
- 2026-10-03: Add MIT license
- 2026-08-02: feat(ci): resolve toolchain versions from repository variables ([#2](https://github.com/bamr87/.github/pull/2))
- 2026-06-24: fix(release): make gem publish resilient to release-time lockfile drift ([#1](https://github.com/bamr87/.github/pull/1))
- 2026-06-15: feat: standardized CI + release pipeline for all bamr87 repos

[Unreleased]: https://github.com/bamr87/.github/compare/v1.0.0...HEAD
[1.0.0]: https://github.com/bamr87/.github/releases/tag/v1.0.0
