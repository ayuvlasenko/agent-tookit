# agent-toolkit

Reusable coding-agent skills, plugins, and workflows.

## Install in Pi

Install the package globally from Git:

```bash
pi install git:github.com/ayuvlasenko/agent-toolkit
```

For the current project only, use the local project settings:

```bash
pi install -l git:github.com/ayuvlasenko/agent-toolkit
```

Check installed packages with `pi list`. Update this package with:

```bash
pi update git:github.com/ayuvlasenko/agent-toolkit
```

## Install an individual skill with Vercel Skills

List skills in this repository:

```bash
npx skills@latest add ayuvlasenko/agent-toolkit --list --full-depth
```

Install `brainstorm` into the current Pi project:

```bash
npx skills@latest add ayuvlasenko/agent-toolkit \
  --skill brainstorm \
  --agent pi \
  --full-depth
```

Install it globally:

```bash
npx skills@latest add ayuvlasenko/agent-toolkit \
  --skill brainstorm \
  --agent pi \
  --full-depth \
  --global
```

Install `planning-make` with the same options by replacing `brainstorm` with `planning-make`.

Update only this skill:

```bash
npx skills@latest update brainstorm
```

## Skills

### brainstorm

Collaborative design dialogue that turns ideas into approaches, designs, and implementation plans.

- Package: [`packages/brainstorm`](packages/brainstorm)
- Skill: [`SKILL.md`](packages/brainstorm/skills/brainstorm/SKILL.md)
- Source: [`umputun/cc-thingz/plugins/brainstorm`](https://github.com/umputun/cc-thingz/tree/master/plugins/brainstorm)

The skill is ported from `cc-thingz` for Pi. Its source wording, structure, and behavior are preserved; only Pi-specific integration points are adapted.

### planning-make

Create structured implementation plan in `docs/plans/`.

- Package: [`packages/planning`](packages/planning)
- Skill: [`SKILL.md`](packages/planning/skills/planning-make/SKILL.md)
- Source: [`umputun/cc-thingz/plugins/planning/commands/make.md`](https://github.com/umputun/cc-thingz/blob/master/plugins/planning/commands/make.md)

Invoke directly with `/skill:planning-make <description>` or let it activate when asking for an implementation plan.
