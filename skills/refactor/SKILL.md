---
name: refactor
description: >
  Refactor existing code to reduce concrete complexity while preserving behavior.
  Use when the user asks to refactor, simplify, clean up, modernize, or remove
  duplication from code, or points at a method, class, or file that needs a
  focused cleanup.
argument-hint: "<file, method, or class to refactor, plus style or library constraints>"
allowed-tools: read, write, edit, bash, subagent
---

<objective>
Produce a focused, working refactor that removes real complexity, preserves
observable behavior, and leaves a clear verification record. Use modern language
features when the module supports them and when they reduce state or branching.
Keep the confirmed scope small. Surface larger design problems instead of
silently expanding the change.
</objective>

## Core behavior

- Start in analysis mode. Read the target, its tests, local conventions, and the
  relevant data flow before proposing a change.
- Use a single proposal gate before editing. The proposal names the diagnosis,
  files, behavior impact, test impact, and any blast radius.
- Treat familiarity as separate from complexity. A newer construct earns its
  place by reducing states or branches and remaining clear in context.
- Use the repository's language level, dependencies, and conventions as
  constraints. User-specified style or library choices apply within the
  confirmed scope.
- Search the target module and its direct neighbors for duplicated code and
  duplicated data operations before adding an abstraction.
- Verify focused tests through the IntelliJ MCP server. A missing MCP server is
  a blocked verification state, not a reason to run a shell test command.
- Finish with a structured report covering the change and verification.

## Workflow

1. Preflight
2. Read and map the target
3. Inventory test coverage
4. Classify tiers and scope
5. Present the proposal gate
6. Implement the confirmed scope
7. Verify and report

## Step 1 — Preflight

Identify the target files, module, language, source level, build system, and
available test infrastructure. Check for these convention sources in order:

1. `AGENTS.md` files that govern the target
2. `.github/agent_docs/CodeConventions.md` and links it provides, when present
3. Language, framework, persistence, and pattern conventions near the target
4. Existing code and tests in the target package

Record each convention source that is present. When an expected repository file
is absent, report that fact and use the available local conventions. Check that
focused IntelliJ MCP operations and the `coder` subagent are available before
planning implementation. If focused IntelliJ execution is unavailable, report a
blocked verification state and ask the user how to proceed before editing.

**Completion**: The target, module, applicable conventions, source level,
dependencies, test entry points, and tool availability are recorded.

## Step 2 — Read and map the target

Read each target file in full. Inspect direct callers, neighboring tests, and
nearby utilities or mappers. Trace the data flow from inputs to outputs,
including repository or service calls, filtering, conversion, normalization,
validation, exception paths, and derived values.

Record two separate finding lists:

- Code duplication: repeated statements, branches, methods, or abstractions.
- Data duplication: repeated reads, transformations, validations, or derived
  business facts that look different in source code.

Check behavior-sensitive areas when they apply: ordering, null versus empty
values, exception type and timing, transaction boundaries, authorization,
serialization, persistence annotations, concurrency, and performance.

**Completion**: The target behavior, callers, data flow, duplication findings,
and behavior-sensitive areas are documented.

## Step 3 — Inventory test coverage

Find the tests that exercise the target and list their classes, methods, and
covered scenarios. Classify the test state from that inventory:

- Complete: happy paths, relevant edges, and error paths have explicit tests.
- Adequate: the main branches have coverage, with some scenarios combined or
  implicit.
- Inadequate: important branches or failure paths lack meaningful tests.

For incomplete coverage, ask the user to choose one sequence:

1. Add missing tests first, then refactor.
2. Add tests alongside the refactor.
3. Refactor against the existing tests and record the coverage gap.

When coverage is adequate or complete, proceed with the existing tests as the
safety net. When the repository has no usable test infrastructure, report that
as a risk in the proposal and ask for the user's chosen verification boundary.

**Completion**: Test classes, covered scenarios, uncovered behavior, test state,
and test sequencing are recorded.

## Step 4 — Classify tiers and scope

Name every applicable tier:

- Simple: local mutation, avoidable branching, an imperative collection
  operation, or a missed construct that makes the method harder to follow.
- Duplication: an existing utility, mapper, service operation, business rule,
  or data operation already serves the target's purpose.
- Architecture: responsibilities, boundaries, or abstractions give the class
  the wrong shape.

Order combined work from local simplification to consolidation to architecture.
Define the smallest file and symbol set that completes the confirmed request.
List findings outside that boundary and ask whether the user wants them in this
pass or in a follow-up.

**Completion**: Applicable tiers, ordered work, confirmed boundaries, and
out-of-scope findings are listed.

## Step 5 — Proposal gate

Present one concise proposal before editing:

1. Diagnosis: concrete complexity, code duplication, and data duplication.
2. Planned changes: each symbol and file, with the intended simplification or
   consolidation.
3. Behavior impact: preserved behavior and any intentional change with its
   reason.
4. Test impact: existing coverage, tests to add or update, and focused runs.
5. Architecture impact, when relevant: target shape, integration points, and
   expected blast radius.

Ask: `Confirm with proceed or request changes?`

Implement only after an explicit `proceed`. A request that names files without
confirming the proposal remains a proposal until the user approves this gate.

**Completion**: The user responds with `proceed`, or the proposal is revised
from the user's requested changes.

## Step 6 — Implement the confirmed scope

Dispatch one `coder` subagent through `subagent` for the confirmed cohesive
change. Pass an inline refactor step block with this shape:

```text
Step type: refactor
Step: {tier and symbol}
Diagnosis: {concrete problem}
Files: {confirmed file list}
Conventions:
  1. User preference: {preference or none}
  2. Repository conventions found during preflight
  3. skills/refactor/references/MODERNIZATION.md
Test sequencing: {tests first | alongside | existing tests sufficient}
Task:
  - {specific implementation and consolidation tasks}
  - Preserve the confirmed behavior and report findings outside scope.
```

The coder reads `skills/coder/SKILL.md` as its contract. The refactor step
block is identified by its existing-target diagnosis, behavior-preservation
requirement, and confirmed refactor scope. It edits only the confirmed files and
reports `Status`, `Test classes (FQCNs)`, `Files created`, and `Files updated`.
The orchestrator reads that report before verification.

Use the modernization reference as a decision guide. Preserve an imperative
loop, mutable local, or explicit branch when it is clearer, required by the
module, or protects behavior. Use records, pattern matching, streams, Vavr,
or another modern construct when the preflight checks support it and the change
reduces complexity in context.

**Completion**: The coder reports a pass, the changed files stay within the
confirmed scope, and any out-of-scope finding is recorded.

## Step 7 — Verify and report

Use `idea-get_run_configurations` scoped to the changed files or reported test
classes. Then use `idea-execute_run_configuration` for the smallest relevant
unit-test set. Keep the IntelliJ MCP server as the test execution path.

If tests fail, return the failure to the coder or fix it through the same
confirmed scope. Preserve all meaningful assertions. If MCP becomes
unavailable, report verification as blocked and ask the user how to proceed.

Finish with:

```text
Refactor: {short description}
Tiers: [simple | duplication | architecture]
Status: verified | blocked | failed
Files changed: [list]
Behavior: {preserved behavior and intentional changes}
Tests: {test classes and focused result}
Out of scope: [findings, or none]
```

**Completion**: Focused tests pass and the final report names the changed files,
behavior impact, test result, and remaining scope. A blocked or failed result
states the blocking evidence clearly.

## Reference

- [MODERNIZATION.md](references/MODERNIZATION.md) — conditional modernization
  decisions for language features, collections, null handling, and Vavr.
- [CHECKLIST.md](references/CHECKLIST.md) — preflight, behavior, delegation, and
  verification checks.
- [Coder contract](../coder/SKILL.md) — the scenario and refactor step shapes,
  scope rules, and structured report required from the implementation subagent.
