---
name: delegate
description: Orchestrate engineering work through bounded, independently verified subagent slices. Use when the user asks to delegate, parallelize, coordinate subagents, or execute an engineering plan through agents.
---

# Delegate

Ground the effort, assign bounded slices, and verify results as the orchestrator.
Delegate only when a concrete task can run independently alongside useful local
work. Handle small tasks directly when coordination adds no value.

## Ground and preserve the scope

Read applicable repository instructions and the task, design, and tracker
documents named by the user. Inspect the current tree and Git commit before
trusting older plans or claims. Capture repository rules for architecture,
testing, concurrency, tracking, and completion once.

Preserve the full requested backlog, its priority order, and explicit exclusions.
A completed slice is progress, not a redefinition of campaign completion. Keep
long-lived plans in the repository's canonical planning location; for short
efforts, use an available plan tool or a concise working plan. Record dependencies
and update the plan when evidence changes them.

## Choose a cohesive slice

Prefer one observable behavior through its canonical authority and a real
consumer. Prove activation at that consumer or public seam; a green infrastructure
test alone does not establish that the behavior works. For replacements, include
removal of the obsolete mechanism in the same slice.

Aim for one authoritative funnel, one consumer, and the minimum necessary layers.
Use roughly seven production files or 300 changed production lines as default
reassessment signals, excluding focused tests, trackers, and gates. Set a larger
boundary up front when the invariant requires it. File counts must never force a
partial ownership migration, parallel policy, or independently useless commits.

Use schema-only or lifecycle-only prerequisites when no safe end-to-end slice is
available. State why, define independently useful acceptance criteria, and name
the next consumer slice. Do not grow a prerequisite into a speculative framework
or absorb unrelated failures into the current assignment.

## Select models and ownership

- Use `gpt-5.6-terra` for implementation, fixes, refactoring, and tests with concrete acceptance criteria.
- Use `gpt-5.6-sol` for investigation, decomposition, design, independent review, and synthesis.
- For mixed tasks, choose by the primary deliverable. Explicit user model choices take precedence.

Check the current tool's supported models. Pass the selected ID explicitly in
`collaboration.spawn_agent` with `fork_turns="none"` and a self-contained
assignment, or a positive integer string for necessary recent context. Do not
omit `fork_turns` or use `"all"` with an override: full-history forks inherit the
parent's model. If a preferred model is unavailable, disclose the limitation and
use the other named model when suitable, otherwise the runtime default. Respect
explicit user restrictions and report the requested model without inventing
runtime-selection claims.

Treat agents as sharing a filesystem and worktree. Parallelize independent
read-only work or disjoint implementation slices with explicit file ownership;
serialize overlapping changes, dependencies, and shared generated artifacts.
Do not edit a live agent's owned files without coordinating a handoff. Check
capacity before spawning and leave recovery capacity when practical.

The orchestrator owns integration by default: staging, commits, pushes, shared
tracker edits, and aggregate tests. Delegate any of these explicitly to one
owner. Assign focused tests to a named owner too; obey repository runner limits
and never start a competing runner. Agents must not infer permission to commit
or push from repository completion rules when integration belongs to the parent.

## Capture the recovery boundary

Before implementation, capture the baseline commit, status, staged and unstaged
diffs separately, and the existing contents of untracked files in the allowed
scope. Include relevant ignored files if they may be modified. Store snapshots
outside the agent's edit scope. A commit plus an ordinary diff does not capture
all pre-existing work.

The snapshot identifies the starting state, not permanent exclusive ownership.
Before cleanup, inspect for subsequent user or sibling changes. Remove only the
agent's separable delta, preserving pre-existing contents, staging, untracked
files, and later unrelated edits. Prefer a targeted reverse patch; never use a
broad reset or checkout. Verify preservation afterward. If overlapping changes
cannot be separated safely, stop cleanup and coordinate a resolution.

## Assign one contract

Use this compact template as the assignment and source of truth; do not repeat
the same contract across multiple planning documents:

```text
Context: working directory, project purpose, applicable instructions to read
first, then relevant plan/source paths, prior results, and dependencies.
Outcome: one observable result and its canonical authority or lifecycle.
Scope: owned files/directories, non-goals, risks, and agreed size boundary.
Evidence: failing-before test or activation witness where applicable;
acceptance criteria, exact focused verification, and named test owner.
Coordination: recovery snapshot, integration owner, critical repository rules,
and preservation of unrelated changes. Report stalls and tripwires promptly.
Return: changed files, tests/results, activation evidence, commits if assigned,
assumptions, blockers, and remaining work.
```

Use a fresh agent when isolation helps; resume an existing agent when its context
is useful. Include enough information to reconstruct the task without full
conversation history.

## Handle tripwires and recovery

An agent must stop editing and report `SLICE TRIPWIRE` before exceeding the
contract: touching an unlisted owner or public API, adding a second policy path
or consumer, crossing the agreed size boundary, discovering a distinct
prerequisite, or losing independent reviewability. Report the exact evidence,
current diff state, dependency implications, and proposed revised boundary.
Do not keep editing just to make the interrupted slice green.

The orchestrator inspects the evidence and tests a concrete counter-design when
safe. Then choose one of these actions:

- Resume under a revised cohesive contract when the change remains within the user's authorized objective. Preserve safe partial work while completing the whole owner migration; do not land half of it.
- Land an independently useful, verified prefix and contract the remaining work separately. Do not claim consumer activation for an isolated prerequisite.
- Remove unsafe or abandoned exploratory changes using the recovery boundary when they cannot be completed safely or stand independently.

A tripwire requires orchestrator reassessment, not automatic user approval or
rollback. After two successive prerequisite discoveries without activating the
intended behavior, stop dependent launches, reassess the dependency chain, update
the plan, and explain the finding to the user. Continue autonomously when a safe
route remains within scope. Ask only when new authority, a material expansion
of the user's objective, or a decision that cannot be inferred is required.

After interruption, inspect existing work before relaunching. If an agent fails
twice at the same step, take over that step or re-plan; do not repeat the same
prompt a third time. If an agent ends early without a blocker, send the remaining
finish line as a follow-up.

## Verify and close

After each slice or safe batch:

1. Inspect status, diffs, and history; verify claimed commits and pushes.
2. Independently review scope, architecture, acceptance, and preservation of unrelated work. Confirm activation evidence and removal of replaced paths.
3. Have the designated test owner run proportionate focused checks. Reuse results tied to the unchanged reviewed state; repeat checks for changes, failures, or unresolved claims that gate later work.
4. Record results, discoveries, and remaining dependencies in the canonical plan or tracker before starting dependent implementation.

Run broad gates at repository-required integration or push checkpoints. Do not
repeat a known-identical broad failure without a change or a new diagnostic
question. Follow the repository's commit, gate, and push ordering.

Monitor agents, respond to stalls, and communicate meaningful progress during
work, including long-running slices. Before ending, collect child reports and
ensure no delegated work remains running unattended; interrupt and account for
unfinished work when stopping. A status request does not cancel the objective.

Close only when the requested scope is complete or a concrete blocker requires
user input. Reconcile trackers, perform the authorized completion workflow, and
report outcomes, commits, verification, and explicit residual gaps.
