# Goalcraft

Goalcraft is an Agent Skill for turning a rough task idea into an activation-ready Codex `/goal` objective.

It helps write goals as completion contracts: scoped, evidence-based, bounded by clear stop conditions, and hard to mark complete without real verification.

## Install

Copy or symlink this repository into a Codex skills directory:

```bash
ln -s "$(pwd)" ~/.codex/skills/goalcraft
```

Restart Codex, then invoke the skill with:

```text
Use $goalcraft to turn this rough draft into an activation-ready Codex /goal objective.
```

## What It Produces

Goalcraft drafts `/goal` prompts with:

- destination and starting point;
- scope and deliverables;
- constraints and non-regression rules;
- autonomy and checkpoint guidance;
- verification gates;
- done criteria, stop conditions, and success metrics.

By default it drafts goal text only. It should not activate a Codex goal unless explicitly asked.
