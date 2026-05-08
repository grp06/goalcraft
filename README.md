# Goalcraft

Goalcraft is an Agent Skill for turning a rough task idea into an activation-ready Codex `/goal` objective.

It helps write goals as completion contracts: scoped, evidence-based, bounded by clear stop conditions, and hard to mark complete without real verification.

## Install

Copy or symlink this repository into a Codex skills directory:

```bash
ln -s "$(pwd)" ~/.codex/skills/goalcraft
```

Restart Codex, then invoke the skill with a rough, real-world brief. It does not need to be clean:

```text
Use $goalcraft to turn this into a Codex /goal:

I need to clean up the billing settings page in the web app. It's kind of half-done from a previous pass. The upgrade button works but the loading and error states are janky, and I think mobile is probably broken. Please make it match the rest of the settings UI, don't touch the Stripe webhook stuff or pricing logic unless something is obviously wrong, and don't do a huge refactor. It should have tests or at least whatever checks this repo normally uses. I want Codex to keep going until the page is actually usable and verified, but it should stop and ask me before changing public APIs, database schema, or anything payment-risky.
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

Before returning a goal, Goalcraft validates that the objective text after `/goal ` is less than 4,000 characters. Its working target is 3,400 characters, and drafts at 3,800+ characters should be compressed. The bundled validator can be used directly:

```bash
python3 scripts/validate_goal_length.py --target-chars 3400 --strict-target goal.txt
```
