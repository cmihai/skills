---
name: untangle-codebase
description: Restructure tangled code, modules, and files into cohesive, low-coupling modules while preserving observable behavior. Use when Codex should diagnose or implement a reorganization involving misplaced responsibilities, dependency cycles, oversized or grab-bag files, scattered policy decisions, shallow wrappers, parallel code paths, poor test seams, or confusing source-tree layout. Supports language-agnostic work and C/C++ codebases.
---

# Untangle Codebase

Turn a tangled area into deep modules: substantial behavior behind small interfaces, with related knowledge and change concentrated in one place. Diagnose only when asked for analysis; otherwise carry the selected refactor through implementation, verification, tracking, and the repository's completion workflow.

Preserve observable behavior unless the user explicitly authorizes a change. Treat compatibility, error behavior, ordering, performance contracts, file formats, and public interfaces as behavior. Never smuggle a redesign into a reorganization.

## Ground the work

1. Read applicable repository instructions and the architecture documents, trackers, and decision records relevant to the affected area before editing.
2. Inspect the current worktree and preserve unrelated changes. Identify overlapping user edits before moving files or symbols.
3. Establish the relevant build and test baseline. Record existing failures rather than attributing them to the refactor.
4. Trace the selected behavior from callers through implementation, tests, generated files, build rules, and runtime dependencies. Search for existing helpers and decision funnels before proposing new ones.
5. For C or C++, read [references/c-cpp.md](references/c-cpp.md) before designing or implementing the change.

Do not equate a directory, file, class, or namespace with a module. A **module** is behavior behind an **interface**; its implementation may span several physical files. A good physical layout makes that conceptual ownership obvious.

## Map the tangle

Build a compact responsibility-and-dependency map for the affected area:

- Name each responsibility in domain language.
- Identify which files define it, which callers know about it, and which tests exercise it.
- Mark duplicated policy, bidirectional dependencies, mutable shared state, leaky data representations, pass-through wrappers, and changes that routinely touch several locations together.
- Distinguish stable dependencies from volatile details. Dependencies should point toward stable domain policy, not outward toward transports, emitters, frameworks, or command-line wiring.
- Apply the deletion test to proposed modules: if deleting one merely spreads its complexity across callers, it is earning its place; if almost nothing changes, it is shallow indirection.

Use evidence from the repository rather than line counts alone. Large cohesive files can be healthy; many tiny files can form spaghetti. If no concrete ownership or dependency problem warrants a change, explain the evidence and leave the structure alone.

## Design the target shape

State a one-sentence invariant for every proposed module: what it owns and what is always true of it. If the invariant is vague or conjunctive, the module is probably a grab bag.

Choose the target by these priorities:

1. **High cohesion:** behavior, policy, data, and tests that change for the same reason live together.
2. **Low coupling:** callers depend on a small interface and do not know representation, lifecycle, or orchestration details.
3. **Depth:** the interface hides meaningful complexity and gives callers leverage.
4. **Locality:** a likely change or bug can be handled and verified in one module.
5. **Directed dependencies:** the dependency graph is explainable and preferably acyclic.

Place a boundary where it hides representation or policy, clarifies ownership, isolates a volatile dependency, or supports real behavioral variation. One implementation can justify a boundary; adapter count is not the criterion. Prefer a direct module interface unless dependency inversion requires a callable, function table, or abstract interface. Keep seams introduced solely for testing internal.

Sketch at least two plausible target layouts when seam placement or ownership is ambiguous. Compare them by caller knowledge, dependency direction, test surface, migration risk, and the deletion test. Prefer the smallest interface that centralizes the most policy.

Do not create `common`, `shared`, `helpers`, or `utils` as a destination for homeless code. Assign each operation to the module whose invariant it protects. Keep cross-cutting decisions behind one funnel; extend an existing descriptor or registry instead of scattering another kind switch.

## Plan a safe slice

Select the smallest self-contained slice that makes the dependency shape materially better. Define:

- behavior and compatibility invariants;
- files and callers in scope;
- the intended module, interface, and dependency direction;
- characterization or regression tests;
- structural evidence, such as a removed include edge, deleted symbol, eliminated cycle, or enforced layering rule;
- explicit non-goals and follow-up work.

Prefer moving one complete responsibility over partially reorganizing many. Order slices by prerequisites so each landed slice is coherent and reversible. Move dependency leaves first when they are independent; establish the authoritative interface first when callers need it to migrate safely.

## Implement the move

1. Reuse existing tests that cover the affected behavior; add or strengthen tests where coverage is missing. For a pure refactor, characterization tests should pass before and after. For an authorized bug fix, first add a regression test that fails for the right reason.
2. Introduce the target module with its invariant and narrow interface. Accept required dependencies explicitly; return results where practical instead of hiding side effects.
3. Move policy and representation together. Update callers to use the new interface rather than teaching them the new internals.
4. Delete the superseded path, wrapper, duplicated helper, and stale build entry in the same slice. Preserve regression coverage when adapting or replacing implementation-specific tests. Do not leave two paths that should behave identically.
5. Preserve a fallback only when a real compatibility or rollout constraint requires it. Funnel both paths through shared policy and document exactly which inputs use each path and why.
6. Revisit names and file locations only after responsibility is clear. Let ownership determine layout, not aesthetics or file size.

Avoid opportunistic semantic changes, broad formatting churn, speculative abstractions, dependency injection containers, and boolean-flag interfaces. If a necessary behavior change or public-interface break exceeds existing authorization, ask before making that change and continue independent authorized work. Defer unrelated behavior changes to the canonical backlog.

## Verify the result

Run proportionate repository-prescribed checks in the required order and concurrency model:

- focused tests at the module interface;
- affected integration or parity tests;
- full suite, static analysis, layering checks, and build-system validation when required;
- searches proving obsolete symbols, includes, paths, and parallel implementations are gone.

Inspect the final diff against the slice's behavior and structural criteria. Confirm that callers use the intended interface, the old ownership or policy duplication is gone, unrelated edits are preserved, and documentation, trackers, generated manifests, and build files agree with the new layout.

A green suite is necessary but not sufficient. Show that tests reach the migrated behavior and that the planned dependency or caller-knowledge reduction actually occurred.

## Close the slice

Follow the repository's tracking, commit, and push rules. Record deferred tangles in the canonical backlog rather than comments or a new ad hoc tracker. Summarize:

- the responsibility moved and its new owner;
- the dependency or interface simplification;
- behavior and structural verification;
- any pre-existing failures, approved exceptions, or remaining follow-up.
