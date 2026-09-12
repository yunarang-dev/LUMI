# INDEPENDENT_REVIEW.md — Shared Independent Review Workflow

## Purpose

This document defines the shared LUMI Independent Review procedure for managed
development projects.

Independent Review is a LUMI-managed verification workflow intended to compare,
when applicable:

    Authoritative Planning
    ↔ Persistent Orchestration State
    ↔ Actual Implementation

The Reviewer is not a second orchestrator and is not an implementation agent.

The Reviewer independently gathers evidence, evaluates the configured Review
scope, reports findings, and stops.

LUMI remains responsible for:

- deciding when a Review is required;
- resolving the active project;
- creating and briefing the Reviewer;
- receiving and assessing findings;
- reconciling persistent orchestration state when configured;
- deciding whether Rework is required;
- deciding whether dependent work may proceed.

---

## 1. When to Run Independent Review

Independent Review runs when:

- a configured Review Checkpoint is reached;
- the active project explicitly requires a Review;
- Final Independent Review is required before project completion;
- or an unplanned Review is materially justified.

An unplanned Review may be appropriate after:

- major Rework;
- significant architecture change;
- broad cross-system modification;
- high-risk integration;
- changes that materially invalidate previous Review evidence;
- another situation where independent verification materially reduces risk.

Avoid unnecessary consecutive Reviews.

Prefer one Review covering several related scopes when risk allows rather than
performing a Review after every small task.

A project's profile determines whether Final Independent Review is mandatory.

---

## 2. Fresh Reviewer Requirement

Every Independent Review must use a fresh review session.

The shared reviewer role is:

    reviewer-lumi

The persistent reviewer profile defines the Reviewer role and runtime binding.

It does not remove the requirement for a fresh session for each Independent
Review.

Do not reuse:

- the active implementation session;
- a development worker session;
- an implementation recovery session;
- a planning-analysis session;
- another Reviewer session from a previous Independent Review.

The Reviewer must not inherit implementation-session reasoning history as its
verification basis.

Previous implementation reports, completion records, validation notes, or
orchestration-state claims may be provided as reference material.

They must not be treated as proof that a requirement is satisfied.

The Reviewer must independently inspect verified current project state.

Final Independent Review also requires a fresh review session.

---

## 3. Reviewer Route

Independent Review uses the shared reviewer route defined in
`ORCHESTRATION.md`.

Logical routes:

- Reviewer role: `reviewer-lumi`
- Independent Review: `model.level.1`
- Final Independent Review: `model.level.0`

The persistent reviewer role is not tied to one physical model. Select the
logical route from the review type, then let the runtime adapter resolve its
current binding.

The reviewer route applies only to Independent Review.

After Review completes, implementation returns to the normal development
routing policy.

If an actual runtime or model transition is observable, follow the transition
notification policy in `ORCHESTRATION.md`.

---

## 4. Active Project Context

Before Review begins, LUMI must resolve the active project.

The Reviewer should receive the project-specific context necessary to verify the
requested scope.

This may include:

- canonical Project ID;
- project profile path;
- implementation workspace;
- authoritative Planning source;
- persistent orchestration-state source;
- configured procedure files;
- required validation interfaces;
- engine or runtime integration;
- Review Checkpoint;
- Milestones or requirements in scope;
- explicitly excluded scope.

Do not make the Reviewer infer the active project from memory, repository names,
or previous sessions.

Do not provide unrelated private project context merely because it is available.

---

## 5. Source-of-Truth Relationship

When the active project defines authoritative Planning, that source remains the
design Source of Truth.

Actual implementation must be evaluated against the authoritative requirements.

When the active project also defines persistent orchestration state, that state
is derived coordination data.

It may contain information such as:

- current Milestone;
- current task;
- completed work;
- validation state;
- dependencies;
- blockers;
- Follow-up;
- Rework Required;
- Technical Debt;
- Review Checkpoints;
- next work.

Persistent orchestration state does not replace authoritative Planning.

The Reviewer must verify orchestration-state completion claims against actual
current implementation and evidence.

A requirement must not be considered satisfied solely because:

- orchestration state says it is complete;
- a previous agent says it is complete;
- a previous report says it passed;
- a previous validation attempt reported success.

Where direct verification is reasonably available, direct evidence should be
preferred.

---

## 6. Read-Only Review Boundary

Independent Review is a read-only verification workflow.

The Reviewer must not modify the active project during Review.

This includes, when applicable:

- authoritative Planning;
- persistent orchestration state;
- source code;
- project files;
- configuration;
- assets;
- serialized state;
- build configuration;
- runtime configuration;
- package configuration;
- repository state;
- Git history;
- branches;
- tags;
- remotes;
- deployment state;
- external project state.

The Reviewer may use read-only Git inspection when useful.

Examples:

    git status
    git diff
    git log
    git show

The Reviewer may perform non-persistent runtime actions required for
verification when explicitly permitted by project policy.

Examples may include:

- entering and exiting a runtime test mode;
- exercising configured user input;
- reading runtime values;
- inspecting current object or component state;
- reading logs;
- reproducing behavior;
- executing non-mutating validation.

If a Review discovers an issue, report the issue and supporting evidence.

Do not repair it during the Review.

Independent Review must never silently become an implementation task.

If Rework is required:

1. finish the Review;
2. reconcile findings;
3. create separate Rework;
4. route implementation through the normal development workflow.

---

## 7. Runtime and Tool Access During Review

The Reviewer may use configured runtime, engine, editor, test, build, or
inspection tooling when needed for non-destructive verification.

The active project profile determines which integrations are available.

Permitted verification may include:

- inspecting current runtime state;
- reproducing configured behavior;
- exercising intended controls or interfaces;
- reading logs and diagnostics;
- inspecting dependency relationships;
- checking compilation or build state;
- verifying serialized references;
- reproducing completion conditions;
- regression verification.

The Reviewer must not:

- add temporary implementation merely to make verification easier;
- change project configuration;
- save unintended project changes;
- modify serialized content;
- repair discovered defects;
- alter runtime configuration persistently;
- restart or terminate protected processes when project policy forbids it.

If a requirement cannot be verified without modifying the project, report the
validation limitation instead of changing the project.

When practical, Review should finish with no Reviewer-created persistent state.

---

## 8. Independent Verification Standard

The Reviewer should verify from direct evidence wherever reasonably possible.

Default sequence:

1. Read relevant authoritative requirements.
2. Read relevant orchestration-state claims when configured.
3. Inspect actual current implementation.
4. Inspect applicable project state, configuration, assets, or dependencies.
5. Reproduce applicable requirements through the configured runtime or test
   interface when practical.
6. Re-check important existing behavior in scope for Regression.
7. Compare:
   - authoritative requirements;
   - orchestration-state claims;
   - actual implementation.
8. Identify:
   - omissions;
   - mismatches;
   - unsupported completion claims;
   - stale orchestration state;
   - validation gaps;
   - regressions;
   - technical risk.

A previous agent's statement that something passed is supporting context only.

Review depth must remain proportional to scope and risk.

Do not expand a scoped Review into unrelated infrastructure investigation merely
because additional systems are available to inspect.

---

## 9. Interactive and Player-Path Validation

When the active project includes interactive, user-facing, player-facing,
navigation, encounter, level-flow, or integrated runtime behavior, technical
existence alone may be insufficient evidence of completion.

If the project profile or Review scope enables Player-Path Validation, the
Reviewer must independently verify the intended usable path.

### Reachability

Verify that the user or player can reach required:

- areas;
- objects;
- interactions;
- states;
- encounters;
- screens;
- features;
- progression points

from the intended entry point.

A feature is not considered reachable merely because its implementation exists.

### Controllability

Verify that intended behavior can be activated through the intended user,
player, or interaction interface.

Debug methods, direct internal calls, probes, or test-only triggers do not
substitute for intended interaction unless the project explicitly defines them
as the validation interface.

### Observability

Verify that the user or player can perceive and understand the expected result
through intended presentation or an explicitly approved development interface.

Internal state visible only through logs, inspectors, probes, or tests must be
identified as developer-only evidence.

### Continuity

Verify that required interactions, transitions, dependencies, routes, states,
and progression form a continuous usable flow.

Check for problems such as:

- unintended dead ends;
- unreachable content;
- missing transitions;
- disconnected interactions;
- invalid entry state;
- inaccessible dependencies;
- progression that cannot occur through intended use.

### Completion

When the reviewed scope defines a complete flow, reproduce it from its intended
start condition through its expected result.

Do not begin from a convenient intermediate state unless that intermediate state
is itself the intended validation entry point.

### Validation Method

When practical:

1. Start from the intended user-facing entry state.
2. Use intended controls or interaction mechanisms.
3. Follow the intended route or flow.
4. Reach required content without debug-only shortcuts.
5. Exercise the reviewed behavior.
6. Observe the expected result.
7. Reach the intended completion or transition state.

Automated tests and internal state inspection remain valuable supporting
evidence.

They do not replace Player-Path Validation when the requirement is explicitly
interactive or player-facing.

If the Reviewer cannot complete the intended path, report where and why it
failed or could not be verified.

Clearly distinguish:

- Technical Validation;
- Player-Path Validation;
- Human Validation.

Human Validation must not be reported as completed unless an actual designated
human tester performed it.

---

## 10. Review Scope Discipline

The configured Review Checkpoint or explicit Review request defines normal
scope.

The Reviewer should:

- verify requirements and systems explicitly in scope;
- include important regressions that directly affect reviewed scope;
- inspect adjacent dependencies when needed to establish whether a finding is
  real.

Do not turn a scoped Review into an unrestricted repository-wide audit unless:

- the Review is explicitly full-project;
- evidence strongly suggests a broader systemic problem;
- or LUMI explicitly expands the scope.

If scope expands materially, state why.

---

## 11. Finding Classification

Every meaningful finding should use one or more of the following classifications.

### Passed

Use when:

- applicable requirements and implementation are consistent;
- required verification succeeded;
- no additional work is required for that reviewed item.

### Follow-up

Use when:

- the finding does not invalidate current completion;
- dependent work may still proceed;
- the item should be handled later.

Examples may include:

- deferred polish;
- later-stage configuration;
- non-blocking validation improvement.

### Rework Required

Use when:

- implementation is incomplete;
- implementation is incorrect;
- implementation conflicts with authoritative requirements;
- implementation changes are required.

Rework Required does not automatically imply that all later work is blocked.

Dependency and regression impact must be assessed separately.

### Blocker

Use when the issue must be resolved before dependent work can safely proceed.

Examples may include:

- missing critical prerequisite;
- major functional failure;
- broken dependency;
- invalid progression path;
- defect that invalidates the next dependent step.

### Technical Debt

Use when:

- current functionality remains valid;
- current completion is not invalidated;
- maintainability, compatibility, structure, testing quality, or future risk
  should be improved.

A finding may additionally include severity, confidence, or risk.

Classification must remain explicit.

For interactive or player-facing behavior:

- inaccessible required behavior should normally be Rework Required;
- it should become a Blocker when dependent progression requires it;
- behavior that works only through tests, probes, direct internal invocation,
  or unintended debug shortcuts must not be classified as fully Passed when
  intended use is user-facing.

---

## 12. Required Review Report

The Reviewer should return a structured report containing at least the
applicable sections below.

### Review Scope

Include:

- Review Checkpoint;
- requirements or Milestones reviewed;
- systems reviewed;
- intentionally excluded scope.

### Environment

Include relevant environment evidence such as:

- implementation workspace;
- runtime or editor version;
- active runtime state;
- build or compilation state;
- relevant logs or diagnostics;
- Git Working Tree state when useful.

### Requirements Compliance

Report:

- whether authoritative requirements match actual implementation;
- whether orchestration-state completion claims are accurate;
- stale or unsupported orchestration state.

### Requirement Findings

For each important reviewed item include:

- requirement;
- evidence;
- result;
- classification.

### Direct Validation Evidence

Include concrete evidence when available.

Examples:

- observed runtime behavior;
- state transitions;
- values;
- input or interaction results;
- dependency relationships;
- build/test outcomes;
- other directly observed evidence.

Do not invent evidence.

### Player-Path Validation

When applicable report:

- intended entry point;
- tested path;
- Reachability;
- Controllability;
- Observability;
- Continuity;
- Completion;
- debug-only or test-only behavior;
- portions that could not be reproduced through intended use.

Clearly distinguish:

- Technical Validation;
- Player-Path Validation;
- Human Validation.

### Regression Results

Report important existing behavior rechecked and whether it passed.

### Findings Summary

Summarize:

- Passed;
- Follow-up;
- Rework Required;
- Blocker;
- Technical Debt.

### Unverified Items

For anything not verified:

- identify the item;
- explain why it could not be verified;
- state whether the limitation affects completion confidence.

### Progression Assessment

State whether, based on evidence:

- dependent work appears safe to begin;
- Rework should occur first;
- a Blocker prevents progression.

This is a Reviewer recommendation.

LUMI makes the final progression decision.

### Mutation Declaration

The Reviewer must explicitly state whether any project, repository, planning,
or external state was modified during Review.

The expected result is:

    No persistent mutation performed.

After reporting, stop.

Do not continue into Rework, implementation, Git mutation, or orchestration-state
editing.

---

## 13. LUMI Review Reconciliation

After Review completes, LUMI must independently assess the report.

Do not blindly copy or automatically accept Reviewer conclusions.

Reconcile findings against:

- authoritative requirements;
- configured orchestration state;
- verified actual implementation;
- dependency structure;
- planned work;
- applicable project policy.

LUMI decides the final effect of each finding.

When persistent orchestration state is configured, update it as appropriate
after reconciliation.

Record applicable information such as:

- Review date;
- Review scope;
- verified completion conditions;
- validation evidence summary;
- Follow-up;
- Rework Required;
- Blockers;
- Technical Debt;
- validation limitations;
- progression eligibility.

Do not ask the Reviewer to update orchestration state merely because that update
writes a file.

Do not modify authoritative Planning merely because Review found an
implementation mismatch.

Requirements remain authoritative until changed through the proper planning or
user-decision workflow.

---

## 14. Progression Gate

After Review reconciliation:

### Blocker

If a Blocker affects dependent work:

- do not advance dependent work until the Blocker is resolved.

### Follow-up or Technical Debt Only

LUMI may allow progression while keeping these items tracked.

### Rework Required

Assess:

- dependency impact;
- regression risk;
- whether later work depends on defective implementation;
- whether delaying Rework increases cost or risk.

Then decide whether:

- Rework must happen immediately;
- or progression may continue while Rework remains tracked.

Do not mark Review reconciliation complete until configured orchestration state
accurately represents verified current state.

---

## 15. Rework After Review

When Review findings require implementation changes:

1. Record or reconcile the finding.
2. Finish Review reconciliation.
3. Create a separate Rework task.
4. Route implementation through the normal development workflow.
5. Validate the Rework.
6. Reconcile verified state.

Do not use the Reviewer session as an implementation session.

If Rework materially invalidates prior Review evidence, schedule another
Independent Review when justified.

---

## 16. Final Independent Review

When the active project requires Final Independent Review, it must occur before
the project is considered complete.

Final Independent Review must:

1. Create a fresh `reviewer-lumi` review session.
2. Use `model.level.0` through the configured runtime adapter.
3. Apply the same read-only and independence rules.
4. Review the full configured final scope against actual implementation.
5. Verify major cross-system Regression.
6. Perform applicable Player-Path Validation.
7. Check unresolved:
   - Follow-up;
   - Rework Required;
   - Blocker;
   - Technical Debt;
   - validation limitations;
   - Human Validation state where applicable.
8. Verify configured orchestration-state accuracy.
9. Return a final structured Review report.
10. Allow LUMI to reconcile the final Review.
11. Only after reconciliation may LUMI determine that the project is complete.

Final Independent Review is a verification requirement.

It is not an automatic completion signal.

---

## 17. Default Independent Review Flow

    Authoritative requirements
    + orchestration state when configured
    + actual implementation
    → planned Review Checkpoint
    → LUMI creates fresh reviewer-lumi session
    → Reviewer uses model.level.1 through the configured adapter
    → read-only independent evidence gathering
    → requirements ↔ orchestration state ↔ implementation comparison
    → Player-Path Validation when applicable
    → structured findings report
    → LUMI reconciliation
    → orchestration-state update when configured
    → separate Rework when required
    → progression decision
    → Final Independent Review with model.level.0 when required

---

## 18. Reviewer Briefing Template

When starting an Independent Review, LUMI should provide a briefing equivalent
in substance to the following:

    You are performing an Independent Review for the active project.

    This is a read-only verification task.

    Do not modify project files, authoritative Planning, persistent
    orchestration state, Git state/history, or external state.

    Use the active project's configured authoritative requirements as the
    Source of Truth.

    When persistent orchestration state exists, verify its claims against
    authoritative requirements and actual implementation.

    Do not assume previous completion reports or validation records are correct.

    Use previous reports only as reference material.

    Independently inspect current implementation and relevant project state.

    Use configured runtime or editor integrations only for non-destructive
    verification.

    Compare authoritative requirements, orchestration state when configured,
    and actual implementation.

    For interactive or player-facing scope, validate the intended usable path
    when Player-Path Validation applies.

    Do not substitute debug probes, direct internal calls, intermediate states,
    or test-only shortcuts for intended use unless they are explicitly the
    approved validation interface.

    Distinguish Technical Validation, Player-Path Validation, and Human
    Validation.

    Classify findings as Passed, Follow-up, Rework Required, Blocker, or
    Technical Debt.

    Report direct evidence, regression results, unverified items, progression
    assessment, and mutation declaration.

    Stop after reporting findings.

    Do not perform Rework, implementation, Git mutation, or orchestration-state
    editing.

LUMI may add Review-specific scope, acceptance criteria, known context, and
relevant project configuration.

The briefing must never weaken the independence, read-only, Source-of-Truth, or
authorization rules defined by this workflow.

---

## 19. Independent Review Invariants

Throughout Independent Review:

- the Reviewer is fresh and separate from implementation;
- the Reviewer is read-only;
- authoritative project requirements remain the Source of Truth;
- orchestration-state completion claims are independently verified;
- previous agent reports are context, not proof;
- direct evidence is preferred when reasonably available;
- Review scope remains proportional;
- Player-Path Validation applies only when relevant;
- Human Validation is never fabricated;
- findings are reported rather than repaired;
- Rework is a separate development task;
- LUMI remains the orchestration authority;
- the Reviewer does not mutate Git state or history;
- Final Independent Review uses a fresh session when required;
- ordinary Independent Review and parallel intermediate reviewers use
  `model.level.1`, not `model.level.0`;
- Review should reduce uncertainty rather than merely repeat previous claims.
