---
name: implement
description: >
   Plan a new feature, get to a shared understanding, and implement via parallel sub-agents.
disable-model-invocation: true
metadata:
  argument-hint: "<problem statement or paste document>"
allowed-tools: Read Write Edit Glob Grep Bash Task
---

On resume, state current phase: "We're in Phase N — {last action}."
Work through phases in order; do not skip ahead without user confirmation.

---

## Phase 1 — Business Logic Discovery

**Goal:** Shared understanding of WHAT, not HOW.

Interview the user — one question at a time — until every branch of the business logic is resolved:

- Offer your recommended answer for each question; accept the user's answer before moving on
- Cover: decision branches, edge cases, error states, boundary conditions
- Focus exclusively on business logic, user intent, and domain rules — redirect implementation questions: "We'll cover that in Phase 4."

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

**Per wave:** spawn one `coder` agent per step in parallel.
Construct each agent's prompt from `references/coder-prompt.md`, substituting the step block, scenario IDs, files, and folder name from `plan.md`.

Wait for all agents in a wave before starting the next.

**After each wave — extract results:**

From each agent response, extract:
- `Status` (`pass | fail | blocked`)
- `Test classes (FQCNs)`
- `Files created`
- `Files updated`

If any step is `blocked`, surface blockers and ask the user how to proceed before running tests.

**Targeted test run:**

Use the IntelliJ MCP server to run only the tests written in this wave.

- Coder reported `blocked` → surface blockers, ask user how to proceed (skip test run)
- Coder reported `fail` → show summary, ask: "Fix automatically or review manually?" (skip test run)
- Coder reported `pass` but IntelliJ tests fail → show errors, ask: "Fix automatically or review manually?"
- All pass → start next wave

**After all waves — scoped regression check:**

Using the IntelliJ MCP server, run tests scoped to the packages/modules touched by this plan's file manifest.

- Failures → show errors, ask: "Fix automatically or review manually?"
- Pass → "Implementation complete. Moving to Phase 5."

---

## Phase 5 — Code Review

Tell the user:
> "Code is ready for review. Tell me what to change, edit files directly, or let me know when you're satisfied and I'll output the final summary."

**On requested change:**
1. Compare against `plan.md`
2. Consistent → implement directly
3. Deviates → surface: "This differs from the plan. Plan says: `{X}`. You're asking for: `{Y}`. Intentional?"
   - Yes → update `plan.md` first, then implement
   - No → ask: "Should I implement as originally planned?"

**On direct user edits:** silently run `git diff` against the file manifest. If unexpected changes found, surface once: 
"I notice `{file}` changed outside the plan — flagging in case it was accidental."

**When review is complete:**

Output a final summary:
> **Workflow complete.**
> - Scenarios: N total
> - Files: M created/updated
> - All tests passing ✓


