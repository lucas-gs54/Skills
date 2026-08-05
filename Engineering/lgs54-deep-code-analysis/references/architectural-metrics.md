# Architectural Metrics — Coupling, Cohesion, Cycle Detection, Layering Rules

Quantitative measures for Pass 6 (Architectural Integrity). Thresholds are defaults — calibrate per project.

---

## Coupling Metrics

### Afferent Coupling (Ca)
**Definition**: Number of modules that depend on this module.
**Interpretation**: High Ca = module is **responsible** (many depend on it). Should be stable.
**Threshold**: `Ca > 20` → consider splitting or stabilizing interface.

### Efferent Coupling (Ce)
**Definition**: Number of modules this module depends on.
**Interpretation**: High Ce = module is **dependent** (knows too much). Should be abstract.
**Threshold**: `Ce > 15` → too many dependencies, extract abstractions.

### Instability (I)
```
I = Ce / (Ca + Ce)  // 0 = completely stable, 1 = completely unstable
```
**Interpretation**:
- `I ≈ 0` → Stable (many depend on it, few deps) → good for core/domain
- `I ≈ 1` → Unstable (few depend, many deps) → good for plugins/adapters

### Abstractness (A)
```
A = Na / Nc
Na = number of abstract types (traits, interfaces, abstract classes)
Nc = total concrete + abstract types
```
**Interpretation**:
- `A ≈ 1` → Pure interface/abstraction
- `A ≈ 0` → Pure implementation

### Main Sequence (Stable Abstractions Principle)
```
Distance D = |A + I - 1| / sqrt(2)
```
- `D ≈ 0` → On main sequence (ideal)
- `D > 0.5` → **Zone of Pain** (high I, low A) — concrete, unstable, hard to change
- `D > 0.5` other side → **Zone of Uselessness** (high A, low I) — abstract, stable, unused

**Threshold**: `D > 0.3` → flag for review.

---

## Cohesion Metrics

### LCOM4 (Lack of Cohesion of Methods v4)
**Definition**: Number of connected components in method-field access graph.
```
Graph: nodes = methods + fields; edges = method accesses field
LCOM4 = connected_components(graph)
```
- `LCOM4 = 1` → Single responsibility (all methods use shared fields)
- `LCOM4 > 1` → Multiple responsibilities → split class
- `LCOM4 = 0` → No methods (struct only)

**Threshold**: `LCOM4 > 2` → flag.

### LCOM HS (Henderson-Sellers)
```
LCOM_HS = (N * sum(m_i) - sum(m_i)^2) / (N^2 - N)
N = number of methods
m_i = number of fields accessed by method i
```
- `LCOM_HS ≈ 0` → High cohesion
- `LCOM_HS ≈ 1` → Low cohesion

**Threshold**: `LCOM_HS > 0.8` → flag.

### TCC / LCC (Tight / Loose Class Cohesion)
```
TCC = direct_connections / max_possible
LCC = (direct + indirect) / max_possible
```
Direct = method A calls B AND both access same field.
Indirect = path via other methods.

---

## Complexity Metrics

### Cyclomatic Complexity (per function)
```
M = E - N + 2P
E = edges in CFG, N = nodes, P = connected components (usually 1)
Simplified: M = decision_points + 1
```
**Thresholds**:
- `M ≤ 10` → Low risk
- `11-20` → Moderate
- `21-50` → High
- `> 50` → Untestable

### Cognitive Complexity
```
Base = 1
+1 for: if, else, for, while, catch, switch case, ternary, &&, ||, ??
+1 for nested (nesting_level)
+1 for recursion
```
**Threshold**: `> 15` → flag.

### Function Length
**Thresholds**:
- `≤ 20 lines` → Good
- `21-50` → Acceptable
- `51-100` → Consider split
- `> 100` → Flag

### Parameter Count
**Thresholds**:
- `≤ 4` → Good
- `5-7` → Consider struct/object
- `> 7` → Flag (builder pattern, config struct)

---

## Cycle Detection

### Module/Package Cycles
**Algorithm**: Tarjan's SCC on module dependency graph.
**Output**: List of strongly connected components with size > 1.

**Severity by size**:
- `2 modules` → Direct cycle (A→B, B→A) — easy fix
- `3-5 modules` → Indirect cycle — extract common
- `> 5 modules` → Architectural rot — major refactor

**Breaking strategies**:
1. Extract common → new module C, A→C, B→C
2. Dependency inversion: A defines trait, B implements
3. Merge if truly cohesive
4. Event-driven / mediator pattern

### Call Graph Cycles (Recursion)
**Detection**: SCC on call graph.
**Types**:
- Direct recursion: `f → f`
- Mutual recursion: `f → g → f`
- Indirect: `f → g → h → f`

**Acceptable**: Bounded recursion (parser, tree visitor, graph algorithm).
**Flag**: Unbounded recursion, no base case visible, large stack frames.

---

## Layering Rules

### Standard Layers (Clean Architecture)
```
1. Domain / Core          (entities, value objects, domain events, policies)
2. Application / Use Case (use cases, ports, DTOs, orchestration)
3. Infrastructure         (repositories, external APIs, DB, messaging, FS)
4. Presentation           (HTTP handlers, CLI, GraphQL, gRPC, GUI)
```

### Dependency Rule
```
Inner layers MUST NOT depend on outer layers.
Outer layers MAY depend on inner layers (via interfaces/ports).
```

### Allowed Dependencies
| From Layer | To Layer | Mechanism |
|------------|----------|-----------|
| Presentation | Application | Direct (use case call) |
| Application | Domain | Direct (entity, value object) |
| Application | Infrastructure | **Port/Interface** (dependency inversion) |
| Infrastructure | Application | **Implements port** (dependency inversion) |
| Infrastructure | Domain | Direct (entity persistence) |
| Domain | *none* | Pure, no external deps |

### Violation Detection
```
for each module M in layer L:
  for each import N in M:
    if layer(N) > layer(M):  // outer layer number > inner
      VIOLATION: M (layer L) depends on N (layer > L)
```

### Common Violations & Fixes
| Violation | Fix |
|-----------|-----|
| Domain → DB (sqlx, diesel) | Repository trait in Domain, impl in Infra |
| Use case → HTTP client | Port trait in Application, impl in Infra |
| Entity → HTTP framework | Remove; use DTO in Application |
| Config in Domain | Config in Application/Infra, pass as param |
| Logger in Domain | Trait in Domain, impl in Infra, pass logger |

### Layer Assignment Heuristics
```
if path contains "domain" or "core" or "entity" or "value" → Domain
if path contains "usecase" or "service" or "port" or "dto" → Application
if path contains "repo" or "db" or "http" or "grpc" or "kafka" or "fs" → Infrastructure
if path contains "handler" or "controller" or "api" or "cli" or "graphql" → Presentation
```

---

## God Object Detection

### Composite Score
```
god_score = w1*LOC_norm + w2*methods_norm + w3*params_norm + w4*fields_norm + w5*(1 - cohesion_norm)
```
Weights: `w1=0.25, w2=0.25, w3=0.15, w4=0.15, w5=0.2`

**Normalization**: Min-max across all types in codebase.

**Threshold**: `god_score > 0.7` (top 10%) → flag.

### Symptoms Checklist
- [ ] `LOC > 500`
- [ ] `methods > 20`
- [ ] `avg_params > 7`
- [ ] `fields > 15`
- [ ] `LCOM4 > 2` or `LCOM_HS > 0.8`
- [ ] `fan_in > 50` (many callers)
- [ ] `fan_out > 30` (many dependencies)
- [ ] Multiple responsibilities evident (naming: `Manager`, `Service`, `Handler`, `Processor`, `Engine`)

**Fix**: Extract by responsibility → `Repository`, `Cache`, `Client`, `Validator`, `Transformer`, `Publisher`.

---

## Hidden Global State Detection

### Static Patterns
| Pattern | Language | Detection |
|---------|----------|-----------|
| `static mut` | Rust | Grep / clippy `static_mut` |
| `lazy_static!` / `once_cell` | Rust | Macro expansion analysis |
| `thread_local!` | Rust | Macro |
| `static` / `global` | Go | `var` at package level |
| `static` / `thread_local` | C++ | `static` / `thread_local` keyword |
| `global` / `module-level` | Python | Assignment at module level |
| `window` / `globalThis` | JS/TS | Property access |
| `Singleton` pattern | All | Private constructor + static instance |

### Runtime Detection (Test Flakiness)
```
Run test suite 50x with randomized order.
Flaky tests (pass/fail non-deterministically) → global state suspect.
Correlate: which tests touch which global variables.
```

### Remediation
1. **Config**: Pass `Config` struct explicitly (not `std::env::var` in fn body)
2. **Logger**: `trait Logger` in domain, impl in infra, pass `&dyn Logger`
3. **Time**: `trait Clock { now() -> DateTime }`, inject `Clock`
4. **RNG**: `trait Rng`, inject `&mut dyn RngCore`
5. **FS/DB/HTTP**: Repository traits, inject implementations
6. **Metrics**: `MetricsRegistry` passed, not global `prometheus::register`

---

## Testability Metrics

### Test Coverage (Per Module)
```
Line coverage ≥ 80%
Branch coverage ≥ 70%
Function coverage ≥ 90%
```
**Critical paths**: `≥ 95%` line, `≥ 90%` branch.

### Testability Index
```
TI = (1 / (1 + cyclomatic)) * (1 / (1 + params)) * (1 / (1 + deps)) * purity
purity = 1.0 if pure (no I/O, no global state), 0.5 if impure but injectable, 0.0 if hardcoded I/O
```
**Threshold**: `TI > 0.3` → testable. `TI < 0.1` → hard to test.

### Mockability
```
Mockable = has_trait_interface AND constructor_injection AND no_static_calls
```
**Score**: % of dependencies that are mockable.
**Threshold**: `> 80%` mockable.

---

## Automated Computation (Tooling)

| Language | Coupling | Cohesion | Complexity | Cycles | Layers | God Object |
|----------|----------|----------|------------|--------|--------|------------|
| Rust | `cargo deps`, `cargo-modules` | `cargo-lcom` | `cargo complexity` | `cargo cycles` | `cargo-deps` + tags | `cargo-geiger` + custom |
| Go | `go mod graph`, `go-depgraph` | `gocohesion` | `gocyclo` | `go-cycles` | `goarch` | `golangci-lint` |
| TypeScript | `madge`, `dependency-cruiser` | `ts-lcom` | `eslint complexity` | `madge --circular` | `depcruise` | `eslint` rules |
| Python | `pydeps`, `importlab` | `pycohesion` | `radon` | `pycycle` | `pyarch` | `pylint` |
| C++ | `include-what-you-use`, `clang-include-fixer` | `cppcohesion` | `cppcheck`, `clang-tidy` | `cdeps` | `cppdepend` | `clang-tidy` |

---

## Threshold Calibration

Start with defaults. Adjust per project:

| Metric | Conservative | Balanced | Permissive |
|--------|--------------|----------|------------|
| Ca | > 10 | > 20 | > 30 |
| Ce | > 8 | > 15 | > 25 |
| I | > 0.7 | > 0.8 | > 0.9 |
| A | < 0.3 | < 0.2 | < 0.1 |
| D (main seq) | > 0.2 | > 0.3 | > 0.4 |
| LCOM4 | > 1 | > 2 | > 3 |
| Cyclomatic | > 8 | > 10 | > 15 |
| Cognitive | > 10 | > 15 | > 20 |
| Function length | > 30 | > 50 | > 80 |
| Params | > 5 | > 7 | > 10 |
| God score | > 0.6 | > 0.7 | > 0.8 |
| Line coverage | > 90% | > 80% | > 70% |
| Branch coverage | > 80% | > 70% | > 60% |

**Calibrate**: Run on codebase, plot distributions, set thresholds at 90th percentile for "flag for review".