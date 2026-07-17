# Agent Limits

How AI agents and tools are allowed to operate on this system. These limits
are human-authored intent, per `intentOS.md`: agent behavior is guardrailed
here, not inferred from prompts. When a task conflicts with a limit, stop and
surface the conflict rather than working around it.

## Allowed commands

| Command | Purpose |
| --- | --- |
| `npm run dev` | Local dev server for manual verification |
| `npm run build` | Confirm the app compiles for production |
| `npm run lint` | Lint before proposing changes |
| `npm test` | Run the test suite |
| `npx shadcn@latest add <component>` | Add approved UI primitives |
| `git status` / `diff` / `log` / `show` | Read-only repository inspection |
| File read / search / list tools | Always allowed |

Commands not listed here — including `npm install`, `git commit`, `git push`,
and file deletion — require explicit user approval before each use.

## Allowed directories

- **Read/write:** application source (`app/`, `components/`, `lib/`, and their
  tests), `Docs/`, and `claude.md`.
- **Read-only:** `intentOS.md`, `README.md`, and configuration files
  (`package.json`, `tsconfig.json`, `next.config.*`, `tailwind.config.*`) —
  propose config changes as a diff for review instead of applying them.
- **Never touch:** `.git/` internals, environment files, anything outside the
  repository root.

## Forbidden operations

- Sending user planning data anywhere **except** through the explicit,
  user-toggled AI-assisted mode:
  - No analytics, telemetry, error reporting, or remote persistence of
    inputs or outputs.
  - When AI-assisted mode is enabled, user planning data may be sent only to
    the configured model endpoint for the sole purpose of drafting artifacts,
    never for logging, training, or unrelated processing.
- Adding backend services, databases, authentication, or accounts (explicitly
  out of scope for v1).
- Adding dependencies outside the approved stack (Next.js, TypeScript,
  Tailwind CSS, shadcn/ui) without prior approval.
- Rewriting git history, pushing, or deleting branches without instruction.
- Building v2/v3 roadmap features (Agent Core generation, existing-project
  inference) under the guise of a v1 task.
- Weakening or deleting tests, lint rules, or type checks to make a change
  pass.

## Review expectations for high-impact changes

The following require explicit human review before merge — the agent's
deliverable is a proposed diff plus a summary of risks and mitigations, not an
applied-and-committed change:

- Changes to the local-storage schema or keys (risk: silent loss of users'
  saved planning packs).
- Changes to the generator logic or text structure of the four output
  artifacts (Intent Brief, Guardrailed Prompt, Implementation Plan,
  Verification Checklist) — these are the product.
- Changes to Intent Form fields or field copy (the spec treats exact copy as
  a deliberate product decision).
- Any new dependency, any configuration change, any change to build tooling.
- Anything that touches how data enters or leaves the browser.

For each, state which invariants from `Docs/invariants.md` were checked and
how the change was verified.
