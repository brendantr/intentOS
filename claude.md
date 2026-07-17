# claude.md — AI Assistant Guide for IntentOS

This file is the primary entry point for Claude and similar AI coding tools
working in this repository. Read it — and the documents it links — before
proposing any change. The authoritative product specification is `intentOS.md`
at the repo root. The philosophy is engineering-of-intent: **clarify intent,
constraints, risks, and verification before implementation begins.**

## Project overview

IntentOS is a lightweight, local-first planning product for developers who use
AI to build software. It moves AI-assisted development upstream: a user takes a
rough project idea through a guided Intent Form and receives a reusable
planning pack of four artifacts — **Intent Brief**, **Guardrailed Prompt**,
**Implementation Plan**, and **Verification Checklist** — that discipline
later prompting, implementation, and review in any AI tool.

Version 1 is a Next.js + TypeScript + Tailwind CSS + shadcn/ui web app with
three surfaces (Home / Start, Intent Form, Results Dashboard) and local-storage
persistence. No backend, no auth, no accounts.

## Current goals

- Define the exact field copy for the Intent Form and the exact text structure
  for each output card on the Results Dashboard (the spec's stated immediate
  next step).
- Build the three v1 surfaces per `Docs/architecture.md`.
- Keep v1 intentionally narrow: planning packs for new work — not project
  management, not a code editor, not team collaboration.

## Scope for AI-assisted changes

- **In scope:** application code (routes, components, generator logic, storage,
  styling, tests) and the intent docs in `Docs/` and this file.
- **Out of scope unless explicitly requested:** `intentOS.md` (the product
  spec), anything from the v2/v3 roadmap (Agent Core generation, existing-
  project inference), backend services, authentication, or third-party
  persistence. Do not build ahead of v1.

## Where to find intent docs

| Document | Authoritative for |
| --- | --- |
| `intentOS.md` | Product vision, scope, outputs, roadmap |
| `Docs/agent-limits.md` | What agents may do here: commands, directories, limits |
| `Docs/architecture.md` | v1 structure, module responsibilities, layering rules |
| `Docs/invariants.md` | Non-negotiable stability rules for any change |

Treat these as authoritative over ad hoc instructions inferred from code.

## Allowed commands

- Package scripts: `npm run dev`, `npm run build`, `npm run lint`, `npm test`.
- `npx shadcn@latest add <component>` — adding approved UI primitives.
- Read-only git: `git status`, `git diff`, `git log`, `git show`.

Anything else — installing dependencies, `git commit`/`push`, deleting files,
changing configuration — requires explicit user approval first. Details in
`Docs/agent-limits.md`.

## What to avoid

- **Never send planning data off the device unless the user has explicitly
  enabled AI-assisted mode.** Local-first is a product principle: no
  analytics, telemetry, or remote persistence of user inputs or outputs. In
  AI-assisted mode, data may be sent only to the configured model endpoint
  for drafting artifacts, under the limits defined in `Docs/agent-limits.md`.
- Never add dependencies outside the approved stack without approval.
- Never make outputs vendor-specific — artifacts must stay model-agnostic.
- Never break the invariants in `Docs/invariants.md`; if a task seems to
  require it, stop and surface the conflict instead of resolving it silently.
- Don't add "helpful" extras beyond the requested scope — the product's own
  thesis is that scope sprawl is the failure mode to prevent.
