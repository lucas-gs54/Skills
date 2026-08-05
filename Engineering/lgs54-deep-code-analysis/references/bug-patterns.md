# Bug Patterns — Catalog of Deep Bug Patterns with Detection Strategies

Organized by category. Each pattern includes: **symptoms**, **root cause**, **detection strategy**, **false positive rate**, **fix pattern**.

---

## Logic & Correctness

### Off-by-One / Fencepost
**Symptoms**: First/last element skipped or duplicated; array index OOB at boundary; pagination returns wrong page.
**Root cause**: Confusion between inclusive/exclusive bounds, 0-based vs 1-based, count vs index.
**Detection**:
- Enumerate loop bounds: `for i in 0..n` vs `0..=n` vs `1..n`
- Check slice indices: `arr[lo..hi]` where `hi` exclusive
- Test cases: `n=0, n=1, n=max, n=len-1, n=len`
**Fix**: Use iterator methods (`for x in arr`), explicit `len()` checks, property-based testing.

### Integer Overflow / Wrapping
**Symptoms**: Negative result from positive inputs; `x + 1 < x`; buffer size miscalculation.
**Root cause**: Implicit wrapping (C/C++, Rust `wrapping_add`), no checked arithmetic.
**Detection**:
- Flag all arithmetic on untrusted input without explicit checked/saturating
- Track value ranges via abstract interpretation
- Special cases: `len * size_of`, `offset + length`, `page * page_size`
**Fix**: `checked_add`, `saturating_add`, `OverflowError` handling, `size_of` multiplication with `checked_mul`.

### Division by Zero / Modulo Zero
**Symptoms**: Crash, exception, panic at runtime.
**Root cause**: Divisor derived from user input or dynamic state without guard.
**Detection**:
- Data flow: trace divisor to source — if not constant non-zero, flag
- Check `a / b`, `a % b`, `a / b * c` patterns
**Fix**: Guard `if b == 0 { return Err }`, `checked_div`, `NonZero` types.

### Float Precision / NaN Propagation
**Symptoms**: `0.1 + 0.2 != 0.3`; comparison fails; sort order wrong; `NaN` spreads.
**Root cause**: IEEE 754 binary representation; `NaN != NaN`; non-associativity.
**Detection**:
- Flag `==`/`!=` on floats — require epsilon comparison
- Flag `NaN` propagation without `is_nan()` check
- Flag `f64` as map key / set member
**Fix**: `abs(a-b) < eps`, `total_cmp` for sort, `is_finite()` guards, integer cents for money.

### Signed/Unsigned Conversion
**Symptoms**: Large unsigned becomes negative; comparison always true/false.
**Root cause**: Implicit conversion, mixing `usize`/`isize`, `u32`/`i32`.
**Detection**:
- Flag mixed signed/unsigned arithmetic/comparison
- Track `usize` from `len()` vs `i32` indices
**Fix**: Explicit `as` casts with range checks, `try_from`, `NonZeroU*` types.

### Empty Collection / Zero-Length Edge Case
**Symptoms**: Panic on `first()`/`last()`/`pop()`; division by `len()`; loop never executes.
**Root cause**: Assumption `len > 0` without proof.
**Detection**:
- Trace collection sources: if from user input / dynamic, flag unguarded access
- Check `arr[0]`, `vec.pop()`, `iter.next()`, `len() as divisor`
**Fix**: `if let Some(x) = arr.first()`, `checked_div`, `Option` return.

### Iterator Invalidation / Concurrent Modification
**Symptoms**: Panic, skipped elements, duplicated elements, UB.
**Root cause**: Mutating collection while iterating (directly or via alias).
**Detection**:
- Data flow: detect `for x in &mut v` + `v.push()` in body
- Check `retain`, `drain_filter`, `splice` during iteration
- Check aliasing: `&v` iteration + `&mut v` mutation
**Fix**: Collect indices first, use `drain`, `split_off`, `Vec::swap_remove`.

### State Machine Invalid Transition
**Symptoms**: Illegal state reached; double-init; use-after-close; protocol violation.
**Root cause**: Missing transition guards; enum state not exhaustive.
**Detection**:
- Model states as enum, transitions as methods taking `&mut self`
- Verify all match arms covered (`#[non_exhaustive]` check)
- Flag `match state { A => ..., B => ... }` without `_ => unreachable!()`
**Fix**: Typestate pattern (state as type parameter), `enum State { A(Data), B }`.

---

## Concurrency

### Data Race (Shared Mutable State)
**Symptoms**: Heisenbug; corrupted data; different results each run; TSan report.
**Root cause**: Multiple threads access same memory, at least one writes, no synchronization.
**Detection**:
- Static: find `static mut`, `Arc<Mutex>` missing, `RefCell` across threads
- Dynamic: ThreadSanitizer (`-fsanitize=thread`, `go test -race`)
- Pattern: `&mut T` sent to thread without `Mutex`/`RwLock`/`Atomic`
**Fix**: `Mutex`, `RwLock`, `Atomic*`, `crossbeam`, channels, `Send`/`Sync` bounds.

### Deadlock (Lock Ordering Cycle)
**Symptoms**: Program hangs; threads blocked forever; timeout on lock.
**Root cause**: Thread A holds L1 wants L2; Thread B holds L2 wants L1.
**Detection**:
- Build lock graph: nodes=locks, edges=acquisition order per thread
- Detect cycles in graph (Tarjan's SCC)
- Flag lock held across `.await` / function call / callback
**Fix**: Global lock ordering (by address/id), `std::lock` multi-lock, lock-free, channels.

### Lock Held Across Yield / Await
**Symptoms**: Deadlock under load; executor thread starved; latency spikes.
**Root cause**: `Mutex` guard not dropped before `.await`; async mutex needed.
**Detection**:
- CFG: find `.await` / `yield` with live `MutexGuard` / `RwLockWriteGuard`
- Check `lock()` result used after `.await` in same scope
**Fix**: `async_mutex::Mutex`, `tokio::sync::Mutex`, drop guard before `.await`, `scopeguard`.

### Atomic Memory Ordering Bug
**Symptoms**: Subtle corruption; works on x86 fails on ARM; reordering visible.
**Root cause**: `Relaxed` where `Acquire`/`Release`/`SeqCst` needed; missing fence.
**Detection**:
- Classify pattern: flag publish/consume, mutex lock/unlock, reference counting
- Flag `compare_exchange` with `Relaxed` success/failure
- Flag `AtomicPtr` load/store without ordering
**Fix**: Document required ordering per variable; use `SeqCst` when unsure.

### ABA Problem (Lock-Free)
**Symptoms**: CAS succeeds but state logically changed; lost update.
**Root cause**: Pointer reused between load and CAS; tag/counter missing.
**Detection**:
- Find `compare_exchange` on `AtomicPtr` / `AtomicUsize` without version tag
- Check freelist, stack, queue implementations
**Fix**: Tagged pointer (low bits), `AtomicU64` with counter, hazard pointers, epoch GC.

### Goroutine / Task Leak
**Symptoms**: Memory grows; goroutine count increases; program doesn't exit.
**Root cause**: `go`/`spawn` without join; channel never closed; `select` default busy-loop.
**Detection**:
- Static: `go func()` without `done` chan / `WaitGroup` / `sync.Once`
- Dynamic: `pprof` goroutine profile, `runtime.NumGoroutine()`
- Pattern: `for { select { case <-ctx.Done(): return; default: } }` without sleep
**Fix**: Context cancellation, `errgroup`, `sync.WaitGroup`, bounded channels.

### Channel Deadlock
**Symptoms**: Sender/receiver blocked forever; program hangs.
**Root cause**: Unbuffered channel send before receive; closed channel send; nil channel.
**Detection**:
- Trace channel creation → send/recv sites
- Flag unbuffered `make(chan T)` without guaranteed pairing
- Flag `close(ch)` with pending sends
**Fix**: Buffered channels, `select` with `default`, `context` for cancellation.

---

## Memory & Lifetime

### Use-After-Free / Dangling Reference
**Symptoms**: Crash, corruption, UB, ASan report, "pointer being freed was not allocated".
**Root cause**: Memory freed while reference/pointer still exists.
**Detection**:
- Rust: borrow checker (compile-time) — check `unsafe` blocks
- C/C++: ASan, static analysis (Clang SA), ownership annotation
- Go: escape analysis + manual `cgo` pointers
- Pattern: `Box::into_raw` without `from_raw`/`drop`, `Rc::into_raw` not paired
**Fix**: RAII, smart pointers, `Pin`, `ManuallyDrop`, explicit ownership docs.

### Double Free
**Symptoms**: Crash, "double free or corruption", ASan report.
**Root cause**: `drop`/`free` called twice on same allocation.
**Detection**:
- ASan, `malloc` debugging, ownership transfer tracking
- Pattern: `drop` in `Drop` impl + manual `drop()`, `Rc::try_unwrap` panic + `drop`
**Fix**: `Option::take()`, `ManuallyDrop`, `Box::leak` if intentional.

### Memory Leak (Unbounded Growth)
**Symptoms**: RSS grows indefinitely; OOM kill; GC pressure.
**Root cause**: References held indefinitely; caches without eviction; cycle refs.
**Detection**:
- Heap profiling: `jemalloc`/`pprof`/`tracemalloc`/`objgraph`
- Static: `Rc`/`Arc` cycles, `Vec`/`HashMap` growing without bound, `lazy_static` accumulation
- Pattern: `lru_cache` without `cap`, `Vec` in global never cleared
**Fix**: `Weak` for backrefs, `LruCache` with capacity, periodic cleanup, `Drop` impl.

### Reference Cycle (Rc/Arc)
**Symptoms**: Memory not freed after last external ref dropped; leak.
**Root cause**: Parent→Child `Rc`, Child→Parent `Rc` — refcount never reaches 0.
**Detection**:
- Static: struct with `Rc<Self>` field, parent/child mutual `Rc`
- Dynamic: `heaptrack`/`valgrind`/`pprof` shows unreachable but allocated
**Fix**: `Weak` for backref, `Weak` for parent, `Rc::downgrade`, explicit `drop`.

### Stack Overflow (Deep Recursion)
**Symptoms**: Crash, "stack overflow", SIGSEGV at guard page.
**Root cause**: Unbounded recursion; large stack frames; no tail call optimization.
**Detection**:
- Call graph: find recursive cycles without base case limit
- Frame size analysis: large locals in recursive fn
- Pattern: `fn f() { f() }`, AST visitor, parser combinators
**Fix**: Iterative with explicit stack, `Vec` as stack, increase stack size, TCO hint.

---

## Performance

### Accidental Quadratic / N+1
**Symptoms**: Latency grows non-linearly; timeout on large input; CPU spikes.
**Root cause**: Nested loop over same collection; loop with query/async call.
**Detection**:
- Complexity estimation: loop nest depth × dynamic bounds
- SQL: `EXPLAIN` shows N queries for N items
- Pattern: `for x in xs { for y in ys }`, `for item in items { db.query(item) }`
**Fix**: Hash join (`HashMap`), batch query (`WHERE id IN (...)`), `join`/`collect`.

### Allocation in Hot Loop
**Symptoms**: High GC pressure; latency spikes; `perf` shows `malloc`/`free` hot.
**Root cause**: `Vec::new()`, `String::new()`, `format!`, `to_owned`, `clone` in loop.
**Detection**:
- Escape analysis: allocation not escaping → stack, else heap
- Pattern: `for x in xs { let s = format!(...); v.push(s) }`
- Perf: `perf record -g`, `cargo flamegraph`
**Fix**: Reuse buffer (`String::with_capacity`), `write!`, `extend`, `Cow`, arena.

### Cache Miss / False Sharing
**Symptoms**: Scalability stalls; per-core latency increases; `perf` shows `LLC-load-misses`.
**Root cause**: Strided access, adjacent atomics on different threads, AoS vs SoA.
**Detection**:
- Access pattern analysis: `arr[i].field` vs `fields[i]`
- False sharing: `AtomicU64` array — each thread hits different index but same cache line
- `perf stat -e cache-misses,cache-references`
**Fix**: `#[repr(align(64))]`, `#[repr(C)]` SoA, `crossbeam::utils::CachePadded`, batch processing.

### Lock Contention
**Symptoms**: Throughput drops with threads; latency variance high; `perf` shows `futex` wait.
**Root cause**: Coarse lock; lock in hot path; critical section too large.
**Detection**:
- Profile: `perf record -g --call-graph=dwarf`, `mutex` wait time
- Static: `Mutex` guard spans > 10 lines / function call / `.await`
**Fix**: Fine-grained locks, sharding, lock-free, `RwLock`, `DashMap`, `crossbeam::skiplist`.

### N+1 Database Queries
**Symptoms**: Query count = 1 + N items; latency linear in result size.
**Root cause**: ORM lazy loading; loop with `find_by_id`; missing `join`/`preload`.
**Detection**:
- Query log: count queries per request
- Pattern: `for post in posts { comments = db.comments(post.id) }`
**Fix**: `SELECT * FROM comments WHERE post_id IN (...)`, `JOIN`, DataLoader, `preload`.

### Unnecessary Serialization / Copy
**Symptoms**: CPU in `serde_json`, `bincode`, `pickle`, `memcpy`.
**Root cause**: Serializing same data multiple times; deep clone; `to_string` in loop.
**Detection**:
- Call graph: serialize → serialize without mutation between
- Pattern: `json(data)` in middleware + handler + response
**Fix**: Serialize once, pass bytes, zero-copy (`bytes`, `Vec<u8>`), `serde::Serialize` impl.

---

## Security

### Injection (SQL, Command, Path, LDAP, XSS)
**Symptoms**: Data breach, RCE, data corruption, XSS payload executes.
**Root cause**: User input concatenated into command/query without parameterization.
**Detection**:
- Taint analysis: source (HTTP param, header, body, cookie, DB, file, env) → sink (SQL, shell, path, HTML, LDAP)
- Pattern: `format!("SELECT * FROM t WHERE x = {}", input)`, `exec(user_input)`, `open(user_input)`
**Fix**: Parameterized queries (`$1`, `?`), `Command::arg` (not `args`), `Path::join` + validation, HTML escaping.

### Authentication Bypass
**Symptoms**: Unauthorized access; privilege escalation; session fixation.
**Root cause**: Missing check; logic error; token not validated; confused deputy.
**Detection**:
- Trace all entry points: HTTP handlers, gRPC, CLI, message queue
- Flag handlers without `require_auth` / `authorize` call
- Check token validation: signature, expiry, audience, issuer, nonce
- Check RBAC: role checked per action, not just at login
**Fix**: Middleware auth, capability tokens, zero-trust, audit log.

### Information Leakage
**Symptoms**: Secrets in logs/errors/metrics; timing attacks; cache side-channels.
**Root cause**: Debug logging in prod; error details to client; non-constant-time compare.
**Detection**:
- Log scan: `password`, `token`, `secret`, `key`, `authorization` in structured logs
- Error responses: stack trace, SQL, internal IDs to client
- Timing: `==` on secrets, `if secret == input` branch
- Cache: secret-dependent memory access
**Fix**: Redact logs, generic errors, constant-time compare (`subtle.ConstantTimeEq`), `secrecy` crate.

### Cryptographic Misuse
**Symptoms**: Decryptable ciphertext; forged signatures; key recovery.
**Root cause**: Nonce reuse, weak algorithm, non-constant-time, bad RNG, hardcoded keys.
**Detection**:
- Flag `AES-GCM`/`ChaCha20Poly1305` with static nonce
- Flag `RSA` < 2048, `ECDSA` P-256 only, `MD5`/`SHA1`/`DES`/`3DES`
- Flag `rand::thread_rng()` for keys — require `OsRng` / `getrandom`
- Flag `==` on MAC/tag — require constant-time
**Fix**: `libsodium`/`ring`/`aws-lc-rs`, `nonce: [u8; 12]` from CSPRNG, `Key` types.

### Supply Chain / Dependency
**Symptoms**: Compromised dependency; malicious code execution; version confusion.
**Root cause**: Unpinned versions; typosquatting; compromised maintainer; transitive dep.
**Detection**:
- `cargo audit` / `npm audit` / `govulncheck` / `pip-audit` / `govulncheck`
- Flag `*` / `^` / `~` in `Cargo.toml` / `package.json` / `go.mod` / `requirements.txt`
- Verify provenance: `cargo verify-project`, `npm sigs`, `cosign`
- Check `build.rs` / `scripts` for network / file access
**Fix**: Lockfiles, `cargo vet`, `npm ci`, `go mod verify`, `pip install --require-hashes`.

---

## Architecture

### Circular Module Dependency
**Symptoms**: Compile error (Rust/Go); runtime init order (Python/JS); tight coupling.
**Root cause**: Module A imports B, B imports A (direct or transitive).
**Detection**:
- Module graph SCC (Tarjan)
- Rust: `cargo cycles`, `cargo tree -d`
- Go: `go mod graph` + cycle detection
- Python: `pydeps`, `importlab`
**Fix**: Extract common to C, dependency inversion (interface in A, impl in B), `pub use`.

### Layer Violation
**Symptoms**: Domain logic in DB code; HTTP handler calls SQL directly; UI imports domain.
**Root cause**: Missing abstraction; convenience; no architectural enforcement.
**Detection**:
- Tag modules with layer: `domain`, `application`, `infrastructure`, `presentation`
- Check imports: `domain` → `infra` ✗, `presentation` → `domain` ✗
- Allow: `infra` → `domain` (impl trait), `application` → `domain`
**Fix**: Repository pattern, ports/adapters, dependency inversion, `mockall` for tests.

### God Object / Large Type
**Symptoms**: >500 LOC, >20 methods, >7 params, low cohesion, hard to test.
**Root cause**: Feature accretion; missing decomposition; anemic domain model.
**Detection**:
- Metrics: LOC, method count, param count, LCOM4 (cohesion), fan-in/fan-out
- Pattern: `struct Service { db, cache, http, config, logger, ... 20 fields }`
**Fix**: Extract: `Repository`, `Cache`, `HttpClient`, `Config`, `Logger` → compose.

### Hidden Global State
**Symptoms**: Flaky tests; non-deterministic behavior; hard to mock; init order bugs.
**Root cause**: `static mut`, `lazy_static`, `OnceCell`, `thread_local`, env vars at runtime.
**Detection**:
- Grep: `static mut`, `lazy_static!`, `OnceCell::new`, `thread_local!`, `std::env::var` in fn body
- Test: run tests in random order — flakes indicate global state
**Fix**: Dependency injection, `Config` struct passed explicitly, `test_with` pattern.

### Untestable Code
**Symptoms**: No unit tests; integration tests only; mocks require `unsafe`/`reflection`.
**Root cause**: Hardcoded dependencies; no interfaces; side effects in constructors; `static` calls.
**Detection**:
- Check: `new()` does I/O; `impl Trait` not used; `Box<dyn Trait>` missing; `#[cfg(test)]` only
- Metrics: cyclomatic complexity > 10, function length > 50
**Fix**: Constructor injection, `trait` for externals, pure functions, functional core / imperative shell.

---

## Language-Specific Deep Patterns

### Rust: `unsafe` Soundness Hole
**Symptoms**: UB only in `unsafe` block; passes tests but fails Miri.
**Detection**: `cargo miri` — run all tests under Miri; `cargo geiger` for `unsafe` count.
**Patterns**: `transmute`, `ptr::read/write`, `union`, `inline asm`, `extern "C"`, `manually_drop`.

### TypeScript: `any` Leakage
**Symptoms**: Runtime error "cannot read property of undefined" in typed code.
**Detection**: `tsc --noImplicitAny --strictNullChecks`, `eslint @typescript-eslint/no-explicit-any`.

### Go: Interface Nil Check
**Symptoms**: Panic "nil pointer dereference" on interface method call.
**Detection**: `staticcheck` `SA5007`, pattern `var i Interface = nil; i.Method()`.

### Python: Mutable Default Argument
**Symptoms**: List/dict accumulates across calls.
**Detection**: `ruff` `B006`, `pylint` `W0102`, pattern `def f(x=[])`.

### C++: Iterator Invalidation
**Symptoms**: UB, crash, ASan heap-use-after-free.
**Detection**: `clang-tidy` `bugprone-iterator-invalidation`, `cppcheck` `--enable=performance`.

---

## Detection Strategy Matrix

| Pattern Category | Static | Dynamic | Hybrid |
|------------------|--------|---------|--------|
| Logic/Correctness | Data flow, abstract interpretation, symbolic execution | Property-based testing, fuzzing | Concolic execution |
| Concurrency | Lock graph, happens-before, model checking | ThreadSanitizer, stress testing, `loom` | Systematic concurrency testing |
| Memory/Lifetime | Ownership, escape analysis, region inference | ASan, MSan, LSAN, `valgrind`, `heaptrack` | Miri (Rust), `go test -race` |
| Performance | Complexity estimation, allocation profiling, cache modeling | `perf`, `pprof`, `flamegraph`, `scalene` | Continuous benchmarking |
| Security | Taint analysis, SAST, dependency scanning | DAST, fuzzing (`libFuzzer`, `go-fuzz`), IAST | Runtime protection (WAF, RASP) |
| Architecture | Module graph, coupling metrics, layer tags | Test structure, build graph | ArchUnit, `go mod graph`, `cargo deps` |

---

## False Positive Reduction

| Technique | Applies To |
|-----------|------------|
| Confidence scoring (proven/high/medium/speculative) | All findings |
| Cross-pass correlation (data flow + semantic = higher confidence) | Logic + correctness |
| Blast radius computation (single fn / module / cross-module / system) | Prioritization |
| Suppression with justification (`// deep-analysis: ignore REASON`) | Known safe patterns |
| Test generation for each finding (regression test) | Validation |
| Incremental analysis (only changed functions + transitive callers) | CI performance |