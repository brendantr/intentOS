# Architecture (v1)

This document captures the intended v1 structure per `intentOS.md`, which is
authoritative. It explains why the code is shaped this way so that humans and
agents avoid changes that violate the design.

## High-level architecture

Version 1 is a local-first Next.js + TypeScript + Tailwind CSS + shadcn/ui web
app with no backend. All user data lives in browser local storage.

```
  Home / Start ──► Intent Form ──► Results Dashboard
      (/)           (/intent)          (/results)
                        │                  ▲
                 typed PlanInputs          │ editable, copyable cards
                        ▼                  │
                pure generator functions ──┘
        (Intent Brief · Guardrailed Prompt ·
     Implementation Plan · Verification Checklist)
                        │
                  storage module ◄──► browser local storage
```

### The three product surfaces

1. **Home / Start (`/`)** — introduces the value proposition ("build with
   guardrails before building with AI") and offers simple paths into a new or
   existing project workflow.
2. **Intent Form (`/intent`)** — the core surface. Collects the eleven v1
   planning inputs as a wizard or a structured single-page form grouped by
   basics, constraints, risks, and verification.
3. **Results Dashboard (`/results`)** — presents the four generated artifacts
   as editable, copyable (later exportable) cards, and supports revising
   inputs and regenerating outputs.

### Key data flow

Form inputs are captured as a single typed `PlanInputs` object → pure
generator functions transform it into the four artifacts → the dashboard
renders them as cards → the storage module persists inputs (and any manual
edits to outputs) to local storage. Regeneration is always driven by explicit
state: `PlanInputs` and the current generation mode/settings. There is no
hidden state or extra data source feeding the generators.

## File / folder structure

```
app/                    Routes: / (home), /intent (form), /results (dashboard)
components/ui/          shadcn/ui primitives — generated, unmodified
components/<feature>/   Feature components (form sections, artifact cards)
lib/types.ts            PlanInputs, PlanningPack, and artifact types
lib/generators/         One pure module per artifact (brief, prompt, plan,
                        checklist)
lib/storage.ts          The only module that touches local storage
Docs/                   Intent documentation (this file and its siblings)
intentOS.md             Authoritative product specification
claude.md               Entry point for AI tools
```

Guidelines:

- One generator module per artifact; shared formatting helpers live beside
  them, not inside components.
- Feature components are grouped by surface, not by component type.
- Tests sit next to the module they cover.

## Import / layering rules

1. **`lib/` is the foundation** and must stay framework-free: no React, no
   Next.js, no `window`/`localStorage` access — except `lib/storage.ts`,
   which is the single place browser persistence is touched.
2. **Generators have two modes:**

   - **Deterministic mode (default):** generators behave as pure functions
     (`PlanInputs → artifact`) with no storage or network I/O. The same
     inputs produce the same outputs. This mode must always be available.
   - **AI-assisted mode (explicitly toggled):** generators may call AI models
     via a thin adapter layer to help draft artifact text, but:
       - All prompts and configuration are derived solely from `PlanInputs`
         and the current generation settings (no hidden state).
       - Network calls live only in the adapter; the core generation logic
         remains testable without AI.
       - The structure of the artifacts and inclusion of user-provided
         constraints, risks, and “must not break” items must not depend on AI
         behavior.
3. **`components/` may import `lib/`**, never the reverse. Feature components
   may import `components/ui/`; `components/ui/` primitives import nothing
   from the app.
4. **`app/` routes import components and `lib/`**; nothing imports from
   `app/`.
5. No cross-surface imports between feature folders — shared logic is
   promoted to `lib/` or a shared component, not reached across.
6. No circular imports, and no dependencies beyond the approved stack (see
   `Docs/agent-limits.md`).
