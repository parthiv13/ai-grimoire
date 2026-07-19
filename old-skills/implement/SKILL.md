---
name: implement
description: >
  Feature implementation orchestrator. Six-phase workflow: business logic
  discovery → scenario generation → planning → implementation → code review →
  commit. Invoke with /implement followed by a problem statement or document.
argument-hint: "<problem statement or paste document>"
---

<objective>
Guide the user through a structured implementation workflow in six phases:

1. **Discover** — understand business intent via relentless questioning (no coding yet)
2. **Scenarios** — enumerate test scenarios, get approval, persist to SCENARIO.md
3. **Plan** — create branch, produce concise plan.md with file manifest and todos
4. **Implement** — generate tests first, then src; parallelize independent steps via agents
5. **Review** — user-driven review; detect plan drift before applying any changes
6. **Commit** — generate commit message, push branch

Orchestrator runs inline (main context). Heavy sub-tasks delegate to agents.
</objective>

---

## Phase 1 — Business Logic Discovery

**Goal:** Shared understanding of WHAT, not HOW.

Read the problem statement or document the user provided in `$ARGUMENTS`.

Conduct a relentless Socratic interview — one question at a time — until every
branch of the business logic tree is resolved:

- Walk each decision branch to its leaf
- For each question, offer your recommended answer
- Accept the user's answer before moving on
- Probe edge cases, exceptions, error states, boundary conditions
- Do NOT discuss implementation, frameworks, classes, or code structure
- Do NOT discuss testing strategy yet
- Redirect any implementation tangents: "We'll get to that in Phase 3."

When confident you understand the full business intent, summarize it back in plain
language. Ask: "Is this a complete picture of the business logic? Anything missing?"

Persist the business logic summary in memory — needed in Phase 2.

**Model:** Run Phase 1 inline. Sonnet-class required for nuanced questioning.

---

## Phase 2 — Test Scenario Generation

**Goal:** Enumerate all meaningful test scenarios. No code yet.

Using business logic from Phase 1, identify and list all test scenarios grouped by:

- **Happy path** — normal successful flows
- **Edge cases** — boundary values, empty inputs, limits
- **Error / failure cases** — invalid input, missing data, system failures
- **Business rule violations** — constraint breaches, illegal state transitions

For each scenario:

```
### S-{N}: {Short title}
**Given:** {precondition}
**When:** {action}
**Then:** {expected outcome}
**Category:** happy-path | edge-case | error | business-rule
```

Present all scenarios to the user. Ask:

> "Does this cover all scenarios? Anything missing or incorrect?"

Incorporate all feedback. Loop until user approves.

Once approved:

1. Derive folder name from problem statement: 3–6 words, camelCase
   (e.g., `hotelRoomPriceCalculation`, `guestCheckInFlow`)
2. Create `planning/{folderName}/` if missing
3. Write approved scenarios to `planning/{folderName}/SCENARIO.md`

**SCENARIO.md format:**

```markdown
# Test Scenarios: {Human-readable title}

> Generated from: {one-line problem summary}

## Happy Path

...

## Edge Cases

...

## Error / Failure Cases

...

## Business Rule Violations

...

---

_Total scenarios: N_
```

Announce: "Scenarios saved. Moving to Phase 3 — planning."

---

## Phase 3 — Planning

**Goal:** Create a branch and a concise, executable plan.

### Branch

Ask the user: "What should the branch name be?"
Suggest one based on folder name (e.g., `feature/hotel-room-price-calculation`).

Once confirmed:

```bash
git checkout -b {branch-name}
```

If branch already exists: "Branch `{name}` already exists. Use it, or pick a different name?"

### Plan

Read `copilot-instructions.md` (root level) for project conventions before planning.

Create `planning/{folderName}/plan.md`:

```markdown
# Plan: {title}

## Goal

{One sentence}

## Approach

{2–3 sentences max}

## File Manifest

| Action | File path | Notes |
| ------ | --------- | ----- |
| CREATE | ...       | ...   |
| UPDATE | ...       | ...   |
| DELETE | ...       | ...   |

## Steps

### Step 1 — {title}

**Depends on:** none | Step N
**Parallelizable:** yes | no
**Scenarios covered:** S-1, S-2
**Files:** list
**Tasks:**

- [ ] Write failing tests for S-1, S-2
- [ ] Implement src to make tests pass

### Step 2 — ...
```

Rules:

- Each step = one cohesive unit of test + implementation
- Mark dependencies explicitly
- Steps with no dependencies = parallelizable (same wave)
- Keep steps small (1–3 scenarios each ideally)

Present plan. Ask: "Does this look right? Any changes?"
Incorporate feedback and finalize before proceeding.

---

## Phase 4 — Implementation

**Goal:** Tests first, then make them pass.

Parse steps from `plan.md`. Build dependency graph. Group into waves:

- Wave 1 = all steps with `Depends on: none`
- Wave N = steps whose dependencies are all complete

**For each wave:**

- Spawn one `tdd-implementer` agent per step (in parallel via Task tool)
- Pass each agent: step details, path to SCENARIO.md, path to plan.md, folderName
- Wait for all agents in wave to complete before starting next wave

**After each wave (fast feedback loop):**

Derive the Gradle module path from the file paths in `plan.md` (e.g. a file under
`g2-casper/g2-casper-ip/src/...` → module `:g2-casper:g2-casper-ip`). Do not assume a module.

Run only the test classes written in that wave using the targeted test command:

```bash
./gradlew :<derived-module>:test --tests "<fully.qualified.TestClassName>"
```

Collect all test class FQCNs from the wave's implementer agents and pass them as separate `--tests` flags:

```bash
./gradlew :<derived-module>:test \
  --tests "<fully.qualified.TestClassName1>" \
  --tests "<fully.qualified.TestClassName2>"
```

If steps span multiple modules, group by module and run one command per module.
Gradle's `test` task implicitly compiles the module before running — **no separate build step needed**.
This mirrors running tests in IntelliJ and completes in seconds, not minutes.

- Tests fail → show errors, ask: "Fix automatically or review manually?"
- All wave tests pass → start next wave

**After all waves complete (scoped regression check):**

Do NOT run the full test suite — derive the affected module(s) and package scope from `plan.md`'s
file manifest. Collect the unique top-level packages of every file touched and build a `--tests`
pattern per package. Group by module:

```bash
./gradlew :<derived-module>:test --parallel \
  --tests "<fully.qualified.package1>.*" \
  --tests "<fully.qualified.package2>.*"
```

If files span multiple modules, run one command per module.

`--parallel` runs test classes concurrently (safe because unit tests have no shared state).
This catches regressions within the affected domain without paying the cost of the full suite.

- Tests fail → show errors, ask: "Fix automatically or review manually?"
- Tests pass → "Implementation complete. Moving to Phase 5 — review."

> **Note:** Full `./gradlew :g2-casper:g2-casper-ip:build` is intentionally deferred to Phase 6
> pre-commit, when the user is satisfied with the code. No point running it during active iteration.

---

## Phase 5 — Code Review

**Goal:** User reviews. You assist — but guard against plan drift.

Tell the user:

> "Code is ready for your review. You can:
>
> - Tell me what to change and I'll implement it
> - Edit files directly yourself
> - Say 'review done' when finished to move to Phase 6"

**When user asks you to make a change:**

1. Compare requested change against `plan.md` before touching code
2. Change is **consistent with plan** → implement directly
3. Change **deviates from plan** (new behaviour, removed feature, structural shift):
   - Do NOT implement yet
   - Surface: "This differs from the plan. Plan says: `{X}`. You're asking for: `{Y}`. Intentional?"
   - User says **yes** → update `plan.md` first, then implement
   - User says **no** → ask: "Should I implement as originally planned instead?"

**When user edits files directly:**

- Do not interfere
- On next interaction, silently run `git diff` against plan's file manifest
- If unexpected changes detected, surface once: "I notice `{file}` changed outside the plan — flagging in case it was accidental."

---

## Phase 6 — Commit and Push

Ask: "Ready to commit and push?"

If yes:

1. Ask the user: "Should I run the full build before committing? This compiles everything, runs all tests, and checks the module — recommended but takes a few minutes."
   - User says **yes** → run one build command per module derived from `plan.md`'s file manifest:

     ```bash
     ./gradlew :<derived-module>:build
     ```

     - Build fails → show errors, fix before proceeding
     - Build passes → continue

   - User says **no** → skip and proceed to the commit step

2. Show `git diff --stat` summary
3. Generate commit message:

   ```
   {type}({scope}): {short summary}

   - {bullet: what changed}
   - {bullet: what changed}

   Co-authored-by: Copilot <223556219+Copilot@users.noreply.github.com>
   ```

4. Ask: "Any changes to this commit message?"
5. Once confirmed:
   ```bash
   git add -A
   git commit -m "{message}"
   git push -u origin {branch-name}
   ```
6. Confirm push. Print branch URL if available.

---

## General Rules

- Track current phase. On resume: "We're in Phase N — {last action}."
- One phase at a time. Do not skip ahead without user confirmation.
- Never write code in Phase 1 or Phase 2.
- `plan.md` is the source of truth for Phase 5 drift detection.