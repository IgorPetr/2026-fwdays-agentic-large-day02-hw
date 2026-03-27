# Create Component

Generate a React component that fits Excalidraw's codebase and surrounding module patterns.

## Inputs

- Use the user's request, selected files, and current folder to infer the component name, responsibility, props, and destination.
- If the destination or responsibility is unclear, inspect nearby components first and follow the closest local pattern.
- Ask one short clarifying question only if an essential detail cannot be inferred safely.

## Implementation rules

- Use TypeScript and a functional component.
- Define a typed props interface near the component, usually `ComponentNameProps` or `Props`.
- Keep the component focused on one responsibility.
- Prefer hooks for local state, derived values, and side effects.
- Use semantic HTML and accessible labels, roles, and keyboard behavior for interactive UI.
- Add only short comments for non-obvious logic.

## Excalidraw conventions

- Respect package direction `common <- math <- element <- excalidraw <- excalidraw-app`.
- In `packages/excalidraw`, use direct relative imports rather than local barrel imports.
- Use `import type` for type-only imports.
- Reuse existing primitives, icons, and helpers before inventing new ones.
- If conditional class names are needed, use `clsx`.
- If nearby components use SCSS, prefer a sibling `./ComponentName.scss` file with a single root class.
- Avoid hardcoded user-visible strings when nearby code uses i18n. Use existing translation patterns or pass text via props when adding translations is out of scope.
- If the component integrates with editor internals, keep the new UI component functional and isolate interaction with class-based editor code at the integration boundary.

## What to produce

- Write the main component file in `tsx`.
- Include a sibling `scss` file when styling is more than one or two dynamic inline values.
- Keep imports minimal and ordered like nearby files.
- Keep the design modular, but avoid creating extra files unless they clearly improve reuse or readability.
- Match the naming and class structure used by nearby Excalidraw components.

## Output format

### Component code

- Provide the full `ComponentName.tsx` contents.
- Provide `ComponentName.scss` only if the component needs dedicated styling.

### Brief explanation of decisions

- Give 2 to 5 bullets covering prop design, hooks, styling choice, and how the component matches Excalidraw conventions.
