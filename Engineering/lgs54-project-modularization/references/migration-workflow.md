# Migration Workflow

Guidance for building and executing the restructuring plan safely.

## 1. Before/After Inventory

Track every file affected. Use one table:

| Action | Before (`from`) | After (`to`) | Reason | Status |
|--------|-----------------|--------------|--------|--------|
| move   | `src/utils/date.ts` | `src/shared/date.ts` | shared by 3 modules | done |
| rename | `admin_panel.ts` | `admin/index.ts` | folder convention | pending |
| merge  | `user_auth.ts` + `user_sessions.ts` | `user/auth.ts` | same responsibility | pending |
| delete | `legacy/` | — | dead code | pending |

- Create the inventory in Step 4 (plan) and update it after every change during Step 6 (implement).
- Every final entry must have `Status: done` or a documented exception.

## 2. Safe Migration Order

Order steps by risk, lowest first:

1. Additive changes with no behavior impact (new folders, exports, empty module stubs).
2. Re-exports and wrapper measures so old paths keep working temporarily.
3. Mechanical moves and renames (safe, low risk; verify imports after each batch).
4. Rewiring dependencies, one module at a time, keeping the build green after each step.
5. Cleanup: remove temporary wrappers only after imports point at the new locations.

General rule: the project should stay buildable/testable after each step whenever feasible. Small steps keep failures local and reverts easy.

## 3. Verification

After each step (or at least after every batch):

- Run the build and the existing test suite.
- If neither exists, do targeted spot checks on called modules.
- Re-run after the final structural change, not just after the first.

## 4. Stop Conditions

Stop and ask the user if implementation reveals:

- An uncovered edge case that requires changing behavior or contracts.
- A step that cannot keep the project buildable without a larger rewrite.
- A conflict with an explicit user requirement or constraint.

## 5. Revert

- Prefer a clean version-control checkout when the current working state is committed; otherwise, reverse steps one at a time using the inventory in reverse order.