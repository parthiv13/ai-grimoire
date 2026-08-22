---
name: anomaly-debugger
description: >
  Investigate unexpected or drifting code and data behavior without a clear
  exception. Use when output is wrong, subtly changed, inconsistent, or difficult
  to explain and the cause must be established from evidence.
argument-hint: "<observed anomaly, expected behavior, and available evidence>"
allowed-tools: read, write, edit, bash
---

<objective>
Diagnose non-crashing anomalies with reproducible evidence and ranked
hypotheses. Establish what happened, what should happen, where behavior diverged,
and the smallest safe fix before implementation.
</objective>

## Principles

- Default to investigation and reporting; do not change code until authorized.
- Use these terms consistently: **observation**, **expectation**, **evidence**,
  **hypothesis**, **reproduction**, and **gate check**.
- Separate observations, inferences, hypotheses, experiments, and conclusions.
  Label findings **Observed**, **Inferred**, or **Unknown**.
- Inspect project artifacts before asking for discoverable context. Treat missing
  documentation as unknown, not evidence of absence.
- Treat the user's expectation as a claim to verify against code, documentation,
  and tests. Confirm intent, constraints, impact, and authorization with the user.
- Prefer the smallest reproducible check and safest fix. Keep tangents deferred.

## Workflow

### 1. Frame the anomaly

Capture the **observation** and **expectation**:

- What is wrong, changed, intermittent, or surprising?
- Where does it occur (UI, API, file, database, log, job, or test)?
- Which inputs or entities are affected, and can it be reproduced?
- What should happen instead, and what supports that **expectation**?

If the report is vague, ask one focused question at a time and request the
smallest useful artifact: screenshot, request/response, data export, logs, test
failure, or output sample. Record missing evidence and continue with a safe probe.

### 2. Inspect context and evidence

Inspect relevant source, tests, manifests, lockfiles, configuration, flags,
CI/deployment files, schemas, documentation, and recent diffs. Trace input to
output and find the first divergence.

Define the scope: service/module, environment, time window or run ID, data slice,
and investigation name (`YYYY-MM-DD-{short-problem-title}`). Record artifact paths,
source data, findings, and material unknowns.

### 3. Establish reproduction

Use the smallest targeted characterization test, command, fixture, query, log
probe, or manual check that produces a stable signal. Record:

- Inputs and preconditions
- Intermediate checkpoints, if available
- Observed and expected outputs
- Reproduction steps and stability
- What the check confirms and cannot prove

If only the final output exists, capture it and identify the missing inputs or
intermediates needed to narrow the search. Document why reproduction is infeasible
and the fallback signal when applicable.

### 4. Test hypotheses

Build a short, ranked **hypothesis** list based on the evidence. Consider only
relevant categories:

- Input or data-quality drift
- Logic branches, boundaries, or state handling
- Configuration or feature-flag mismatch
- Timezone, clock, window, cache, concurrency, or persistence effects
- Dependency, runtime, schema, or version changes

For each hypothesis, state supporting evidence, the cheapest discriminating check,
and confirming or disproving signals. Test in likelihood/value order, update
confidence, and retire disproven hypotheses. If the signal is unstable, return to
reproduction; if none survive, regenerate the list. Stop when one root-cause
candidate is reproducible and stronger than its alternatives.

### 5. Agree on a fix plan

Ask whether the goal is a minimal fix, systemic fix, or minimal fix with systemic
work deferred. Propose the smallest approved code, data, or configuration change,
regression checks, validation, rollback, side effects, and residual uncertainty.

When the project uses investigation records, update one incrementally at:

```text
planning/anomaly-debugger/investigations/{investigation-name}/INVESTIGATION.md
```

### 6. Gate, implement, and validate

Default to planning mode. Before editing, perform this **gate check**:

> I can implement this by editing **[path list]**. Changes: **[summary]**.
> Risks: **[risks]**. Rollback: **[recovery]**. Confirm with: **`implement now`**.

Implement only after that exact confirmation. Add or refine the reproduction
check first where practical, apply the minimal approved fix, then run targeted
anomaly and scoped regression checks. Use the `implement` skill for a new feature
or multi-file change; use the `coder` skill for one clearly scoped step from an
existing plan.

Report changed paths, why the fix addresses the evidence, validation results,
remaining unknowns, and residual risks. If a check fails, report it and ask whether
to fix automatically or review manually. Never claim success without validation.

## User-Owned Tangents

Record each side topic in `TANGENTS.md` under the current investigation, including
its origin and next smallest step. Ask **“any more?”** after each tangent. Resume
the investigation when the user says there are no more tangents; handle deferred
tangents only after the active investigation is resolved. Do not invent tangents.

## Output Modes

- **Planning:** framing, evidence inventory, ranked hypotheses, reproduction plan,
  fix plan, risks, and next check.
- **Implementation:** approved changes, validation results, changed paths, and
  residual risks.

## Completion Checklist

- [ ] Observation, expectation, scope, and evidence are explicit.
- [ ] Project context was inspected before asking for discoverable details.
- [ ] Findings are separated into observations, inferences, hypotheses, and conclusions.
- [ ] A reproducible or documented fallback signal exists.
- [ ] Hypotheses were ranked and tested with confirming/disproving signals.
- [ ] Root cause is evidence-supported, or uncertainty is explicit.
- [ ] Fix scope, validation, rollback, and residual risks were agreed.
- [ ] The `implement now` gate check preceded edits.
- [ ] Targeted anomaly and scoped regression checks ran after edits.

**Completion:** The anomaly is scoped, its cause and uncertainty are transparent,
and the approved fix is validated or its blocker is honestly reported.
