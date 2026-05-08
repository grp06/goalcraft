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

Convert messy intent into a compact, activation-ready Codex `/goal` objective that keeps the agent moving until real completion evidence exists. The returned goal objective must be less than 4,000 characters; target 3,800 characters or fewer so minor formatting changes cannot break Codex validation. Prefer drafting the goal text only. Do not call `create_goal`, `thread/goal/set`, or otherwise activate a goal unless the user explicitly asks to start or set it.

For current Codex `/goal` mechanics, read [references/codex-goal-contract.md](references/codex-goal-contract.md) when the exact runtime behavior matters.

## Workflow

1. Identify the rough objective, workspace, and expected end state.
   - If the user supplied a draft, preserve their intent and tighten it.
   - If a repo or files are mentioned, inspect them before finalizing the goal.
   - If a critical success criterion is missing, ask the smallest necessary question; otherwise state assumptions.

2. Shape the goal around evidence, not effort.
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
   - Target limit: draft to 3,800 characters or fewer.
   - Make every requirement auditable against files, commands, PR state, logs, screenshots, or explicit user confirmation.
   - Include exact commands only when they are already known from the repo or user.
   - Avoid hidden flags in slash text. `/goal --tokens 50K ...` is literal objective text in the TUI, not parsed syntax.
   - If a token budget is requested, present it separately from the objective text unless the target surface supports a separate budget field.
   - For complex or ambiguous work, recommend a planning/interview pass before setting the goal.
   - For very large work, include subagent/orchestration guidance only when the current Codex environment supports subagents and the work can be split into bounded, reviewable lanes.

4. Validate length before returning.
   - Put only the ready-to-paste `/goal ...` command in a temporary file or pipe it to `scripts/validate_goal_length.py`.
   - The script strips a leading `/goal ` and counts the actual objective Codex validates.
   - If validation fails, compress the goal and validate again.
   - Do not return a final goal until the validator passes.
   - If the script is unavailable, count characters with a deterministic local command or script before returning.

5. Decide output mode.
   - Default: return a ready-to-paste `/goal ...` block plus a short assumptions list.
   - If the user asks for review, critique the draft first and include a revised version.
   - If the user explicitly asks to activate the goal, call the goal tool or app-server surface only after the objective is final and no existing active goal conflict is unresolved.

## Output Format

Use this shape unless the user requests something shorter:

```markdown
Assumptions:
- ...

Ready-to-paste goal:

/goal Destination: ...

Starting point: ...

Core objective: ...

Scope: ...

Deliverables: ...

Must not regress: ...

Autonomy rules: ...

Checkpoint rhythm: ...

Verification gates: ...

Done when: ...

Stop conditions: ...

Success metric: ...
```

For very short goals, keep the same fields but compress each to one sentence.

After the block, report the validated objective character count, for example: `Objective length: 2,914 characters`.

## Quality Bar

- The goal should be operationally sharp enough that another agent can continue after compaction or resume.
- The goal should make premature completion hard: "done" must require evidence, not intent, elapsed time, or passing unrelated checks.
- The goal should avoid over-prescribing implementation details unless those details are part of the actual requirement.
- The goal should preserve user boundaries: planning-only, no edits, no deploys, no commits, or approval requirements must be explicit when present.
- The final ready-to-paste goal must pass `scripts/validate_goal_length.py` or an equivalent deterministic character-count check.
