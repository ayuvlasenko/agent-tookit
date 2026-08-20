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

List available skills from this GitHub repository:

```bash
npx skills@latest add ayuvlasenko/agent-toolkit --list
```

Install one skill into the current project:

```bash
npx skills@latest add ayuvlasenko/agent-toolkit \
  --skill <skill> \
  --agent pi \
  --copy
```

Add `--global` to install globally.

Install all public skills globally for Pi:

```bash
npx skills@latest add ayuvlasenko/agent-toolkit \
  --skill '*' \
  --agent pi \
  --global \
  --copy \
  --yes
```

Update an installed skill:

```bash
npx skills@latest update <skill>
```

## Skills

### brainstorm

Collaborative dialogue that turns ideas into designs — use before creative work or significant changes.

- Skill: [`SKILL.md`](skills/brainstorm/SKILL.md)
- Source: [`umputun/cc-thingz/plugins/brainstorm`](https://github.com/umputun/cc-thingz/tree/master/plugins/brainstorm)
- Invoke: `/skill:brainstorm`, or activates automatically on design/brainstorm requests

### planning-make

Create a structured implementation plan in `docs/plans/`.

- Skill: [`SKILL.md`](skills/planning-make/SKILL.md)
- Source: [`umputun/cc-thingz/plugins/planning/commands/make.md`](https://github.com/umputun/cc-thingz/blob/master/plugins/planning/commands/make.md)
- Invoke: `/skill:planning-make <description>`, or activates automatically when asking for an implementation plan

### dialectic

Prove and counter-prove a statement using parallel agents to eliminate confirmation bias.

- Skill: [`SKILL.md`](skills/dialectic/SKILL.md)
- Source: [`umputun/cc-thingz/plugins/thinking-tools/skills/dialectic`](https://github.com/umputun/cc-thingz/tree/master/plugins/thinking-tools/skills/dialectic)
- Invoke: `/skill:dialectic <statement>`

### root-cause-investigator

Systematic root cause analysis for errors, bugs, and unexpected behaviors using 5-Why methodology.

- Skill: [`SKILL.md`](skills/root-cause-investigator/SKILL.md)
- References: [`references`](skills/root-cause-investigator/references)
- Source: [`umputun/cc-thingz/plugins/thinking-tools/skills/root-cause-investigator`](https://github.com/umputun/cc-thingz/tree/master/plugins/thinking-tools/skills/root-cause-investigator)
- Invoke: activates automatically for errors, bugs, build/test failures, performance issues, integration problems, or other unexpected behavior
