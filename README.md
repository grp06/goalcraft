# Goalcraft

Goalcraft turns a messy task description into a Codex `/goal` you can paste and run.

When you use `/goal`, the agent runs autonomously toward whatever you wrote down. A weak goal means the agent will claim "done" on something that isn't. Goalcraft writes goals that are tight on scope, clear on what counts as done, and structured so the agent has to show real evidence before declaring success.

## Install

Symlink this repo into your Codex skills directory:

```bash
ln -s "$(pwd)" ~/.codex/skills/goalcraft
```

Restart Codex, then invoke it with a rough brief. The brief doesn't need to be clean — that's the point:

> Use `$goalcraft` to turn this into a Codex `/goal`:
>
> I need to clean up the billing settings page in the web app. It's kind of half-done from a previous pass. The upgrade button works but the loading and error states are janky, and I think mobile is probably broken. Please make it match the rest of the settings UI, don't touch the Stripe webhook stuff or pricing logic unless something is obviously wrong, and don't do a huge refactor. It should have tests or at least whatever checks this repo normally uses. I want Codex to keep going until the page is actually usable and verified, but it should stop and ask me before changing public APIs, database schema, or anything payment-risky.

## What you get back

A `/goal` that spells out:

- **Where things stand and what "done" looks like** — so the agent has a clear destination
- **What to work on, what to leave alone** — scope and non-negotiables
- **How to verify the work** — specific commands, tests, or checks that prove it shipped
- **When to keep going vs. stop and ask** — autonomy rules for risky or ambiguous moves
- **What counts as success** — measurable criteria, not "looks good to me"

By default, goalcraft only drafts the goal. It won't activate it unless you tell it to.

## Length safety

Codex caps `/goal` text at 4,000 characters. Goalcraft targets 3,400 and compresses any draft over 3,800 before returning it. If you want to check a goal manually:

```bash
python3 scripts/validate_goal_length.py --target-chars 3400 --strict-target goal.txt
```

That script is bundled with the Goalcraft skill repo. When using the skill inside another project, the validator path should point back to the installed Goalcraft skill directory, not the target project's `scripts/` folder.
