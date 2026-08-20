---
name: brainstorm
description: Use before any creative work or significant change, including architecture, feature design, refactoring strategy, or requests to brainstorm, analyze, think through, discuss, design, or explore options. Follow the complete gated workflow even when the user provides a detailed proposal or asks for immediate opinions. Do not skip directly to solutions or implementation.
---

# Brainstorm

Turn ideas into validated designs through collaborative dialogue before implementation.

## Custom rules loading

Before starting, use the `read` tool to check for user-provided custom rules:

1. Read `.pi/brainstorm-rules.md` in the current working directory.
2. If it is absent or empty, read `~/.pi/agent/brainstorm-rules.md`.
3. If both files are absent or empty, continue without custom rules.

If the rules are non-empty, treat them as additional instructions that supplement, rather than replace, the workflow below. Project-level rules take precedence over user-level rules; when both are non-empty, load only the project-level rules.

### Rules management

When the user asks to add, show, or clear custom brainstorm rules:

- **Show rules**: read `.pi/brainstorm-rules.md`; if it is absent or empty, read `~/.pi/agent/brainstorm-rules.md`. Display the first non-empty file and identify its level, or report that neither level is configured.
- **Add or update project rules**: write `.pi/brainstorm-rules.md` in the current working directory.
- **Add or update user rules**: write `~/.pi/agent/brainstorm-rules.md`.
- **Clear project rules**: write empty content to `.pi/brainstorm-rules.md`.
- **Clear user rules**: write empty content to `~/.pi/agent/brainstorm-rules.md`.

See `references/custom-rules.md` for full documentation.

**CRITICAL: this skill must NEVER modify its own files (skills, scripts, references). The only files it may create or modify for rules management are `.pi/brainstorm-rules.md` and `~/.pi/agent/brainstorm-rules.md`. If the user asks to change the skill's behavior, suggest creating a plan instead of editing skill files directly.**

## Gated workflow

Treat the phases as a state machine, not as optional guidance. The gates protect the conversation from converging on a solution before the problem and trade-offs are understood.

At activation, use the task-management tool when available to create and track these items:

1. Phase 1: understand the idea
2. Phase 2: explore approaches
3. Phase 3: validate architecture and components
4. Phase 3: validate APIs and data flow
5. Phase 3: validate errors and lifecycle
6. Phase 3: validate testing and migration
7. Phase 3: approve complete design
8. Phase 4: choose next steps

When activating or resuming during an existing brainstorm, audit the conversation against every workflow item before continuing. Mark an item complete only when its required section was explicitly presented and the user approved that section. Informal discussion, technical corrections, summaries, and approval of a related detail do not count as section approval. Preserve valid explicit approvals so the user is not forced to repeat completed work, then start with the earliest incomplete item. For a combined item, such as testing and migration, require evidence that every named concern was validated.

Start an item immediately before working on it and complete it immediately after its exit condition is satisfied. Do not complete items speculatively or in batches.

Follow these transition rules throughout the conversation:

- Work in only one phase, and one Phase 3 section, per assistant response.
- Do not enter a phase until every earlier item is complete.
- A short reply such as “yes,” “ok,” or “looks good” approves only the question or section immediately before it. It never approves unpresented sections or the complete design.
- If the user introduces new information, return to the affected phase or section and revalidate it.
- Do not edit code, write a plan, or delegate implementation before the user selects a Phase 4 action. Repository reads used to understand the design are allowed.

## Phase 1: Understand the idea

Inspect relevant project context first, including files, documentation, and recent changes where useful. Then clarify the idea one question at a time.

Focus on:

- purpose and user-visible outcome;
- constraints and non-goals;
- success criteria;
- integration points and existing behavior.

Prefer multiple-choice questions when reasonable. Never ask more than one question in a message.

Before leaving Phase 1:

1. Summarize the understood problem, constraints, success criteria, and integration points.
2. Ask the user to confirm or correct that summary.
3. Receive explicit confirmation.

Do not propose approaches, recommendations, APIs, or architecture during Phase 1. A detailed initial user proposal is context to understand, not permission to skip this gate.

## Phase 2: Explore approaches

After Phase 1 is explicitly confirmed:

1. Present two or three meaningfully different approaches.
2. Lead with the recommended approach and explain why it best fits the confirmed criteria.
3. Explain the important trade-offs of every approach.
4. Ask the user to select an approach.

Keep this conversational rather than presenting a complete design. Do not begin Phase 3 in the same response, even if one approach is strongly preferred.

Phase 2 is complete only when the user explicitly selects or approves an approach.

## Phase 3: Present and validate the design

Develop the selected approach incrementally. Present exactly one section per response, normally 200–300 words, and ask whether that section is correct before continuing.

Validate these sections in order:

1. **Architecture and components**: boundaries, responsibilities, and dependencies.
2. **APIs and data flow**: public contracts, types, state changes, and runtime flow.
3. **Errors and lifecycle**: validation, failure behavior, recovery, concurrency, cleanup, and observability where relevant.
4. **Testing and migration**: verification strategy, compatibility, rollout, and migration steps.

Keep irrelevant details brief, but explicitly state why a concern does not apply rather than silently skipping its section. Apply YAGNI and remove design elements that do not support the confirmed success criteria.

After all four sections are individually approved:

1. Present a concise synthesis of the complete design.
2. Ask explicitly whether the complete design is approved.
3. Treat only the answer to that question as complete-design approval.

Do not combine the final approval question with implementation readiness or Phase 4 choices.

## Phase 4: Choose next steps

Immediately after explicit complete-design approval, use the `question` tool with exactly these choices:

- **Use planning-make**: load and follow the `planning-make` skill, carrying the validated brainstorm design into its planning context.
- **Start now**: begin implementing the validated design.

When the user selects **Use planning-make**, do not write a plan directly from this skill. Load `planning-make` and let it own context gathering, plan creation, review, and its next-step workflow. Treat the validated design as established context so it is preserved during planning.

Do not replace the Phase 4 tool call with a plain-text “ready to implement?” question. If the tool is unavailable or the selection is cancelled, state that no next step was selected and do not begin implementation.

## Principles

- Ask one question at a time so each answer has an unambiguous scope.
- Prefer multiple choice where it reduces user effort.
- Explore genuine alternatives before converging.
- Lead with a recommendation while leaving the decision to the user.
- Validate incrementally because local approval is not approval of the whole design.
- Apply YAGNI ruthlessly.
- Backtrack when new information invalidates an earlier decision.
- When duplication appears, ask whether the user prefers simpler duplication or an abstraction with additional coupling, and explain the trade-off before deciding.

## Implementation tracking

After the user selects implementation in Phase 4:

- Track implementation tasks with the available task-management tools.
- Complete each task immediately when done rather than batching status updates.
- Keep the user informed of material progress and blockers.
