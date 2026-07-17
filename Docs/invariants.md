# Invariants

Non-negotiable stability rules for IntentOS v1, translating the product's
verification mindset into explicit contracts. Use this document to review
AI-generated changes and human refactors alike: a change that breaks an
invariant is wrong even if it builds, lints, and tests cleanly. Changing an
invariant requires updating `intentOS.md` first, with explicit human sign-off.

## General invariants

- **Local-first.** By default, user planning data never leaves the device:
  no analytics, telemetry, error reporting, or remote persistence of inputs
  or outputs. The only exception is the explicitly toggled AI-assisted mode,
  where user inputs may be sent to a configured model endpoint solely to help
  draft artifacts, under the constraints in `Docs/agent-limits.md`.
- **Model-agnostic.** Generated artifacts work with any AI coding tool; no
  output format or wording depends on a single vendor.
- **Lightweight, not bureaucratic.** The planning flow stays completable in a
  short session without explanation; features that add ceremony without
  clarifying intent are rejected.
- **v1 stays narrow.** No project management suite, code editor, model
  hosting, compliance features, or roles/permissions.

## Data / state invariants

- Browser local storage is the **single source of truth** for saved planning
  data; there is no second store that can drift from it.
- A planning pack is always **reconstructable from its inputs and the current
  generation mode**:
  - In deterministic mode, the four artifacts (Intent Brief, Guardrailed
    Prompt, Implementation Plan, Verification Checklist) are pure functions
    of `PlanInputs` (same inputs → same outputs).
  - In AI-assisted mode, the structure, sections, and inclusion of
    user-provided constraints, risks, and “must not break” items are still
    strictly determined by `PlanInputs`, even if wording varies between
    runs. AI must not drop or ignore these elements.
- The user can always switch back to deterministic mode. AI assistance is a
  toggle, not a requirement; the product must remain usable and trustworthy
  without any external model calls.
- The four artifacts are generated **together as one pack** — never a partial
  pack with a missing Verification Checklist.
- User input is never silently lost: revising inputs and regenerating must
  not destroy form data, and storage schema changes must migrate existing
  saved packs or fail loudly — never drop them.
- Manual edits to output cards are preserved and distinguishable from
  generated content, so regeneration doesn't silently overwrite user work
  without consent.

## UX / workflow invariants

- The three-surface flow always holds: Home / Start → Intent Form → Results
  Dashboard. A user can always get from a completed form to visible results.
- Every artifact card is **editable and copyable**; nothing generated is
  locked away from the user.
- The user can always revise inputs and regenerate — iteration is a core
  behavior, not an edge case.
- Verification is required, not optional: the workflow never lets a pack be
  presented as complete without success criteria.
- Constraints, risks, and "must not break" answers provided by the user
  always surface in the generated artifacts — guardrails are never dropped
  in generation.

## Architectural invariants

- The layering rules in `Docs/architecture.md` hold: `lib/` is
  framework-free, generators follow the two-mode (deterministic and
  AI-assisted) behavior defined there, and `lib/storage.ts` is the only
  module touching local storage.
- No backend, no authentication, no server-side persistence in v1.
- No dependencies beyond the approved stack (Next.js, TypeScript, Tailwind
  CSS, shadcn/ui) without explicit approval — no cross-domain imports or
  coupling introduced to work around the layering.
- `components/ui/` primitives remain unmodified shadcn output; product
  behavior lives in feature components and `lib/`, never patched into
  primitives.
- TypeScript stays strict: the `PlanInputs` and artifact types are the
  contract between form, generators, storage, and dashboard — no `any`
  escape hatches across that boundary.
