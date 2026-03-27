# Review Code

Review the provided file, diff, or code snippet for correctness, maintainability, performance, and security. Keep the review concise, evidence-based, and actionable.

## Scope

- Use the user's prompt, selected code, diff, or referenced files as the review target.
- If a file path is provided, read the file and only the smallest useful amount of nearby context such as related types, helpers, callers, or tests.
- If only a snippet is provided, review it in isolation and call out assumptions instead of guessing missing context.

## What to evaluate

1. Correctness
   - Bugs, broken logic, edge cases, null or undefined handling, async issues, race conditions, and error paths.
2. Performance
   - Unnecessary rerenders, repeated work, avoidable allocations, expensive loops, bundle impact, and memoization mistakes.
3. Readability and maintainability
   - Naming, duplication, overly complex control flow, unclear abstractions, dead code, missing tests, and weak separation of concerns.
4. Security and safety
   - Unsafe URLs or HTML or SVG, missing `noopener`, risky DOM APIs, weak validation, secret leakage, and unsafe network or encryption handling.
5. Excalidraw conventions
   - Respect package direction `common <- math <- element <- excalidraw <- excalidraw-app`.
   - In `packages/excalidraw`, prefer direct relative imports over local barrel imports.
   - Use `import type` for type-only imports.
   - Reuse existing helpers and patterns before suggesting new abstractions or dependencies.
   - Preserve the established editor architecture instead of suggesting new global state or rendering systems.
   - When behavior changes, expect the nearest existing test to be added or updated.
   - When relevant, look for existing Excalidraw helpers for URL sanitization and encrypted collaboration or share flows.

## Review rules

- Prioritize high-impact findings over style nits.
- Report only issues you can justify from the code or nearby context.
- Group related observations instead of repeating small variants of the same problem.
- Suggest the smallest change that solves the problem.
- Provide a refactored example only when it materially clarifies the fix.
- Cite file paths and line numbers when they are available.

## Output format

### Summary

- Give 1 to 3 bullets on overall quality, risk, and readiness.

### Issues found

- If there are no meaningful issues, say `No significant issues found.`
- Otherwise list each issue as:
  - `[high|medium|low] file/path:line or snippet` - problem, why it matters, and the main failure mode or edge case.

### Suggested improvements

- Give 1 to 5 concise, actionable bullets ordered by impact.

### Refactored code (if applicable)

- Include only a minimal revised snippet, using the most relevant language fence.
