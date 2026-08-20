# Brainstorm Usage

## Triggers

The brainstorm skill activates for:

- “brainstorm” and “let's brainstorm”;
- “deep analysis” and “analyze this feature”;
- “think through” and “help me design”;
- “explore options for”;
- requests to discuss architecture, significant refactoring, feature design, or other substantial changes.

Invoke it directly with `/skill:brainstorm` or let it activate through intent matching.

## Gated workflow

The skill tracks each phase and Phase 3 section as a task. It does not advance until the user explicitly satisfies the current gate. A short confirmation approves only the immediately preceding question or section.

When resumed during an existing brainstorm, it audits the conversation against the complete workflow. It preserves sections that were explicitly presented and approved, but informal discussion, summaries, and approval of related details do not count. It resumes from the earliest incomplete item.

### Phase 1: Understand

- inspects relevant files, documentation, and recent changes;
- asks one clarification question at a time;
- establishes purpose, constraints, success criteria, and integration points;
- summarizes its understanding and receives explicit confirmation before proposing solutions.

### Phase 2: Explore approaches

- proposes two or three meaningfully different approaches;
- recommends one and explains the trade-offs;
- waits for the user to select an approach before designing it.

### Phase 3: Present design

The skill presents and validates one section per response:

1. architecture and components;
2. APIs and data flow;
3. errors and lifecycle;
4. testing and migration.

After every section is approved, it synthesizes the design and separately requests complete-design approval.

### Phase 4: Choose next steps

Only after complete-design approval, the skill uses the `question` tool:

- **Use planning-make** loads the `planning-make` skill and passes it the validated brainstorm context; that skill owns plan creation and review.
- **Start now** begins implementation.

The brainstorm skill does not write plans itself. No planning workflow, code change, or implementation delegation occurs before this selection.

## Examples

```
User: “Let's brainstorm how to add caching to the API.”
→ Phase 1 inspects the API, clarifies requirements, and confirms its understanding.
→ Phase 2 compares in-memory LRU, Redis, and HTTP caching.
→ Phase 3 validates each section of the selected approach separately.
→ Phase 4 offers Use planning-make or Start now through the question tool.

User: “Could we move this application module into the shared package?”
→ Phase 1 treats the proposed move as context rather than a settled solution.
→ It confirms goals, constraints, success criteria, and current dependencies first.
→ Only then does it compare package-boundary approaches.

User: “/skill:brainstorm add my Go rules to user-level brainstorm rules.”
→ asks what rules to add;
→ writes only `~/.pi/agent/brainstorm-rules.md`.
```

## Principles

- one question per response;
- explicit gates between phases;
- local approval is not whole-design approval;
- genuine alternatives before convergence;
- one design section at a time;
- YAGNI and willingness to backtrack.
