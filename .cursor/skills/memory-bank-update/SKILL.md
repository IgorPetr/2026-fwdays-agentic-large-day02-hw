---
name: memory-bank-update
description: Updates docs/memory/ with verified, source-backed facts after project changes. Use when the user asks to update the memory bank, sync documentation, refresh project context, or align docs/memory with the codebase.
---

# Memory Bank Update

## When to use

After meaningful changes: new features, refactors, architecture shifts, dependency or tooling updates, milestones, or when the user asks to sync or refresh Memory Bank docs.

**Triggers:** “update memory bank”, “sync docs”, “refresh project docs”, “align memory bank with code”.

## Inputs

- **What changed:** from `git diff`, recent commits, conversation, or an explicit user description.

Canonical layout and file roles: [docs/memory-bank.md](../../../docs/memory-bank.md) (read order and table).

## Steps

1. **Scope changes:** Run `git diff --stat HEAD~5` (or `git log -5 --oneline` / `git diff main...HEAD` if history is shallow or the branch diverged). Prefer the range that reflects the work being documented; use the user’s description when git history is not representative.
2. **Read before writing:** Open only the Memory Bank files that may need edits, following the read order in `docs/memory-bank.md`.
3. **Route updates** (adjust to what actually changed; overlap is OK when several areas moved):
   - **New feature / milestone / workflow status** → `progress.md`, `activeContext.md`
   - **Architecture, boundaries, patterns** → `systemPatterns.md`, `decisionLog.md` when a durable decision was made or reversed
   - **Stack, tooling, commands, runtime** → `techContext.md`
   - **Scope, audience, goals** → `projectbrief.md`, `productContext.md` for user-facing behavior and UX
4. **Verify:** Every technical claim must match current source, `package.json`/workspace config, or other repo artifacts you actually checked.
5. **Size:** Keep each file **under 200 lines**; if a file would exceed that, compress with bullets and links to code paths instead of long prose.

## Excalidraw change → Memory Bank routing examples

- **Element architecture changes:** changes in `packages/element/src/*`, especially `types.ts`, `newElement.ts`, `mutateElement.ts`, renderers, or frame and arrow logic → update `docs/memory/systemPatterns.md`; also update `docs/memory/progress.md` if the feature reach or stability changed.
- **Canvas feature or rendering changes:** changes in `packages/excalidraw/components/App.tsx`, `packages/excalidraw/scene/Renderer.ts`, `packages/excalidraw/renderer/*`, or `packages/excalidraw/index.tsx` → update `docs/memory/progress.md`; also update `docs/memory/productContext.md` when behavior is user-visible and `docs/memory/systemPatterns.md` when the rendering pipeline or state flow changed.
- **Collaboration, encryption, or security boundary changes:** changes in `excalidraw-app/collab/*`, `excalidraw-app/data/firebase.ts`, `packages/excalidraw/data/encryption.ts`, or `packages/excalidraw/data/reconcile.ts` → update `docs/memory/techContext.md`; add `docs/memory/decisionLog.md` when the protocol, trust boundary, or transport decision changed.
- **Collaboration protocol or hotspot changes:** if the work changes active collaboration workflows, operational guidance, or current hot areas without creating a durable new architectural decision, update `docs/memory/activeContext.md`.
- **Tooling and workspace workflow changes:** changes under `.cursor/`, workspace build scripts, or package boundaries → update `docs/memory/progress.md` and `docs/memory/techContext.md` only when the durable facts changed.
- **Trigger rule:** capture each durable fact once in the smallest correct set of Memory Bank files; do not copy the same note into `progress.md`, `systemPatterns.md`, and `techContext.md` unless each file needs a distinct fact.

## Outputs

1. **Updated files:** List paths under `docs/memory/` that changed.
2. **Summary:** What factual deltas you recorded and why (short).

## Safety

- Do **not** delete or rewrite manually curated content without explicit user consent; add or adjust adjacent to preserve intent.
- Do **not** add guesses, roadmaps, or “probably” — only verified facts.
- Do **not** spam the whole Memory Bank: touch only files whose facts changed, per `docs/memory-bank.md`.
- If `docs/technical/decisions.md` or other docs duplicate decisions, prefer updating `docs/memory/decisionLog.md` for durable decisions and keep duplication minimal.
