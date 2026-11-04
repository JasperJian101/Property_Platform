# CLAUDE.md

Guidance for AI agents (and humans) working in this repository. Read this first in every session.

## Project

A property listings platform for the South African market (residential sale and rental first),
delivered as a Next.js web app (installable PWA) and, in Phase 2, a native Expo mobile app,
backed by an ASP.NET Core (C#) API.

- Stack and layout: [docs/architecture.md](docs/architecture.md)
- Feature roadmap: [docs/features.md](docs/features.md)
- Competitor research: [docs/research/competitor-analysis.md](docs/research/competitor-analysis.md)
- Feature specs: [docs/specs/](docs/specs/)
- Decision records: [docs/decisions/](docs/decisions/)

**Current state:** documentation only. No application code exists yet.

## How we work (spec-driven, test-backed, vertical slices)

1. **No feature code without an approved spec.** Specs live in `docs/specs/NNNN-short-name.md`
   and start from `docs/specs/0000-template.md`. Only implement specs with `Status: Approved`.
2. **Ask, don't assume.** If a spec is ambiguous, add the question to its *Open questions*
   section and ask the user instead of guessing business rules.
3. **Contract first.** API changes start with the endpoint/DTO shape in the spec, then the
   ASP.NET Core OpenAPI output, then the regenerated TypeScript client in `packages/api-client`.
   Never hand-edit generated client code.
4. **Tests from acceptance criteria.** Every acceptance criterion (`AC-n`) maps to at least one
   automated test. Reference the criterion ID in the test name or a comment.
5. **Vertical slices.** Build one feature end to end (DB → API → UI) per pull request.
   Keep PRs small enough for a human to review.
6. **Record decisions.** Significant or hard-to-reverse choices get a decision record in
   `docs/decisions/` (start from `0000-template.md`).
7. **Keep docs true.** If implementation changes behaviour, update the spec in the same PR.

### Definition of done

- Acceptance criteria covered by passing tests
- CI green (build, lint, type check, tests, API client up to date)
- Spec updated to reflect what was actually built
- No secrets, personal data or real customer data committed

## Repository layout (target)

```
apps/web            Next.js web app
apps/mobile         Expo app (Phase 2)
packages/api-client Generated TypeScript client (do not edit by hand)
packages/core       Shared types, validation, calculators, formatting
packages/ui         Design tokens and shared UI primitives
backend/            ASP.NET Core solution (Api, Application, Domain, Infrastructure, Worker, tests)
docs/               Architecture, features, specs, decisions, research
```

## Commands

Not set up yet. Add build, test, lint and run commands here as the codebase is scaffolded.

## Conventions

- **C#:** nullable reference types on, warnings as errors, file-scoped namespaces, async all the way.
  EF Core migrations live in `PropertyPlatform.Infrastructure`.
- **TypeScript:** `strict` mode, no `any`, Zod for runtime validation.
- **Money:** store ZAR amounts as whole rands in integer columns (`bigint`/`long`); format with
  spaces as thousand separators in the UI (e.g. `R 3 499 900`). No floating point for money.
- **Areas:** store square metres as integers; display conversions (hectares, acres) in the UI only.
- **Dates/times:** store UTC; display in `Africa/Johannesburg` (SAST, UTC+2, no daylight saving).
- **Language:** South African English in UI copy (e.g. "neighbourhood", "colour", "erf").
- **Personal data (POPIA):** collect only what a feature needs; never log phone numbers, emails or
  ID numbers in plain text.

## Domain glossary

| Term | Meaning |
|---|---|
| Erf (pl. erven) | A registered plot of land; "erf size" is the land area |
| Freehold | Buyer owns the land and the building outright |
| Sectional title | Ownership of a unit in a scheme (e.g. a flat); body corporate charges **levies** |
| Rates and taxes | Monthly municipal property charges |
| Levies | Monthly body corporate / HOA charges for sectional title or estates |
| Bond | A home loan (mortgage) |
| Transfer duty | Tax on property transfers, payable by the buyer (not on VAT-registered new developments) |
| Mandate | Seller's agreement with an agency to market a property (sole or open) |
| PPRA | Property Practitioners Regulatory Authority, regulates estate agents |
| FFC | Fidelity Fund Certificate: annual PPRA certificate every practising agent must hold |
| POPIA | Protection of Personal Information Act, SA's data protection law |
| On show | A scheduled open house |
| Flatlet / garden cottage | A separate small dwelling on the property |
