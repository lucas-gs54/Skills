# Report Template

Delivery output format for the final report. Follow sections in order.

## Markdown template

```markdown
# Reorganization Report

## Summary
- Mode: <plan | full | implement>
- Language: <language/framework>
- Files affected: <n>
- Folders created: <n>
- Modules split: <n>
- Modules merged: <n>

## Before (Problems Found)
- <Problem 1>: <example/location>
- <Problem 2>: <example/location>

## After (Target Structure)
<the final tree, or the target tree when delivery is a plan>

## Decisions & Rationale
- <Decision 1> → <rationale>
- <Decision 2> → <rationale>

## Migration Log
| Step | Action | Changed | Verified |
|------|--------|---------|----------|
| 1 | <move/rename/rewire> | <files> | <build/tests: pass> |

## Verification Results
- Build: <pass/fail, command>
- Tests: <results>
- Manual spot checks: <list>

## Orphaned or Unresolved
- <Anything not migrated, with reason and proposal>
```

## Plan-mode delivery

In `plan` mode, the report includes: Summary, Before (Problems Found), Suggested Target Structure, Decisions & Rationale, and a proposed execution order; it ends with an explicit request for approval. Do not include the Migration Log or Verification Results.

## Implement-mode delivery rules

- Append git diff / commit summaries if a version control exists.
- Any deviation from the approved plan must be stated explicitly in the log with its reason — never silently.