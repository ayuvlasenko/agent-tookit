---
name: skill-porting
description: Port an existing skill, plugin, command, or related workflow from another repository or harness to Pi with minimal compatibility changes. Use this skill whenever the user explicitly asks to port, migrate, adapt, or convert an existing skill to Pi, including requests involving Claude Code, Codex, Agent Skills, plugin commands, or a source repository and target Pi layout.
metadata:
  internal: true
---

# Skill Porting

Port an existing skill or workflow to Pi while preserving the source behavior and authorial style.

## Scope

This skill handles source-to-Pi ports. It does not create a new skill from scratch, rewrite a source skill for quality, or normalize sibling ports.

Before editing, identify:

- source repository and revision, if available;
- source file or plugin directory;
- target Pi path;
- files that belong to the requested port;
- source files intentionally outside the port scope.

If the source, target, or scope is unclear, inspect the repository and ask one focused question before making consequential changes.

## Porting workflow

### 1. Inspect the source and target

Read the relevant source files and the target project's instructions before editing. Determine which files are source-derived and which files are Pi packaging files.

Create a source-to-target file map. A source-derived file is any copied skill, reference, script, asset, template, or documentation file. A Pi-only `package.json`, README entry, or license file is a target packaging file.

Do not modify the source repository.

### 2. Copy source-derived files first

Create every in-scope source-derived target file with `cp` or an equivalent exact-copy operation before adapting it. Do not recreate source-derived content from memory with `write`, and do not rewrite a copied file wholesale.

When the target layout requires a file to move, copy it first and relocate it without changing its contents. Change only relative paths that would otherwise be broken by the required layout change.

Record excluded source files and the reason for each exclusion. Typical reasons are an explicitly narrower port scope, an unsupported source-harness component, or a component that has not been requested.

### 3. Build a compatibility ledger

Before changing copied files, list each required Pi adaptation. Each entry must name the file and explain the concrete incompatibility it fixes.

Valid adaptations include:

- Pi skill metadata required for discovery;
- source-harness tool names replaced with the corresponding Pi tools; use Pi tool names exactly, such as `read`, `write`, `edit`, `find`, `grep`, `bash`, `question`, `todo`, `subagent`, and `revdiff_review`;
- source-harness paths, environment variables, or rule locations replaced with Pi paths; use project paths under `.pi/` and user-level paths under `~/.pi/agent/` unless the target project explicitly defines another convention;
- source-harness commands replaced with Pi skill invocation using `/skill:<skill-name>` or with the corresponding Pi tool; do not invent a bare slash command;
- source-harness plugin metadata or hooks excluded when they cannot function in Pi;
- packaging layout changes required for Pi or the requested installer;
- relative paths updated after a required layout change.

Do not change wording, examples, section names, phase names, formatting, capitalization, punctuation, or behavior merely because another version seems clearer or more consistent. Do not synchronize sibling ports unless the user explicitly requests it.

If a proposed change is not clearly required for Pi compatibility or the declared package layout, leave it unchanged and discuss it before editing.

### 4. Apply the smallest edits

Use targeted edits on copied files. Preserve all unrelated source text byte-for-byte where practical.

Do not add, remove, or omit source-derived content without recording the reason in the port report. A source file may be removed only when it is incompatible with Pi, outside the declared scope, or no longer referenced after an approved packaging adaptation. Check references before removing it.

Keep target-only packaging changes separate from source-derived content changes. Do not change versions, descriptions, README wording, or package structure for polish unless the user asks for that or the target tooling requires it.

Do not create temporary Git repositories or temporary commits to compare files. Direct file comparison is sufficient.

### 5. Verify after the final edit

For every mapped source-derived file:

1. compare source and target with `diff -u`, `cmp`, or an equivalent file review;
2. inspect every changed hunk and match it to the compatibility ledger;
3. check that copied scripts, references, and assets are present and reachable;
4. search for stale source-harness paths, tools, commands, and environment variables;
5. validate Pi skill metadata and package layout where applicable;
6. rerun all checks after any later edit.

Check target-only files separately. A target-only file does not need a source diff, but its contents and purpose must be explained.

## Completion report

After the port, report:

### Source and scope

- source repository, revision, and path;
- target path;
- included source files;
- excluded source files and reasons.

### File actions

- files copied unchanged;
- files adapted;
- target-only files added;
- files removed or relocated, with reasons.

### Compatibility ledger

For every changed hunk, provide:

- target file and location;
- source file and corresponding location;
- exact Pi incompatibility;
- change made.

### Verification

- file comparisons performed;
- stale-reference checks;
- metadata and layout checks;
- unresolved compatibility concerns.

Do not claim that a file was copied unchanged, a check passed, or a source reference was removed unless it was verified directly.
