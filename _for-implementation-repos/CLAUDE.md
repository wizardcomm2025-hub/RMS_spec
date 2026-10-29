# CLAUDE.md — spec-reading rules for the RMS implementation repos

> **Where this file goes:** these sections are merged into the existing `CLAUDE.md` of `D:\RMS\RMS_API` and `D:\RMS\RMS_next_js\RMS_NextJS` (done 2026-10-28 as the "Specs" section of each). Keep each repo's own project-specific sections; this file adds the spec-reading rules and general behaviour.
>
> The specs live in `D:\RMS\App-Specs` (`../App-Specs/` from `RMS_API`, `../../App-Specs/` from `RMS_NextJS`). That folder has its own `CLAUDE.md`, which governs **writing or editing specs**.

Behavioral guidelines to reduce common LLM coding mistakes. They bias toward caution over speed; for trivial tasks, use judgment.

## 1. Read the Specs First

**Specs are the source of truth. Read them before forming any plan.**

Before starting any feature task:
- Read `App-Specs/README.md` and `App-Specs/project-overview.md` (platform decisions, API conventions, glossary) if you haven't already in this session
- Read `App-Specs/features/<feature>/README.md` and the relevant use-case file(s), including the Platform-notes sub-section for the layer you touch (**RMS API**, **Data — Tenant DB**, **Data — Central DB**, **Integrations**, **Admin dashboard**)
- Read `api-contract.md`, `data-model.md`, `ui-ux.md` and the feature's `test-cases/` for the layers you touch
- For any screen, read `RMS_API/specs/UI_UX_STANDARDS.md`

If a spec is missing, ambiguous, or contradicts another spec, stop and ask. Do not infer.

## 2. Think Before Coding

- State your assumptions explicitly. If uncertain, ask.
- If multiple interpretations exist, present them — don't pick silently.
- If a simpler approach exists, say so. Push back when warranted.

## 3. Simplicity First

Minimum code that solves the problem. No features, abstractions or configurability beyond what the spec asks for.

## 4. Surgical Changes

- Touch only what the approved plan names. Match existing style.
- Don't "improve" adjacent code, comments or formatting; mention unrelated issues instead.
- Remove only the orphans your own change created.

## 5. Goal-Driven Execution

Turn each task into a verifiable goal tied to test IDs from `App-Specs/features/<feature>/test-cases/`, run them, and record actual results in `RMS_API/specs/<feature>/test-results.md`. Never mark a test Pass that was not executed.

## 6. Plan First — Never Execute Without Explicit Approval

- Present the spec changes and the plan (`RMS_API/specs/<feature>/plan.md`), then wait for an explicit "go" before editing application code, UI files or database scripts.
- Approval of a plan covers only the steps in it.

## 7. Always Ask Before Creating a New Branch

Default to the current branch. Ask before any branch-creating command.
