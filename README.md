# agent-toolkit

Reusable coding-agent skills for Pi.

## Install

### Whole package (all skills)

Install globally from Git:

```bash
pi install git:github.com/ayuvlasenko/agent-toolkit
```

Install for the current project only:

```bash
pi install -l git:github.com/ayuvlasenko/agent-toolkit
```

List installed packages and update this package:

```bash
pi list
pi update git:github.com/ayuvlasenko/agent-toolkit
```

### Individual skills (Vercel Skills)

List the skills in this repository:

```bash
npx skills@latest add ayuvlasenko/agent-toolkit --list --full-depth
```

Install a skill into the current project:

```bash
npx skills@latest add ayuvlasenko/agent-toolkit \
  --skill <skill> \
  --agent pi \
  --full-depth \
  --copy
```

Add `--global` to install globally.

Update an installed skill:

```bash
npx skills@latest update <skill>
```

## Skills

### brainstorm

Collaborative dialogue that turns ideas into designs — use before creative work or significant changes.

- Package: [`packages/brainstorm`](packages/brainstorm)
- Skill: [`SKILL.md`](packages/brainstorm/skills/brainstorm/SKILL.md)
- Source: [`umputun/cc-thingz/plugins/brainstorm`](https://github.com/umputun/cc-thingz/tree/master/plugins/brainstorm)
- Invoke: `/skill:brainstorm`, or activates automatically on design/brainstorm requests

### planning-make

Create a structured implementation plan in `docs/plans/`.

- Package: [`packages/planning`](packages/planning)
- Skill: [`SKILL.md`](packages/planning/skills/planning-make/SKILL.md)
- Source: [`umputun/cc-thingz/plugins/planning/commands/make.md`](https://github.com/umputun/cc-thingz/blob/master/plugins/planning/commands/make.md)
- Invoke: `/skill:planning-make <description>`, or activates automatically when asking for an implementation plan

### dialectic

Prove and counter-prove a statement using parallel agents to eliminate confirmation bias.

- Package: [`packages/thinking-tools`](packages/thinking-tools)
- Skill: [`SKILL.md`](packages/thinking-tools/skills/dialectic/SKILL.md)
- Source: [`umputun/cc-thingz/plugins/thinking-tools/skills/dialectic`](https://github.com/umputun/cc-thingz/tree/master/plugins/thinking-tools/skills/dialectic)
- Invoke: `/skill:dialectic <statement>`
