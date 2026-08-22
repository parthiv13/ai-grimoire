---
name: tests
description: >
  Structured unit-test authoring in five phases: scope definition →
  per-unit scenario approval → parallel implementation → refactor →
  code review. Use when the user asks to write, improve, or analyse tests,
  mentions test coverage, or asks to test recent code changes.
argument-hint: "<method | class | 'write tests for my changes'>"
allowed-tools: read, write, edit, bash, subagent
---

<objective>
Guide the user through deliberate, high-coverage unit-test authoring in five phases.
Reach shared understanding before writing a single line of code.
Target ≥ 90 % coverage through meaningful scenario coverage, not line-padding.
</objective>

## Core Behavior

**Default to deliberate test design** — establish scope and obtain scenario approval before implementation.
**IDE-first testing** — use the IntelliJ MCP server for test discovery, execution, results, navigation, and inspections. Keep test execution exclusively in IntelliJ MCP; when the server cannot perform the requested test operation, pause and ask the user how to proceed.
**Progressive reveal** — keep the workflow here and consult project-specific testing guidance only during implementation.
**Completion criteria** — each phase ends with an observable handoff or user decision.

### Workflow
1. **Scope definition** → identify testable units and obtain approval
2. **Scenario enumeration** → agree on meaningful coverage one unit at a time
3. **Implementation** → delegate independent test units and run them through IntelliJ MCP
4. **Refactor** → remove duplication while preserving approved scenarios
5. **Code review** → validate the result and detect scenario drift

---

## Phase 1 — Scope Definition

**Goal:** Agree on which logical units need tests before any scenarios are written.

### 1a — Gather input

Determine what the user has provided:

| Input type | How to handle |
|---|---|
| Single method pasted inline | Use it directly |
| Class pasted inline | Read all non-trivial methods |
| "Write tests for my changes" (or no explicit input) | Run `git diff HEAD~1` (or `git diff main...HEAD` if on a feature branch) to collect all changed/added code; read the full bodies of every touched method |
| Git diff pasted inline | Parse changed hunks; resolve full method bodies with the `read` tool |

When using git diff, also read the surrounding class context so you understand
what each changed method does.

**Completion**: The requested source or diff is identified, and the full context for each relevant method is available.

### 1b — Identify candidate units

A **unit** is a method that:
- Encodes a meaningful business rule or transformation
- Can be exercised in isolation (possibly with mocks)
- Is **not** trivially delegating to a single other call without adding logic

Prioritize meaningful units and exclude:
- Getters / setters / simple field assignments
- Methods whose only logic is a null-guard with no branch behaviour worth testing
- Generated boilerplate (constructors with no logic, `toString`, `equals/hashCode`)

For **private methods**: infer whether the logic is rich enough to warrant
promoting to package-private. Surface each such method explicitly:

> "`adjustRate(room, date)` is private but contains meaningful pricing logic.
>  Should we promote it to package-private and treat it as an independent unit?"

### 1c — Present unit table

Present a table — one row per candidate unit:

| # | Unit (class#method signature) | Brief description | Est. scenarios* |
|---|---|---|---|
| 1 | `PricingEngine#calculate` | Applies rate rules and returns final price | 6 |
| 2 | `PricingEngine#adjustRate` | (private → package-protected?) Applies occupancy discount | 3 |

\* Scenario count **excludes** null-guard-only cases.

Ask:
> "Does this list cover all the units you want tested? Should any be removed,
>  added, or should a private method be promoted to package-private?"

Loop until the user approves the unit list. Record the final approved list in memory.

**Completion**: The user approves the candidate-unit table, including any private-method promotion decisions.

---

## Phase 2 — Scenario Enumeration (one unit at a time)

**Goal:** Agree on an exhaustive, meaningful scenario list per unit before any code is written.

Work through the approved units one at a time. Do **not** present multiple units together.

For the current unit, enumerate scenarios grouped by:

- **Happy path** — normal successful flows
- **Business rule / branching** — distinct conditional branches that encode domain rules
- **Edge / boundary** — limits, empty collections, zero values, max values
- **Error / exception** — invalid input, illegal state, expected exceptions

For each scenario use this format:

```
S-{N}: {Short title}
  Input : {key inputs / preconditions}
  Action: {what is called}
  Expect: {outcome / return value / side-effect / exception}
```

For null handling, add null-pointer scenarios or checks only when the production code specifically mentions or handles `null`; otherwise omit them.

After presenting all scenarios for the unit ask:
> "Does this cover everything for `{unit}`? Anything missing or wrong?"

Incorporate feedback. Once approved, move immediately to the next unit.
Repeat until all units are done.

**Completion**: The current unit's scenario list is approved before the next unit is presented.

Once all units have approved scenario lists:
> "All scenario lists approved. Ready to implement? (yes / tweak first)"

**Completion**: Every approved unit has an agreed scenario list and the user authorizes implementation.

---

## Phase 3 — Implementation

**Goal:** Write passing tests with test-file scope.

**Test execution policy:** Use IntelliJ MCP exclusively for every test run, including targeted runs, regression checks, and reruns after fixes. Use terminal commands for repository inspection and file operations, not for test execution. When IntelliJ MCP cannot perform the required operation, explain the limitation and ask the user how to proceed.

### 3a — Pre-implementation checks

Read the relevant agent docs before generating any code:
- `.github/agent_docs/Testing.md`
- `.github/agent_docs/conventions/Java.md`

**Completion**: Project testing guidance and language conventions are loaded, and the target test files are identified.

### 3a.1 — Mandatory coding conventions for test code

Apply these rules to every generated test class:

| Rule | Detail |
|---|---|
| **Immutable test data** | Follow functional programming guidelines. Bind values once with `final var` and build new objects instead of mutating existing ones. |
| **List-based helpers** | Define test helper methods with `List<T>` parameters; call them with `List.of(...)` rather than varargs (`...`). |
| **Immutable constants** | Declare values shared across tests (e.g. dates, IDs, strings) as `private static final TYPE NAME = ...` using UPPER_CASE names. |
| **Stateless `@BeforeEach`** | Use `@BeforeEach` only for external setup helpers (e.g. `setPropertyContext()`) and Mockito stubs (`when(...)`). Store shared values as `static final` constants rather than instance fields. |
| **Deterministic dates** | Use concrete `private static final LocalDate` values (e.g. `LocalDate.of(2025, 1, 10)`) so tests remain independent of the system clock. |
| **Meaningful variable names** | Name variables after the domain concept they represent. Prefer a collective `List.of(...)` (e.g. `datesWithNonZeroCapacity`) over indexed names such as `d1`, `d2`, `item1`, or `item2`. |

**Completion**: Every generated test class satisfies the project conventions and all mandatory rules above.

### 3b — Structure

- Each unit gets its own `@Nested` inner class inside the test file.
- Inner class name = unit method name in UpperCamelCase (e.g., `Calculate`, `AdjustRate`).
- Test method names describe the scenario outcome in plain language (`shouldApplyOccupancyDiscount`, `shouldReturnBaseRateWhenNoRulesMatch`).
- **Self-evident structure** — express setup, action, and expectation through clear names rather than `// GIVEN`, `// WHEN`, or `// THEN` comments.
- Keep test coverage aligned with the scenarios approved in Phase 2.

**Completion**: Test structure, naming, and scenario scope match the approved design.

### 3c — Parallelisation

Group units into independent batches (units that share no setup dependencies can run in parallel).

For each batch:
1. Spawn one `coder` subagent per unit (parallel via the `subagent` tool).
   Pass each agent: unit signature, approved scenario list, test file path, project conventions summary.
2. Wait for all agents in the batch to complete.
3. Run the tests for that batch through the IntelliJ MCP server, selecting only the newly written test classes.

Capture the IntelliJ MCP result, including failures and stack traces when present.

- Tests **fail** → show errors; fix automatically (max 2 self-correction loops), then report.
- Tests **pass** → continue to next batch.

**Completion**: Every test in the batch has been executed through IntelliJ MCP, and its result is recorded.

After all batches complete, use IntelliJ MCP to run a scoped regression check across the affected packages or modules.

- All pass → "Implementation complete. Moving to Phase 4 — refactor."
- Failures → fix, re-run through IntelliJ MCP; if still failing after 2 attempts, surface to the user.

**Completion**: Batch tests and the scoped regression check pass through IntelliJ MCP, or unresolved failures are surfaced with their details.

---

## Phase 4 — Refactor

**Goal:** Remove duplication in test code while preserving test semantics.

Scan the newly written test classes for:
- Repeated object construction that can move to `@BeforeEach`
- Identical stub setups duplicated across `@Nested` classes that can share a parent fixture
- Repeated assertion sequences that can become a private helper

Apply refactors while preserving scenario coverage — every scenario approved in Phase 2 remains tested after refactoring.

Run the same scoped IntelliJ MCP test configuration from Phase 3 after refactoring.

- Tests pass → "Refactor complete. Moving to Phase 5 — review."
- Tests fail → fix (max 2 attempts) and re-run through IntelliJ MCP; if failures remain, surface them to the user with the diff.

**Completion**: Refactoring preserves the approved scenarios and the scoped IntelliJ MCP test run passes.

---

## Phase 5 — Code Review

**Goal:** User reviews. You assist — but guard against scenario drift.

Tell the user:
> "Tests are ready for your review. You can:
>  - Tell me what to change and I'll implement it
>  - Edit files directly yourself
>  - Say 'review done' to finish"

**When user asks you to make a change:**

1. Compare against the approved Phase 2 scenario list.
2. Change is **consistent with an approved scenario** → implement directly.
3. Change **adds or removes scenario coverage**:
    - Pause implementation.
    - Surface: "This adds/removes coverage beyond Phase 2. Intentional? Shall I update the scenario list to reflect this?"
    - User says **yes** → update the record, then implement.
    - User says **no** → ask what they actually want.

**When user edits files directly:**
- Preserve the user's edits and avoid overwriting them.
- On next interaction, silently read the current file.
- If the file diverges from Phase 2 scenarios, surface **once**:
  > "I notice `{file}` has changes outside the agreed scenarios — flagging in case it was accidental. Should I incorporate these as-is?"
- If user says yes or does not respond, treat the current file state as the truth and continue from it.

**Completion**: The user says `review done`, or the current reviewed test files are accepted as the source of truth.

---

## General Rules

- Track current phase. On resume: "We are in Phase N — {last action}."
- Work through one phase at a time; proceed to the next phase after user confirmation.
- Keep Phase 1 and Phase 2 focused on analysis, approval, and scenario design.
- Treat the Phase 2 scenario list as the source of truth for Phase 5 drift detection.
- Target ≥ 90 % line/branch coverage through meaningful scenarios; preserve scenario quality when coverage is lower.
