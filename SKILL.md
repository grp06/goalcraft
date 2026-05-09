---
name: goalcraft
description: >-
  Turn a rough draft, vague ambition, or messy task brief into a powerful Codex
  /goal objective for long-running autonomous work. Use when the user asks to
  write, improve, format, sharpen, stress-test, or activate a Codex goal,
  thread goal, durable goal, or /goal prompt.
---

# Goalcraft

## Core Contract

Convert messy intent into a compact, activation-ready Codex `/goal` objective that keeps the agent moving until real completion evidence exists. The returned goal objective must be less than 4,000 characters, and the normal working target is 2,800 characters. Prefer drafting the goal text only. Do not call `create_goal`, `thread/goal/set`, or otherwise activate a goal unless the user explicitly asks to start or set it.

For current Codex `/goal` mechanics, read [references/codex-goal-contract.md](references/codex-goal-contract.md) when the exact runtime behavior matters.

## Workflow

1. Identify the rough objective, workspace, and expected end state.
   - If the user supplied a draft, preserve their intent and tighten it.
   - If a repo or files are mentioned, inspect them before finalizing the goal.
   - If a critical success criterion is missing, ask the smallest necessary question; otherwise state assumptions.

2. Shape the goal around evidence, not effort. Use this list as input thinking, not as required output structure.
   - Destination: what must be true at the end.
   - Starting point: current context, repo, branch, artifact, issue, or known state.
   - Core objective: the concrete work to complete.
   - Scope: files, directories, systems, or task boundaries the agent should stay within.
   - Deliverables: concrete artifacts such as code paths, tests, PRs, reports, screenshots, or logs.
   - Must not regress: constraints, behavior to preserve, safety boundaries.
   - Autonomy rules: when to continue, when to ask, what to verify directly.
   - Checkpoint rhythm: how often to test, review, summarize progress, or pause.
   - Verification gates: commands, inspections, screenshots, evals, or other proof loops.
   - Done when: artifact-level acceptance criteria.
   - Stop conditions: blockers, destructive actions, missing credentials, user approval boundaries.
   - Success metric: the observable result that proves completion.

3. Keep the objective usable by Codex.
   - Hard limit: the objective text after `/goal ` must be less than 4,000 characters.
   - Normal target: draft to 2,800 characters or fewer.
   - Strict fallback ceiling: do not return a normal goal above 3,400 characters.
   - Treat 3,800 characters or more as a failed draft even if it is technically below the hard limit.
   - Put useful but nonessential detail in companion notes outside the `/goal` payload.
   - Make every requirement auditable against files, commands, PR state, logs, screenshots, or explicit user confirmation.
   - Include exact commands only when they are already known from the repo or user.
   - Avoid hidden flags in slash text. `/goal --tokens 50K ...` is literal objective text in the TUI, not parsed syntax.
   - If a token budget is requested, present it separately from the objective text unless the target surface supports a separate budget field.
   - For complex or ambiguous work, recommend a planning/interview pass before setting the goal.
   - For very large work, include subagent/orchestration guidance only when the current Codex environment supports subagents and the work can be split into bounded, reviewable lanes.

4. Choose the output shape.
   - Always start with compact shape: Destination, Context, Scope, Preserve, Verify, Done/stop.
   - Do not use the full checklist as output structure unless the user explicitly asks for a verbose draft outside `/goal`.
   - Do not force every planning concept into the `/goal`. Reason with the full checklist, then compress related items before output.
   - Merge deliverables into Scope.
   - Merge must-not-regress into Preserve.
   - Merge autonomy and checkpoint rhythm into Done/stop.
   - Keep examples and candidate lists outside the goal unless they are essential execution constraints.
   - Section soft budgets: Destination 250, Context 350, Scope 700, Preserve 400, Verify 550, Done/stop 550 characters.

5. Validate length before returning.
   - Put only the ready-to-paste `/goal ...` command in a temporary file or pipe it to this skill's bundled validator: resolve `scripts/validate_goal_length.py` relative to the directory containing this `SKILL.md`, then run it with `--target-chars 2800 --strict-target`.
   - The validator belongs to Goalcraft, not to the user's working project. Do not search the user's repository for `scripts/validate_goal_length.py`.
   - The script strips a leading `/goal ` and counts the actual objective Codex validates.
   - If validation fails once, rewrite by cutting at least 25%: remove examples, collapse lists, merge fields, and move nonessential detail to companion notes.
   - If validation fails twice, abandon the draft and switch to emergency shape: `/goal Complete [objective] in [scope]. Preserve [constraints]. Verify with [checks/evidence]. Stop for [risks]. Done when [criteria].`
   - Do not keep making small incremental trims. More than two validation failures means the draft shape failed.
   - Do not return a final goal until the validator passes.
   - If the bundled script path is unavailable, use this deterministic fallback on the file containing the exact final `/goal ...` command:

```bash
python3 - "$GOAL_FILE" <<'PY'
import pathlib, sys
text = pathlib.Path(sys.argv[1]).read_text(encoding="utf-8").strip()
if text.startswith("```"):
    lines = text.splitlines()
    if lines and lines[0].startswith("```"):
        lines = lines[1:]
    if lines and lines[-1].startswith("```"):
        lines = lines[:-1]
    text = "\n".join(lines).strip()
if text.startswith("/goal") and text[len("/goal"):].startswith((" ", "\n", "\t")):
    text = text[len("/goal"):].strip()
count = len(text)
print(f"objective_chars={count}")
if count > 2800:
    raise SystemExit(1)
PY
```

6. Decide output mode.
   - Default: return a ready-to-paste `/goal ...` block plus a short assumptions list.
   - If the user asks for review, critique the draft first and include a revised version.
   - If the user explicitly asks to activate the goal, call the goal tool or app-server surface only after the objective is final and no existing active goal conflict is unresolved.

## Output Format

Return companion notes outside the goal when details should not spend objective characters. Use this shape by default:

```markdown
Assumptions:
- ...

Ready-to-paste goal:

/goal Destination: ...

Context: ...

Scope: ...

Preserve: ...

Verify: ...

Done/stop: ...

Objective length: N characters

Notes outside the goal:
- ...
```

Omit companion notes when they add no value. Do not put assumptions, rationale, candidate lists, or optional details inside the `/goal` unless they are execution-critical.

## Quality Bar

- The goal should be operationally sharp enough that another agent can continue after compaction or resume.
- The goal should make premature completion hard: "done" must require evidence, not intent, elapsed time, or passing unrelated checks.
- The goal should avoid over-prescribing implementation details unless those details are part of the actual requirement.
- The goal should preserve user boundaries: planning-only, no edits, no deploys, no commits, or approval requirements must be explicit when present.
- The final ready-to-paste goal must pass this skill's bundled `scripts/validate_goal_length.py --target-chars 2800 --strict-target` or the deterministic fallback above.
