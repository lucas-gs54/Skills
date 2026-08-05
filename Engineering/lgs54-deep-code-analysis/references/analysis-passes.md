# Analysis Passes — Algorithms, Data Structures, Pseudocode

Detailed implementation guidance for each analysis pass. Adapt to language/tooling.

---

## Pass 1: Static Structure

### Symbol Table Construction
```
for each file in codebase:
  parse AST
  for each declaration:
    if function: record (name, params, return_type, async, visibility, span)
    if type: record (name, kind, generics, bounds, visibility, span)
    if const/static: record (name, type, mutability, visibility, span)
    if module: record (name, parent, visibility, span)
  for each import: record (source, items, visibility, span)
  for each attribute/macro: record (name, args, span)
```

**Output**: `SymbolTable { functions: Map<SymbolId, FunctionInfo>, types: Map<SymbolId, TypeInfo>, ... }`

### Call Graph Construction
```
for each function f:
  for each call site in f.body:
    callee = resolve_call(callee_expr, f.generics, type_context)
    if callee.is_dynamic:
      add_edge(f, callee, edge_type=DynamicDispatch)
    else:
      add_edge(f, callee, edge_type=Direct)
  for each async block/await:
    add_async_edge(f, continuation)
```

**Output**: `CallGraph { nodes: Set<FunctionId>, edges: Vec<CallEdge> }`

### Type Hierarchy
```
for each trait/interface T:
  for each impl I of T:
    record_subtype(I.self_type, T)
  for each supertrait S of T:
    record_supertrait(T, S)
for each generic G with bounds:
  record_bounds(G, bounds)
```

**Output**: `TypeHierarchy { subtypes: Map<Type, Set<Type>>, supertraits: Map<Trait, Set<Trait>>, bounds: Map<Generic, Bounds> }`

### Module Dependency Graph
```
for each module M:
  for each import in M:
    target = resolve_module(import.path)
    add_edge(M, target, kind=import.kind)
  for each re_export in M:
    add_edge(M, re_export.target, kind=ReExport)
```

**Detect cycles**: Tarjan's SCC on module graph.

---

## Pass 2: Control & Data Flow

### Control Flow Graph (Per Function)
```
blocks = []
current = new_block()
for stmt in function.body:
  if stmt.is_terminator (return, break, continue, throw, await, if, match, loop):
    current.terminator = stmt
    blocks.push(current)
    current = new_block()
  else:
    current.statements.push(stmt)
blocks.push(current)

// Add edges
for block in blocks:
  match block.terminator:
    Return | Throw | Break | Continue => no successors
    If(cond, then, else) => add_edge(block, then); add_edge(block, else)
    Match(scrutinee, arms) => for arm in arms: add_edge(block, arm.body)
    Loop(body) => add_edge(block, body); add_edge(body, block)
    Await(fut) => add_edge(block, continuation); add_async_edge(block, fut)
```

### Data Flow Analysis (Gen/Kill)
```
domain = Variables × Lattice(Uninit, Init, Moved, BorrowedImm, BorrowedMut)
for each block in reverse_postorder(cfg):
  in[block] = meet(out[pred] for pred in preds(block))
  out[block] = transfer(block, in[block])
until fixed_point
```

**Transfer functions**:
- `let x = expr` → `kill(x)`, `gen(x := Init)`
- `let mut x = expr` → `kill(x)`, `gen(x := Init)`
- `x = expr` → `kill(x)`, `gen(x := Init)` (move if !Copy)
- `&x` → `require(x == Init)`, `gen(x := BorrowedImm)`
- `&mut x` → `require(x == Init)`, `gen(x := BorrowedMut)`
- `drop(x)` / scope end → `kill(x)`
- `move x` → `kill(x)`, `gen(x := Moved)`

**Check at use sites**: `require(var != Uninit && var != Moved)`

### Taint Analysis (Security Pass)
```
sources = { user_input, http_request, cli_args, env_vars, db_results, file_read, ffi_return }
sinks = { sql_query, shell_exec, file_write, http_response, log, ffi_call }

taint[var] = False for all var
worklist = all source assignments
while worklist not empty:
  stmt = worklist.pop()
  if stmt assigns tainted value to var:
    if not taint[var]:
      taint[var] = True
      for use in uses(var):
        worklist.push(use)
        if use in sinks:
          report_finding(use, "tainted flow")
```

---

## Pass 3: Semantic Correctness

### Invariant Inference (Daikon-style)
```
for each function f:
  for each program point p in f:
    collect values of all visible variables at p over test runs
    infer likely invariants:
      - x > 0, x < 100
      - len(arr) == n
      - sorted(arr)
      - x != null
    check against explicit contracts (requires/ensures)
```

### Boundary Condition Enumeration
```
for each loop for i in 0..n:
  test_cases = [n=0, n=1, n=max_int, n=len-1, n=len]
for each slice access arr[i..j]:
  test_cases = [i=0, j=0, i=j, i>j, i=len, j=len, i<0, j<0]
for each division a / b:
  test_cases = [b=0, b=1, b=-1, b=max_int]
for each float comparison a == b:
  test_cases = [a=NaN, b=NaN, a=inf, b=-inf, a=0.0, b=-0.0]
```

### Integer Overflow Classification
```
for each arithmetic op:
  if language.has_checked_arithmetic:
    if not explicit (wrapping/saturating/checked):
      flag "implicit wrapping — verify intent"
  else:
    if no explicit check:
      flag "potential overflow"
```

---

## Pass 4: Concurrency & Memory

### Lock Graph & Deadlock Detection
```
for each mutex M:
  for each lock acquisition in function f:
    record (f, M, line, held_locks_at_point)
for each function f:
  for each pair (M1, M2) acquired in f:
    if order(M1, M2) != global_order:
      flag "lock ordering violation"
detect cycles in lock wait-for graph across threads
```

### Atomic Ordering Verification
```
for each atomic operation:
  required_ordering = infer_from_algorithm(algorithm)
  actual_ordering = operation.ordering
  if actual_ordering < required_ordering:
    flag "insufficient ordering"
  if actual_ordering > required_ordering:
    flag "overly strong ordering (perf)"
```

**Common patterns**:
- Mutex lock → Acquire
- Mutex unlock → Release
- Flag publish → Release
- Flag consume → Acquire
- SeqCst only for: single global order required (e.g., sequence lock)

### Escape Analysis (Go/Rust/C++)
```
for each allocation site:
  escapes = false
  for each use:
    if use is return / global assign / channel send / thread spawn / closure capture:
      escapes = true
  if escapes:
    flag "heap allocation (consider stack/arena)"
```

### Lifetime Analysis (Rust)
```
for each reference r with lifetime 'a:
  for each use of r:
    require 'a: 'b where 'b is lifetime at use point
  for each assignment to r:
    require 'b: 'a where 'b is lifetime of source
detect:
  - 'static overuse (leaks, incorrect Send/Sync)
  - variance violations (covariant where contravariant needed)
  - higher-ranked trait bounds missing
```

---

## Pass 5: Performance & Security

### Complexity Estimation
```
for each loop nest:
  complexity = product of loop bounds (if constant)
  if any bound is dynamic:
    complexity = "O(n^k) where k = nest_depth"
  if loop contains allocation / syscall / await:
    flag "hidden cost in hot loop"
```

### Allocation Profiling (Static)
```
for each function:
  allocations = count(Box::new, Vec::new, String::new, format!, vec!, to_owned, clone)
  if allocations > threshold and function in hot path:
    flag "excessive allocation"
```

### Crypto Constant-Time Check
```
for each secret-dependent branch:
  flag "timing leak"
for each secret-dependent memory access:
  flag "cache timing leak"
for each division/modulo by secret:
  flag "variable-time arithmetic"
```

---

## Pass 6: Architectural Integrity

### Coupling Metrics
```
for each module M:
  Ca = count of modules depending on M (afferent)
  Ce = count of modules M depends on (efferent)
  I = Ce / (Ca + Ce)  // instability (0=stable, 1=unstable)
  A = abstract_types / total_types  // abstractness
  // Stable Dependencies Principle: I should decrease as A increases
  // Stable Abstractions Principle: A + I ≈ 1
```

### Layer Violation Detection
```
layers = [Domain, Application, Infrastructure, Presentation]
for each module M:
  layer = assign_layer(M)  // by path, naming, or annotation
for each dependency M -> N:
  if layer(M) < layer(N):  // higher layer depends on lower (wrong)
    flag "layer violation: M (layer X) -> N (layer Y)"
```

### God Object Detection
```
for each type T:
  loc = lines_of_code(T)
  methods = count_methods(T)
  params = avg_params(methods)
  cohesion = LCOM4(T)  // Lack of Cohesion of Methods
  if loc > 500 or methods > 20 or params > 7 or cohesion > threshold:
    flag "god object candidate"
```

---

## Integration: Multi-Pass Pipeline

```
index = build_indices(codebase)
findings = []

findings += pass1_static_structure(index)
findings += pass2_control_data_flow(index)
findings += pass3_semantic_correctness(index)
findings += pass4_concurrency_memory(index)
findings += pass5_performance_security(index)
findings += pass6_architectural(index)

// Cross-pass correlation
for f in findings:
  f.confidence = correlate(f, findings)  // e.g., data flow + semantic = higher confidence
  f.blast_radius = compute_blast_radius(f, call_graph, module_graph)

rank(findings, by: severity * confidence * blast_radius / fix_effort)
```

---

## Tooling Integration Points

| Pass | Rust | TypeScript | Go | Python | C++ |
|------|------|------------|----|--------|-----|
| Index | rust-analyzer, syn | ts-morph, TypeScript API | go/analysis, golang.org/x/tools | jedi, pyright | clang, tree-sitter |
| CFG | rustc MIR | ts-morph CFG | go/analysis CFG | pycfg | clang CFG |
| Data flow | rustc MIR borrowck | custom | go/analysis dataflow | custom | clang dataflow |
| Concurrency | send/sync, lock_order | — | go vet -race, staticcheck | threading | TSan, clang thread safety |
| Perf | cargo-flamegraph, perf | — | pprof | py-spy, scalene | perf, VTune |
| Security | cargo-audit, clippy | npm audit, semgrep | govulncheck, gosec | bandit, semgrep | cppcheck, clang SAST |