# Skill Validation Checklists

## Complete Validation Checklist
Run this on any created skill before considering it complete.

### ✅ Frontmatter Validation
- [ ] `name` is kebab-case and descriptive
- [ ] `description` is brief (user-invoked) or has triggers (model-invoked)
- [ ] `disable-model-invocation` is `true` or omitted appropriately
- [ ] `argument-hint` guides user input
- [ ] `allowed-tools` uses actual platform tool names

### ✅ Structure Validation  
- [ ] Has `<objective>` section with clear purpose
- [ ] Core behavior section defines default mode
- [ ] Steps are numbered (X.Y format)
- [ ] Each step has **Completion**: with checkable criteria
- [ ] Reference section points to external files (if any)
- [ ] Uses progressive disclosure appropriately

### ✅ Skill Writing Principles Validation
- [ ] Follows **information hierarchy** (core vs reference)
- [ ] Has **checkable completion criteria** for all steps
- [ ] Uses **progressive reveal** (external files for reference)
- [ ] **Positive framing** throughout (not negation)
- [ ] No **duplication** of concepts
- [ ] Proper **invocation strategy** (model vs user)

### ✅ Content Quality Validation
- [ ] No **no-op** lines (already default behavior)
- [ ] No **sediment** (stale/unnecessary content)
- [ ] No **sprawl** (appropriately concise)
- [ ] **Gate checks** before file edits
- [ ] **Risk flags** where appropriate
- [ ] Clear **branch definitions** (if multiple paths)

### ✅ Technical Validation
- [ ] Tool names match the platform's actual tools
- [ ] File paths are correct
- [ ] Links to external files work
- [ ] Markdown formatting is consistent
- [ ] No broken references

## Phase Completion Checklists

### Phase 1: Architect Mindset Complete When
- [ ] Skill purpose restated and confirmed
- [ ] Invocation strategy decided (model/user)
- [ ] Risks flagged and mitigation agreed
- [ ] User confirms "proceed to Phase 2"

### Phase 2: Intent Capture Complete When  
- [ ] Skill name (kebab-case) chosen
- [ ] Purpose statement written
- [ ] Target audience identified
- [ ] Tool constraints documented
- [ ] Branches defined or "single path" confirmed

### Phase 3: Structure Design Complete When
- [ ] Information hierarchy planned (SKILL.md vs external)
- [ ] Progressive disclosure plan created
- [ ] Completion criteria drafted for all planned steps
- [ ] User confirms structure plan

### Phase 4: Skill Drafting Complete When
- [ ] Frontmatter written and validated
- [ ] Objective section matches purpose
- [ ] Core behavior section written
- [ ] All steps written with completion criteria
- [ ] Reference links added
- [ ] Draft reviewed by user

### Phase 5: Implementation Complete When
- [ ] Gate check passed (user said "proceed")
- [ ] All external files written (GLOSSARY.md, TEMPLATES.md, etc.)
- [ ] SKILL.md written and validated
- [ ] All validation checks pass
- [ ] User confirms skill is complete

## Quick Validation (30-second check)
- [ ] Each step ends with **Completion**: [checkable criteria]
- [ ] Uses **leading words** from GLOSSARY.md
- [ ] No **duplication** of concepts
- [ ] **Positive framing** (not "don't" or "never")
- [ ] Proper **invocation strategy**

## Common Issues to Check For

### ❌ Fix These Immediately
- Steps without completion criteria
- Negation framing ("don't", "never", "avoid")
- Duplicated concepts in multiple places
- Vague completion criteria ("when ready", "if appropriate")
- Incorrect tool names (e.g., "Read" instead of `read`)

### ⚠️ Review These Carefully
- Overly long SKILL.md (consider progressive disclosure)
- Mixed invocation strategy (confusing model/user)
- Unclear branch definitions
- Missing risk flags for complex operations
- No gate checks before file edits

### ✅ Good Signs
- Clear, checkable completion criteria
- Consistent leading word usage
- Appropriate progressive disclosure
- Positive framing throughout
- Single source of truth for each concept

## Validation Questions to Ask

### During Design
1. "Can the agent tell when this step is done?" (completion criteria)
2. "Is this concept defined in one place only?" (no duplication)
3. "Does this need to be in SKILL.md or external file?" (hierarchy)
4. "Is this stated positively?" (framing)
5. "What leading word represents this concept?" (vocabulary)

### During Implementation
1. "Have I performed a gate check before editing?"
2. "Are all tool names correct?"
3. "Do all links to external files work?"
4. "Does the skill pass all validation checks?"
5. "Does this structure follow established skill writing principles?"

## Reference Validation
Check that all external references:
- [ ] Exist (files created)
- [ ] Are linked correctly
- [ ] Contain appropriate content
- [ ] Follow the same principles as SKILL.md
- [ ] Are actually needed (not sediment)

## Final Sign-off Checklist
Before declaring a skill complete:

- [ ] All validation checks pass
- [ ] User has reviewed final draft
- [ ] All external files are written
- [ ] Skill follows established skill writing principles
- [ ] Skill creator can run it successfully
- [ ] Documentation complete (if needed)

## Continuous Improvement
Even after validation, skills can be improved:
- Refine leading words
- Improve completion criteria clarity
- Enhance progressive disclosure
- Remove any remaining sediment
- Optimize for context/cognitive load