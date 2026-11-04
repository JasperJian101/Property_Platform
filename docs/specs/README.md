# Feature Specs

One spec per feature (or vertical slice), named `NNNN-short-name.md`. Start from
[0000-template.md](0000-template.md).

## Lifecycle

| Status | Meaning |
|---|---|
| Draft | Being written; open questions remain |
| In review | Waiting for product-owner approval |
| Approved | Ready to build; changes need sign-off |
| Implemented | Built and merged; spec reflects what shipped |
| Superseded | Replaced by a later spec (link to it) |

## Rules

- Acceptance criteria are numbered (`AC-1`, `AC-2`, …) and must each be testable.
- Every acceptance criterion maps to at least one automated test that references its ID.
- Unresolved business questions go in *Open questions*. The spec can't be approved until they are answered.

## Index

| Spec | Feature | Status |
|---|---|---|
| [0001](0001-agent-manages-listings.md) | F-03, F-04 Agent creates and manages listings | Draft |
