# pi-goal `/goal` Contract

Use this reference when formatting objectives for the `@narumitw/pi-goal`
extension in Pi.

## What `/goal` is

- `/goal` is a Pi extension command for a session-scoped, evidence-checked goal.
- The goal persists in the current Pi session and continues from Pi's settled idle boundary.
- It is not global memory, project instructions, or unbounded background work.
- Goal completion requires evidence; a budget limit or elapsed time is not completion.

## Direct command surface

```text
/goal
/goal <objective>
/goal --tokens 100k <objective>
/goal status
/goal edit <objective>
/goal pause
/goal resume
/goal clear
```

The objective must be non-empty and at most 4,000 characters. pi-goal parses
`--tokens` before the objective and accepts positive budgets with `k` and `m`
suffixes.

When `experimental.goals` is enabled, ordered work can use:

```text
/goal add [--tokens 20k] <objective>
/goal prioritize [--tokens 20k] <objective>
/goal drop-last
/goal skip
```

Queue behavior is experimental. Do not use queue commands unless ordered work
was requested and the setting is enabled.

## Completion and blocked tools

The active agent receives these pi-goal tools:

- `goal_complete({ goal_id, summary })` marks the current goal complete.
- `goal_blocked({ goal_id, reason, evidence, repeated_turns })` stops at a true impasse.

Completion requires the exact current `goal_id`, a non-empty evidence summary,
and no known required work remaining. A blocked report requires the exact
current id, a specific external or user action, concrete evidence, and the same
blocker recurring for at least three consecutive goal turns.

Do not use `goal_blocked` for ordinary uncertainty, incomplete work, difficulty,
normal clarification, or recoverable tool/provider failures.

## Continuation and states

pi-goal may continue an active goal after Pi reports `agent_settled`. It waits
for retries, compaction, queued input, and pending messages to settle before
starting another goal turn.

Possible states include `active`, `paused`, `blocked`, `usage_limited`,
`budget_limited`, and `complete`. User interruption, a true impasse, provider
quota exhaustion, and the user's token budget are distinct outcomes.

## Cross-extension integration

Executable Pi extensions can start a goal by emitting `pi-goal:rpc:start` with:

```json
{
  "requestId": "request-id",
  "objective": "validated objective",
  "tokenBudget": 100000
}
```

The reply is emitted on `pi-goal:rpc:start:reply:<requestId>`. A pre-existing
goal is rejected by RPC start. RPC pause uses `pi-goal:rpc:pause` with a matching
`goalId` or originating `requestId`.

State changes are broadcast on `pi-goal:state` with an authoritative `goalId`
and `status`, plus a completion `summary` or terminal `reason` when available.
A skill that has no executable Pi integration should return a `/goal` command
rather than claim that RPC activation occurred.
