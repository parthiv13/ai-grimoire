# Skill Writing Principles

## Leading Words

**gate check**  
A confirmation step before proceeding. The agent states what it will do, shows scope, and asks for explicit "proceed". Replaces vague permissions like "should I continue?".

**progressive reveal**  
Information hierarchy principle: push reference material to external files, keep only core workflow in SKILL.md. Manages context load by loading material only when needed.

**completion criteria**  
Checkable condition that ends a step. Must be observable (agent can tell done from not-done) and exhaustive where it matters. Prevents premature completion.

**architect mindset**  
Holistic, risk-aware thinking before planning. Asks: "What's the real problem? What can go wrong? Are there simpler alternatives?"

**leading word**  
A compact concept already in the model's pretraining that anchors execution. Repeated throughout to create predictability with minimal tokens.

**information hierarchy**  
Ladder ranking material by how immediately the agent needs it:
1. In-skill steps (core workflow)
2. In-skill reference (definitions, rules)  
3. External reference (linked files)

**context load**  
Tokens consumed by always-loaded material (like model-invoked descriptions). Trade-off against cognitive load (user remembering skill names).

**cognitive load**  
Mental effort user spends remembering skill names and when to use them. User-invoked skills trade context load for cognitive load.

## Core Principles

### Predictability
A skill exists to wrangle determinism out of a stochastic system. The agent should take the same *process* every run, not necessarily produce the same output.

### Invocation Strategy
- **Model-invoked**: Description stays in context, agent can fire it autonomously. Use when skill must be reached by other skills.
- **User-invoked**: `disable-model-invocation: true`, zero context load. Use when skill is only fired by hand.

### Information Hierarchy Decisions
1. **In-skill step**: Ordered action with completion criteria
2. **In-skill reference**: Definition, rule, or fact consulted on demand  
3. **External reference**: Pushed to separate file, loaded via context pointer

### Progressive Disclosure Test
Inline what every branch needs; push behind a pointer what only some branches reach.

### When to Split Skills
- **By invocation**: When a distinct leading word should trigger it autonomously
- **By sequence**: When steps ahead tempt premature completion of current step

## Failure Modes

**premature completion**  
Ending a step before genuinely done. Defense: sharpen completion criteria first, then hide post-completion steps by splitting.

**duplication**  
Same meaning in more than one place. Costs maintenance and tokens.

**sediment**  
Stale layers that settle because adding feels safe and removing feels risky.

**sprawl**  
Skill too long even when every line is live. Cure: disclose reference behind pointers, split by branch.

**no-op**  
Line the model already obeys by default. Test: does it change behavior versus default?

**negation**  
Steering by prohibition backfires. Fix: state target behavior positively.

## Positive Framing Examples

| Negation (Avoid) | Positive Framing (Use) |
|-----------------|-----------------------|
| Never implement code | Default to planning mode |
| Do not proceed until | Proceed after confirmation |
| If user does not confirm | Implement only on explicit approval |
| Don't think of elephants | Focus on the target behavior |

## Completion Criteria Patterns

**Good (checkable)**:
- "User confirms with 'proceed'"
- "All risk items acknowledged"  
- "Frontmatter written and validated"
- "External files created"

**Bad (vague)**:
- "When shared understanding is reached"
- "After risks are mitigated"
- "When planning is complete"
- "If implementation might help"

## Tool Naming Convention
Use the platform's actual tool names. In Pi:
- `read` for reading files
- `write` for creating or overwriting files
- `edit` for precise text replacement
- `bash` for shell commands (search, list directories, etc.)

## User Interaction
Pi agents interact with users through natural conversation, not through a
dedicated tool. Confirmation gates ("proceed?", "apply?") and clarifications
are part of the dialogue and do not require listing a user-input tool in
`allowed-tools`.

## Reference
