# A/B Test: architecture.mdc

- **Prompt**: "Create a new component for displaying element coordinates"
- **Rule under test**: `.cursor/rules/excalidraw-architecture.mdc` (used as the repository's architecture rule equivalent)

## Method

- **Test A (rule ON)**: evaluated with `.cursor/rules/excalidraw-architecture.mdc` applied alongside the repo's always-on rules.
- **Test B (rule OFF)**: evaluated with the same repository context, but without applying `.cursor/rules/excalidraw-architecture.mdc`.
- **Context inspected for both runs**:
  - `.cursor/rules/conventions.mdc`
  - `.cursor/rules/do-not-touch.mdc`
  - `.cursor/rules/memory-bank.mdc`
  - `.cursor/rules/testing.mdc`
  - `docs/memory-bank.md`
  - `docs/memory/projectbrief.md`
  - `docs/memory/systemPatterns.md`
  - `docs/memory/activeContext.md`
  - `packages/excalidraw/components/Stats/index.tsx`
  - `packages/excalidraw/components/Stats/Position.tsx`
  - `packages/excalidraw/components/Stats/MultiPosition.tsx`
  - `packages/excalidraw/components/Stats/DragInput.tsx`

## Result A (rule ON)

```text
I’ll add a `Coordinates` component under `packages/excalidraw/components/Stats/` and wire it into `packages/excalidraw/components/Stats/index.tsx`, reusing the existing `Position`, `MultiPosition`, and `DragInput` patterns so it stays consistent with current stats editing behavior.

It’ll preserve the existing `packages/excalidraw` architecture by keeping all updates in the current scene/app-state flow, and I’ll cover the new UI with a focused test in the nearest existing stats-related suite.
```

## Result B (rule OFF)

```text
Agent’ll add a dedicated coordinates component under `packages/excalidraw/components/Stats/`, wire it into `packages/excalidraw/components/Stats/index.tsx`, and follow the existing `Position`, `MultiPosition`, and `DragInput` patterns so it works consistently for single and multi-selection.

Agent’ll keep the current rotated/cropped position behavior intact and update the closest tests if the stats UI behavior changes.
```

## Key Differences

- **State management**: Result A is stronger. It explicitly preserves the existing scene/AppState flow already used by `packages/excalidraw/components/Stats/DragInput.tsx`. Result B implies the same by reusing `Position`, `MultiPosition`, and `DragInput`, but it does not explicitly guard against introducing a parallel state pattern.
- **Export style**: No meaningful difference. Both outputs point toward the existing `Stats/` component pattern, where leaf components such as `packages/excalidraw/components/Stats/Position.tsx` and `packages/excalidraw/components/Stats/MultiPosition.tsx` use default exports.
- **Type safety**: No major difference in the generated response text. Both approaches are anchored to the existing typed `Stats` components and typed scene/app-state APIs, so this prompt does not reveal a material type-safety improvement from the rule alone.
- **Adherence to Excalidraw conventions**: Result A is slightly stronger because it explicitly references preserving `packages/excalidraw` architecture and adding a focused nearby test. Result B is still conventionally solid because the surrounding `Stats` implementation already strongly constrains the correct direction.
- **Architectural correctness**: Result A is better. It directly reinforces Excalidraw's editor orchestration model inside `packages/excalidraw`. Result B remains architecturally correct, but mostly because the nearby code context already nudges the implementation toward the right pattern.

## Conclusion

The rule improves consistency, but only modestly for this prompt.

- It **does help prevent undesirable patterns** by explicitly steering the assistant toward the existing scene/AppState architecture and away from architectural drift or parallel global state systems.
- It **does improve consistency with the Excalidraw codebase**, especially in `packages/excalidraw`, where preserving editor orchestration patterns matters.
- For this specific prompt, the difference is not dramatic because the existing `Stats` code already makes the intended implementation style fairly obvious.

Overall verdict: **yes**, the architecture rule is beneficial; **yes**, it improves consistency with Excalidraw conventions; but in this case it acts more like a guardrail than a major behavioral differentiator.
