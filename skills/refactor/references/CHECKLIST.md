# Refactor validation checklist

Run the applicable checks before declaring a refactor verified.

## Preflight

- [ ] Target files, module, language, source level, and dependencies are known.
- [ ] `AGENTS.md` and available repository convention files were read.
- [ ] Missing expected convention files are reported rather than treated as
      present.
- [ ] Direct callers, neighboring tests, and relevant utilities were inspected.
- [ ] Focused IntelliJ MCP operations are available.
- [ ] The `subagent` tool and the repository's coder contract are available.
- [ ] If a required tool is unavailable, the skill reports a blocked state and
      asks how to proceed.

## Scope and proposal

- [ ] Code duplication and data-flow duplication are recorded separately.
- [ ] Behavior-sensitive areas were checked where relevant.
- [ ] Applicable tiers are named and ordered.
- [ ] The confirmed file and symbol scope is explicit.
- [ ] Out-of-scope findings are listed.
- [ ] The proposal includes diagnosis, planned files, behavior impact, test
      impact, and architecture blast radius when relevant.
- [ ] The user explicitly responded with `proceed` before editing.

## Implementation

- [ ] The coder receives an inline refactor step block with diagnosis,
      behavior, confirmed files, and test sequencing.
- [ ] The coder edits only the confirmed scope.
- [ ] The coder reports `Status`, test classes, created files, and updated files.
- [ ] No tests or assertions were weakened, deleted, disabled, or bypassed.
- [ ] No unrelated TODOs, commented-out code, or cleanup were introduced.
- [ ] Modern constructs are supported by the module and reduce complexity in
      context.

## Behavior preservation

- [ ] Public method signatures remain unchanged unless explicitly approved.
- [ ] Null, empty, invalid, and exception paths retain their intended contracts.
- [ ] Collection ordering, duplicate handling, and laziness remain correct.
- [ ] Transaction, authorization, validation, serialization, and persistence
      behavior remain correct where applicable.
- [ ] Concurrency and performance risks were checked where applicable.

## Verification

- [ ] `idea-get_run_configurations` identified the smallest relevant test run.
- [ ] `idea-execute_run_configuration` ran the focused tests through IntelliJ
      MCP.
- [ ] No shell test runner or project-wide build was used as a substitute.
- [ ] Failures were fixed within the confirmed scope, or the final status is
      `failed` with evidence.
- [ ] MCP unavailability is reported as `blocked`, not as a guessed pass.

## Final report

- [ ] Final status is `verified`, `blocked`, or `failed`.
- [ ] Changed files are listed.
- [ ] Preserved and intentionally changed behavior is stated.
- [ ] Test classes and focused results are stated.
- [ ] Remaining out-of-scope findings are stated.
