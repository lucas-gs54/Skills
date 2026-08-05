# Report Template — Output Format, Annotation Style, Machine-Readable JSON Schema

Standardized report format for deep code analysis findings. Supports both human-readable Markdown and machine-readable JSON.

---

## Markdown Report Structure

```markdown
# Deep Code Analysis Report

## Metadata
- **Project**: <name>
- **Commit**: <sha>
- **Branch**: <branch>
- **Analyzed at**: <ISO 8601 timestamp>
- **Analyzer version**: lgs54-deep-code-analysis v1.0.0
- **Mode(s)**: full / correctness / concurrency / performance / security / architecture
- **Language(s)**: Rust, TypeScript, Go, etc.
- **Files analyzed**: 247
- **Total LOC**: 43,211
- **Analysis duration**: 4m 32s

## Summary
| Severity | Count | % of Total |
|----------|-------|------------|
| Critical | 12 | 5.2% |
| High | 34 | 14.7% |
| Medium | 67 | 29.0% |
| Low | 118 | 51.1% |
| **Total** | **231** | **100%** |

| Confidence | Count |
|------------|-------|
| Proven | 45 |
| High | 89 |
| Medium | 72 |
| Speculative | 25 |

| Blast Radius | Count |
|--------------|-------|
| Function | 156 |
| Module | 48 |
| Cross-module | 22 |
| System-wide | 5 |

| Fix Effort | Count |
|------------|-------|
| XS | 67 |
| S | 89 |
| M | 52 |
| L | 19 |
| XL | 4 |

## Findings by Category
| Category | Critical | High | Medium | Low |
|----------|----------|------|--------|-----|
| Logic/Correctness | 5 | 18 | 32 | 41 |
| Concurrency | 3 | 9 | 14 | 12 |
| Memory/Lifetime | 2 | 4 | 8 | 15 |
| Performance | 0 | 2 | 9 | 28 |
| Security | 2 | 1 | 3 | 14 |
| Architecture | 0 | 0 | 1 | 8 |

## Top 10 Findings (by Priority Score)
| # | ID | Severity | Confidence | Blast Radius | Fix Effort | Title | Location |
|---|----|----------|------------|--------------|------------|-------|----------|
| 1 | DA-2024-0042 | Critical | Proven | Cross-module | M | Use-after-free in connection pool | `src/net/pool.rs:142` |
| 2 | DA-2024-0017 | Critical | High | System-wide | L | Nonce reuse in AES-GCM | `crypto/aead.rs:88` |
| 3 | DA-2024-0089 | High | Proven | Module | S | Off-by-one in pagination cursor | `src/api/posts.rs:88` |
| 4 | DA-2024-0156 | High | High | Cross-module | M | Deadlock in payment pipeline | `src/payments/pipeline.rs:201` |
| 5 | DA-2024-0203 | High | Medium | Module | XS | Missing auth check on admin endpoint | `src/api/admin.rs:45` |

---

## Detailed Findings

### DA-2024-0042 — Use-After-Free in Connection Pool
**Severity**: Critical
**Confidence**: Proven
**Blast Radius**: Cross-module
**Fix Effort**: M
**Category**: Memory/Lifetime
**CWE**: CWE-416
**Location**: `src/net/pool.rs:142-158` (fn `Pool::return_conn`)
**Module**: `crate::net::pool`

**Root Cause**:
`Connection` returned to pool after `TxnGuard` drop but before `TxnGuard` dereference completes. The `TxnGuard` holds `&mut Connection`; returning the `Connection` to the pool moves it, invalidating the borrow. When `TxnGuard::drop` runs, it accesses freed memory.

**Execution Trace**:
```
1. txn = pool.begin_txn() → TxnGuard { conn: &mut pool.connections[0] }
2. pool.return_conn(conn) → moves conn back to pool.pool[0] (invalidates &mut)
3. txn.commit() → TxnGuard dropped → accesses self.conn (freed)
```

**Impact**: Silent memory corruption; potential RCE if attacker controls freed memory content; data loss.

**Fix Suggestion**:
```rust
// Option 1: Return in Drop impl
impl Drop for TxnGuard {
    fn drop(&mut self) {
        if let Some(conn) = self.conn.take() {
            self.pool.return_conn(conn);
        }
    }
}

// Option 2: Pool returns guard, not connection
fn return_txn(&self, txn: TxnGuard) { /* ... */ }
```

**Regression Test**: `tests/regression/DA-2024-0042.rs`
```rust
#[test]
fn test_pool_return_during_guard_drop() {
    let pool = Pool::new(2);
    let txn = pool.begin_txn().unwrap();
    // Simulate early return
    pool.return_txn(txn); // should not double-free or use-after-free
    // Pool should still be usable
    let txn2 = pool.begin_txn().unwrap();
    txn2.commit().unwrap();
}
```

**Related Findings**: DA-2024-0043 (similar pattern in `Cache::return_entry`)

**Status**: Open

---

### DA-2024-0017 — Nonce Reuse in AES-GCM
**Severity**: Critical
**Confidence**: High
**Blast Radius**: System-wide
**Fix Effort**: L
**Category**: Security
**CWE**: CWE-323
**Location**: `crypto/aead.rs:88` (fn `encrypt`)
**Module**: `crate::crypto::aead`

**Root Cause**: `nonce` derived from `timestamp || counter` but `counter` resets on process restart. If two messages encrypted within same millisecond after restart, nonce repeats → catastrophic AES-GCM failure (key recovery).

**Execution Trace**:
```
1. Process starts → counter = 0
2. encrypt("msg1") → nonce = timestamp_ms || 0
3. encrypt("msg2") → nonce = timestamp_ms || 0 (same ms)
4. Attacker collects both ciphertexts → recovers auth key → forges messages
```

**Impact**: Full plaintext recovery, message forgery, key compromise.

**Fix Suggestion**:
```rust
use chacha20poly1305::XChaCha20Poly1305; // 192-bit nonce
// OR
fn generate_nonce() -> [u8; 12] {
    let mut nonce = [0u8; 12];
    getrandom::getrandom(&mut nonce).unwrap(); // CSPRNG
    nonce
}
// AND persist counter to disk / use monotonic counter
```

**Regression Test**: `tests/regression/DA-2024-0017.rs`
```rust
#[test]
fn test_nonce_uniqueness_under_restart() {
    let crypto = Crypto::new();
    let nonces: HashSet<_> = (0..10000)
        .map(|_| crypto.generate_nonce())
        .collect();
    assert_eq!(nonces.len(), 10000); // all unique
}
```

**Related Findings**: DA-2024-0018 (weak RNG in `generate_key`)

**Status**: Open

---

### DA-2024-0089 — Off-by-One in Pagination Cursor
**Severity**: High
**Confidence**: Proven
**Blast Radius**: Module
**Fix Effort**: XS
**Category**: Logic/Correctness
**CWE**: CWE-193
**Location**: `src/api/posts.rs:88` (fn `list_posts`)
**Module**: `crate::api::posts`

**Root Cause**: `OFFSET $2` with 1-based page parameter but 0-based offset calculation: `offset = page * page_size` instead of `(page - 1) * page_size`.

**Execution Trace**:
```
1. GET /posts?page=1&page_size=20
2. offset = 1 * 20 = 20
3. Returns posts 21-40 instead of 1-20
```

**Impact**: Page 1 returns empty/wrong data; pagination broken for first page.

**Fix Suggestion**:
```rust
// Before
let offset = page * page_size;

// After
let offset = (page.saturating_sub(1)) * page_size;
```

**Regression Test**: `tests/regression/DA-2024-0089.rs`
```rust
#[test]
fn test_pagination_page_1_returns_first_items() {
    let posts = create_test_posts(50);
    let page1 = list_posts(1, 20);
    assert_eq!(page1.len(), 20);
    assert_eq!(page1[0].id, 1); // first post
}
```

**Status**: Fixed in PR #1247

---

## Appendix: All Findings (Machine-Readable)

See `findings.json` for complete machine-readable list.

## Methodology
- **Passes executed**: 1-6 (full mode)
- **Tools**: rust-analyzer, clippy, miri, loom, cargo-audit, cargo-geiger, custom data flow
- **Lines of code analyzed**: 43,211
- **Functions analyzed**: 2,847
- **Modules analyzed**: 156

## Disclaimer
This analysis finds *potential* issues. Proven findings have reproducible test cases. Speculative findings require manual verification. Absence of finding ≠ absence of bug.
```

---

## Machine-Readable JSON Schema

```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "title": "Deep Code Analysis Report",
  "type": "object",
  "required": ["metadata", "summary", "findings"],
  "properties": {
    "metadata": {
      "type": "object",
      "required": ["project", "commit", "branch", "analyzed_at", "analyzer_version", "modes", "languages", "files_analyzed", "total_loc", "analysis_duration_seconds"],
      "properties": {
        "project": { "type": "string" },
        "commit": { "type": "string", "pattern": "^[0-9a-f]{40}$" },
        "branch": { "type": "string" },
        "analyzed_at": { "type": "string", "format": "date-time" },
        "analyzer_version": { "type": "string" },
        "modes": { "type": "array", "items": { "type": "string", "enum": ["full", "correctness", "concurrency", "performance", "security", "architecture"] } },
        "languages": { "type": "array", "items": { "type": "string" } },
        "files_analyzed": { "type": "integer", "minimum": 0 },
        "total_loc": { "type": "integer", "minimum": 0 },
        "analysis_duration_seconds": { "type": "number", "minimum": 0 }
      }
    },
    "summary": {
      "type": "object",
      "required": ["by_severity", "by_confidence", "by_blast_radius", "by_fix_effort", "by_category"],
      "properties": {
        "by_severity": {
          "type": "object",
          "properties": {
            "critical": { "type": "integer" },
            "high": { "type": "integer" },
            "medium": { "type": "integer" },
            "low": { "type": "integer" }
          }
        },
        "by_confidence": {
          "type": "object",
          "properties": {
            "proven": { "type": "integer" },
            "high": { "type": "integer" },
            "medium": { "type": "integer" },
            "speculative": { "type": "integer" }
          }
        },
        "by_blast_radius": {
          "type": "object",
          "properties": {
            "function": { "type": "integer" },
            "module": { "type": "integer" },
            "cross_module": { "type": "integer" },
            "system_wide": { "type": "integer" }
          }
        },
        "by_fix_effort": {
          "type": "object",
          "properties": {
            "xs": { "type": "integer" },
            "s": { "type": "integer" },
            "m": { "type": "integer" },
            "l": { "type": "integer" },
            "xl": { "type": "integer" }
          }
        },
        "by_category": {
          "type": "object",
          "patternProperties": {
            "^[a-z_]+$": {
              "type": "object",
              "properties": {
                "critical": { "type": "integer" },
                "high": { "type": "integer" },
                "medium": { "type": "integer" },
                "low": { "type": "integer" }
              }
            }
          }
        }
      }
    },
    "findings": {
      "type": "array",
      "items": {
        "type": "object",
        "required": ["id", "severity", "confidence", "blast_radius", "fix_effort", "category", "title", "location", "module", "root_cause", "impact", "fix_suggestion", "regression_test", "status"],
        "properties": {
          "id": { "type": "string", "pattern": "^DA-\\d{4}-\\d{4}$" },
          "severity": { "type": "string", "enum": ["critical", "high", "medium", "low"] },
          "confidence": { "type": "string", "enum": ["proven", "high", "medium", "speculative"] },
          "blast_radius": { "type": "string", "enum": ["function", "module", "cross_module", "system_wide"] },
          "fix_effort": { "type": "string", "enum": ["xs", "s", "m", "l", "xl"] },
          "category": { "type": "string", "enum": ["logic", "concurrency", "memory", "performance", "security", "architecture"] },
          "cwe": { "type": "string", "pattern": "^CWE-\\d+$" },
          "title": { "type": "string" },
          "location": { "type": "string" },
          "module": { "type": "string" },
          "root_cause": { "type": "string" },
          "execution_trace": { "type": "array", "items": { "type": "string" } },
          "impact": { "type": "string" },
          "fix_suggestion": { "type": "string" },
          "regression_test": { "type": "string" },
          "related_findings": { "type": "array", "items": { "type": "string" } },
          "status": { "type": "string", "enum": ["open", "fixed", "wont_fix", "suppressed"] },
          "suppressed_reason": { "type": "string" },
          "priority_score": { "type": "number" }
        }
      }
    }
  }
}
```

---

## JSON Output Example (Single Finding)

```json
{
  "id": "DA-2024-0042",
  "severity": "critical",
  "confidence": "proven",
  "blast_radius": "cross_module",
  "fix_effort": "m",
  "category": "memory",
  "cwe": "CWE-416",
  "title": "Use-After-Free in Connection Pool",
  "location": "src/net/pool.rs:142-158",
  "module": "crate::net::pool",
  "root_cause": "Connection returned to pool after TxnGuard drop but before TxnGuard dereference completes. TxnGuard holds &mut Connection; returning Connection moves it, invalidating borrow. TxnGuard::drop accesses freed memory.",
  "execution_trace": [
    "txn = pool.begin_txn() → TxnGuard { conn: &mut pool.connections[0] }",
    "pool.return_conn(conn) → moves conn back to pool.pool[0] (invalidates &mut)",
    "txn.commit() → TxnGuard dropped → accesses self.conn (freed)"
  ],
  "impact": "Silent memory corruption; potential RCE if attacker controls freed memory content; data loss.",
  "fix_suggestion": "Return connection in TxnGuard::drop after all access, or use Option<Connection> + take().\n\n```rust\nimpl Drop for TxnGuard {\n    fn drop(&mut self) {\n        if let Some(conn) = self.conn.take() {\n            self.pool.return_conn(conn);\n        }\n    }\n}\n```",
  "regression_test": "tests/regression/DA-2024-0042.rs",
  "related_findings": ["DA-2024-0043"],
  "status": "open",
  "priority_score": 84.7
}
```

---

## Annotation Style (In-Code)

### Finding Reference Comment
```rust
// deep-analysis: finding DA-2024-0042
// SEVERITY: critical | CONFIDENCE: proven
// Use-after-free: Connection returned to pool before TxnGuard drop completes
fn return_conn(&self, conn: Connection) { ... }
```

### Suppression Comment
```rust
// deep-analysis: suppress DA-2024-0042 REASON: Fixed in PR #1247 - TxnGuard now returns conn in Drop
fn return_conn(&self, conn: Connection) { ... }
```

### Confidence Marker
```rust
// deep-analysis: confidence=proven (miri + test)
// deep-analysis: confidence=high (data flow + semantic)
// deep-analysis: confidence=medium (pattern + context)
// deep-analysis: confidence=speculative (heuristic only)
```

---

## CI/CD Integration

### GitHub Actions Annotation
```yaml
- name: Deep Code Analysis
  run: |
    deep-analysis --format json --output findings.json
    deep-analysis --format github-annotation --output annotations.txt
  continue-on-error: true

- name: Annotate PR
  if: github.event_name == 'pull_request'
  uses: actions/github-script@v7
  with:
    script: |
      const fs = require('fs');
      const annotations = fs.readFileSync('annotations.txt', 'utf8').split('\n').filter(Boolean);
      for (const ann of annotations) {
        const [path, line, severity, message] = ann.split('|');
        github.rest.checks.createAnnotation({
          owner: context.repo.owner,
          repo: context.repo.repo,
          check_run_id: ${{ steps.analysis.outputs.check_run_id }},
          path,
          start_line: parseInt(line),
          end_line: parseInt(line),
          annotation_level: severity === 'critical' || severity === 'high' ? 'failure' : 'warning',
          message,
        });
      }
```

### Annotation Format (pipe-delimited)
```
path/to/file.rs|142|failure|DA-2024-0042: Use-after-free in connection pool (critical, proven)
path/to/file.rs|88|warning|DA-2024-0089: Off-by-one in pagination (high, proven)
```

---

## Suppression File Format (`.deep-analysis/suppressions.toml`)

```toml
[suppressions]
# ID = "reason"
DA-2024-0042 = "Fixed in PR #1247"
DA-2024-0089 = "Fixed in commit abc123"

[suppression_rules]
# Pattern-based suppressions
"UNWRAP_PANIC" = "test code only"
"DEAD_CODE" = "generated code in src/generated/"
"UNUSED_IMPORT" = "prelude imports"

[confidence_overrides]
# Override confidence for specific patterns in this codebase
"test code" = "speculative"
"generated code" = "speculative"
"example code" = "medium"
```

---

## Report Generation CLI

```bash
# Full report
deep-analysis analyze --mode full --format markdown --output report.md

# JSON for tooling
deep-analysis analyze --mode full --format json --output findings.json

# GitHub annotations
deep-analysis analyze --mode full --format github-annotation --output annotations.txt

# Only critical/high
deep-analysis analyze --mode full --min-severity high --format markdown

# Specific category
deep-analysis analyze --mode security --format json

# Incremental (only changed files + transitive)
deep-analysis analyze --incremental --base main --head HEAD
```