# Language Semantics — Deep Analysis Rules Per Language

Each language has unique semantics that dictate what constitutes a "deep bug." This reference defines the critical rules per language.

---

## Rust

### Ownership & Borrowing
| Pattern | Bug Class | Detection |
|---------|-----------|-----------|
| `&mut` aliasing | UB | Borrow checker (compile-time) — but check `unsafe` blocks |
| Use after move | Logic | MIR data flow — `move` then use |
| Double free | Memory | `drop` called twice — `ManuallyDrop` misuse |
| Leak via `Rc`/`Arc` cycle | Memory | Reference cycle — `Weak` missing |
| `Pin` projection violation | UB | `Pin::get_unchecked_mut` on `!Unpin` |
| Lifetime elision mismatch | Logic | Function signature vs body — elided vs explicit |
| Variance unsoundness | UB | Covariant `&mut T`, contravariant `Fn(T)` |
| `unsafe` block without justification | Safety | Every `unsafe` must have `// SAFETY:` comment |

### Type System
| Pattern | Bug Class | Detection |
|---------|-----------|-----------|
| `any` leakage via `Box<dyn Any>` | Logic | Downcast unchecked |
| Trait object safety violation | Compile | Non-object-safe trait used as `dyn Trait` |
| Generic bounds missing `Send`/`Sync` | Concurrency | `spawn`/`spawn_blocking` requires `Send` |
| Associated type inference ambiguity | Logic | Multiple impls satisfy bounds |
| Specialization unsoundness | UB | `default` impl overlap |

### Concurrency
| Pattern | Bug Class | Detection |
|---------|-----------|-----------|
| `Mutex` held across `.await` | Deadlock | Async yield point with lock held |
| `RwLock` write starvation | Performance | Many readers, rare writers |
| `Condvar` notify without lock | Logic | Missed wakeup |
| `Atomic` wrong ordering | UB | `Relaxed` where `AcqRel` needed |
| `channel` send on closed | Panic | `try_send`/`send` unchecked |
| `JoinHandle` not joined | Leak | Detached thread with side effects |

### Memory
| Pattern | Bug Class | Detection |
|---------|-----------|-----------|
| `Box::into_raw` not paired | Leak/Double-free | Missing `from_raw`/`drop` |
| `alloc::alloc` not deallocated | Leak | Manual alloc without `dealloc` |
| `Vec` capacity vs len confusion | Logic | `reserve` vs `resize`, `set_len` misuse |
| `String` as bytes (UTF-8) | Logic | `from_utf8_unchecked` invalid |
| `MaybeUninit` uninitialized read | UB | `assume_init` before write |

### Async
| Pattern | Bug Class | Detection |
|---------|-----------|-----------|
| `.await` in loop without batching | Performance | N+1 async calls |
| `select!` biased | Fairness | Starvation of one branch |
| `Future` not `Send` in `spawn` | Compile | `!Send` future in multi-threaded executor |
| Cancellation safety | Logic | `drop` during `.await` leaves invariant broken |
| `Pin` projection in async fn | UB | `self` referenced after yield |

---

## TypeScript / JavaScript

### Type Soundness
| Pattern | Bug Class | Detection |
|---------|-----------|-----------|
| `any` implicit/explicit | Logic | `noImplicitAny`, `explicitAny` |
| `unknown` not narrowed | Logic | Use without type guard |
| Non-exhaustive `switch`/`match` | Logic | `never` check missing |
| `null`/`undefined` not handled | Runtime | `strictNullChecks` off |
| `this` context loss | Runtime | Callback without `bind`/arrow |
| Prototype pollution | Security | `Object.prototype` mutation |

### Async / Concurrency
| Pattern | Bug Class | Detection |
|---------|-----------|-----------|
| Missing `await` | Logic | Promise returned but not awaited |
| `forEach` with async callback | Logic | Promises not awaited |
| Race condition in closure | Concurrency | Loop variable captured |
| Unhandled rejection | Runtime | `.catch()` missing |
| Top-level `await` in non-module | Runtime | Module type mismatch |
| Event loop blocking | Performance | Sync CPU work in async fn |

### Runtime Semantics
| Pattern | Bug Class | Detection |
|---------|-----------|-----------|
| `==` vs `===` | Logic | Coercion surprises |
| `NaN` comparison | Logic | `NaN === NaN` false |
| Float precision | Logic | `0.1 + 0.2 !== 0.3` |
| Array hole / sparse | Logic | `for...in` vs `for...of` |
| `this` in detached method | Runtime | `obj.method` passed as callback |
| Microtask queue ordering | Concurrency | `Promise.then` vs `queueMicrotask` |

### Security
| Pattern | Bug Class | Detection |
|---------|-----------|-----------|
| `eval` / `Function` constructor | Injection | Dynamic code execution |
| `innerHTML` / `dangerouslySetInnerHTML` | XSS | Unsanitized user input |
| Prototype pollution | Prototype | `obj[userInput] = value` |
| Timing attack | Side-channel | `===` on secrets |
| Prototype pollution via `Object.assign` | Prototype | `Object.assign({}, userInput)` |

---

## Go

### Concurrency
| Pattern | Bug Class | Detection |
|---------|-----------|-----------|
| Goroutine leak | Leak | `go` without `done` channel / `sync.WaitGroup` |
| Channel deadlock | Deadlock | Unbuffered send/receive mismatch |
| `sync.Mutex` held across call | Deadlock | Lock in defer / callback |
| `sync.RWMutex` write starvation | Performance | `RLock` in hot loop |
| `atomic` wrong type | UB | `int32` vs `int64` on 32-bit |
| `context` not propagated | Cancellation | Missing `ctx` param |
| `init()` side effects | Startup | Global state mutation |

### Memory & Escape
| Pattern | Bug Class | Detection |
|---------|-----------|-----------|
| Heap escape unnecessarily | Performance | `go vet -m` / escape analysis |
| `defer` in loop | Performance | Stack growth, delayed unlock |
| `make` vs `new` confusion | Logic | `make([]int, n)` vs `new([n]int)` |
| Slice header copy | Logic | `s := a[:]; s[0]=x` mutates `a` |
| `string` / `[]byte` conversion | Allocation | Unnecessary copy |

### Error Handling
| Pattern | Bug Class | Detection |
|---------|-----------|-----------|
| Error ignored (`_ = foo()`) | Logic | `errcheck` linter |
| `panic` in library | Runtime | `recover` only at top level |
| Error wrapping lost | Debugging | `%v` vs `%w` |
| Sentinel error comparison | Logic | `err == io.EOF` vs `errors.Is` |

### Standard Library Gotchas
| Pattern | Bug Class | Detection |
|---------|-----------|-----------|
| `http.Client` no timeout | Hang | `Timeout` not set |
| `json.Unmarshal` into `interface{}` | Logic | Type assertion panic later |
| `time.Time` zero value | Logic | `IsZero()` check missing |
| `regexp` compile in hot path | Performance | `MustCompile` at init |
| `rand` not crypto | Security | `math/rand` vs `crypto/rand` |

---

## Python

### Runtime Semantics
| Pattern | Bug Class | Detection |
|---------|-----------|-----------|
| Mutable default argument | Logic | `def f(x=[])` — shared across calls |
| Late binding in closure | Logic | `lambda: i` in loop |
| `__eq__` without `__hash__` | Logic | Unhashable in `set`/`dict` |
| `__slots__` missing | Memory | Dict per instance |
| `__init__` not called | Logic | `super().__init__()` missing |
| Circular import | Import | `import` at top level |

### Concurrency
| Pattern | Bug Class | Detection |
|---------|-----------|-----------|
| GIL misunderstanding | Performance | CPU-bound in threads |
| `asyncio` blocking call | Hang | `time.sleep` vs `await asyncio.sleep` |
| `asyncio` task not awaited | Leak | `create_task` without `await` |
| `threading` shared mutable | Race | No `Lock`/`RLock` |
| `multiprocessing` pickle fail | Runtime | Non-picklable in args |

### Type System (mypy/pyright)
| Pattern | Bug Class | Detection |
|---------|-----------|-----------|
| `Any` leakage | Logic | `--no-implicit-any` |
| `Optional` not handled | Runtime | `None` attribute access |
| Protocol not implemented | LSP | Structural subtyping |
| Generics variance | Logic | `List[Child]` not `List[Parent]` |
| `TypedDict` total=False | Logic | Key access without check |

### Security
| Pattern | Bug Class | Detection |
|---------|-----------|-----------|
| `subprocess` shell=True | Injection | User input in command |
| `pickle.loads` untrusted | RCE | Deserialization |
| `yaml.load` unsafe | RCE | `Loader=yaml.SafeLoader` missing |
| `eval`/`exec` user input | Injection | Dynamic code |
| Path traversal | FS | `open(user_input)` |

---

## C / C++

### Undefined Behavior
| Pattern | Bug Class | Detection |
|---------|-----------|-----------|
| Buffer overflow | UB | `-fsanitize=address`, static analysis |
| Use after free | UB | ASan, lifetime analysis |
| Double free | UB | ASan |
| Uninitialized read | UB | MSan, static analysis |
| Signed overflow | UB | `-fsanitize=signed-integer-overflow` |
| Shift UB | UB | `1 << 32` on 32-bit |
| Null deref | UB | Static analysis |
| Division by zero | UB | Static analysis |
| Strict aliasing violation | UB | `-fstrict-aliasing` |
| Alignment violation | UB | `reinterpret_cast` misaligned |

### Memory Management
| Pattern | Bug Class | Detection |
|---------|-----------|-----------|
| `new`/`delete` mismatch | UB | `new[]` vs `delete` |
| `malloc`/`free` mismatch | UB | `new` vs `free` |
| RAII missing | Leak | Smart pointers (`unique_ptr`, `shared_ptr`) |
| Circular `shared_ptr` | Leak | `weak_ptr` missing |
| `unique_ptr` array deleter | UB | Default deleter calls `delete` not `delete[]` |
| `std::string` SSO invalidation | UB | Pointer to internal buffer after move |

### Concurrency
| Pattern | Bug Class | Detection |
|---------|-----------|-----------|
| Data race | UB | TSan, `-fsanitize=thread` |
| Deadlock | Logic | Lock ordering, `std::lock` |
| `mutex` held across call | Deadlock | Callback with lock |
| `condition_variable` spurious wake | Logic | `wait` without predicate |
| `atomic` memory order | UB | `memory_order_relaxed` misused |
| `thread_local` destruction order | UB | TLS destructor order |

### Template / Generic
| Pattern | Bug Class | Detection |
|---------|-----------|-----------|
| Template bloat | Compile | Code size explosion |
| SFINAE misuse | Compile | Substitution failure not error |
| Concepts not constrained | Compile | Unconstrained template |
| ADL surprise | Logic | `swap` found in wrong namespace |
| ODR violation | Link | Multiple definitions |

### Security
| Pattern | Bug Class | Detection |
|---------|-----------|-----------|
| Format string | Injection | `printf(user_input)` |
| `gets` / `strcpy` / `sprintf` | Overflow | Banned functions |
| Integer truncation | Logic | `size_t` to `int` |
| TOCTOU | Race | Check then use |
| Untrusted pointer deref | UB | FFI input validation |

---

## Cross-Language Patterns

### FFI Boundaries (All Languages)
| Pattern | Bug Class | Detection |
|---------|-----------|-----------|
| ABI mismatch | UB | Calling convention, layout, alignment |
| Ownership transfer unclear | Leak/Double-free | Document: who frees? |
| Exception across FFI | UB | C++ exception into C, panic into C |
| Callback lifetime | UB | Callback outlives context |
| Thread safety | Race | FFI function not thread-safe |

### Serialization / Deserialization
| Pattern | Bug Class | Detection |
|---------|-----------|-----------|
| Version skew | Logic | Schema evolution rules |
| Denial of service | DoS | Recursive structures, large allocations |
| Type confusion | Logic | Untagged unions, `any` deserialization |
| Side-channel | Timing | Variable-time parsing |

### Cryptography (All)
| Pattern | Bug Class | Detection |
|---------|-----------|-----------|
| Nonce reuse | Crypto | CTR/GCM nonce repeat |
| Constant-time violation | Side-channel | Branching on secret |
| Weak algorithm | Crypto | MD5, SHA1, DES, RSA < 2048 |
| RNG not crypto | Crypto | `rand()` vs `getrandom` |
| Key management | Crypto | Hardcoded, logged, weak derivation |

---

## Tooling Per Language

| Language | Static Analysis | Dynamic Analysis | Lint/Format |
|----------|-----------------|------------------|-------------|
| Rust | `clippy`, `rust-analyzer`, `cargo-geiger` | `miri`, `loom`, `asan` | `rustfmt` |
| TypeScript | `tsc --strict`, `eslint`, `semgrep` | `node --inspect`, `playwright` | `prettier`, `eslint` |
| Go | `go vet`, `staticcheck`, `golangci-lint` | `go test -race`, `pprof` | `gofmt`, `golines` |
| Python | `mypy`, `pyright`, `ruff`, `bandit` | `pytest`, `py-spy`, `scalene` | `black`, `ruff` |
| C++ | `clang-tidy`, `cppcheck`, `clang SA` | `asan`, `tsan`, `msan`, `ubsan`, `perf` | `clang-format` |