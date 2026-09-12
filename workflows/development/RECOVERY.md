# RECOVERY.md — Shared Development Recovery Workflow

## Purpose

This document defines the shared LUMI recovery procedure for interrupted,
uncertain, or partially completed development work.

Recovery exists to preserve confirmed successful work and resume from the
earliest genuinely incomplete or unverified point.

The core principle is:

> Recover from verified current state. Do not restart the whole task merely
> because an execution path was interrupted.

Recovery should remain idempotent.

Temporary failure must not cause duplicate implementation, lost successful
work, duplicate sessions, repeated destructive retries, unnecessary validation,
or state drift caused by repeating already completed work.

Project-specific recovery procedures may extend this workflow.

---

## 1. Recovery Triggers

Use Recovery when development completion becomes uncertain because of events
such as:

- development-agent timeout;
- rate limit;
- missing or incomplete completion response;
- orchestration interruption;
- runtime or tool disconnection;
- temporary network failure;
- external-service failure;
- permission or environment failure;
- validation-tool failure;
- automation interruption;
- another transient infrastructure problem.

A tool failure does not automatically imply implementation failure.

Verified current project state and direct validation evidence take precedence
over assumptions based only on a missing or failed response.

---

## 2. Recovery Authority

LUMI remains the orchestration authority during Recovery.

Recovery must not be used to bypass the normal implementation boundary.

If project content must change:

- use the active project's configured implementation path;
- follow normal development delegation;
- do not silently switch to another modifying path merely because the normal
  path failed temporarily.

LUMI may perform permitted non-destructive inspection and orchestration.

Git mutation remains governed by the applicable Git workflow.

---

## 3. Preserve Current State First

When interruption occurs:

1. Do not immediately rerun the full task.
2. Do not assume failure merely because the final response is missing.
3. Do not recreate content that may already exist.
4. Inspect actual current state before deciding what remains.
5. Preserve unrelated user work.

Preserve confirmed state such as source changes, project files, generated
content, validated behavior, usable development sessions, runtime state, and
unrelated user changes.

Do not reset, normalize, discard, or rewrite current state merely to simplify
Recovery.

---

## 4. Recovery Inspection

Inspect only enough state to determine what actually happened.

### Project State

Inspect relevant current project content such as:

- modified source;
- created or changed files;
- generated content;
- configuration;
- build output;
- runtime artifacts;
- other task-specific state.

### Git State

Use read-only Git inspection when useful:

    git status
    git diff
    git log
    git show

Do not mutate Git merely as part of Recovery diagnosis.

### Development Session State

When persistent development sessions are available, determine:

- whether the existing session is usable;
- whether it targets the correct implementation workspace;
- whether implementation already occurred;
- whether only the final response was interrupted;
- whether relevant context remains available;
- whether continuing that session is safe.

### Runtime and Tool State

Inspect relevant current tool state when applicable, including build systems,
compilers, editors, test runners, pipelines, services, and other development
tooling.

Project-specific Recovery procedures may add further inspection requirements.

---

## 5. Classify Current Work State

After inspection, classify each affected portion of the task.

### Already Implemented

The required implementation exists.

Do not implement it again.

### Already Validated

The implementation exists and sufficient required validation already succeeded.

Do not repeat validation without a concrete reason.

### Implemented but Not Yet Validated

The implementation exists but required validation is incomplete or uncertain.

Resume from validation rather than implementation.

### Genuinely Incomplete or Incorrect

The implementation is missing, incomplete, or demonstrably incorrect.

Resume only the missing or incorrect portion through the normal implementation
workflow.

Recovery should locate the earliest incomplete or unverified step rather than
restart the original task.

---

## 6. Existing Session Reuse

Prefer an existing appropriate development session when:

- it remains usable;
- it targets the correct implementation workspace;
- it contains relevant context;
- continuing it does not introduce ambiguity or state risk.

Do not create a duplicate implementation session merely because a previous
request timed out or failed to return a final response.

Create a new session only when the previous session is unusable, incorrectly
scoped, materially stale, corrupted, or otherwise unsafe to continue.

This does not override Independent Review requirements.

Independent Review uses a fresh review session when required by the applicable
review workflow.

---

## 7. Bounded Retry

For a clearly transient failure, allow at most one automatic retry of the same
blocked path unless materially new evidence justifies another attempt.

Do not repeatedly retry when:

- the cause is already known;
- the relevant environment has not changed;
- the same path already failed again;
- another retry is unlikely to provide new information.

A retry tests whether the transient condition cleared.

It must not become a loop.

---

## 8. After the Retry Limit

When the same blocked path still fails after bounded retry, stop repeating it.

Preserve successful work.

### Alternative Supported Validation

Use another already-supported non-destructive validation path when it can
safely verify the same requirement.

Do not invent a risky workaround merely to avoid reporting a limitation.

### Validation Limitation

When implementation appears complete but validation cannot be established
because of infrastructure state, separate:

- implementation status;
- validation status;
- infrastructure blocker.

### PARTIAL

Use `PARTIAL` when meaningful work is complete but the requested final result
cannot yet be fully established.

### BLOCKED

Use `BLOCKED` when progress cannot continue safely without resolving an
external or infrastructure condition.

Do not classify correct implementation as failed merely because one validation
mechanism is unavailable.

---

## 9. Implementation Failure vs Infrastructure Failure

Always distinguish implementation failure from environment or infrastructure
failure.

Implementation failure includes cases such as incorrect code, missing required
content, implementation-caused compilation failure, incorrect runtime behavior,
or a directly demonstrated project defect.

Infrastructure failure includes cases such as timeout, rate limit, runtime
disconnect, licensing failure, network failure, unavailable external service,
permission errors, or a missing completion response despite persisted work.

Do not rewrite correct implementation merely because infrastructure failed.

Do not escalate model quality solely because an external tool is unavailable.

---

## 10. Recovery Model Routing

Recovery follows `ORCHESTRATION.md`.

### Clear and Localized Technical Failure

When the problem is clear and bounded:

- verify current state;
- resume normal development with `model.level.3` when implementation remains;
- retry only the missing step when appropriate.

### Complex or Structural Technical Failure

When the cause is uncertain, structurally significant, tightly coupled, or
high-risk:

- use `model.level.2` when heavier technical reasoning materially reduces risk;
- keep implementation within the normal development boundary.

### Replanning Required

When Recovery requires complex plan adjustment, dependency redistribution, or
work reallocation:

- use `model.level.1` for the orchestration decision;
- separate replanning from implementation.

Do not enter `model.level.0` merely because an existing plan changed. Re-enter
Initial Planning only when Recovery invalidates the existing strategy so
completely that the project or major work must genuinely be planned again from
zero.

### Independent Review

When independent verification is required:

- use the configured reviewer route;
- use a fresh review session;
- follow the shared Independent Review policy.

Formal Independent Review uses `model.level.1`; Final Independent Review uses
`model.level.0`. Ordinary read-only Recovery QA does not become Independent
Review automatically.

Do not escalate merely because a timeout, rate limit, disconnected tool, or
unavailable service occurred.

---

## 11. Git Recovery Discipline

Recovery must preserve pre-existing and unrelated user work.

Do not use destructive Git operations merely to simplify Recovery.

Do not automatically:

- reset hard;
- clean the repository;
- discard uncommitted work;
- rewrite history;
- revert unrelated changes;
- mutate branches solely to simplify state.

If Git mutation is genuinely required, use the applicable Git workflow after
implementation and validation state are understood.

---

## 12. Project-Specific Extensions

An active project may define its own Recovery extension.

Project-specific Recovery may add rules for:

- engines or editors;
- GUI lifecycle;
- build infrastructure;
- planning-state persistence;
- orchestration-state documents;
- deployment systems;
- external services;
- specialized runtime tooling.

Project-specific procedures extend this shared workflow.

They should avoid duplicating shared rules unless a project-specific difference
requires it.

---

## 13. Persistent Orchestration State

When the active project uses persistent orchestration state, reconcile that
state from verified actual project state before normal progression continues.

Do not update persistent orchestration state from assumptions.

Record as applicable:

- implementation completed;
- validation evidence;
- result status;
- validation limitations;
- PARTIAL or BLOCKED state;
- unfinished work;
- Rework Required;
- next task or Milestone.

The active project profile defines the actual orchestration-state mechanism.

---

## 14. Recovery Completion

Recovery is complete when LUMI has established with reasonable confidence:

- what implementation exists;
- what validation already succeeded;
- what validation remains;
- what work is genuinely incomplete;
- whether the current development session can continue;
- whether an infrastructure blocker remains;
- what the correct next step is.

If Recovery succeeds, preserve completed work and continue only from missing
implementation or validation.

If Recovery cannot safely complete, preserve successful work, stop repeated
attempts, record the remaining limitation or blocker, and report the minimum
next action.

---

## 15. Default Recovery Flow

    Task interruption or uncertain completion
    → Verify current project / session / runtime / Git state
    → Classify existing work
    → Reuse appropriate session when safe
    → Resume earliest incomplete or unverified step
    → At most one retry for clearly transient failure

If successful:

    Validate
    → Reconcile orchestration state when applicable
    → Continue

If still blocked:

    Stop repeated path
    → Use supported alternative validation when available
    → Otherwise record limitation / PARTIAL / BLOCKED
    → Preserve successful work
    → Report minimum next action

---

## 16. Recovery Invariants

Throughout Recovery:

- A missing final response does not prove implementation failure.
- Verified actual state takes precedence over response assumptions.
- Confirmed successful work is preserved.
- Completed implementation is not repeated without evidence that it is missing
  or unusable.
- Existing appropriate development sessions are reused when safe.
- Transient failures receive bounded retries rather than retry loops.
- Infrastructure failure is distinguished from implementation failure.
- Model escalation follows reasoning need rather than infrastructure failure.
- Git state is not destructively simplified.
- Normal implementation boundaries remain enforced.
- Persistent orchestration state is reconciled from verified state when
  applicable.
- Recovery should reduce uncertainty rather than create duplicate work.
