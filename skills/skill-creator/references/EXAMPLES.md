# Example Skills Created with Skill Creator

## Example 1: Simple File Linter (User-Invoked)

### SKILL.md
```markdown
---
name: file-linter
description: "Check files for common issues and suggest fixes"
disable-model-invocation: true
argument-hint: "<file paths or patterns to check>"
allowed-tools: read, bash
---

<objective>
Check files for common issues like trailing whitespace, inconsistent formatting,
and missing documentation. Suggest fixes but don't apply them automatically.
</objective>

## Core Behavior

**Default to analysis mode** — report issues without editing files.  
**Gate check** before suggesting automatic fixes — confirm with explicit "apply".  
**Positive framing** — focus on improvements, not criticism.

### Workflow
1. **File scan** → Read files, identify issues
2. **Issue categorization** → Group by severity and type  
3. **Fix suggestions** → Provide specific improvement recommendations
4. **Apply fixes** → Only on explicit "apply" confirmation

**Completion**: Issue report generated, user decides on fixes.

## Step 1 — File Scan

Read specified files, identify common issues:
- Trailing whitespace
- Inconsistent indentation
- Missing documentation headers
- Overly long lines

**Completion**: All files scanned, issues logged.

## Step 2 — Issue Categorization  

Group issues by:
- **Critical**: Syntax errors, broken imports
- **Warning**: Style violations, formatting issues
- **Suggestion**: Documentation improvements, best practices

**Completion**: Issues categorized by severity and type.

## Step 3 — Fix Suggestions

For each issue category, provide specific fix recommendations:
- "Remove trailing whitespace on lines 15, 23, 42"
- "Add docstring to function `process_data()`"
- "Break line 89 (120 chars) into multiple lines"

**Completion**: All fix suggestions documented.

## Step 4 — Apply Fixes

### Gate Check
Before applying fixes:
1. State: "I will edit [file list] to fix [issue count] issues"
2. Show: "Changes: [specific fixes summary]"
3. Ask: "Confirm with 'apply' or review suggestions?"

Only proceed on explicit "apply".

**Completion**: User decides — either reviews suggestions or confirms "apply".

## Reference

See [GLOSSARY.md](GLOSSARY.md) for skill writing principles.
```

## Example 2: Code Review Assistant (Model-Invoked)

### SKILL.md
```markdown
---
name: code-reviewer
description: >
  Review code changes for quality and consistency. Use when user wants code review,
  mentions pull requests, or asks for quality checks.
argument-hint: "<code changes or files to review>"
allowed-tools: read, bash
---

<objective>
Provide thorough code reviews focusing on correctness, maintainability,
and adherence to team standards. Flag issues but don't block progress.
</objective>

## Core Behavior

**Default to review mode** — analyze code, provide feedback.  
**Progressive reveal** — detailed standards in external STANDARDS.md.  
**Positive framing** — constructive suggestions, not criticism.

### Workflow
1. **Change understanding** → Read diff, understand context
2. **Standard checks** → Apply team coding standards  
3. **Issue reporting** → Document findings with examples
4. **Improvement suggestions** → Provide actionable recommendations

**Completion**: Review report generated with prioritized suggestions.

## Step 1 — Change Understanding

Read the code changes, understand:
- What functionality is being added/changed
- Dependencies and side effects
- Test coverage implications

**Completion**: Changes understood, context documented.

## Step 2 — Standard Checks

Apply team standards from [STANDARDS.md](STANDARDS.md):
- Naming conventions
- Error handling patterns
- Documentation requirements
- Test structure

**Completion**: All standards checked against changes.

## Step 3 — Issue Reporting

Document findings:
- **Blockers**: Must-fix issues (security, correctness)
- **Important**: Should-fix issues (maintainability, performance)  
- **Nice-to-have**: Optional improvements (readability, consistency)

**Completion**: Issues documented with clear examples.

## Step 4 — Improvement Suggestions

Provide actionable suggestions:
- "Consider using `contextlib` for resource management"
- "Add type hints to public API functions"
- "Extract repeated logic into helper function"

**Completion**: All suggestions documented and prioritized.

## Reference

See external files:
- [STANDARDS.md](STANDARDS.md) — Team coding standards
- [GLOSSARY.md](GLOSSARY.md) — Review terminology
```

## Example 3: Before/After Comparison

### Before (Violating Principles)
```markdown
---
name: bad-skill
description: >
  This skill does things. Don't use it for production code.
  Never run it without checking first. Avoid using on important files.
allowed-tools: Read, Write, Edit
---

<objective>
Make changes to files carefully.
</objective>

## Steps

1. Read the file
2. Make changes if needed
3. Save the file

Don't proceed if you're not sure.
```

### After (Following Principles)
```markdown
---
name: careful-editor
description: "Edit files with safety checks and confirmation gates"
disable-model-invocation: true
argument-hint: "<files to edit and changes to make>"
allowed-tools: read, write, edit, bash
---

<objective>
Edit files safely by always showing changes before applying them
and requiring explicit confirmation for each edit.
</objective>

## Core Behavior

**Default to preview mode** — show changes without applying.  
**Gate check** before each edit — confirm with explicit "apply".  
**Positive framing** — focus on safe editing practices.

### Workflow
1. **Change preview** → Show what will be modified
2. **Impact assessment** → Identify potential side effects  
3. **Confirmation gate** → Require explicit "apply"
4. **Change application** → Apply edits and verify

**Completion**: Edits applied only after user confirmation.

## Step 1 — Change Preview

Read target files, show exactly what will change:
- Before/after comparison
- Line numbers affected
- Character-level changes

**Completion**: Changes clearly displayed for user review.

## Step 2 — Impact Assessment

Identify potential side effects:
- Breaking changes to other code
- Dependency impacts
- Test compatibility

**Completion**: All potential impacts documented.

## Step 3 — Confirmation Gate

Before applying:
1. State: "I will edit [file] at lines [X-Y]"
2. Show: "Changes: [specific modification]"
3. Ask: "Confirm with 'apply' or adjust changes?"

**Completion**: User responds with explicit "apply".

## Step 4 — Change Application

Apply the confirmed changes and verify:
- Changes match what was previewed
- No unintended modifications
- Files still valid/syntactically correct

**Completion**: Changes applied and verified.

## Reference

See [GLOSSARY.md](GLOSSARY.md) for skill writing principles.
```

## Key Improvements Shown

### From Bad to Good:
1. **Invocation**: `disable-model-invocation: true` for clarity
2. **Framing**: Positive ("safe editing") not negative ("don't use")
3. **Completion criteria**: Each step ends with checkable condition
4. **Gate checks**: Explicit confirmation before edits
5. **Progressive disclosure**: Reference to GLOSSARY.md
6. **Tool names**: Actual platform tools (`write` not "Write")
7. **Structure**: Clear workflow with numbered steps
8. **Vocabulary**: Leading words ("gate check", "preview mode")

## Teaching Through Examples

These examples demonstrate how the skill creator:
1. **Models good structure** in its own files
2. **Creates skills** that follow the same principles
3. **Shows before/after** to illustrate improvements
4. **References established skill writing principles** as foundation
5. **Uses progressive disclosure** appropriately

## How to Use These Examples

1. **Study the structure** of each example
2. **Compare before/after** to see principles applied
3. **Use as templates** for similar skills
4. **Validate against CHECKLISTS.md** to ensure compliance
5. **Iterate based on feedback** to improve further