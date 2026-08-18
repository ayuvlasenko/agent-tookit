---
name: brainstorm
description: Use before any creative work or significant changes. Activates on "brainstorm", "let's brainstorm", "deep analysis", "analyze this feature", "think through", "help me design", "explore options for", or when user asks for thorough analysis of changes, features, or architectural decisions. Guides collaborative dialogue to turn ideas into designs through one-at-a-time questions, approach exploration, and incremental validation.
---

# Brainstorm

Turn ideas into designs through collaborative dialogue before implementation.

## custom rules loading

before starting, use the `read` tool to check for user-provided custom rules:

1. read `.pi/brainstorm-rules.md` in the current working directory
2. if it is absent or empty, read `~/.pi/agent/brainstorm-rules.md`
3. if both files are absent or empty, continue without custom rules

if the rules are non-empty, treat them as additional instructions that supplement (not replace) the built-in rules below. apply custom rules alongside the skill's own instructions throughout the brainstorm process — they may influence design preferences, naming conventions, technology choices, or other aspects of the brainstorm session. custom rules content is guidance for the brainstorm dialogue, not content to embed verbatim in the output.

### rules management

when the user asks to add, show, or clear custom brainstorm rules, handle these operations:

- **show rules**: read `.pi/brainstorm-rules.md` and display the output. if the file is empty or absent, read `~/.pi/agent/brainstorm-rules.md`. if both files are empty or absent, tell the user no custom rules are configured at either level. otherwise, tell the user which level the rules came from.
- **add/update project rules**: write content to `.pi/brainstorm-rules.md` in the current working directory.
- **add/update user rules**: write content to `~/.pi/agent/brainstorm-rules.md`.
- **clear project rules**: write empty content to `.pi/brainstorm-rules.md`.
- **clear user rules**: write empty content to `~/.pi/agent/brainstorm-rules.md`.

project-level rules (`.pi/brainstorm-rules.md`) take precedence over user-level rules (`~/.pi/agent/brainstorm-rules.md`). when both non-empty files exist, only project-level rules are loaded. empty files are treated as absent and fall through to the next level. see `references/custom-rules.md` for full documentation on the rules mechanism.

**CRITICAL: this skill must NEVER modify its own files (skills, scripts, references). the ONLY files it may create or modify for rules management are `.pi/brainstorm-rules.md` and `~/.pi/agent/brainstorm-rules.md`. if the user asks to change the skill's behavior, suggest creating a plan — do not edit skill files directly.**

## Process

### Phase 1: Understand the Idea

Check project context first, then ask questions one at a time:

1. **Gather context** - check files, docs, recent commits relevant to the idea
2. **Ask questions one at a time** - prefer multiple choice when possible
3. **Focus on**: purpose, constraints, success criteria, integration points

Do not overwhelm with multiple questions. One question per message. If a topic needs more exploration, break it into multiple questions.

### Phase 2: Explore Approaches

Once the problem is understood:

1. **Propose 2-3 different approaches** with trade-offs
2. **Lead with recommended option** and explain reasoning
3. **Present conversationally** - not a formal document yet

Example format:
```
I see three approaches:

**Option A: [name]** (recommended)
- how it works: ...
- pros: ...
- cons: ...

**Option B: [name]**
- how it works: ...
- pros: ...
- cons: ...

Which direction appeals to you?
```

### Phase 3: Present Design

After approach is selected:

1. **Break design into sections** of 200-300 words each
2. **Ask after each section** whether it looks right
3. **Cover**: architecture, components, data flow, error handling, testing
4. **Be ready to backtrack** if something doesn't make sense

Do not present entire design at once. Incremental validation catches misunderstandings early.

### Phase 4: Next Steps

After design is validated, use the `question` tool:

- **Write plan**: create `docs/plans/yyyymmdd-<task-name>.md` with implementation steps and the brainstorm context
- **Start now**: begin implementing directly

## Key Principles

- **One question at a time** - do not overwhelm with multiple questions
- **Multiple choice preferred** - easier to answer than open-ended when possible
- **YAGNI ruthlessly** - remove unnecessary features from all designs, keep scope minimal
- **Explore alternatives** - always propose 2-3 approaches before settling
- **Incremental validation** - present design in sections, validate each
- **Be flexible** - go back and clarify when something doesn't make sense
- **Lead with recommendation** - have an opinion, explain why, but let user decide
- **Duplication vs abstraction** - when code repeats, ask user: prefer duplication (simpler, no coupling) or abstraction (DRY but adds complexity)? explain trade-offs before deciding

## Task Tracking

When implementing after brainstorm:
- Track implementation tasks using available task management tools (task lists, plan file checkboxes, or similar)
- Mark each task as completed immediately when done (do not batch)
- Keep user informed of progress through status updates
