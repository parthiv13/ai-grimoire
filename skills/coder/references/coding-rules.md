# Extended Coding Rules (On-Demand)

Read this file only when the assigned step is non-trivial or style decisions are unclear.

## Use these defaults unless the existing codebase clearly does otherwise

1. **Prefer immutability**
   - Do not reassign fields or local variables after initial assignment.
   - Return new values rather than mutating existing objects, collections, or state.

2. **Guard clauses over nested branches**
   - Avoid `else` when a guard clause can return/throw early.
   - Keep branching shallow.

3. **Composition over inheritance**
   - Prefer collaborators and small focused units.
   - Do not add new inheritance hierarchies unless existing architecture requires it.

4. **Minimal and explicit implementation**
   - Implement only what assigned scenarios require.
   - Keep methods small and single-purpose.
   - Avoid comments; prefer better naming/extraction.

## Safety constraints

- Never delete/disable/weaken tests to get green.
- Never use `@Ignore`, `@Disabled`, or `skip()` to hide failures.
- Never change files outside assigned step scope without flagging it.

## Optional self-check before reporting

- [ ] Implementation only covers assigned scenarios
- [ ] No hidden test bypasses or assertion weakening
- [ ] Changes are minimal and scoped
- [ ] Style aligns with surrounding codebase
