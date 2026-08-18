---
name: claude-workflow
description: Use this workflow when the user asks for a Claude-specific transformation.
allowed-tools: Read, Write, Edit, AskUserQuestion
---

# Claude Workflow

Before starting, read `.claude/workflow-rules.md`.

Use the `AskUserQuestion` tool when a decision is needed.

Invoke the workflow with `/claude:workflow`.

## Process

1. Read the source.
2. Ask the user one question at a time when needed.
3. Apply the selected change.

Keep the source's wording and process intact.

###   Preserve This Heading's Spacing

Do not polish this sentence. Keep its casing, punctuation, and spacing exactly as written.

-   Keep   these   spaces.
- use the example: `DoNotNormalizeThis`

this workflow is deliberately terse. it says what to do, then it stops.

Prefer the direct path. Do not replace this voice with a smoother or more formal one.
