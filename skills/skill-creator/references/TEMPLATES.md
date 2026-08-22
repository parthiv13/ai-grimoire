# Skill Creation Templates

## Frontmatter Templates

### User-Invoked Skill
```yaml
---
name: skill-name
description: "Brief human-readable description"
disable-model-invocation: true
argument-hint: "<what user should provide>"
allowed-tools: read, write, edit, bash
---
```

### Model-Invoked Skill  
```yaml
---
name: skill-name
description: >
  Skill purpose. Use when user wants to [action], mentions [concept],
  or needs [capability]. Other skills may reach this when [condition].
argument-hint: "<what user should provide>"
allowed-tools: read, write, edit, bash
---
```

## Objective Template
```markdown
<objective>
Clear purpose statement. What the skill does, what principles it follows,
and what completion looks like.
</objective>
```

## Core Behavior Template
```markdown
## Core Behavior

**Default mode** — what the skill does by default.  
**Key principle** — main guiding concept (e.g., "gate check before edits").  
**Workflow overview** — brief step sequence.  
**Completion** — final checkable completion criteria.
```

## Step Template
```markdown
### Step X.Y — Step Name

Step description and actions to take.

**Completion**: Checkable condition that ends this step.
```

## Gate Check Template
```markdown
### Gate Check (use this exact pattern)

Before proceeding:
1. State: "I will [action] affecting [files/scope]"
2. Show: "Changes: [specific summary]"  
3. Ask: "Confirm with 'proceed' or request changes?"

Only proceed on explicit "proceed".
```

## Risk Flag Template
```markdown
### Risk Flag

Before planning, flag potential issues:
- **Risk**: [description]
- **Impact**: [what could go wrong]
- **Mitigation**: [how skill should handle]

Ask: "How should the skill handle this — abort, proceed with warning, or skip?"
```

## Reference Section Template
```markdown
## Reference

This skill follows established skill writing principles:
- **Leading words**: [word1], [word2], [word3]
- **Information hierarchy**: Core in SKILL.md, reference in external files
- **Progressive disclosure**: [see TEMPLATES.md], [see GLOSSARY.md]

See external files for details:
- [GLOSSARY.md](GLOSSARY.md) — Definitions and principles
- [TEMPLATES.md](TEMPLATES.md) — Reusable patterns
- [EXAMPLES.md](EXAMPLES.md) — Usage examples
```

## Branch Definition Template
```markdown
## Branches

### Branch Name
Path: Step A → B → C  
Output: [what this branch produces]  
Completion: [checkable completion for this branch]

### Branch Selection
- User says "[trigger]" → [branch name]
- User says "[trigger]" → [branch name]  
- Default: Ask "[selection question]"
```

## Completion Criteria Examples

### Good Examples (Checkable)
```markdown
**Completion**: User responds with explicit "yes" or "proceed"
**Completion**: All 3 files written and validated
**Completion**: Risk log reviewed and acknowledged
**Completion**: Frontmatter matches template exactly
**Completion**: User confirms draft matches intent
```

### Bad Examples (Vague)
```markdown
❌ **Completion**: When understanding is reached
❌ **Completion**: After risks are mitigated  
❌ **Completion**: When planning is complete
❌ **Completion**: If implementation might help
```

## Positive Framing Templates

### Instead of Negation
```markdown
❌ "Never implement without permission"
✅ "Default to planning mode, implement only on explicit approval"

❌ "Do not proceed until confirmation"
✅ "Proceed after gate check confirmation"

❌ "If user doesn't confirm, stop"
✅ "Implement only when user confirms with 'proceed'"
```

## Leading Word Usage
```markdown
Use **leading words** consistently:
- "Perform a **gate check** before editing"
- "Apply **progressive reveal** to manage context load"
- "Define **completion criteria** for each step"
- "Adopt **architect mindset** for risk assessment"
```

## Progressive Disclosure Examples

### In SKILL.md (Core)
```markdown
## External Reference
For detailed templates: [TEMPLATES.md](TEMPLATES.md)
For definitions: [GLOSSARY.md](GLOSSARY.md)
For examples: [EXAMPLES.md](EXAMPLES.md)
```

### In External File
```markdown
# TEMPLATES.md

## Gate Check Template
[Detailed template...]

## Risk Flag Template  
[Detailed template...]
```

## Validation Questions
Use these when designing a skill:

1. "Is this skill model-invoked or user-invoked?"
2. "What are the leading words?"
3. "What goes in SKILL.md vs external files?"
4. "What's the completion criteria for each step?"
5. "Are there distinct branches?"
6. "Is there any duplication?"
7. "Is all framing positive?"

## Quick Start Template
```markdown
---
name: [skill-name]
description: "[brief description]"
disable-model-invocation: true
argument-hint: "<purpose>"
allowed-tools: read, write, edit, bash
---

<objective>
[Skill purpose following established principles]
</objective>

## Core Behavior

**Default mode** — [default behavior].  
**Gate check** — confirm before editing.  
**Completion** — [final checkable completion].

## Step 1 — Understand Purpose

[Actions...]

**Completion**: User confirms purpose statement.

## Step 2 — [Step Name]

[Actions...]

**Completion**: [Checkable criteria].

## Reference

See [GLOSSARY.md](GLOSSARY.md) for skill writing principles.
```