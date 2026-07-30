---
name: goalcraft
description: >-
  Turn a rough draft, vague ambition, or messy task brief into a sharp pi-goal
  /goal objective for persistent, evidence-checked work. Use when the user asks
  to write, improve, format, sharpen, stress-test, or activate a pi-goal.
---

# Goalcraft

## Core contract

Convert messy intent into a compact pi-goal `/goal` objective. A strong goal
defines a closed loop: choose the next action, run it, score progress against an
explicit evaluator or checklist, record the result, then continue or stop based
on evidence.

The default result is a validated, ready-to-run `/goal` command. Do not claim
that a goal is active unless Pi confirms activation. If the current surface can
submit `/goal` and the user asked for activation, submit it; otherwise return
the command for the user to run.

## Workflow

1. Decide whether a Goal is the right tool.
   - Use one when the work has a durable objective, an evidence-based finish line, and may require several turns of investigation or iteration.
   - Prefer a normal prompt for a one-line edit, simple explanation, short code review, or request where the user wants one answer and then a stop.
   - If the finish line is vague, recommend a goal-sharpening pass first.

2. Identify the rough objective, workspace, and expected end state.
   - Preserve the user's intent and tighten it.
   - Inspect mentioned repositories or files before finalizing the goal.
   - Ask only for a missing criterion that is necessary to define success; otherwise state the assumption.

3. Shape the goal around evidence. Use these fields as the drafting spine.
   - Outcome: what must be true at the end.
   - Verification surface: tests, benchmarks, reports, artifacts, logs, command output, screenshots, source material, or explicit confirmation that proves it.
   - Constraints: what must not regress.
   - Boundaries: files, directories, systems, tools, data, repositories, and externally visible actions to stay within.
   - Iteration policy: how to choose the next useful action after each attempt.
   - Blocked stop condition: when to stop, what evidence to report, and what input would unlock progress.

4. Add supporting details only when they improve the contract.
   - Starting point, deliverables, approval boundaries, checkpoint rhythm, success metric, realistic environment, and durable progress surfaces are useful when execution-critical.
   - For multi-hour work, use a plan, attempt ledger, decision log, scratchpad, or current-state summary so the next turn can continue without rereading the conversation.

5. Design the feedback loop before drafting.
   - Identify a score, checklist, test result, benchmark, report, artifact count, or other evidence that distinguishes progress from stalling.
   - For measurable work, define a baseline, target, measurement method, and anti-gaming check.
   - Prefer a fast evaluator during iteration and a slower final gate.
   - Label proxy evidence as proxy evidence when the realistic environment is unavailable.
   - For research, separate confirmed findings, approximate reconstructions, support-only evidence, blocked claims, and remaining uncertainty.

6. Keep the objective usable by pi-goal.
   - The objective after `/goal` must be at most 4,000 characters.
   - `/goal --tokens 50K ...` is parsed by pi-goal as a token-budget option. Include it only when the user requests a budget.
   - `k` and `m` budget suffixes are supported by pi-goal's command surface.
   - Use `/goal add`, `/goal prioritize`, `/goal drop-last`, or `/goal skip` only when the experimental ordered queue is enabled and requested.
   - Do not embed implementation details, rationale, or a large plan when a file reference will do.

7. Choose the compact output shape.
   - Outcome
   - Context
   - Boundaries
   - Constraints
   - Verify
   - Iterate/done/stop
   - Merge deliverables into Boundaries and must-not-regress requirements into Constraints.

8. Validate before activation.
   - Put only the final `/goal ...` command in a temporary file.
   - Run the bundled validator relative to this skill directory with `--max-chars 4000`.
   - Validate once. If it passes, do not keep shortening it.
   - If it fails, switch to the emergency shape:
     `/goal Complete [outcome] within [boundaries]. Preserve [constraints]. Verify with [checks/evidence]. Stop for [risks/blockers]. Done when [criteria].`
   - Do not present an unvalidated command as ready to run.

9. Activate or return the command.
   - If Pi confirms that `/goal` was submitted, report activation and the objective length.
   - Otherwise return the ready-to-run `/goal ...` command and say that it must be run in Pi.
   - Do not silently replace an existing active goal; ask for confirmation first.
   - If an executable integration uses pi-goal RPC, pass an absolute positive integer `tokenBudget`, not a `k`/`m` string.

## Quality bar

- Completion must require authoritative current evidence, not intent, elapsed time, budget exhaustion, or a proxy signal.
- Preserve every explicit requirement; do not narrow success to an easier result.
- Make fake progress hard by preserving meaningful coverage, realistic inputs, real interaction paths, and relevant counter-metrics.
- For long-running implementation work, include a cleanup/review gate and summarize final evidence plus remaining risks.
- Preserve user boundaries such as no edits, no deploys, no commits, or approval requirements.

## Output format

For an ordinary request:

```markdown
Ready goal:

/goal Outcome: ...

Context: ...

Boundaries: ...

Constraints: ...

Verify: ...

Iterate/done/stop: ...

Objective length: N characters

Notes outside the goal:
- ...
```

For an explicitly draft-only request, return the same command without claiming
activation. For a successful Pi activation, report the confirmed active state.

For pi-goal's completion behavior and command details, read
[references/pi-goal-contract.md](references/pi-goal-contract.md).
