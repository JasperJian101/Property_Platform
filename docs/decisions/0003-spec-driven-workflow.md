# 0003: Spec-driven, test-backed development in vertical slices

- **Status:** Accepted
- **Date:** 2026-11-04

## Context

Much of the code will be written by an AI agent, which keeps no memory between sessions and can drift
when it has to make assumptions. The product owner holds the domain knowledge.

## Decision

- Each feature starts with a short spec in `docs/specs/` that the product owner approves.
- API contracts are defined before implementation.
- Acceptance criteria become automated tests.
- Work ships as small, end-to-end vertical slices, one pull request each, gated by CI.
- Project context lives in `CLAUDE.md`, and decisions are recorded in `docs/decisions/`.

## Alternatives considered

- **Big up-front specification:** too slow, and would be wrong in places.
- **Ad-hoc prompting without specs:** fast at first, but loses context and reasoning between sessions.

## Consequences

- There is a short review step before each feature is built.
- Specs and tests document the system, and stay accurate because PRs must update them.
