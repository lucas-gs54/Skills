# Modularization Rules

Concrete organizational rules applied when designing the target structure. Adapt to the target language/framework; these are the baseline.

## 1. Module Boundaries

- One responsibility per module; name the module after that responsibility.
- Keep a single, explicit direction of dependency between modules.
- Expose a minimal public interface; hide implementation details.
- Split a module when it holds unrelated responsibilities; merge when a split creates more coupling than it removes.
- Prefer cohesion over file count: ask "what does this belong with?" before "where does this file fit?".

## 2. Responsibilities

- No god modules: a module with many unrelated concerns must be split into cohesive units.
- Group by domain before by technical layer when the framework permits it (feature folders over type folders).
- Keep shared/utility code in a dedicated module only when shared by 2+ modules; move it back if it has one user.

## 3. Dependencies

- Depend inward, not outward: high-level modules must not depend on low-level details.
- Forbid circular dependencies between modules; break cycles with interfaces or by extracting shared dependencies.
- Minimize coupling: prefer dependency injection and interfaces over direct global access.
- Track the dependency graph during design; every edge must have a reason.

## 4. Naming

- File/folder names follow the framework's convention (PascalCase files, kebab-case, snake_case, etc.).
- Same term for the same concept everywhere; consistent vocabulary across modules.
- Names describe responsibility, not location or implementation.
- Avoid generic names that depend on a parent folder to make sense.

## 5. Layout

- One conventional root layout per project; do not mix incompatible patterns.
- Config, entry points, and build/framework files stay at their conventional location.
- Keeping a flat layout is fine when it preserves cohesion; do not add nesting without a grouping need.

## 6. Language/Framework Conventions

Apply native conventions instead of generic advice:

- **TypeScript/JavaScript**: feature folders under `src/`, barrels for public API, co-located styles/tests, `index.ts` exports.
- **Python**: package layout with `__init__.py`, clear import paths, avoid circular imports, keep modules small.
- **Go**: package per responsibility, direction via internal, avoid import cycles.
- **Rust**: module tree mirrors directory tree, `mod`/`pub use` for public API, keep crates by responsibility.
- **Java/Kotlin**: package by feature within the module, public API via package-private internals.
- **Other**: use the framework's official project-layout guide when one exists (e.g., Rails, Django, Angular, Next.js structure conventions).

When the target has an official structure convention, it wins over these baseline rules.