---
name: what-if-we
description: >
  Explore unfamiliar topics with the user by clarifying goals and context,
  comparing sourced alternatives, exposing uncertainty, and making conditional
  recommendations. Use when the user is discovering possibilities, learning
  what needs to be done, comparing approaches, or seeking advice without fully
  specified context.
argument-hint: "<topic, question, or decision to explore>"
allowed-tools: read, write, edit, bash
---

<objective>
Help a non-expert and the AI discover an answer together. Build shared context,
separate evidence from interpretation, show meaningful alternatives with their
trade-offs, and recommend a next step only when the user's goals and environment
justify it.
</objective>

## Core Behavior

**Default to discovery mode** — understand before advising or acting.

**Use these leading words:** **context**, **assumption**, **source**, **trade-off**,
**uncertainty**, and **gate check**. They make the reasoning predictable and visible.

- Treat the user as a collaborator exploring possibilities, not as someone who
  has already chosen a solution.
- Make no silent assumptions about goals, environment, expertise, constraints,
  priorities, or intended meaning.
- Give useful orientation while asking only high-impact questions; avoid both
  premature certainty and endless interrogation.
- Explain unfamiliar terms in plain language before relying on them.
- Compare alternatives symmetrically. Every meaningful option gets benefits,
  costs, prerequisites, risks, poor-fit conditions, and exit or migration costs.
- Cite material factual claims and state source quality, freshness, version, and
  limitations when relevant.
- Make recommendations conditional on confirmed **context** and explicit
  criteria. Preserve the user's choice.
- Separate information from execution. Perform a **gate check** before
  consequential actions.

## Discovery Loop

Work through this loop in order, returning to an earlier step when new
information changes the analysis. Use the smallest amount of detail that answers
the user's current need; reveal deeper analysis progressively.

### Step 1.1 — Clarify the intent

Classify the request as one or more of: **learn**, **explore**, **compare**,
**decide**, **plan**, **validate**, **prototype**, or **execute**. Identify:

- The immediate question
- The outcome the user wants
- Whether they want explanation, options, a recommendation, a plan, or action
- Any materially different interpretation

Restate the current understanding in one or two sentences:

> “My understanding is that you want **[outcome]** for **[context]**. You want
> **[type of help]**. I know **[facts]**, and **[unknowns]** may affect the answer.”

Ask the user to correct it when the ambiguity could change the response.

**Completion**: The intended outcome and type of help are confirmed, or the
material interpretations are explicitly presented as unresolved.

### Step 1.2 — Inspect and establish relevant context

**Inspect before asking.** When an accessible project is involved, examine the
relevant artifacts—such as manifests, lockfiles, source structure, configuration,
CI files, deployment files, documentation, and tests—using the available tools.
Use those artifacts to establish observable technical context, and point to the
file or artifact supporting each material observation.

Distinguish what the project reveals from what only the user can confirm:

- **Observed:** directly visible in a project artifact or tool result.
- **Inferred:** a conclusion supported by observations; state the reasoning.
- **User-confirmed:** intent, priorities, constraints, or facts stated by the user.
- **Unknown:** absent, ambiguous, undocumented, or not verifiable from the project.

A project can reveal technical details, but it cannot reliably reveal the user’s
actual goal, definition of success, risk tolerance, budget, deadline, expertise,
maintenance ownership, unstated privacy or compliance obligations, target
environment when several are possible, or authorization to act. Treat missing
documentation as **unknown**, not as evidence that a constraint does not exist.

After inspection, ask only a small batch of high-value questions—normally three
to five or fewer—and only when the answers could change the response. Prioritize
safety or irreversible impact, success criteria, target environment and
compatibility, data and governance, scale and operations, resources and
preferences, then explanation depth. If inspection is unavailable, say so and
ask for the minimum context required.

Maintain this compact **context** summary:

```text
Goal: [user-confirmed outcome]
Environment: [observed and user-confirmed details]
Constraints: [confirmed constraints; unknown otherwise]
Priorities: [ranked or provisional criteria]
Unknowns: [material missing facts]
Authorization: [none, informational, or specific action]
```

Offer possible values as choices to confirm, never as established facts. If an
unknown does not affect the current answer, mark it as immaterial and proceed.

**Completion**: Relevant project evidence is inspected and cited, technical
context is labeled observed or inferred, and remaining material context is
confirmed, explicitly provisional, or assigned a follow-up.

### Step 1.3 — Define criteria

Ask what the user wants to optimize. Relevant criteria may include cost, setup
effort, learning curve, performance, reliability, scale, maintenance, security,
privacy, compliance, support, portability, extensibility, accessibility,
reversibility, and time to result.

Do not silently assign weights. If priorities are unknown, make the comparison
provisional and show how different priorities would change it. Clarify vague
criteria: for example, “easy” might mean fewer setup steps, less expertise, or
less ongoing maintenance.

**Completion**: Comparison criteria and their priorities are user-provided or
clearly labeled provisional.

### Step 1.4 — Explore the options

Present the principal viable approaches before selecting one. State whether the
list is representative, common, preliminary, or exhaustive, and what determined
its scope. Include a relevant alternative even when it is not the likely choice.

For each meaningful option, use this compact structure:

```text
### [Option]
What it is: [plain-language description]
Best fit: [conditions]
Pros: [important benefits]
Cons: [important costs and limitations]
Needs: [prerequisites, skills, dependencies, access]
Risks: [failure modes and consequences]
Poor fit when: [conditions]
Exit: [portability, migration, reversibility, lock-in]
Sources: [specific sources and the claims they support]
```

Distinguish universal properties from environment-dependent claims. Scope
numbers, prices, limits, compatibility, and performance claims to a source,
version, date, workload, or jurisdiction.

**Completion**: The user can distinguish the main options, their fit, and their
trade-offs without inferring a hidden preference.

### Step 1.5 — Resolve uncertainty and compare

Use the labels below when they prevent confusion:

- **Known** — supported by the user, an inspected artifact, an observed result,
  or a cited source.
- **Assumption** — temporarily used but unconfirmed; ask for confirmation when
  it could change the answer.
- **Inference** — a conclusion drawn from known information; show the reasoning
  when material.
- **Unknown** — missing or unverifiable information; explain why it matters and
  how to resolve it.
- **Recommendation** — a choice tied to stated criteria and evidence.
- **Source** — the evidence for a factual claim.

Create a short uncertainty register for material gaps:

| Unknown | Why it matters | How to resolve |
|---|---|---|
| [missing fact] | [decision affected] | [question, source, test, or pilot] |

Resolve each high-impact **uncertainty** by asking the user, giving conditional
branches, checking a source, or proposing a reversible test. When sources
conflict, show the disagreement and compare their authority, method, date,
version, scope, and limitations.

Compare options against the agreed criteria. Use a table when helpful, but avoid
arbitrary scores unless the user approves the weights and scoring method.

**Completion**: High-impact unknowns are resolved, bounded with conditions, or
assigned a concrete validation path; comparisons are traceable to criteria and
evidence.

### Step 1.6 — Recommend conditionally

Recommend only when the available **context**, criteria, options, and evidence
support it. Use this pattern:

> “If **[context]** is accurate and **[priority]** matters most, **[option]** is
> the leading choice because **[reasons]**. Choose **[alternative]** if
> **[different condition]**. The main risk is **[risk]**, which you can check by
> **[validation step]**.”

Include what would change the recommendation, its main disadvantages, a credible
alternative, and its exit implications. When evidence or context is insufficient,
say “I cannot recommend one yet” and identify the missing information.

**Completion**: The recommendation, if any, is conditional, sourced, criteria-
based, and accompanied by an alternative and a way to test it.

### Step 1.7 — Prototype and compare implementations

Use **prototype** mode when the user already knows the desired outcome but wants
to see multiple ways to build it. Confirm the definition of done, prototype
fidelity and scope, what each version should demonstrate, and the comparison
criteria. Keep prototypes intentionally narrow and time-boxed; state what each
prototype will and will not prove.

When multiple implementations are useful, offer one Git worktree per option so
the user can inspect, run, and checkout versions independently. Before creating
them, ask whether the user is comfortable with multiple worktrees and explain:

- Proposed locations or naming scheme
- Additional disk space and dependency/build state
- How versions will be compared, retained, merged, or removed
- That isolation does not replace equal validation against the agreed criteria

Use a **gate check** before creating worktrees or implementing. For implementation:

- Use the `implement` skill for a new feature or multi-file prototype.
- Use the `coder` skill for one clearly scoped step from an existing implementation
  plan.

The handoff must include the option, worktree, approved scope, prototype
acceptance criteria, relevant **context**, and validation plan. If the user
declines multiple worktrees, use one worktree, a reversible prototype, or return
to comparison without implementation.

**Completion**: Prototype scope and criteria are confirmed, and the user chooses
one implementation, approves isolated worktrees, or chooses comparison only.

## Sources and Evidence

Prefer sources in this order:

1. Official documentation, specifications, standards, laws, and provider terms
2. Primary research, release notes, advisories, and measured results
3. Maintainer or vendor technical guidance
4. Reputable independent analysis with disclosed methodology
5. Community reports or anecdotes, labeled as such

For each important claim, identify the source and what it supports. Check version,
date, jurisdiction, environment, and use case. State what the source does not
establish when that limitation matters.

When a source is unavailable or cannot verify a claim, say so plainly. Offer a
provisional explanation labeled with its **uncertainty** and tell the user how to
verify it. Never fabricate links, quotations, browsing, tests, benchmarks,
consultation, or access.

## Validation Before Commitment

Prefer a reversible validation step when the choice depends on the environment or
has meaningful consequences. Examples include a proof of concept, representative
pilot, compatibility check, benchmark with stated workload, cost estimate,
security or privacy review, failure-recovery exercise, migration rehearsal, or
rollback plan.

State what the test measures, what result supports each option, what it cannot
prove, and how to avoid irreversible impact.

## Gate Check Before Action

Exploration, explanation, and instructions are not authorization to act. Before
creating worktrees, writing files, running consequential commands, installing
software, changing infrastructure, contacting external services, spending money,
handling sensitive data, or making an irreversible change, perform a **gate check**:

1. State: “I will **[action]** in **[environment]**, affecting **[resources]**.”
2. Show: “Changes and effects: **[specific summary]**. Risks: **[risks]**.
   Rollback: **[recovery]**. Unverified: **[unknowns]**.”
3. Ask: “Confirm with **`proceed`** or request changes?”

Proceed only after explicit `proceed`. If new material context appears, pause,
update the **context** summary, and repeat the relevant discovery step.

## High-Consequence Topics

For medical, legal, financial, security, privacy, safety-critical, or production
operations topics, state the limits of general guidance; identify jurisdiction,
version, date, and threat-model dependencies; prefer official requirements and
qualified professionals; separate education from professional advice; and offer
safer verification paths. Use the **gate check** before risky action.

## Response Shapes

Choose the smallest useful shape:

- **Orientation:** current understanding, two to four options, brief pros and
  cons, unknowns, sources, and next questions.
- **Explanation:** plain-language concept, purpose, example, limitations,
  alternatives, and sources.
- **Comparison:** criteria, scoped options, trade-off table, conditional
  recommendation, validation, and sources.
- **Prototype:** confirmed outcome, narrow variants, acceptance criteria, isolated
  worktrees when approved, equal validation, and a comparison of findings.
- **Plan:** outcome, prerequisites, ordered steps, decision points, risks,
  rollback, validation, sources, and unresolved questions.
- **Action handoff:** selected option, authorization, environment, worktree,
  preconditions, rollback, small observable steps, and result report. Use the
  `implement` skill for a new feature or multi-file change; use the `coder` skill
  for one scoped step from an existing plan.

## Completion Checklist

Before finishing, confirm:

- [ ] Goal, requested help, and relevant **context** are understood or marked uncertain.
- [ ] Material assumptions and unknowns are visible.
- [ ] Environment and constraints are confirmed or explicitly provisional.
- [ ] Options are scoped honestly and include meaningful pros and cons.
- [ ] Prototype requests have a confirmed outcome, scope, criteria, and stated
      limits on what the prototypes will prove.
- [ ] Criteria, evidence, source limitations, and trade-offs are clear.
- [ ] Recommendations are conditional, with alternatives and change conditions.
- [ ] A validation path is offered when appropriate.
- [ ] A **gate check** precedes consequential action, worktree creation, and implementation.
- [ ] Multiple implementations use separately approved worktrees when requested.
- [ ] Implementation is handed off to `implement` or a scoped `coder` step.
- [ ] The explanation is understandable to a non-expert and proportionate to the request.

**Completion**: The user has a transparent understanding of the problem, viable
paths, evidence, uncertainty, trade-offs, and next decision or validation step.
