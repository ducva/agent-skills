---
description: Update an existing document to reflect the current state of the codebase, rewriting only stale sections
argument-hint: "<filepath>  (e.g., README.md, docs/api-spec.md)"
allowed-tools:
  - Read
  - Write
  - Glob
  - Grep
  - Bash
  - AskUserQuestion
---

You are updating an existing document to keep it in sync with the current codebase. You rewrite only the sections that are stale — leave accurate sections untouched.

## Steps

1. **Resolve the file**: Get `<filepath>` from `$ARGUMENTS`.
   - If not provided, use `AskUserQuestion` to ask which file to update.
   - Use `Read` to load the full document content.

2. **Research the codebase**: Understand what has changed since the doc was written:
   - Use `Glob` and `Grep` to find relevant source files, configs, and interfaces
   - Use `Bash` to check git log for recent changes if needed: `git log --oneline -20 -- <related-paths>`
   - Read key source files to get current ground truth

3. **Identify stale sections**: For each section of the document, determine:
   - Is this information still accurate?
   - Are file paths, API names, config keys, or behavior descriptions outdated?
   - Are there new features/components that should be added?
   - Flag each stale section with what needs to change

4. **Confirm before writing**: Present the list of stale sections to the user:
   ```
   Found X sections to update:
   - Section "Installation": [reason]
   - Section "API": [reason]

   Proceed with updates?
   ```
   Use `AskUserQuestion` if uncertain about scope.

5. **Update the document**: Use `Write` to save the updated document:
   - Rewrite only the stale sections
   - Preserve all accurate content verbatim
   - Add new sections at the end if needed (or in logical position)
   - Do not add a "Last Updated" header unless the doc already has one

6. **Report**: Tell the user exactly which sections were changed and what was updated.
