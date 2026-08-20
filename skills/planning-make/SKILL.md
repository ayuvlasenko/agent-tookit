---
name: planning-make
description: Create and review structured implementation plans in docs/plans/. Use for any feature, fix, refactor, or migration plan, especially after a validated brainstorm. Preserve approved decisions, verify repository assumptions, resolve review findings, and prevent flawed plans from being committed silently.
argument-hint: describe the feature or task to plan
---

# Implementation Plan Creation

create an implementation plan in `docs/plans/yyyymmdd-<task-name>.md` with interactive context gathering.

## custom rules loading

before starting, use the `read` tool to check for user-provided custom rules:

1. read `.pi/planning-rules.md` in the current working directory
2. if it is absent or empty, read `~/.pi/agent/planning-rules.md`
3. if both files are absent or empty, continue without custom rules

if the rules are non-empty, treat them as additional instructions that supplement (not replace) the built-in rules below. apply custom rules alongside the skill's own instructions throughout the planning process — they may influence plan structure, testing approach, naming conventions, or other aspects of plan creation. custom rules content is guidance for creating the plan, not content to embed verbatim in the output plan file.

### rules management

when the user asks to add, show, or clear custom planning rules, handle these operations:

- **show rules**: read `.pi/planning-rules.md` and display the output. if the file is empty or absent, read `~/.pi/agent/planning-rules.md`. if both files are empty or absent, tell the user no custom rules are configured at either level. otherwise, tell the user which level the rules came from.
- **add/update project rules**: write content to `.pi/planning-rules.md` in the current working directory.
- **add/update user rules**: write content to `~/.pi/agent/planning-rules.md`.
- **clear project rules**: write empty content to `.pi/planning-rules.md`.
- **clear user rules**: write empty content to `~/.pi/agent/planning-rules.md`.

project-level rules (`.pi/planning-rules.md`) take precedence over user-level rules (`~/.pi/agent/planning-rules.md`). when both non-empty files exist, only project-level rules are loaded. empty files are treated as absent and fall through to the next level. see `references/custom-rules.md` for full documentation on the rules mechanism.

**CRITICAL: this skill must NEVER modify its own files (skills, scripts, references). the ONLY files it may create or modify for rules management are `.pi/planning-rules.md` and `~/.pi/agent/planning-rules.md`. if the user asks to change the skill's behavior, suggest creating a plan — do not edit skill files directly.**

## step 0: parse intent and gather context

before asking questions, understand what the user is working on.

If the conversation contains an approved brainstorm, design review, or explicit user decisions, first create a decision ledger covering purpose, scope, non-goals, constraints, compatibility, selected architecture, public APIs, data flow, errors, lifecycle, testing, migration, and naming. Treat explicit approvals as established planning input. Informal discussion and unapproved suggestions are context, not decisions. Do not ask the user to reconfirm established answers or silently replace them.

1. **parse user's command arguments** to identify intent:
   - "add feature Z" / "implement W" → feature development
   - "fix bug" / "debug issue" → bug fix plan
   - "refactor X" / "improve Y" → refactoring plan
   - "migrate to Z" / "upgrade W" → migration plan
   - generic request → explore current work

2. **gather relevant context quickly** — use direct tool calls (`read`, `find`, `grep`), NOT an subagent. keep discovery under 30 seconds:

   **for feature development:**
   - glob for files matching the feature area (e.g., `**/*auth*`, `**/*cache*`)
   - read 1-3 most relevant files to understand existing patterns
   - check project structure with a quick `ls` of key directories

   **for bug fixing:**
   - grep for error messages or function names mentioned in the request
   - read the specific file(s) involved
   - check `git log --oneline -5` for recent changes

   **for refactoring/migration:**
   - glob for files matching the area being refactored
   - read 2-3 key files to understand current structure
   - grep for imports/references to identify dependencies

   **for generic/unclear requests:**
   - check `git status` and `git log --oneline -5`
   - read README.md or CLAUDE.md for project overview
   - `ls` the top-level directory structure

   **CRITICAL: do NOT launch an Explore agent or read more than 5 files in this initial scan. the goal is quick reconnaissance, not exhaustive analysis. The five-file limit does not apply to targeted verification before writing the plan.**

3. **synthesize findings** into a brief context summary (3-5 bullet points):
   - what the project is and primary language/framework
   - which files/areas are relevant to the request
   - key patterns or conventions observed

## step 0.5: verify decisions and risky assumptions

Before asking for missing information or writing the plan, verify decision-relevant repository claims with targeted reads and searches. Keep this investigation bounded to facts that can change feasibility, task ordering, compatibility, or testing.

For broad changes, check where applicable:

- dependency injection and module visibility;
- runtime and peer dependencies, including dynamically loaded packages;
- package source versus built-output resolution;
- build and test ordering between packages and consumers;
- public exports and internal package import style;
- protocol identifiers, event names, channel strings, and schema paths;
- all direct consumers while excluding intentionally unchanged copies;
- project testing guidance and minimum integration coverage for wiring-heavy changes.

Compare evidence with the decision ledger. Preserve supported approved decisions. If evidence contradicts one, explain the conflict and ask one focused question rather than resolving it silently. Record identifiers and paths exactly, and classify newly discovered requirements as required or optional.

Run `date +%Y%m%d` and use its output as the only source for the plan filename date.

## step 1: present context and ask focused questions

show the discovered context and inherited decision ledger. Ask questions **one at a time** using the `question` tool only for information that is genuinely missing or contradicted. When an item is already explicitly established, show its inherited value and skip its question. Never repeat the complete interview merely because this skill started.

"based on your request and validated prior decisions, i found: [context summary and decision ledger]"

**ask only missing questions, one at a time:**

1. **plan purpose, if missing**: use the `question` tool - "what is the main goal?"
   - provide multiple choice with suggested answer based on discovered intent
   - wait for response before next question

2. **scope, if missing**: use the `question` tool - "which components/files are involved?"
   - provide multiple choice with suggested discovered files/areas
   - wait for response before next question

3. **constraints, if missing**: use the `question` tool - "any specific requirements or limitations?"
   - can be open-ended if constraints vary widely
   - wait for response before next question

4. **testing approach, if missing**: use the `question` tool - "do you prefer TDD or regular approach?"
   - options: "TDD (tests first)" and "Regular (code first, then tests)"
   - store preference for reference during implementation
   - wait for response before next question

5. **plan title, if missing**: use the `question` tool - "short descriptive title?"
   - provide suggested name based on intent

after all required information is available, synthesize inherited decisions, new answers, and verified repository evidence into a concise planning brief. If nothing is missing, proceed without redundant questions.

## step 1.5: explore approaches

once the problem is understood, propose implementation approaches:

1. **propose 2-3 different approaches** with trade-offs for each
2. **lead with recommended option** and explain reasoning
3. **present conversationally** - not a formal document yet

example format:
```
i see three approaches:

**Option A: [name]** (recommended)
- how it works: ...
- pros: ...
- cons: ...

**Option B: [name]**
- how it works: ...
- pros: ...
- cons: ...

which direction appeals to you?
```

use the `question` tool to let user select preferred approach before creating the plan.

**skip this step** if:
- an earlier brainstorm explicitly selected and validated the approach
- the implementation approach is obvious (single clear path)
- user explicitly specified how they want it done
- it's a bug fix with clear solution

Do not invent alternatives merely to repeat an already completed brainstorm.

## step 2: create plan file

Before writing, compare the proposed plan against every item in the decision ledger. Each approved decision must appear unchanged or be explicitly superseded by the user after a verified conflict. Do not introduce contradictory architecture, renamed protocol identifiers, or new scope silently.

Check `docs/plans/` for existing files, then create `docs/plans/yyyymmdd-<task-name>.md`, using the date captured from `date +%Y%m%d`.

### execution-ready quality bar

A plan is execution-ready when an implementer can complete it without rediscovering requirements already available during planning. Detail must be proportional to complexity: avoid filler and arbitrary word counts, but do not compress away known semantics, constraints, edge cases, or verification steps.

Before presenting the plan, verify that it:

- explains current behavior, target behavior, motivation, scope, non-goals, success criteria, and dependencies;
- cites concrete repository anchors such as files, symbols, schemas, configuration keys, and consumers;
- distinguishes files in scope from intentionally excluded areas;
- includes concrete API signatures, types, data shapes, examples, and runtime flows when they remove implementation ambiguity;
- records invariants, compatibility requirements, edge cases, and known gotchas;
- turns every verified requirement into a task step or acceptance check;
- names exact files instead of vague groups such as “affected imports” when searches can enumerate them;
- splits work when independent parts can be implemented or verified separately;
- states unresolved questions, resolved decisions, and deliberately deferred follow-ups;
- contains no generic boilerplate that is unrelated to the repository or change.

### plan structure

```markdown
# [Plan Title]

## Overview
- current behavior and concrete deficiency
- target behavior and user/system outcome
- motivation and why the change is needed now
- complete scope inventory grouped by meaningful surface or component
- explicit non-goals and intentionally deferred work
- measurable success criteria and dependencies on other work

## Context (from discovery)
- reference implementations and existing patterns with file/symbol anchors
- verified domain, schema, protocol, and configuration rules
- direct consumers and integration points
- files involved, with their responsibilities
- files not involved, with reasons for exclusion
- constraints or tensions discovered in the repository

## Validated Design Decisions
- approved purpose, scope, constraints, and non-goals
- selected architecture and important rejected alternatives
- exact public APIs, protocol identifiers, data-flow, error, lifecycle, testing, and migration decisions
- repository evidence or explicit user decision for any changed assumption

## Development Approach
- testing approach inherited from validated context or selected by the user
- task sequencing rationale and dependency gates
- task granularity: one independently implementable and verifiable outcome per task
- compatibility and rollout strategy
- rules for keeping the plan synchronized during implementation

## Testing Strategy
- project testing guidance and existing infrastructure, cited from repository evidence
- unit, integration, e2e, build, lint, generation, and manual checks that actually apply
- observable behaviors, success cases, failure cases, and regressions per affected boundary
- exact commands and when each command runs
- if relevant test infrastructure does not exist, state the evidence and use appropriate build/lint/manual verification; do not invent a new test stack unless it is in scope

## Progress Tracking
- mark completed items with `[x]` immediately when done
- add newly discovered tasks with ➕ prefix
- document issues/blockers with ⚠️ prefix
- update plan if implementation deviates from original scope
- keep plan in sync with actual work done

## Solution Overview
- component boundaries and ownership
- key design decisions and rationale
- end-to-end runtime flow
- integration with existing systems
- concrete examples or diagrams when they clarify non-trivial behavior

## Technical Details
- exact public APIs, signatures, types, data shapes, formats, and identifiers
- state transitions, lifecycle, errors, concurrency, and cleanup
- edge cases, parsing/coercion rules, compatibility invariants, and implementation gotchas
- source anchors for rules being mirrored or migrated

## What Goes Where
- **Implementation Steps** (`[ ]` checkboxes): tasks achievable within this codebase - code changes, tests, documentation updates
- **Post-Completion** (no checkboxes): items requiring external action - manual testing, changes in consuming projects, deployment configs, third-party verifications

## Implementation Steps

<!--
Task structure guidelines:
- Each task = ONE logical unit (one function, one endpoint, one component)
- Use specific descriptive names, not generic "[Core Logic]" or "[Implementation]"
- Each task MUST have a **Files:** block listing files to Create/Modify (before checkboxes)
- Use as many checkboxes as needed to capture known semantics and verification without filler; never compress a complex task to meet a checkbox target
- **CRITICAL: number ALL tasks with concrete sequential integers** - the two trailing tasks below are shown as "Task N-1" and "Task N" where N is a PLACEHOLDER for the total task count, NOT literal text. Substitute real numbers continuing the sequence from your last implementation task (e.g. with 14 implementation tasks they become "Task 15: Verify acceptance criteria" and "Task 16: ... Update documentation"). NEVER write the literal strings "Task N-1" or "Task N" into the plan.
- Each task must end with the repository-appropriate verification for that outcome
  - add or update tests for changed behavior when relevant test infrastructure exists or project guidance requires them
  - include success, failure, and regression cases appropriate to the boundary
  - list tests as separate checklist items rather than bundling them with implementation
  - when tests do not apply, state why and use exact lint, build, generation, or manual checks instead

Example (NOTICE: Files block + tests as separate checklist items):

### Task 1: Add password hashing utility

**Files:**
- Create: `src/auth/hash`
- Create: `src/auth/hash_test`

- [ ] create `src/auth/hash` with HashPassword and VerifyPassword functions
- [ ] implement bcrypt-based hashing with configurable cost
- [ ] write tests for HashPassword (success + error cases)
- [ ] write tests for VerifyPassword (success + error cases)
- [ ] run tests - must pass before task 2

### Task 2: Add user registration endpoint

**Files:**
- Create: `src/api/users`
- Modify: `src/api/router`
- Create: `src/api/users_test`

- [ ] create `POST /api/users` handler in `src/api/users`
- [ ] add input validation (email format, password strength)
- [ ] integrate with password hashing utility
- [ ] write tests for handler success case with table-driven cases
- [ ] write tests for handler error cases (invalid input, missing fields)
- [ ] run tests - must pass before task 3
-->

### Task 1: [specific independently verifiable outcome]

**Purpose:** [why this task exists and which requirement it satisfies]

**Observable outcome:** [what behavior or repository state is true when complete]

**Files and symbols:**
- Create: `exact/path/to/new_file` — `[exports or responsibility]`
- Modify: `exact/path/to/existing` — `[symbols or call sites]`

**Prerequisites:**
- [earlier build, migration, generated artifact, or decision required first]

**Semantics and invariants:**
- [exact behavior, identifiers, data rules, compatibility constraints, and edge cases]
- [known gotchas and approaches that must not be used]

- [ ] [specific implementation action with file/symbol reference]
- [ ] [specific implementation action with exact semantics]
- [ ] add or update repository-appropriate success, failure, and regression tests
- [ ] run `[exact targeted verification command]`
- [ ] verify [task-specific observable completion criterion]

**Commit checkpoint:** `[project-appropriate commit message or "combine with Task X" rationale]`

<!-- replace "N-1" and "N" below with the actual next sequential numbers continuing from your last implementation task - do NOT emit the literal letter N -->
### Task N-1: Verify acceptance criteria
- [ ] verify all requirements from Overview are implemented
- [ ] verify edge cases are handled
- [ ] run full test suite: `<project test command>`
- [ ] run e2e tests if project has them: `<project e2e test command>`
- [ ] verify test coverage meets project standard

### Task N: [Final] Update documentation
- [ ] update README.md if needed
- [ ] update CLAUDE.md if new patterns discovered
- [ ] move this plan to `docs/plans/completed/`

## Open Questions and Resolved Decisions
- unresolved choices that require user input before the affected task
- resolved questions with the selected answer and supporting evidence
- rejected alternatives and deliberately deferred follow-ups

## Post-Completion
*Items requiring manual intervention or external systems - no checkboxes, informational only*

**Manual verification** (if applicable):
- manual UI/UX testing scenarios
- performance testing under load
- security review considerations

**External system updates** (if applicable):
- consuming projects that need updates after this library change
- configuration changes in deployment systems
- third-party service integrations to verify
```

### plan readiness review

Before offering next steps, review the complete plan as an implementer:

1. Trace every Overview success criterion and validated decision to concrete task steps and verification.
2. Check that each task has one independently verifiable outcome; split tasks that combine unrelated modules, APIs, migrations, and tests.
3. Replace vague file groups with enumerated paths or a precise discovery command when the final set is intentionally dynamic.
4. Ensure task ordering respects generated artifacts, built package outputs, migrations, and consumer dependencies.
5. Confirm semantics, invariants, edge cases, exact commands, and acceptance criteria are present where implementation would otherwise require rediscovery.
6. Verify Open Questions are either resolved before their dependent task or explicitly marked as blockers.
7. Remove generic filler and duplicated global guidance that does not help execute the specific change.

## step 3: review and next steps

After creating the file, tell the user: "Created plan: `docs/plans/yyyymmdd-<task-name>.md`" and use the `question` tool with this complete menu:

```json
{
  "question": "Plan created. What's next?",
  "options": [
    {"label": "Interactive review", "description": "Open the plan for manual annotation and revision"},
    {"label": "Auto review", "description": "Review the plan, apply verified findings, and report the changes"},
    {"label": "Implement", "description": "Commit the validated plan and choose implementation mode"},
    {"label": "Done", "description": "Commit the validated plan without implementation"}
  ]
}
```

Always show all four options after every completed review cycle. In particular, never remove **Auto review**; repeated independent reviews are allowed.

### Interactive review

Check `which revdiff`.

- If available, run `scripts/launch-plan-review.sh <plan-file-path>`, read every annotation, update the plan, and repeat until the user exits without annotations.
- Otherwise use `scripts/plan-annotate.py <plan-file-path>`, apply the resulting diff as review feedback, and repeat until there is no diff.

After changes, verify the affected sections against the decision ledger and repository evidence. State what changed and what remains unresolved, then show the complete four-option menu again.

### Auto review

1. Read `references/plan-review.md` completely before launching the reviewer.
2. Launch a read-only plan-review subagent using at least a medium model. Its self-contained task must include:
   - the exact path to `references/plan-review.md` and an instruction to read and follow the complete rubric;
   - the exact plan path;
   - the validated decision ledger;
   - loaded custom planning rules, or an explicit statement that none are configured;
   - any user-requested review focus;
   - an instruction not to modify files.
3. Require the reviewer to classify every finding as `required`, `optional`, `unverified`, or `decision-conflict`, cite repository evidence and plan sections, and return an `APPROVE`, `NEEDS REVISION`, or `BLOCKED` verdict. Reject malformed review output rather than guessing its intent.
4. Independently verify consequential findings in the repository before editing.
5. Automatically apply every verified required finding that does not conflict with an approved decision.
6. Apply optional findings only when they clearly improve the plan without expanding scope or violating YAGNI.
7. When a finding conflicts with an approved decision, explain the evidence and ask one focused user question. Apply the selected resolution; never overwrite the decision silently.
8. Update the plan’s decision ledger, affected tasks, file lists, dependencies, task ordering, tests, and verification commands consistently. Fix the failure class, not only the reviewer’s example.
9. Re-check every changed section against the original findings, approved decisions, reviewer rubric, and repository evidence. Confirm required fixes are represented throughout the plan and no contradiction was introduced.
10. Report to the user under these headings:
   - **Changed**: exact plan changes and the finding each resolves;
   - **Not changed**: rejected or unverified suggestions with reasons;
   - **Remaining required findings**: blockers or unresolved decisions.
11. Show the complete four-option menu again, including **Auto review**.

Do not merely summarize findings and leave a known-defective plan unchanged.

### Required-findings gate

Track unresolved required findings after every review. **Implement** and ordinary **Done** are available only when none remain.

If the user selects Implement or Done while required findings remain, do not commit yet. Use the `question` tool:

```json
{
  "question": "Required review findings remain. How should we proceed?",
  "options": [
    {"label": "Apply required fixes", "description": "Resolve the verified findings before committing"},
    {"label": "Auto review", "description": "Run another review and correction cycle"},
    {"label": "Interactive review", "description": "Resolve the findings through manual annotations"},
    {"label": "Commit unchanged despite findings", "description": "Explicitly accept the listed risks and commit the current plan"}
  ]
}
```

Only the explicit **Commit unchanged despite findings** selection may bypass the gate. Record the accepted findings and risks in the plan before committing.

### Implement

When no required findings remain, commit the plan with a message such as `docs: add <topic> implementation plan`, then use the `question` tool:

```json
{
  "question": "Implementation mode?",
  "options": [
    {"label": "Interactive", "description": "Implement task by task in this session"},
    {"label": "Autonomous", "description": "Run /planning:exec for autonomous execution with reviews"}
  ]
}
```

- **Interactive**: begin Task 1 and track progress with `todo`, completing tasks immediately rather than batching.
- **Autonomous**: invoke `/planning:exec <plan-file-path>`.

### Done

When no required findings remain, commit the plan with a message such as `docs: add <topic> implementation plan` and stop.

## execution enforcement

During implementation:

1. Complete the task’s implementation, tests or alternative verification, and observable completion criteria before moving on.
2. Add or update tests when required by the plan, project guidance, or existing test infrastructure. Cover the planned success, failure, and regression behavior.
3. When the verified plan intentionally uses lint, build, generation, or manual checks instead of tests, run those exact checks and record the result; do not invent or silently omit a test strategy.
4. If any required check fails, fix it before proceeding. If a task cannot be verified independently, revise the task boundary or plan ordering rather than carrying an unexplained failure forward.
5. Mark checkboxes immediately, add `➕` tasks and `⚠️` blockers as discovered, and update the decision ledger when scope or a validated decision changes.
6. On completion, verify all checkboxes, run final repository-appropriate checks, and move the plan to `docs/plans/completed/`.

## key principles

- **one question at a time** - do not overwhelm user with multiple questions in a single message
- **multiple choice preferred** - easier to answer than open-ended when possible
- **DRY, YAGNI ruthlessly** - avoid unnecessary duplication and features, keep scope minimal (but prefer duplication over premature abstraction when it reduces coupling)
- **lead with recommendation** - have an opinion, explain why, but let user decide
- **explore alternatives** - always propose 2-3 approaches before settling (unless obvious)
- **duplication vs abstraction** - when code repeats, ask user: prefer duplication (simpler, no coupling) or abstraction (DRY but adds complexity)? explain trade-offs before deciding
