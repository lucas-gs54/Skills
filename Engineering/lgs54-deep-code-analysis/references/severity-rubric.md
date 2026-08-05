# Severity Rubric — Scoring Guidelines, Blast Radius, Fix Effort Calibration

Standardized scoring so findings are comparable across codebases, languages, and analysts.

---

## Severity Levels

| Level | Label | Definition | Response Time | Example |
|-------|-------|------------|---------------|---------|
| **0** | **Critical** | Data loss, RCE, authentication bypass, silent data corruption, safety hazard | **Immediate** (stop deploy, hotfix) | SQLi, use-after-free in prod path, nonce reuse, auth bypass |
| **1** | **High** | Logic bug causing incorrect behavior, deadlock, resource leak, DoS, privilege escalation | **24-48h** | Off-by-one in billing, deadlock in payment path, goroutine leak, path traversal |
| **2** | **Medium** | Performance regression, maintainability risk, partial DoS, info leakage (non-secret), flaky test | **Sprint** (2 weeks) | N+1 query, lock contention, missing input validation (non-critical), god object |
| **3** | **Low** | Style, naming, dead code, minor perf, missing doc, test gap | **Backlog** | Unused import, dead code, naming inconsistency, missing `#[must_use]` |

### Severity Decision Tree

```
START: Does this allow unauthorized access / data loss / RCE / safety hazard?
  ├─ YES → CRITICAL (0)
  └─ NO: Does this cause incorrect results / deadlock / leak / DoS / privilege escalation?
    ├─ YES → HIGH (1)
    └─ NO: Does this cause measurable perf regression / maintainability risk / info leak / flaky?
      ├─ YES → MEDIUM (2)
      └─ NO → LOW (3)
```

---

## Confidence Levels

| Level | Label | Criteria |
|-------|-------|----------|
| **A** | **Proven** | Static proof (borrow checker, type checker, model checker) OR reproducible test case OR TSan/ASan report |
| **B** | **High** | Data flow trace + semantic reasoning + multiple independent indicators |
| **C** | **Medium** | Strong heuristic (pattern match + context) but no full trace |
| **D** | **Speculative** | Single pattern match, no context, or heuristic only |

**Default**: Start at **C**, upgrade with evidence, downgrade with uncertainty.

---

## Blast Radius

| Level | Scope | Description | Escalation |
|-------|-------|-------------|------------|
| **1** | **Function** | Single function / closure / method | Fix in place |
| **2** | **Module** | Multiple functions in same file/module | Refactor module |
| **3** | **Cross-module** | Callers across module boundary, same crate/package | API change, coordination |
| **4** | **System-wide** | Cross-crate/service, protocol, schema, config | Architecture review, migration plan |

**Computation**:
```
blast_radius = max(
  call_graph_transitive_callers(finding.location).unique_modules().count(),
  module_graph_transitive_dependents(finding.module).count()
)
if blast_radius == 1: level 1
elif blast_radius <= 3: level 2
elif blast_radius <= 10: level 3
else: level 4
```

---

## Fix Effort

| Level | Estimate | Description |
|-------|----------|-------------|
| **XS** | **< 1h** | One-liner: add guard, fix typo, add `#[must_use]`, change bound |
| **S** | **1-4h** | Local refactor: extract fn, add check, fix loop bound, add test |
| **M** | **4h-2d** | Module refactor: new type, change API, add synchronization, fix leak |
| **L** | **2d-1w** | Cross-module: new abstraction, protocol change, migration, locking redesign |
| **XL** | **> 1w** | Architecture: new service, schema migration, locking overhaul, rewrite |

**Estimation Factors**:
- `+1` level if: no tests, legacy code, FFI boundary, `unsafe` involved
- `-1` level if: good test coverage, pure function, typed language, clear pattern

---

## Priority Score (for ranking)

```
priority = severity_weight * confidence_weight * blast_radius_weight / fix_effort_weight

weights:
  severity:  Critical=100, High=50, Medium=20, Low=5
  confidence: Proven=1.0, High=0.8, Medium=0.5, Speculative=0.2
  blast_radius: L1=1, L2=2, L3=4, L4=8
  fix_effort: XS=1, S=2, M=4, L=8, XL=16
```

**Rank highest first**. Ties broken by: severity → confidence → blast_radius → fix_effort (ascending).

---

## Regression Test Requirement

Every finding **must** include a regression test spec:

```markdown
### Regression Test: <Finding ID>
**Type**: unit / integration / property / fuzz / contract
**Location**: `tests/regression/<finding_id>.rs` (or equivalent)
**Given**: <preconditions, setup, inputs>
**When**: <action that triggers bug>
**Then**: <expected behavior — fix prevents regression>
**Property** (if property-based): `forall x. precondition(x) ==> postcondition(f(x))`
**Fuzz target** (if fuzz): `fuzz_target!(|input| { ... })`
**Contract** (if design-by-contract): `requires: ..., ensures: ...`
```

**Test must fail before fix, pass after**.

---

## False Positive Handling

| Action | When |
|--------|------|
| **Suppress with justification** | Pattern matches but context proves safe (e.g., `unwrap()` after `if let Some(x) = opt`) |
| **Downgrade confidence** | Heuristic match without full trace |
| **Annotate as "known safe"** | Intentional pattern (e.g., `unsafe` with `// SAFETY:`) |
| **Create suppression rule** | Recurring pattern in codebase (e.g., `unwrap()` in test-only code) |

**Suppression format**:
```rust
// deep-analysis: suppress <FindingID> REASON: <one-line justification>
// Example:
// deep-analysis: suppress UNWRAP_PANIC REASON: guarded by `if let Some(x) = opt` above
fn foo(opt: Option<i32>) {
    if let Some(x) = opt { /* use x */ }
    // opt is None here — but we don't unwrap
}
```

**Suppression rules file** (per repo): `.deep-analysis/suppressions.toml`
```toml
[suppressions]
UNWRAP_PANIC = "test code only"
DEAD_CODE = "generated code in src/generated/"
```

---

## Reporting Template (Per Finding)

```markdown
## [SEV] <Short Title> — <FindingID>

**Severity**: Critical / High / Medium / Low
**Confidence**: Proven / High / Medium / Speculative
**Blast Radius**: Function / Module / Cross-module / System-wide
**Fix Effort**: XS / S / M / L / XL
**Category**: Logic / Concurrency / Memory / Performance / Security / Architecture
**CWE**: <CWE-ID if applicable> (e.g., CWE-119, CWE-362, CWE-401)

**Location**: `path/to/file.rs:123-145` (function `foo::bar`)
**Module**: `crate::module::submodule`

**Root Cause**: <one-paragraph explanation of why bug exists>

**Execution Trace** (if applicable):
```
1. <step 1: entry point>
2. <step 2: condition>
3. <step 3: bug triggered>
```

**Impact**: <what happens in production: data loss, wrong result, hang, leak, vuln>

**Fix Suggestion**: <code snippet or description of fix>

**Regression Test**: `tests/regression/<finding_id>.rs`

**Related Findings**: <other finding IDs if same root cause>

**Suppressed**: No / Yes (reason: ...)
```

---

## CWE Mapping (Common)

| Finding Pattern | CWE |
|-----------------|-----|
| Buffer overflow | CWE-119 |
| Use after free | CWE-416 |
| Double free | CWE-415 |
| Memory leak | CWE-401 |
| Null deref | CWE-476 |
| Division by zero | CWE-369 |
| Integer overflow | CWE-190 |
| Off-by-one | CWE-193 |
| Race condition | CWE-362 |
| Deadlock | CWE-833 |
| Lock held too long | CWE-667 |
| SQL injection | CWE-89 |
| Command injection | CWE-78 |
| Path traversal | CWE-22 |
| XSS | CWE-79 |
| Auth bypass | CWE-287 |
| Info leak | CWE-200 |
| Weak crypto | CWE-327 |
| Nonce reuse | CWE-323 |
| Hardcoded secret | CWE-798 |