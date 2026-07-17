# IntentOS

## Executive overview

IntentOS is a lightweight planning product for developers who use AI to build software and want more control over the quality, boundaries, and reliability of what gets produced. Its purpose is to move AI-assisted development upstream by helping users define intent, constraints, risks, and verification before they begin implementation.

The core idea behind IntentOS is simple: AI performs better when the human operating it has already clarified the target. Instead of beginning with a vague request and relying on the model to infer scope, architecture, and trade-offs, IntentOS helps users create a structured planning layer that can guide any AI coding workflow. In version one, this planning layer can be generated in two ways: a deterministic mode that relies only on local templates and rules, and an optional AI-assisted mode that uses external models to help draft text while still honoring the user's explicit constraints and verification criteria.

## Product vision

IntentOS aims to become a practical operating layer for intent-driven software creation. The long-term vision is a product that helps individuals and teams turn a rough idea into a clear problem definition, a guarded implementation path, and an explicit verification plan before code generation begins.

This positions IntentOS somewhere between a planning assistant, a prompt-contract generator, and a lightweight build-spec system. It is not a replacement for engineering judgment; it is a tool for making that judgment more explicit, transferable, and reusable.

## Problem statement

AI coding tools are fast, but they often inherit vague instructions, missing constraints, and weak verification criteria from the humans using them. When that happens, teams can generate code quickly while still accumulating ambiguity, hidden assumptions, and brittle implementation decisions.

The problem is not only prompt quality. The larger problem is that many developers begin implementation before they have defined the full shape of the work. That means the AI has to guess about the user, the problem, the environment, the boundaries, and the success criteria. IntentOS exists to reduce that guessing.

## Product thesis

IntentOS is based on the thesis that better AI-assisted engineering comes from clearer human intent. A strong planning layer can improve outcomes across multiple models and agents because it narrows ambiguity, surfaces trade-offs, and gives the user a reference point for review and verification.

In practical terms, the product is designed around five ideas already reflected in modern AI-coding workflows: intent, context, execution, verification, and ownership. IntentOS is responsible for strengthening the first, second, and fourth of those layers so execution becomes safer and ownership becomes more realistic.

## Target users

IntentOS is designed for several overlapping user groups:

- Developers building greenfield projects with AI assistance who need a clearer way to define scope and constraints.
- Students and early-career builders who want structure before asking AI to generate implementation details.
- Experienced engineers who want a reusable planning framework for guiding agents and reviewing generated output.
- Solo builders and small teams who need a lightweight, local-first way to turn fuzzy project ideas into actionable implementation artifacts.

These users may rely on different AI tools, but they share the same need: a model-agnostic way to clarify what they are actually trying to build before they delegate parts of the implementation process.

## Core use case

The primary use case is converting a rough software idea into a structured planning pack that can be reused inside AI-assisted development sessions. The planning pack can be created entirely through deterministic generation from the user's inputs or, when enabled, with AI assistance for drafting sections. In both cases, the structure and included constraints are defined by the user's answers, not inferred implicitly by the model. A user begins with a project idea, answers guided questions, and receives a set of outputs that make future prompting, implementation, and review more disciplined.

The first version of IntentOS is intended to generate four core outputs:

- **Intent Brief**: a concise definition of the product goal, user, problem, and scope.
- **Guardrailed Prompt**: a structured prompt that includes relevant context and explicit boundaries for an AI coding agent.
- **Implementation Plan**: a staged breakdown of how the work should be approached.
- **Verification Checklist**: a list of conditions, risks, and checks that define whether the implementation is acceptable.

## Version 1 scope

Version one is intentionally narrow. It is scoped around a guided form and a results dashboard rather than a full collaborative platform. This keeps the product focused on the highest-leverage user behavior: clarifying intent before implementation begins.

The proposed inputs for the first version are:

1. Project name  
2. Project type: new or existing  
3. What is being built? (include any major components or modules you already have in mind, such as API, UI, background jobs, or data layer)  
4. Who is it for?  
5. What problem does it solve?  
6. What constraints matter? (technical, regulatory, data, performance, or operational)  
7. What must not break? (for example, billing data, auth flows, or existing user paths)  
8. What risks matter? (for example, safety risks, data integrity risks, or coupling risks)  
9. How will success be verified?  
10. What output is desired from the AI? (for example, code, JSON schema, test cases, or documentation)  
11. (Optional) How do you expect to use AI in this project? (for example, generating new code, refactoring existing code, writing tests, or reviewing changes)

The result is a compact workflow that can be completed quickly but still surfaces the planning decisions most often skipped at the start of AI-assisted development.

## Information architecture

The first release is organized around three main product states.

### Home / Start

This entry point introduces the value proposition and provides simple paths into a new or existing project workflow. The page should communicate that IntentOS helps users build with guardrails before building with AI.

### Intent Form

This is the core product surface. It collects the planning inputs through either a wizard-style experience or a structured single-page form grouped by basics, constraints, risks, and verification.

### Results Dashboard

This view presents the generated planning artifacts as editable, copyable, and eventually exportable cards. It should support quick iteration, allowing users to revise inputs and regenerate outputs as their understanding of the project improves.

## Product principles

The product should follow a small number of explicit design and workflow principles.

- **Clarity before code**: the interface should help users define the problem before they touch implementation.
- **Guardrails over guesswork**: the product should expose constraints, risks, and non-negotiables clearly.
- **Verification is required**: success criteria should be part of the workflow, not an afterthought.
- **Model-agnostic by default**: outputs should work with any AI coding tool rather than depending on a single vendor.
- **AI is optional, not mandatory**: the product must remain fully usable and trustworthy without any external model calls; AI assistance is an explicit user choice.
- **Local-first simplicity**: early versions should prioritize speed, privacy, and low friction over enterprise complexity.

## Repository structure and core docs

To align the implementation of IntentOS with its engineering-of-intent philosophy, every IntentOS project should follow a minimal, opinionated documentation structure in the repository:

- A `claude.md` file in the root directory.
- A `Docs/` folder in the root directory containing:
  - `agent-limits.md`
  - `architecture.md`
  - `invariants.md`

The purpose of these documents is to ensure that the way AI tools and agents interact with the codebase is governed by explicit, human-authored intent rather than ad hoc prompts.

### claude.md (root)

`claude.md` defines how Claude (and similar AI coding tools) should be used with this project. It may include:

- The high-level goal of the project and current focus areas.
- Which parts of the codebase are in scope for AI-assisted changes.
- Any constraints, safety rules, or workflows that Claude should respect when proposing code.
- Links or references to the files in `Docs/` that Claude should treat as authoritative for agent behavior, architecture, and invariants.

This file serves as the primary entry point for AI tools, making the project’s intent and boundaries explicit before any code generation occurs.

### Docs/agent-limits.md

`agent-limits.md` describes how agents and AI tools are allowed to operate on the system. Typical content includes:

- Allowed commands and tooling (for example, test runners, linters, build scripts).
- Allowed directories and files for read/write operations.
- Explicit limits on what agents must not do (for example, modifying certain critical files or bypassing review processes).

This document ensures that agent behavior is guardrailed and aligned with the team’s expectations, not inferred implicitly from prompts.

### Docs/architecture.md

`architecture.md` captures the system’s paradigm and code mapping. Typical content includes:

- The main domains or modules and their responsibilities.
- Layering and import rules (for example, which modules may depend on which others).
- Key data flows and integration points.

This document explains why the code is structured the way it is, helping both humans and agents avoid changes that violate the intended design.

### Docs/invariants.md

`invariants.md` defines the system’s stability rules and non-negotiable conditions. Typical content includes:

- Invariants that must never be broken (for example, data integrity guarantees, authorization rules, safety constraints).
- Forbidden transitions or states.
- Dependencies that must not be introduced (for example, cross-domain imports that would create tight coupling).

This document translates the verification mindset of IntentOS into explicit stability contracts that can be used to review AI-generated changes and human refactors.

Together, `claude.md` and the documents in `Docs/` form the core intent layer for the project. They make the project’s goals, boundaries, architecture, and invariants explicit and reusable across AI-assisted development sessions, reinforcing the central idea that better outcomes come from clearer human intent.

## Tech stack

The recommended version-one stack is:

| Layer | Technology | Purpose |
|------|------------|---------|
| App framework | Next.js | Application shell, routing, and fast front-end iteration. |
| Language | TypeScript | Type-safe state and maintainable UI logic. |
| Styling | Tailwind CSS | Fast, consistent interface development. |
| Component system | shadcn/ui or similar | Reusable primitives for forms, cards, and layout. |
| Persistence | Local storage | Lightweight local-first saving without backend complexity in version one. |
| Optional later | Markdown or JSON export | Reuse and sharing of generated planning artifacts. |

This stack supports rapid product iteration while keeping the first release visually polished and technically manageable. A local-first approach also reduces scope by avoiding authentication, permissions, and backend orchestration too early.

## Product outputs in more detail

Each output should feel immediately useful on its own, while also forming part of a coherent planning pack.

Version one supports an explicit toggle between a deterministic generation path and an AI-assisted path. The deterministic path must always be available; AI assistance is an optional enhancement, not a requirement for using the product.

### Intent Brief

This should summarize the user's project in plain language using a consistent structure: what is being built, for whom, why it matters, what the boundaries are, and what the first version includes. At a minimum, the brief should cover: Overview, Primary users, Core responsibilities, and Out of scope for version one. It acts as the anchor document for later decisions and will serve as the introductory context for more detailed architectural and agent-facing documents in later versions of the product.

### Guardrailed Prompt

This output should translate the brief into an AI-ready instruction set with clearly labeled sections: Context, Constraints, Scope boundaries, and Output format. The goal is not just to improve prompt quality, but to make the prompting process more repeatable and reviewable, and to provide the raw material for future agent-facing instructions (for example, the AGENTS/agent-limits layer introduced in later versions).

### Implementation Plan

This should convert the product definition into an ordered path of work, ideally broken into manageable stages or milestones grouped by area (for example, data layer, API, UI, background processing). It helps users move from concept to execution without handing all planning responsibility to the AI, and it provides a starting point for future descriptions of system structure and typical change workflows.

### Verification Checklist

This should define how success will be checked, what risks deserve attention, and what must not regress during implementation, expressed as concrete conditions and checks. Wherever possible, each item should capture the type of risk (for example, safety, data integrity, performance, or UX) alongside the check itself, reinforcing the principle that verification is a distinct engineering activity rather than an assumption and providing structured input for later stability and invariants documents.

## What success looks like

A successful version one of IntentOS should allow a user to go from a fuzzy software idea to a structured, usable planning pack in a short session. It should feel faster than writing all of this from scratch, but more rigorous than jumping directly into prompting an AI.

Early success indicators include:

- Users can complete the planning flow without needing explanation.
- The generated outputs are specific enough to reuse in real coding sessions.
- The workflow helps users identify missing constraints or vague assumptions before implementation begins.
- The product feels lightweight rather than bureaucratic.

## Product boundaries

IntentOS is intentionally not trying to do everything at once.

Version one is not:

- A full project management suite.
- A code editor.
- A model host or AI provider.
- A compliance platform.
- A team collaboration system with deep roles and permissions.

By staying narrow, the product can focus on one high-value job: helping users define better intent before they start delegating work to AI systems.

## Versions and roadmap

### Version 1: Planning pack for new work

Version one of IntentOS focuses on helping users clarify intent before implementation begins for new or early-stage projects. A user completes the guided Intent Form and receives a compact planning pack consisting of an Intent Brief, a Guardrailed Prompt, an Implementation Plan, and a Verification Checklist. These artifacts are designed to be lightweight, human-readable, and immediately reusable inside any AI-assisted development workflow.

### Version 2: Agent Core for serious systems

Version two introduces an advanced layer for users who want to formalize how agents and humans operate on a system. Building on the planning data captured in version one, IntentOS will help users generate an “Agent Core” trio of repository-ready documents:

- `agent-limits.md` — Agent session behavior, including commands and allowed directories, and guidance on how to build, test, and validate changes in the system.
- `invariants.md` — System stability rules, including forbidden transitions and dependencies, and conditions that must never be broken.
- `architecture.md` — The system’s paradigm and code mapping, including domain boundaries and import rules, and the reasoning behind the structure.

The version two experience is intended as a next step for users who have validated the value of structured intent in version one and now want durable, code-adjacent contracts for agents and humans. Wherever possible, version two will reuse the planning data and structure captured in version one—especially the Intent Brief, Guardrailed Prompt, Implementation Plan, and Verification Checklist—so that users do not need to start over when they graduate from simple planning packs to repository-ready specifications.

### Version 3: Existing projects and inference

Version three will extend IntentOS to existing codebases. Instead of starting only from a fresh planning session, the product will be able to inspect an existing project (for example, directory trees and existing documentation) and propose or refine the Agent Core documents for that system. This version will remain local-first and model-agnostic by default, but may use optional AI assistance to infer architecture boundaries, candidate invariants, and agent behaviors from the project’s structure and artifacts.

Across all versions, IntentOS keeps the same job: reducing ambiguity and guesswork in AI-assisted engineering by making human intent, constraints, and verification explicit, reviewable, and reusable.

## Future directions

Once the core planning workflow is solid, several future expansions become reasonable.

Potential directions include:

- Export to Markdown or JSON for direct reuse across tools, including the Agent Core documents introduced in later versions.
- Saved templates for common project types and stacks.
- Comparison views for multiple prompt variants.
- Revision history for intent changes over time.
- Shared team workflows and review states.
- Integration hooks for AI editors or agent environments.

These features should only be added after the core experience proves that structured intent is valuable on its own.

## Immediate next step

Before implementation begins, the most important next task is to define the exact field copy for the Intent Form and the exact text structure for each output card on the Results Dashboard. That work will turn the product from a good concept into a concrete, buildable interface.