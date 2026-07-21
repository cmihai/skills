---
name: delegate
description: Orchestrate substantial engineering work through grounded, independently verified subagent slices. Use when the user asks Codex to delegate, parallelize, coordinate subagents, execute a long itemized plan, or drive a multi-stage migration or refactor through agents.
---

# Delegate

Act as the orchestrator. Ground the work, assign bounded slices, verify each result, and keep the user informed. Implement work directly only when coordination would add no value or the recovery rules call for taking over a failed step.

## Ground the effort

1. Read the repository instructions and every design, tracker, or task document named by the user.
2. Inspect the current tree before trusting paths, mechanisms, baselines, or claims from an older plan. Record the current commit when the repository uses Git.
3. Convert the request into ordered, independently testable slices. Give each slice a goal, scope, non-goals, risks, dependencies, acceptance criteria, and verification commands.
4. Put the plan in the repository's canonical planning location for a long-lived effort. For a short effort, maintain it with the available plan tool. Do not create a new planning convention when the repository already has one.
5. Capture standing repository rules once: required tests, concurrency limits, architecture tripwires, tracking files, and commit or push policy.
6. Establish relevant baselines before implementation. A green suite alone is insufficient when the change must prove that a new path activates or an old path disappears.

## Choose safe concurrency

- Do not specify or claim a model for delegated subagents; the runtime selects
  the model.
- Treat all subagents as sharing one filesystem and worktree.
- Run slices sequentially when they edit overlapping areas, depend on one another, alter shared generated files, or run expensive test suites.
- Parallelize only independent read-only investigations or clearly disjoint implementation slices with explicit file ownership.
- Never allow two test runners at once when repository instructions prohibit it. Designate one agent or the orchestrator as the test owner.
- Do not edit files owned by a live implementation agent. Inspect read-only state or coordinate through messages until it finishes.
- Check available agent capacity before spawning. Keep one slot available for orchestration or recovery when practical.

## Launch each slice

Use a fresh subagent when isolation improves focus. Prefer a context-light launch backed by committed or otherwise persistent artifacts; include conversation context only when the task cannot be reconstructed safely from the repository.

Make every assignment self-contained:

- State the working directory and one-line project purpose.
- Require the agent to read the applicable `AGENTS.md` and repository instructions first.
- Name the plan and source files to read, in order.
- Describe relevant prior slices and current state.
- State the slice goal, ordered steps, allowed file scope, and explicit non-goals.
- Specify concrete verification, including a failing-before test or activation evidence when applicable.
- Repeat critical concurrency, test, tracking, and commit rules from the repository.
- Require preservation of unrelated user changes.
- Define the stall protocol and the required final report.

Require the final report to include changed files, commits if applicable, tests and results, evidence that the intended path is live, assumptions, and deferred work.

## Supervise without duplicating work

1. Monitor agent status and respond to questions or newly discovered constraints.
2. Send course corrections promptly when scope, repository rules, or upstream state changes.
3. If an agent stops early without a genuine blocker, send a follow-up with the remaining finish line and let it retain its context.
4. Do not start a dependent slice until its prerequisite has passed the verification gate.
5. Report meaningful progress to the user after each landed slice or parallel batch: outcome first, then evidence, findings, and what comes next.
6. Before ending the delegation, collect every child report and confirm no delegated agents remain live. If the user asks for a launch-only status, either continue monitoring in the same turn or explicitly cancel work that should not continue unattended.

## Verify independently

After each slice or safe parallel batch:

1. Inspect repository status, diffs, and history. Confirm claimed commits and pushes actually exist when required.
2. Review the changed code against the slice scope, repository architecture rules, and acceptance criteria.
3. Re-run proportionate checks from a single designated test owner. Spot-check any claim that gates later work.
4. Confirm the test exercises the changed path and that obsolete parallel paths were removed when replacement was the goal.
5. Record discoveries, changed assumptions, and deferred work in the repository's canonical plan or tracker before launching a fresh dependent agent.
6. Keep unrelated pre-existing changes intact.

Do not accept a blocker solely from an agent's assertion. Check the evidence and attempt a concrete counter-design when safe.

## Recover from failure

- If an agent is interrupted, inspect the shared worktree before relaunching. Resume from existing work rather than recreating it.
- If the same agent fails twice at the same step, take over that step or re-plan it. Do not repeat the same prompt a third time.
- If a slice invalidates the plan, stop dependent launches, revise the plan and acceptance criteria, then continue.
- If partial work is unsafe or failing, preserve any verified green prefix and remove only the failed agent's known changes with non-destructive, targeted edits. Never discard unrelated work.
- If completion needs new authority or materially expanded scope, stop and ask the user.

## Close the delegation

When all slices pass, update the plan and trackers, perform the repository's required completion workflow, and give the user a compact summary of slices landed, commits, verification, important discoveries, and any explicitly deferred items.
