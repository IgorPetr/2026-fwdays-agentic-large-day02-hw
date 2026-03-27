---
name: build-verify
description: Runs yarn build at the project root after code changes, fixes compilation errors without ts-ignore or test hacks, and reports status with fixes and full output. Use when the user asks to build, verify, or check compilation, or after edits that might affect compilation.
---

# Build & Verify

## When to use

After code changes that might affect compilation.

**Triggers:** user asks to build, verify, or check compilation; or the agent finishes edits that could affect compilation.

## Inputs

- Changed files (from `git diff` or conversation context)

## Steps

1. Run `yarn build` in the project root.
2. If the build succeeds → report success and list changed files (when known).
3. If the build fails:
   a. Read the error output; identify file, line, and error type.
   b. Open the file at the reported location.
   c. Fix the issue (types, missing imports, syntax, etc.).
   d. Re-run `yarn build`.
   e. Repeat until the build passes or **3 attempts** are exhausted.

## Outputs

- **Build status:** PASS or FAIL
- **Fixes applied:** bullet list of what changed (if any)
- **Full build output:** include the complete terminal output from the final (or failed) run

## Safety

- Do **not** fix errors by adding `@ts-ignore`, `@ts-expect-error`, or broad `any`.
- Do **not** modify test files to silence build failures.
- After **3 failed attempts**, stop, summarize errors, and hand off to the user with the last full output.
