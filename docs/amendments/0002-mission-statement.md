<!-- kit: governance v0.1.0 -->
# 0002 — Missions

- **Status:** proposed
- **Date:** 2026-11-16
- **Proposed by:** Grok Bot (agent), at the maintainer's request
- **Ratified by:**
- **Affects:** constitution §1; PRD §1.1; feature DOTGITHUB-008
- **Supersedes:** none
- **Related:** [PRD.md](../../PRD.md) §1.1; feature DOTGITHUB-008

## Context

Constitution §1 Mission was a product description (or, for a new constitution, had not been written yet), not a three-mission direction statement. The fleet now requires every repo's constitution §1 to be one direction statement followed by exactly three numbered missions, adopted through a numbered amendment the maintainer ratifies by merging. The maintainer asked for those missions to be comprehensive and epic: bamr87/.github as the ultimate open home for all things fleet-wide GitHub automation and community health. Mission 1 follows the maintainer's wording, adapted to this domain: provide data, tools, resources and transparency for all stakeholders, domain practitioners and the general public. The ambition is the direction, not a claim that the work is already finished. The missions are aligned with this repo's existing constitution and PRD.

## Amendment

Replace constitution §1 with the following, retitled **Missions**:

The bamr87/.github repo's direction is to become the ultimate open home for all things fleet-wide GitHub automation and community health — data, tools, resources and transparency, gathered in one place that anyone can inspect and reuse.

1. **Provide data, tools, resources and transparency for all stakeholders, repository maintainers and practitioners, and the general public.** Owners of bamr87 repositories, and anyone adopting the same release and community-health patterns, are first-class audiences. The aim is a shared, inspectable commons: the same workflows, the same actions, and the same default files for everyone.
2. **Be the single source of truth for versioning and releases across bamr87 repositories: reusable GitHub Actions workflows, composite actions, workflow templates, and the default community-health files every repo inherits.** release-please and publish workflows, plus issue, PR, code of conduct, contributing and security defaults, should be defined once and reused everywhere.
3. **Make those workflows pinable, reviewable and safe to adopt: published majors, documented gates, and templates that behave the same in every inheriting repo.** Callers should be able to pin a workflow to a tag, read what it will do, and trust that community-health files stay current without each repo inventing its own.

These missions set the course; they do not claim the work is finished. They serve the PRD's role for this repository as the owner-level source of release automation and inherited community health.

_Source: [PRD.md](../PRD.md) §1.1._


## Consequences

Agents and reviewers judge fit against these three missions before inventing scope, treating them as the direction of the repo rather than a description of what already ships. The same text is added as PRD §1.1 so the constitution's `_Source:` line stays accurate. The original PRD purpose / overview paragraph is left in place. This PR also aligns the README and AGENTS.md taglines with the missions and links them to constitution §1, and adds feature DOTGITHUB-008 to the feature catalog, traced to requirement MR1. Merging this PR ratifies amendment 0002. Until then the record stays `Status: proposed`.
