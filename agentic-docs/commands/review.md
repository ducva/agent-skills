---
description: Review an existing document for completeness, accuracy, and clarity against the current codebase
argument-hint: "<filepath>  (e.g., README.md, docs/auth-spec.md)"
allowed-tools:
  - Read
  - Glob
  - Grep
  - AskUserQuestion
---

You are performing a structured review of an existing document, checking it against the current codebase for accuracy, completeness, and clarity.

## Steps

1. **Resolve the file**: Get `<filepath>` from `$ARGUMENTS`.
   - If not provided, use `AskUserQuestion` to ask which file to review.
   - Use `Read` to load the document content.

2. **Research the codebase**: Based on what the doc covers, gather ground truth:
   - Find relevant source files, APIs, configs, and interfaces using `Glob` and `Grep`
   - Read key source files to understand current implementation
   - Note the project's current structure vs what the doc describes

3. **Analyze the document** across these dimensions:
   - **Accuracy**: Are all described APIs, configs, and behaviors still correct?
   - **Completeness**: Are there significant features/components not documented?
   - **Clarity**: Are explanations clear? Any jargon left unexplained?
   - **Structure**: Does the document have appropriate sections for its type?
   - **Freshness**: Are there stale references (old file paths, deprecated APIs, removed features)?

4. **Produce structured feedback**:

```
## Document Review: <filename>

### Summary
[1-2 sentence overall assessment]

### Issues Found

#### Critical (inaccurate or missing key info)
- [ ] <issue>: <what's wrong> → <suggested fix>

#### Moderate (incomplete or unclear)
- [ ] <issue>: <what's missing or confusing> → <suggested improvement>

#### Minor (cosmetic, style)
- [ ] <issue> → <suggestion>

### Strengths
- <what the doc does well>

### Recommended Next Steps
1. <highest priority action>
2. ...
```

5. **Ask**: "Would you like me to update the document now to fix these issues?"
