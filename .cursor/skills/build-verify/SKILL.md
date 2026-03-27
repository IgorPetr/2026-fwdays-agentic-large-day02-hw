---
name: build-verify
description: Runs the right Excalidraw build command (`yarn build`, `yarn build:packages`, or both) after code changes, fixes compilation errors without ts-ignore or test hacks, and reports status with fixes and full output. Use when the user asks to build, verify, or check compilation, or after edits that might affect compilation.
---

# Build & Verify

## When to use

After code changes that might affect compilation.

**Triggers:** user asks to build, verify, or check compilation; or the agent finishes edits that could affect compilation.

## Inputs

- Changed files (from `git diff` or conversation context)

## Steps

1. Pick the build that matches the changed workspace:
   - `packages/common`, `packages/math`, `packages/element`, or `packages/excalidraw` changed → run `yarn build:packages`.
   - `excalidraw-app/`, `public/`, or app-only config changed → run `yarn build`.
   - Cross-workspace changes, or the user asked for full verification → run `yarn build:packages && yarn build`.
2. If the build succeeds → report success and list changed files (when known).
3. If the build fails: a. Read the error output; identify file, line, and error type. b. Open the file at the reported location. c. Fix the issue (types, missing imports, syntax, etc.). d. Re-run the same build command. e. Repeat until the build passes or **3 attempts** are exhausted.

## Excalidraw-specific considerations

- **Canvas and AppState errors:** failures around `StaticCanvasAppState`, `InteractiveCanvasAppState`, `renderStaticScene`, `renderInteractiveScene`, `renderNewElementScene`, or `Renderer` usually originate in `packages/excalidraw/components/App.tsx`, `packages/excalidraw/scene/Renderer.ts`, or `packages/excalidraw/renderer/*`. Fix the caller and the shared type together.
- **Element hierarchy mismatches:** `ExcalidrawElement`, `NonDeletedExcalidrawElement`, or subtype errors usually start in `packages/element/src/types.ts`, then fan out through `packages/element/src/newElement.ts`, `packages/element/src/mutateElement.ts`, guards, actions, and renderers. Fix the source union, guard, or factory instead of adding casts at the call site.
- **Missing export errors:** `No matching export`, `Module has no exported member`, or similar failures often mean a symbol moved without updating the owning package boundary. Check both the importing file and the package entry point: `packages/common/src/index.ts`, `packages/math/src/index.ts`, `packages/element/src/index.ts`, or `packages/excalidraw/index.tsx`.
- **Dependency direction and cycle risk:** keep `common <- math <- element <- excalidraw <- excalidraw-app`. If a change introduces an upward import or a circular dependency, move the shared contract down, use `import type`, extract an interface, or refactor logic rather than crossing the boundary.
- **How to read the log:** package builds run through `scripts/buildBase.js` or `scripts/buildPackage.js`; the first `packages/...` path in the output usually identifies the workspace that broke. esbuild and Vite errors often show the broken importer first, while TypeScript diagnostics usually point at the real mismatch on the first reported file and line.

### Example error triage

1. Missing export

```text
✘ [ERROR] No matching export in "packages/element/src/index.ts" for import "mutateElement"
packages/excalidraw/components/App.tsx:125:2:
```

- Open `packages/excalidraw/components/App.tsx` at the import site.
- Open `packages/element/src/index.ts` and `packages/element/src/mutateElement.ts`.
- If `mutateElement` should be public, export it from `packages/element/src/index.ts`; otherwise switch the import to the correct existing boundary used in that workspace.
- Re-run `yarn build:packages`.

1. Element type mismatch

```text
packages/excalidraw/renderer/staticScene.ts:312:17 - error TS2345:
Argument of type 'ExcalidrawElement' is not assignable to parameter of type 'NonDeletedExcalidrawElement'
```

- Open `packages/excalidraw/renderer/staticScene.ts` at the reported line.
- Trace where that element list is produced and which guard or filter should have narrowed it.
- Open the relevant source type or guard in `packages/element/src/types.ts`, `packages/element/src/typeChecks.ts`, `packages/element/src/newElement.ts`, or `packages/element/src/mutateElement.ts`.
- Fix the narrowing or upstream type definition; do not add `as any`.

1. Upward import or cycle risk

```text
✘ [ERROR] Could not resolve "@excalidraw/excalidraw/types"
packages/element/src/someFile.ts:3:31:
```

- Open the importing `packages/element/src/someFile.ts`.
- Check whether the file is importing upward from `excalidraw`, which breaks the package direction.
- Move the shared type or helper into `packages/common`, `packages/math`, or `packages/element`, or replace it with a local interface plus `import type`.
- Re-run `yarn build:packages`.

## Outputs

- **Build status:** PASS or FAIL
- **Fixes applied:** bullet list of what changed (if any)
- **Full build output:** include the complete terminal output from the final (or failed) run

## Safety

- Do **not** fix errors by adding `@ts-ignore`, `@ts-expect-error`, or broad `any`.
- Do **not** modify test files to silence build failures.
- After **3 failed attempts**, stop, summarize errors, and hand off to the user with the last full output.
