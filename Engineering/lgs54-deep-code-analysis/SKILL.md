---
name: lgs54-deep-code-analysis
description: "Perform deep, systematic code analysis to uncover subtle syntax errors, logic bugs, scope issues, race conditions, memory leaks, and architectural flaws. Uses multi-pass analysis: static structure, control/data flow, semantic correctness, concurrency, performance, and security. Use when reviewing code for production readiness, debugging elusive issues, or validating critical paths."
metadata:
  author: lucas-gs54
  version: "1.0.0"
  purpose: deep-code-analysis
---

# LGS54 Deep Code Analysis

## Purpose

This skill performs **exhaustive, multi-layered code analysis** to find bugs that typical reviews miss: off-by-one errors, lifetime violations, race conditions, incorrect invariants, silent data corruption, performance regressions, and security vulnerabilities. It goes beyond linting and type-checking into semantic, architectural, and runtime-behavior analysis.

The skill applies to any language (Rust, TypeScript, Go, Python, C++, etc.) and adapts its checks to language-specific semantics.

## Language

Communicate with the user in whatever language they are using. Analysis output and code annotations follow the project's language. Technical terms remain in English.

## Supported Analysis Modes

Determine the mode before executing — if ambiguous, ask the user.

- **full** — All passes (structure, flow, semantics, concurrency, performance, security)
- **correctness** — Logic bugs, invariant violations, edge cases, undefined behavior
- **concurrency** — Data races, deadlocks, lock ordering, atomicity, memory ordering
- **performance** — Algorithmic complexity, allocations, cache misses, hidden costs
- **security** — Injection, auth bypass, info leakage, supply chain, crypto misuse
- **architecture** — Coupling, circular deps, layer violations, god objects, hidden state

Multiple modes can be combined (e.g., "correctness + concurrency").

## Mandatory Operating Rule

The skill MUST always follow this workflow:

1. **Identify Analysis Mode & Scope** — Determine target mode(s), codebase size, language, critical paths.
2. **Ingest & Index Codebase** — Build symbol table, call graph, type hierarchy, module dependency graph.
3. **Pass 1: Static Structure** — Syntax validity, type soundness, unused/dead code, shadowing, naming collisions.
4. **Pass 2: Control & Data Flow** — Reachability, uninitialized reads, null/option deref, exception paths, loop invariants.
5. **Pass 3: Semantic Correctness** — Business logic invariants, contract violations, off-by-one, boundary conditions, math errors.
6. **Pass 4: Concurrency & Memory** — Data races, deadlocks, use-after-free, double-free, leaks, ABA problems.
7. **Pass 5: Performance & Security** — Complexity analysis, allocation hot paths, side-channel risks, input validation.
8. **Pass 6: Architectural Integrity** — Coupling metrics, cycle detection, layer violations, testability barriers.
9. **Synthesize & Rank Findings** — Severity (critical/high/medium/low), exploitability, blast radius, fix effort.
10. **Deliver Annotated Report** — Per-finding: location, root cause, execution trace, fix suggestion, regression test.

Do not skip passes. Each pass feeds the next.

## Core Principles

### 1. No False Negatives Over False Positives
Prefer reporting a suspicious pattern with context over missing a real bug. Every finding includes confidence level.

### 2. Trace Every Finding to Root Cause
Don't just flag "possible null deref" — show the exact path: `init → conditional move → unguarded access`.

### 3. Language Semantics Are Law
Rust: borrow checker, move semantics, drop order. TypeScript: structural typing, `any` leakage, narrowing. Go: escape analysis, goroutine leaks. C++: UB, object lifetime, template instantiation.

### 4. Context Is King
A loop is fine in isolation; inside a hot path with allocation it's a bug. A mutex is fine; locked across `await` it's a deadlock risk.

### 5. Invariants Over Assertions
Find where invariants *should* exist but don't. Missing precondition checks > missing assertions.

### 6. Think Like an Attacker (Security Pass)
Every input is malicious. Every boundary is a fence to test. Every trust assumption is a vulnerability.

## Workflow Steps

### Step 1: Identify Analysis Mode & Scope
Gather or clarify:
- Target mode(s): `full`, `correctness`, `concurrency`, `performance`, `security`, `architecture`
- Language(s) and version
- Codebase entry points (CLI, HTTP handlers, event loops, FFI boundaries)
- Critical modules (auth, payments, consensus, kernel)
- Known flaky tests / past incidents
- Performance budgets / SLAs
- Threat model (if security mode)

**If missing critical info → ask. Do not assume.**

### Step 2: Ingest & Index Codebase
Build (or reuse) these indices:
- **Symbol table**: every function, type, const, global, with visibility, mutability, async/sync
- **Call graph**: direct + transitive callees, async edges, trait/impl dispatch, dynamic dispatch
- **Type hierarchy**: traits/interfaces, impls, generics, variance, associated types
- **Module graph**: imports, exports, re-exports, circular deps, public API surface
- **Control flow graphs** per function: basic blocks, edges, loop headers, exit points
- **Data flow facts**: def-use chains, reaching definitions, live variables, taint sources/sinks

### Step 3: Pass 1 — Static Structure
Check:
- [ ] Syntax validity (parser errors)
- [ ] Type soundness (no `any` leakage, exhausted matches, exhaustive patterns)
- [ ] Unused: imports, variables, functions, types, modules, parameters
- [ ] Dead code: unreachable statements, untested branches, uncalled private fns
- [ ] Shadowing: variable, type, import (intentional vs accidental)
- [ ] Naming collisions: same name different scopes, similar names (typo risk)
- [ ] Visibility leaks: private types in public APIs, internal details exposed
- [ ] Generic bounds: missing `Send`/`Sync`, `Clone`, `Copy`, lifetime bounds
- [ ] Macro hygiene: unintended capture, expansion errors

### Step 4: Pass 2 — Control & Data Flow
For each function, trace:
- [ ] **Uninitialized reads**: all paths to use have prior def
- [ ] **Null/Option/Result deref**: every `unwrap`/`expect`/`[]` guarded or proven safe
- [ ] **Exception/panic paths**: all `?`/`.await`/throw sites have handlers or documented panic
- [ ] **Loop invariants**: variant decreases, invariant holds pre/per/post iteration
- [ ] **Early returns**: cleanup runs (drop, defer, finally, RAII)
- [ ] **Fallthrough**: `switch`/`match` exhaustiveness, implicit fallthrough
- [ ] **Short-circuit evaluation**: side effects in `&&`/`||`/`??` order correct
- [ ] **Async yield points**: state consistent across `.await`, no lock held across yield

### Step 5: Pass 3 — Semantic Correctness
Verify business logic:
- [ ] **Pre/post conditions**: documented, checked, or proven (contracts, types, assertions)
- [ ] **Boundary conditions**: empty collections, max/min values, zero, EOF, timeout=0
- [ ] **Off-by-one**: loop bounds, slice indices, pagination, fencepost errors
- [ ] **Integer semantics**: overflow (wrapping/checked/saturating), division by zero, sign conversion
- [ ] **Float semantics**: NaN propagation, equality comparison, precision loss, non-associativity
- [ ] **Collection mutations during iteration**: invalidation, concurrent modification
- [ ] **State machine validity**: all transitions legal, no unreachable states, terminal states handled
- [ ] **Idempotency**: retry-safe operations, exactly-once semantics
- [ ] **Consistency models**: linearizability, sequential consistency, eventual — matches requirement

### Step 6: Pass 4 — Concurrency & Memory
Analyze:
- [ ] **Data races**: shared mutable state without synchronization (lock, atomic, channel, Arc<Mutex>)
- [ ] **Deadlocks**: lock ordering cycles, lock held across `.await`/yield, nested locks
- [ ] **Lock contention**: coarse locks, lock in hot path, lock-free alternatives missed
- [ ] **Atomic misuse**: wrong ordering (Acquire/Release/SeqCst), RMW vs load/store, ABA
- [ ] **Memory leaks**: reference cycles (Rc/Arc), forgotten handles, unbounded caches
- [ ] **Use-after-free / double-free**: manual memory, FFI, `Box::into_raw` not paired
- [ ] **Lifetime violations**: dangling references, `'static` overuse, variance unsoundness
- [ ] **Thread safety**: `Send`/`Sync` correctness, thread-local misuse, TLS destruction order
- [ ] **Goroutine/task leaks**: unjoined handles, missing cancellation, runaway loops

### Step 7: Pass 5 — Performance & Security
**Performance:**
- [ ] **Algorithmic complexity**: nested loops, accidental quadratic, N+1 queries
- [ ] **Allocation hot paths**: per-iteration allocation, boxing, string concat in loop
- [ ] **Cache behavior**: stride access, false sharing, structure of arrays vs array of structures
- [ ] **Sync overhead**: mutex in hot path, channel vs mutex, lock-free where appropriate
- [ ] **Async overhead**: excessive task spawn, `.await` in loop, missing batching
- [ ] **Hidden costs**: serialization, reflection, dynamic dispatch, regex compilation

**Security:**
- [ ] **Input validation**: all boundaries (HTTP, CLI, FFI, DB, fs) validated, parsed safely
- [ ] **Injection**: SQL, command, path traversal, LDAP, XSS, format string
- [ ] **Auth/AuthZ**: bypass via confused deputy, token reuse, privilege escalation
- [ ] **Info leakage**: logs, errors, timing, cache, memory dumps
- [ ] **Crypto**: constant-time, nonce reuse, weak algorithms, side channels
- [ ] **Supply chain**: pinned deps, signature verification, provenance

### Step 8: Pass 6 — Architectural Integrity
Measure:
- [ ] **Coupling**: afferent/efferent, instability, abstractness (Stable Dependencies Principle)
- [ ] **Cycles**: module, package, crate — break with interfaces or direction
- [ ] **Layer violations**: domain → infra, UI → DB, high-level → low-level detail
- [ ] **God objects**: > 500 LOC, > 20 methods, > 7 params, low cohesion
- [ ] **Hidden global state**: static mut, singletons, thread-locals, env vars at runtime
- [ ] **Testability**: pure functions, dependency injection, no hardcoded externals
- [ ] **API surface**: minimal, stable, versioned, backward compatible

### Step 9: Synthesize & Rank Findings
For each finding, assign:
- **Severity**: Critical (data loss, RCE, corruption) / High (logic bug, deadlock, leak) / Medium (perf, maintainability) / Low (style, nit)
- **Confidence**: Proven / High / Medium / Speculative
- **Blast radius**: Single function / Module / Cross-module / System-wide
- **Fix effort**: Trivial / Small / Medium / Large / Refactor
- **Regression test**: Exact test case to prevent recurrence

### Step 10: Deliver Annotated Report
Output format (Markdown):

```markdown
# Deep Code Analysis Report

## Summary
- Mode: full
- Files analyzed: 247
- LOC: 43,211
- Findings: 12 critical, 34 high, 67 medium, 123 low

## Findings by Severity

### 🔴 Critical: Use-After-Free in Connection Pool
**Location**: `src/net/pool.rs:142-158`
**Root cause**: `Connection` returned to pool after `drop` of `TxnGuard` but before `TxnGuard` deref
**Execution trace**:
  1. `TxnGuard::new(conn)` — borrows `conn` mutably
  2. `pool.return_conn(conn)` — moves `conn` back to pool (invalidates borrow)
  3. `TxnGuard` dropped — tries to access freed `conn`
**Fix**: Return connection in `TxnGuard::drop` after all access, or use `Option<Connection>` + `take()`
**Regression test**: `test_pool_return_during_guard_drop`

### 🟠 High: Off-by-One in Pagination Cursor
**Location**: `src/api/posts.rs:88`
**Root cause**: `LIMIT $1 OFFSET $2` with 1-based page but 0-based offset calculation
**Fix**: `OFFSET (page - 1) * page_size`
**Regression test**: `test_pagination_page_1_returns_first_items`

...
```

## Quality Checklist

Before delivering, verify:

- [ ] Analysis mode identified and all passes executed
- [ ] Codebase fully indexed (no "unknown symbol" gaps)
- [ ] Every finding has: location, root cause, execution trace, fix, regression test
- [ ] Severity/confidence/blast-radius/fix-effort assigned per finding
- [ ] Language-specific semantics respected (borrow checker, type system, memory model)
- [ ] No pass skipped — each feeds the next
- [ ] Critical/High findings have executable reproduction steps
- [ ] Report includes summary stats (files, LOC, finding counts)
- [ ] False positives documented with reasoning
- [ ] Architectural findings reference coupling metrics, not opinion

If any critical item fails, re-run affected passes.

## Reference Files

- `references/analysis-passes.md` — Detailed pass algorithms, data structures, pseudocode
- `references/language-semantics.md` — Per-language rules: Rust, TypeScript, Go, Python, C++
- `references/bug-patterns.md` — Catalog of deep bug patterns with detection strategies
- `references/severity-rubric.md` — Scoring guidelines, blast radius, fix effort calibration
- `references/architectural-metrics.md` — Coupling, cohesion, cycle detection, layering rules
- `references/report-template.md` — Output format, annotation style, machine-readable JSON schema

## Anti-Patterns

Avoid:
- Running only linter/type-checker and calling it "analysis"
- Flagging `unwrap()` without proving it can/cannot panic
- Missing async yield points as race conditions
- Treating all `unsafe` as bugs (some are correct and necessary)
- Ignoring FFI boundaries (C/C++/WASM/JS interop)
- Reporting style nits as "medium" severity
- Missing cross-module cycles because analysis stopped at crate boundary
- No regression test — finding will regress