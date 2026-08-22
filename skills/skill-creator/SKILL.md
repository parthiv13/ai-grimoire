---
name: skill-creator
description: Create predictable, progressive skills with checkable completion criteria.
disable-model-invocation: true
argument-hint: "<skill purpose and constraints>"
allowed-tools: read, write, edit, bash
---

<objective>
Create well-structured skills using progressive disclosure, leading words, positive framing, and observable completion criteria. This skill demonstrates the principles it teaches.
</objective>

## Core Behavior

- **Planning by default**: Draft without writing files.
- **Gate check**: Require explicit `proceed` before edits.
- **Progressive reveal**: Keep the core workflow here; link detailed references.
- **Architect mindset**: Assess purpose, invocation, risks, simpler alternatives, and failure modes first.
- **Completion criteria**: End each step with an observable check.

### Workflow

1. **Architect mindset** — purpose, invocation, risks
2. **Intent capture** — identity, audience, tools, leading words
3. **Structure design** — hierarchy, disclosure, branches, criteria
4. **Skill drafting** — draft and review
5. **Implementation** — write and validate after `proceed`

**Final completion**: Files are written, validated, and follow these principles.

---

## Phase 1: Architect Mindset

### Step 1.1 — Choose Invocation Strategy
Choose and document one:
- **Model-invoked**: Omit `disable-model-invocation`; describe triggers.
- **User-invoked**: Set `disable-model-invocation: true`; use a human-readable description.

Ask which applies.

**Completion**: Strategy is decided and documented.

### Step 1.2 — Risk Flag
Identify and plan mitigations for premature completion, duplication, sediment, sprawl, and no-op lines.

**Completion**: Risks and mitigations are acknowledged.

## Phase 2: Define Skill Shape

Document the skill’s name, purpose, audience, tool constraints, and recurring concepts such as **gate check**, **progressive reveal**, and **completion criteria**. Add branches only when the skill has genuinely distinct workflows.

**Completion**: Scope, constraints, vocabulary, and applicable branches are defined.

## Phase 3: Design Structure

Plan the workflow, decide what belongs in `SKILL.md` versus external references, define conditional branches, and give each step an observable completion criterion. Keep detailed templates, definitions, examples, and checklists in external files.

**Completion**: Structure, disclosure, branches, and completion criteria are defined.

## Phase 4: Skill Drafting

### Step 4.1 — Write Frontmatter
Add valid frontmatter with the skill’s name, description, invocation strategy, argument hint, and allowed tools. See the [Reference](#reference) section for supporting material.

**Completion**: Frontmatter is written and validated.

### Step 4.2 — Draft Skill Content
Write the objective, core behavior, workflow steps, and references. State the purpose, principles, default mode, and completion outcome; give every workflow step a checkable `**Completion**:` condition; and link only the external material required.

**Completion**: The draft contains all planned sections, checkable step criteria, and valid required references.

## Phase 5: Implementation

### Step 5.1 — Gate Check
Before writing files:
1. State the file list.
2. Summarize each file’s scope.
3. Ask: “Confirm with `proceed` or request changes?”

Implement only after explicit `proceed`.

**Completion**: User responds with `proceed`.

### Step 5.2 — Create External Files
Write all planned reference files using progressive disclosure.

**Completion**: Planned external files are written.

### Step 5.3 — Write and Validate `SKILL.md`
Write the main file with all planned sections.

**Completion**: `SKILL.md` is written and validated.

### Step 5.4 — Run Validation
Confirm:
- [ ] Every step has observable completion criteria
- [ ] Hierarchy and progressive reveal are applied
- [ ] Leading words are defined and used
- [ ] Duplication, sediment, sprawl, and no-op lines are removed
- [ ] Framing is positive
- [ ] Invocation strategy and tool names are correct
- [ ] Gate checks precede edits
- [ ] Branches and risk flags are clear where needed
- [ ] References and formatting are valid

**Completion**: All applicable checks pass.

## Branches

### Planning Branch
**Path**: Phases 1 → 2 → 3 → 4 → stop
**Output**: Draft `SKILL.md` only
**Completion**: User reviews the draft.

### Implementation Branch
**Path**: Phases 1 → 2 → 3 → 4 → 5
**Output**: All planned skill files
**Completion**: Files are created and validation passes.

### Branch Selection
- “Planning mode” or “draft only” → Planning
- “Implement” or “write files” → Implementation
- Otherwise ask: “Planning draft or implementation?”

## Reference

This skill uses predictable process, information hierarchy, progressive reveal, completion criteria, leading words, positive framing, gate checks, and risk flags. Detailed guidance remains external:

- [GLOSSARY.md](references/GLOSSARY.md) — terminology, principles, and failure modes
- [TEMPLATES.md](references/TEMPLATES.md) — creation patterns
- [EXAMPLES.md](references/EXAMPLES.md) — examples and comparisons
- [CHECKLISTS.md](references/CHECKLISTS.md) — validation checklists
