---
name: implement-concise
description: >
  Test-Driven Development orchestrator. Six-phase workflow: business logic discovery →
  scenario generation → planning → implementation → code review → commit. Infers test
  and build commands from AGENTS.md — works across any stack. Invoke with
  /skill:implement-concise followed by a problem statement or document.
metadata:
  argument-hint: "<problem statement or paste document>"
allowed-tools: Read Write Edit Glob Grep Bash Task
---

<objective>
Six-phase feature implementation workflow. Orchestrator runs inline; heavy sub-tasks delegate to agents.

1. **Discover** — understand business intent via questioning (no coding)
2. **Scenarios** — enumerate test scenarios, get approval, persist to SCENARIO.md
3. **Plan** — create branch, produce plan.md with file manifest and todos
4. **Implement** — fast-TDD per step (tests + src in first pass, then iterate on failures); parallelize independent steps via agents
5. **Review** — user-driven review; detect plan drift before applying changes
6. **Commit** — generate commit message, push branch
</objective>

---

## Phase 1 — Business Logic Discovery

**Goal:** Shared understanding of WHAT, not HOW.

Interview the user — one question at a time — until every branch of the business logic is resolved:

- Offer your recommended answer for each question; accept the user's answer before moving on
- Cover: decision branches, edge cases, error states, boundary conditions
- No implementation, frameworks, code, or testing strategy discussion — redirect: "We'll cover that in Phase 3."

When complete, summarize the business logic in plain language. Ask: "Is this a complete picture? Anything missing?"

---

## Phase 2 — Test Scenario Generation

Using Phase 1 output, list all scenarios grouped by:

- **Happy path** — normal successful flows
- **Edge cases** — boundary values, empty inputs, limits
- **Error / failure cases** — invalid input, missing data, system failures
- **Business rule violations** — constraint breaches, illegal state transitions

Format each scenario:

```
### S-{N}: {Short title}
**Given:** {precondition}
**When:** {action}
**Then:** {expected outcome}
**Category:** happy-path | edge-case | error | business-rule
```

Present all scenarios. Ask: "Does this cover everything? Anything missing or incorrect?"
Incorporate feedback and loop until approved.

Once approved:

1. Derive folder name from problem statement: 3–6 words, camelCase (e.g. `hotelRoomPriceCalculation`)
2. Create `planning/{folderName}/` if missing
3. Write to `planning/{folderName}/SCENARIO.md`:

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

---

## Phase 3 — Planning

### Branch
Ask: "What should the branch name be?" Suggest one based on folder name (e.g. `feature/hotel-room-price-calculation`).

```bash
git checkout -b {branch-name}
```

If branch exists: "Branch `{name}` already exists. Use it, or pick a different name?"

### Plan
Read `copilot-instructions.md` (root level) if present for project conventions.

Create `planning/{folderName}/plan.md`:

```markdown
# Plan: {title}

## Goal
{One sentence}

## Approach
{2–3 sentences}

## File Manifest

| Action | File path | Notes |
| ------ | --------- | ----- |
| CREATE | ...       | ...   |

## Steps

### Step 1 — {title}
**Depends on:** none | Step N
**Parallelizable:** yes | no
**Scenarios covered:** S-1, S-2
**Files:** list
**Tasks:**
- [ ] Draft scenario-aligned tests and src for S-1, S-2 (single pass allowed)
- [ ] Iterate until targeted tests pass without weakening assertions
```

Rules: each step = one cohesive test + implementation unit; mark dependencies explicitly; steps with no dependencies are parallelizable (same wave).

Present plan. Ask: "Does this look right? Any changes?" Finalize before proceeding.

---

## Phase 4 — Implementation

Parse steps from `plan.md`. Build dependency graph. Group into waves:

- Wave 1 = steps with `Depends on: none`
- Wave N = steps whose dependencies are all complete

**Per wave:** spawn one `coder` agent per step in parallel via Task tool.
Construct each Task prompt as follows — pass minimal context and references only (never inline full file contents):

```
You are a coding implementer.

Step block (exact text):
{paste the exact step block from plan.md}

Scenario IDs for this step:
{extract from "Scenarios covered" in the step block}

Files for this step:
{extract from "Files" in the step block}

SCENARIO.md path: planning/{folderName}/SCENARIO.md
plan.md path: planning/{folderName}/plan.md
Folder: {folderName}

Do not read the full plan or full scenario file by default.
Read only the assigned step details and the listed scenario sections; use plan.md only if required to unblock.
Load `skills/coder/references/coding-rules.md` only for non-trivial steps or unclear style decisions.

Use fast-TDD: draft scenario-aligned tests and src in one pass when possible, then refine until tests pass.
Do not weaken, delete, or bypass tests to get green.
When done, report: status (`pass | fail | blocked`) and the fully-qualified class names of every test class you wrote.
```

Wait for all agents in a wave before starting the next.

**After each wave — collect and discard:**

From each agent response, extract only:
- `Status` (`pass | fail | blocked`)
- `Test classes (FQCNs)`

Discard all other agent narration.

If any step is `blocked`, surface blockers and ask the user how to proceed before running tests.

**Targeted test run (for `pass` steps):**

Read `AGENTS.md` (root level) to infer the project's test command, test targeting syntax, and scope conventions.
Use that to run only the tests written in this wave.

- Any `fail` status from coder agents → show summary, ask: "Fix automatically or review manually?"
- Test command failures → show errors, ask: "Fix automatically or review manually?"
- All pass → start next wave

**After all waves — scoped regression check:**

Using the same test runner inferred from `AGENTS.md`, run tests scoped to the packages/modules touched by this plan's file manifest.

- Failures → show errors, ask: "Fix automatically or review manually?"
- Pass → "Implementation complete. Moving to Phase 5."

---

## Phase 5 — Code Review

Tell the user:
> "Code is ready for review. Tell me what to change, edit files directly, or say 'review done' to move to Phase 6."

**On requested change:**
1. Compare against `plan.md`
2. Consistent → implement directly
3. Deviates → surface: "This differs from the plan. Plan says: `{X}`. You're asking for: `{Y}`. Intentional?"
   - Yes → update `plan.md` first, then implement
   - No → ask: "Should I implement as originally planned?"

**On direct user edits:** silently run `git diff` against the file manifest. If unexpected changes found, surface once: "I notice `{file}` changed outside the plan — flagging in case it was accidental."

---

## Phase 6 — Commit and Push

Ask: "Ready to commit and push?"

1. Ask: "Run the full build before committing? (Recommended but takes a few minutes.)"
   - Yes → infer the build command from `AGENTS.md` and run it. Fix failures before proceeding.
   - No → skip

2. Show `git diff --stat`
3. Generate commit message:
   ```
   {type}({scope}): {short summary}

   - {bullet: what changed}
   - {bullet: what changed}
   ```
4. Ask: "Any changes to this commit message?"
5. Once confirmed:
   ```bash
   git add -A
   git commit -m "{message}"
   git push -u origin {branch-name}
   ```

---

## General Rules

- Track current phase. On resume: "We're in Phase N — {last action}."
- One phase at a time. Do not skip ahead without user confirmation.
- No code in Phase 1 or Phase 2.
- `plan.md` is the source of truth for Phase 5 drift detection.
