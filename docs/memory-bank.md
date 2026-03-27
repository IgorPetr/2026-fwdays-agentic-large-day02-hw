# Memory Bank Pattern

The canonical project Memory Bank lives in `docs/memory/`.

## Purpose

- Preserve durable, project-specific context for future agent sessions.
- Reduce repeated repository re-discovery for architecture, product, and workflow questions.
- Keep long-lived context in the Memory Bank instead of duplicating it across prompts.

## Read Order

1. `docs/memory/projectbrief.md`
2. `docs/memory/productContext.md`
3. `docs/memory/systemPatterns.md`
4. `docs/memory/techContext.md`
5. `docs/memory/activeContext.md`
6. `docs/memory/progress.md`
7. `docs/memory/decisionLog.md`

## Update Rule

- After each project change, update the affected file or files in `docs/memory/` before wrapping up the task.
- Update only the files whose facts changed; do not make broad, redundant edits across the whole Memory Bank.
- Keep updates concise, factual, and verified against the current repository state.
- When work is broad, ambiguous, or architecture-affecting, read the relevant Memory Bank files first.

## File Guide

| File | Update when |
| --- | --- |
| `docs/memory/projectbrief.md` | Project scope, artifacts, audience, or top-level goals change |
| `docs/memory/productContext.md` | User workflows, UX goals, or product behavior change |
| `docs/memory/systemPatterns.md` | Architecture, package boundaries, state patterns, or collaboration/rendering patterns change |
| `docs/memory/techContext.md` | Stack, tooling, commands, runtime constraints, or deployment details change |
| `docs/memory/activeContext.md` | Active workstreams, hotspots, contributor guidance, or near-term focus changes |
| `docs/memory/progress.md` | Completion status, in-progress work, or repository workflow status changes |
| `docs/memory/decisionLog.md` | Durable architectural or process decisions change |

## Writing Rules

- Prefer short, source-backed updates over narrative summaries.
- Link to the most relevant files or docs when deeper context matters.
- Avoid duplicating the same note across multiple Memory Bank files.
- Keep Memory Bank content structured and easy to skim.
