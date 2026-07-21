# C and C++ Restructuring Guide

Read this reference when untangling C or C++. Treat headers, translation units, linkage, templates, ownership, and the build graph as part of the design—not as cleanup after moving code.

## Diagnose the physical dependency graph

- Prefer `compile_commands.json`, the build system, and preprocessor-aware tools when available; text searches alone miss generated and conditional includes.
- Inspect both include direction and symbol direction. A forward declaration can hide an include edge without removing conceptual coupling.
- Distinguish public headers, private implementation headers, and translation-unit-local declarations.
- Find mutable globals, singleton access, registration side effects, macro-defined policy, and `extern` declarations. These often create coupling that the file tree conceals.
- Check generated sources, unity builds, platform variants, and test-only compilation units before moving a symbol.

Useful evidence includes include-graph cycles, rebuild fan-out after touching a header, exported-symbol changes, duplicate decision switches, and lists of callers accessing representation fields directly.

## Choose ownership before files

Group declarations and definitions by the invariant they protect, not by syntax category. Avoid layouts such as `all_types.h`, `helpers.cpp`, or one file containing every visitor merely because the language permits it.

Keep together when they change together:

- a data representation and the operations that maintain its invariant;
- allocation, destruction, and ownership transfer policy;
- a compiler IR node family and transformations that alone understand its representation;
- a descriptor table and the decisions derived from it;
- an external C ABI and its conversion or validation layer.

Separate orchestration from policy. For example, a compiler driver may sequence parse, analyze, lower, and emit, but it should not re-derive element layout or ownership rules owned by those modules.

## Shape headers deliberately

- Put only caller-required declarations in a public header. Includes required solely by implementation belong in the `.c` or `.cpp` file.
- Prefer private headers for implementation shared by several translation units. Do not expose them through the public include tree.
- Use forward declarations only when callers truly do not need the complete type and lifetime rules remain clear.
- Avoid exposing concrete containers, allocator choices, LLVM types, platform handles, or ownership machinery unless they are genuinely part of the interface.
- Do not reach for PImpl mechanically. It is useful for ABI stability or hiding volatile dependencies, but adds allocation, indirection, and lifecycle complexity.
- Keep template implementation visible only when required. Consider a non-template core behind a small template facade when templates cause broad recompilation or leak policy.
- Replace policy macros with typed constants, enums, functions, or descriptor tables when semantics permit. Preserve required configuration and portability behavior.

For C interfaces, use opaque structs and module-owned functions when they create real encapsulation:

```c
typedef struct parser parser;

parser *parser_create(const parser_options *options);
parse_result parser_parse(parser *, source_view);
void parser_destroy(parser *);
```

Document allocation, borrowing, lifetime, nullability, thread safety, and error ownership. These facts are part of the interface.

## Make dependency direction visible

Prefer a shape such as:

```text
command or driver -> domain/compiler module -> stable value types
                                      |
                                      v
                            injected external port
```

Avoid domain headers including command drivers, code generators including interpreter internals, or low-level runtime code knowing about high-level orchestration.

When two modules include each other, do not merely add forward declarations. Identify the misplaced fact:

- Move a neutral value type to the module that owns its invariant, or to a deliberately stable lower-level module if neither side owns it.
- Move an operation next to the representation it interprets.
- Invert a real external dependency through a narrow function table, abstract base, or callable only when at least two adapters justify the seam.
- Merge modules when the cycle reveals one cohesive responsibility rather than inventing an interface between inseparable halves.

## C/C++ examples

### Centralize compiler type policy

Before: the interpreter, optimizer, and code generator each switch on an element-kind enum to derive byte width, signedness, and storage class.

After: the module owning the element kind exposes one immutable descriptor lookup. Each caller asks that interface; duplicated switches disappear. Keep backend-specific emission in the backend, but derive shared policy from the descriptor rather than adding another branch.

Structural proof: search for the old kind switches, verify only the descriptor construction remains, and run semantic parity plus backend tests.

### Break a header cycle by moving responsibility

Before: `analysis.h` includes `lowering.h` for a result type, while `lowering.h` includes `analysis.h` to query facts.

Weak fix: forward-declare types until compilation succeeds. The conceptual cycle remains.

Better fix: let analysis own an immutable `AnalysisResult` value and queries over it. Lowering depends on that public analysis interface; analysis has no knowledge of lowering. Put lowering-only caches and backend types in private lowering headers.

Structural proof: the include and conceptual dependency point one way, touching the private lowering header no longer rebuilds analysis, and tests construct or obtain analysis results without backend setup.

### Split an oversized translation unit by invariant

Before: `runtime.cpp` contains allocation, array layout, numeric conversion, printing, and command dispatch.

Do not split it into arbitrary line-count chunks. First identify ownership:

- array storage owns layout and lifetime;
- numeric conversion owns checked representation changes;
- formatting owns textual rendering;
- the runtime facade sequences them through narrow interfaces.

Move one responsibility at a time, including its tests and private helpers. Keep shared representation decisions behind the storage module rather than copying field knowledge into each new file.

Structural proof: callers no longer access storage fields directly, each moved responsibility has one owner, the old definitions are gone, and the build links no duplicate or missing symbols.

## Verify C/C++ moves

In addition to project tests:

- perform a clean or dependency-sensitive rebuild when headers or build rules move;
- run the repository's compiler warnings, static analysis, sanitizers, and layering checks when applicable;
- check symbol visibility and ABI compatibility when public libraries are involved;
- test destruction, move/copy behavior, error paths, and exceptional exits when ownership changes;
- inspect incremental rebuild fan-out when reducing header coupling is an explicit goal;
- confirm removed files and new files are correctly represented in every build configuration.

Do not claim lower coupling merely because compilation succeeds. Show the removed dependency edge, reduced caller knowledge, or centralized policy decision.
