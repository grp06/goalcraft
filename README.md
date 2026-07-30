# Goalcraft

Goalcraft turns a messy task description into a concise, evidence-checked
pi-goal `/goal` objective.

Pi's `/goal` command keeps the objective active across turns. Goalcraft shapes
that objective around the outcome, verification surface, constraints,
boundaries, iteration policy, and blocked stop condition, so completion requires
current evidence rather than intention.

Goalcraft is a Pi skill, not a replacement for the pi-goal extension. By
itself it drafts and validates the `/goal` command. Run that command in Pi to
activate it, or use an executable integration that submits it through the
pi-goal RPC contract.

## Install

Clone the repo, then symlink it into Pi's global skills directory:

```bash
git clone https://github.com/grp06/goalcraft.git
cd goalcraft
ln -s "$(pwd)" ~/.pi/agent/skills/goalcraft
```

Pi also discovers skills from `.pi/skills/`, `.agents/skills/`, configured skill
paths, packages, and explicit `--skill` paths. Restart Pi after installing or
changing a skill.

Invoke the skill with `/skill:goalcraft` when skill commands are enabled. Give
it a rough brief; it will turn it into a ready-to-run `/goal` command.

## What you get back

A compact `/goal` objective that spells out:

- **Outcome** — what must be true when the work is done
- **Verification surface** — tests, benchmarks, logs, reports, screenshots, source material, or other evidence
- **Constraints** — what must not regress
- **Boundaries** — files, systems, tools, data, repositories, and user-controlled actions to stay within
- **Iteration policy** — how to choose the next useful action after each attempt
- **Blocked stop condition** — when to stop, what evidence to report, and what input would unlock progress
- **Durable state** — plan, experiment, decision, or notes files when the work may run for hours or days

The objective is intentionally short. Put supporting rationale, examples,
detailed evaluator notes, and nonessential context in companion files.

Goalcraft recommends a normal prompt for one-line edits, simple explanations,
short code reviews, or requests without an auditable finish line.

## pi-goal surface

The generated command uses pi-goal's direct command surface:

```text
/goal <objective>
/goal --tokens 100k <objective>
/goal edit <objective>
/goal pause
/goal resume
/goal clear
```

With pi-goal's experimental queue enabled, `/goal add`, `/goal prioritize`,
`/goal drop-last`, and `/goal skip` are also available. Goalcraft should use
those only when ordered work is explicitly requested.

pi-goal validates objectives at up to 4,000 characters. Validate a command
manually from this repository with:

```bash
python3 scripts/validate_goal_length.py --max-chars 4000 goal.txt
```

The validator belongs to Goalcraft. When used from another project, resolve
`scripts/validate_goal_length.py` relative to Goalcraft's own `SKILL.md`.
