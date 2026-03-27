---
name: codebase-explorer
description: Maps unfamiliar modules or features read-only: finds entry points, key files, data flow, and cross-package dependencies, then returns a concise summary and follow-up file list. Use when the user asks to explore, investigate, or understand how something works, or when orientation is needed before changing code.
---

# Codebase Explorer

## When to use

Orientation in an unfamiliar area before planning or editing code.

**Triggers:** “explore”, “investigate”, “how does X work?”, “where is Y handled?”, mapping a module or feature.

## Inputs

- **Area of interest:** directory, feature name, symbol, or file glob pattern (from the user or conversation).

## Steps

1. **Locate the area:** Use repository search scoped to the interest — semantic search, `glob`, `grep`, or the user’s `@folder` / file references if provided — until you have a concrete set of paths (not guesses). In Excalidraw, common entry points are `packages/excalidraw/index.tsx` for the public API, `packages/excalidraw/components/App.tsx` for editor orchestration, `packages/excalidraw/scene/Renderer.ts` plus `packages/excalidraw/renderer/*` for canvas rendering, `packages/element/src/newElement.ts` and `packages/element/src/mutateElement.ts` for element mutations, `excalidraw-app/collab/Collab.tsx` and `excalidraw-app/collab/Portal.tsx` for collaboration, and `packages/excalidraw/data/library.ts` for library persistence.
2. **Read local context:** Open nearby `README`, package docs, or top-of-file / section comments in that area when present. Map the package responsibility while reading: `packages/common` owns shared constants and helpers, `packages/math` owns geometry primitives, `packages/element` owns element types and rendering helpers, `packages/excalidraw` owns the editor UI and API, and `excalidraw-app` owns the hosted app shell, collaboration transport, persistence, and deployment glue.
3. **Map responsibilities:** List the main files (or small groups) and one-line roles — what each owns, not every export. Prefer concrete domain symbols and subsystems such as `ExcalidrawElement`, `AppState`, `Store`, `History`, `ActionManager`, and the rendering pipeline when deciding what is central.
4. **Trace data flow:** Follow the path **entry point → processing → output / side effects** with citations to real call sites. Common Excalidraw traces are `packages/excalidraw/index.tsx` → `packages/excalidraw/components/App.tsx` → `packages/excalidraw/scene/Renderer.ts` → `renderStaticScene` / `renderInteractiveScene` / `renderNewElementScene` for canvas rendering; pointer or action handlers → `packages/element/src/newElement.ts` or `packages/element/src/mutateElement.ts` → scene or store updates for element changes; and `excalidraw-app/collab/Collab.tsx` / `excalidraw-app/collab/Portal.tsx` → `packages/excalidraw/data/reconcile.ts` / `packages/excalidraw/data/encryption.ts` → Firebase or socket side effects for collaboration.
5. **Dependencies:** Note imports that cross internal packages, apps, or workspaces; align descriptions with the repo’s documented dependency direction `common <- math <- element <- excalidraw <- excalidraw-app` (analysis only). Call out upward imports, suspicious cycles, or app-only code that should not move into the library.
6. **Verify:** Every claim in the summary must be checkable from the code you read; flag uncertainty explicitly instead of inferring.

## Outputs

Deliver in this order:

1. **Summary**

   - **Purpose:** what this part of the system does for users or callers.
   - **Key files:** table or short bullets — path, responsibility.
   - **Data flow:** entry → steps → output (can be a short numbered list).
   - **Dependencies:** notable internal and cross-package imports; note cycles or boundary violations if observed.

2. **Related files for deeper dive:** paths worth opening next, ordered by relevance.

When the user or workspace expects line-accurate references, cite behavior as `startLine:endLine:filepath` blocks so links stay navigable.

## Safety

- **READ-ONLY:** Do not create, edit, or delete files during exploration. Do not run fixers, migrations, or refactors.
- **Evidence-based:** Prefer quoted names, symbols, and paths from the codebase over assumptions.
- If the area is too large, narrow the scope in the summary and list the sub-areas to explore next.
