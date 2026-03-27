# AGENTS.md

## Project Overview

Excalidraw is an open-source, browser-based collaborative whiteboard with a hand-drawn visual style. This monorepo ships both the hosted web app (`excalidraw-app`) and the published React library (`@excalidraw/excalidraw`). The library owns the editor, rendering, actions, and public API; the app adds collaboration, persistence, sharing, PWA behavior, and monitoring. Keep durable project context in the Memory Bank instead of expanding this file with fast-changing details.

## Memory Bank

- Start with `docs/memory-bank.md`.
- Canonical long-lived context lives in `docs/memory/`.
- Read the relevant Memory Bank files before broad, ambiguous, or architecture-affecting work.
- After each project change, update the affected `docs/memory/*.md` files in the same task.

## Tech Stack

- Workspace: Yarn `1.22.22`, Node `>=18`, TypeScript `5.9.3`
- Frontend: React `19`, `react-dom` `19`, Vite `5`
- Testing: Vitest `3`, Testing Library, `vitest-canvas-mock`, `fake-indexeddb`
- Rendering: `roughjs`, `perfect-freehand`
- State/UI: React class-based editor core plus Jotai for isolated UI state
- Collaboration in app only: Socket.IO, Firebase, AES-GCM encryption

## Project Structure

- `excalidraw-app/` - hosted web app shell: collab, share, persistence, PWA, Sentry
- `packages/excalidraw/` - published React editor component and public API
- `packages/element/` - element model, rendering, bounds, collision, deltas
- `packages/math/` - geometry primitives and coordinate types
- `packages/common/` - shared constants, colors, utilities
- `packages/utils/` - standalone file, compression, encoding, and font helpers
- `examples/` - integration demos
- `docs/` - Memory Bank, product docs, and technical docs

Dependency direction: `common <- math <- element <- excalidraw <- excalidraw-app`

## Key Commands

```bash
yarn start                # run excalidraw-app locally
yarn build                # build the web app
yarn build:packages       # build common -> math -> element -> excalidraw
yarn start:example        # build packages, then run browser-script example

yarn test:app --watch=false
yarn test:typecheck
yarn test:code
yarn test:other
yarn test:all
yarn test:update          # update snapshots
yarn fix                  # prettier + eslint --fix
```

## Architecture

- `packages/excalidraw/components/App.tsx` is the editor core. It is intentionally a React class component because it coordinates `AppState`, `scene`, `store`, `history`, `renderer`, and `actionManager`.
- Elements live in `Scene`, not inside React state. The Store computes deltas and emits `DurableIncrement` / `EphemeralIncrement`; History builds undo and redo from inverse deltas.
- Rendering uses `StaticCanvas`, `NewElementCanvas`, `InteractiveCanvas`, plus SVG overlays, viewport culling, and per-element offscreen canvas caching.
- `roughjs` provides deterministic sketch rendering via element `seed`; freedraw uses `perfect-freehand`.
- Collaboration is transport-agnostic in the library. `excalidraw-app/collab/` adds Socket.IO, Firebase persistence, and AES-GCM encryption.
- Remote collaboration updates must use the non-history path (`CaptureUpdateAction.NEVER` or equivalent) so they do not pollute local undo and redo.

## Conventions

- Use Yarn workflows and existing repository scripts; do not switch instructions to npm or pnpm.
- Respect package boundaries and `common <- math <- element <- excalidraw <- excalidraw-app`.
- Use TypeScript, `import type` for type-only imports, and existing helpers before adding abstractions or dependencies.
- In `packages/excalidraw`, prefer direct relative imports over local barrel imports.
- Do not import from plain `jotai` where the repo expects `editor-jotai` or `app-jotai`.
- For new UI, prefer small functional components with hooks; when editing the core editor, preserve the existing class-based architecture.
- Add or update the closest existing tests for behavior changes and keep them deterministic.

## Key Hotspots

- `packages/excalidraw/components/App.tsx` - core editor orchestration
- `packages/excalidraw/history.ts` and `packages/excalidraw/store.ts` - undo, redo, and delta flow
- `packages/excalidraw/data/reconcile.ts` - collaboration merge logic
- `packages/element/src/types.ts` - element model and type changes
- `packages/element/src/elbowArrow.ts` - complex arrow routing
- `excalidraw-app/collab/Collab.tsx` and `excalidraw-app/collab/Portal.tsx` - collab lifecycle and encrypted transport

## Validation and PR Workflow

- Preferred local validation: `yarn fix && yarn test:all`
- If snapshots intentionally change, run `yarn test:update`
- Run `yarn build:packages` when package outputs or package boundaries change
- Open PRs against `master`
- PR titles are expected to follow semantic or conventional style such as `feat:`, `fix:`, `docs:`, or `refactor:`

## Do-Not-Touch / Constraints

- Do not move Socket.IO or Firebase collaboration code into `packages/excalidraw`; keep transport-specific logic in `excalidraw-app`.
- Do not weaken encryption, URL sanitization, share-link handling, or other security boundaries.
- Do not hand-edit generated output such as `dist/`, `build/`, coverage folders, or `node_modules`.
- Do not change deployment, release, or env files unless the task is explicitly about them.
- Do not modify tests, snapshots, or coverage thresholds just to hide regressions.
- Do not add secrets, tokens, or credentials to tracked files.

## Related Docs

- `docs/memory-bank.md` - Memory Bank workflow
- `docs/memory/` - canonical long-lived project context
- `docs/technical/dev-setup.md` - setup, commands, and validation
- `docs/technical/architecture.md` - full architecture and data flow
- `docs/product/PRD.md` - product requirements and feature scope
