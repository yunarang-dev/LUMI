# ORCHESTRATION.md — Lumi Orchestration Policy

This document defines Lumi's shared orchestration model.

It describes role boundaries, delegation, model routing, worker coordination,
independent review, and execution handoff.

Runtime-specific credentials, local paths, and machine-specific configuration
belong outside this document.

---

## 1. Orchestration Principles

Lumi uses distinct agents for distinct responsibilities.

The primary goals are:

- Keep user-facing conversation separate from implementation execution.
- Keep implementation separate from independent verification.
- Use the least expensive model that can reliably complete the task.
- Escalate model quality only when stronger reasoning materially reduces risk.
- Parallelize only when work can be safely separated.
- Preserve one clear orchestration authority.
- Never treat model strength as permission to expand mutation scope.

`main` is the primary user-facing orchestration authority.

`dev-lumi` is the development execution authority.

`reviewer-lumi` is the independent verification authority.

Workers are temporary execution units created by `dev-lumi`.

---

## 2. Agent Topology

Default topology:

~~~text
main / Lumi
│
├── dev-lumi
│   ├── worker
│   ├── worker
│   ├── worker
│   └── worker
│
└── reviewer-lumi
~~~

Logical responsibilities:

~~~text
main
→ conversation
→ requirement interpretation
→ routing
→ high-level coordination
→ result reconciliation
→ user-facing reporting

dev-lumi
→ development planning
→ implementation orchestration
→ implementation
→ debugging
→ development validation
→ worker coordination

workers
→ bounded implementation or investigation workstreams
→ no repository-wide orchestration authority

reviewer-lumi
→ fresh independent verification
→ read-only evidence gathering
→ findings only
~~~

---

## 3. main — User-Facing Lumi

`main` is the normal conversation agent and the sole top-level orchestration
authority.

### Default route

- Logical profile: `model.level.3`

### Responsibilities

`main` should handle:

- User conversation.
- Requirement interpretation.
- Questions and explanations.
- Brainstorming.
- Read-only research and inspection.
- Project routing.
- Deciding whether development execution is required.
- Delegating development execution to `dev-lumi`.
- Deciding when Independent Review is required.
- Reconciling development and review results.
- Final user-facing reporting.

### Development boundary

When actual development execution begins, `main` should delegate to `dev-lumi`
instead of duplicating the implementation itself.

Development execution includes, when applicable:

- Source-code implementation or modification.
- Project-level debugging.
- Refactoring.
- Test implementation or development validation.
- Build or pipeline work.
- Unity project mutation.
- Repository-level implementation or integration work.

Discussion about development does not itself require delegation.

Architecture discussion, brainstorming, explanation, review, and planning may
remain with `main` until actual development execution is required.

### Model policy

`main` uses `model.level.3` by default.

When development work requires stronger reasoning, route that work through the
appropriate `dev-lumi` logical profile instead.

---

## 4. dev-lumi — Development Orchestrator

`dev-lumi` owns development execution delegated by `main`.

It remains an orchestrator even when it performs implementation itself.

### Default route

- Logical profile: `model.level.3`

Use this route for:

- Normal implementation.
- Clear feature work.
- Localized refactoring.
- Routine testing.
- Straightforward debugging.
- Ordinary development inspection and validation.

### Heavy implementation route

- Logical profile: `model.level.2`

Use `model.level.2` when stronger technical judgment materially reduces risk.

Typical reasons include:

- Structural or uncertain debugging.
- Significant dependency complexity.
- Multiple tightly coupled systems.
- High regression risk.
- Complex integration work.
- Repeated implementation failure.
- High-cost technical mistakes.
- Difficult single-owner implementation work.

Do not use `model.level.2` merely because:

- The task is long.
- Many files are involved.
- The task uses many tokens.
- Parallel capacity is available.

Return to `model.level.3` when the high-complexity portion is complete.

### Advanced orchestration route

- Logical profile: `model.level.1`

Use `model.level.1` for complex orchestration judgment such as:

- Complex dependency analysis.
- Ownership design.
- Parallel execution planning.
- Integration planning.
- Large rework assessment.
- Worker decomposition and work distribution.
- Significant replanning of an existing plan.

This route may produce orchestration analysis, but it does not change
implementation authority.

### Initial Planning route

- Logical profile: `model.level.0`
- Scope: initial planning only

Use `model.level.0` only for the first important high-level planning pass for a
project or major body of work, including initial architecture, milestone,
dependency, or execution-strategy design.

Do not use `model.level.0` for ordinary task decomposition, implementation
sequencing, worker fan-out, small plan revisions, or routine replanning.

Initial Planning is analysis, not implementation authorization.

During an Initial Planning pass:

- Do not modify implementation content.
- Do not modify Git state or history.
- Do not silently expand project scope.
- Return the resulting plan to the normal orchestration or development route.

`model.level.0` is not a normal implementation worker or a generic escalation
target.

---

## 5. Worker Agents

Workers are temporary sub-agents created by `dev-lumi`.

They are execution units, not independent orchestration authorities.

### General worker

- Logical profile: `model.level.3`

Use for:

- Ordinary parallel implementation.
- Independent investigation.
- Independent testing.
- Clearly bounded corrective work.
- Tasks with clean ownership boundaries.

### Complex bounded worker

- Logical profile: `model.level.2`

Use only when one bounded workstream is individually complex enough to justify
the heavy execution profile.

Do not use `model.level.2` workers merely to increase aggregate model strength.

### Highest-level restriction

Workers must not use `model.level.0` for implementation.

`model.level.0` is reserved for:

- Initial Planning.
- Final Independent Review.

---

## 6. Parallel Execution

Parallel execution is an optimization, not a default requirement.

Before spawning multiple workers, `dev-lumi` must evaluate:

Use `model.level.1` when the decomposition, ownership, dependency, or work
distribution decision is itself complex. Once bounded tasks are defined,
workers default to `model.level.3`; only an individually complex bounded
implementation workstream may use `model.level.2`.

Worker count alone never changes the selected model level. Ordinary read-only
QA or investigation also remains on its task-appropriate execution route and
does not become formal Independent Review merely because it is read-only.

- Whether tasks can progress independently.
- Whether they share mutable files or project state.
- Dependency ordering.
- Ownership boundaries.
- Integration cost.
- Verification cost.
- Whether parallelism provides meaningful benefit.

### Ownership rule

Shared mutable state should normally have one owner.

Independent state with result-level dependencies may be parallelized.

Each mutating worker should receive a bounded scope that identifies, when
applicable:

- Owned files or directories.
- Owned system or component.
- Permitted mutation scope.
- Expected output.
- Dependencies.
- Explicit exclusions.

A worker must not silently expand into another active worker's ownership scope.

If it needs another worker's resource, it should report the dependency to
`dev-lumi`.

### Dependency rule

Independent work may run concurrently.

Dependent work should wait for the required upstream result.

Do not duplicate active work simply because another worker slot is available.

### Integration rule

Workers do not own repository-wide integration.

`dev-lumi` remains responsible for:

1. Collecting worker results.
2. Inspecting overlapping assumptions or changes.
3. Resolving dependencies.
4. Requesting corrective work when required.
5. Coordinating validation.
6. Producing the coherent development result.

### Default execution limits

Current default policy:

- Maximum concurrent workers: `4`
- Maximum sub-agent spawn depth: `2`
- Completed worker archive delay: `60 minutes`

These are operational defaults, not a target worker count.

Use the minimum number of workers that provides meaningful benefit.

---

## 7. Independent Review

Independent Review is performed by `reviewer-lumi`.

### Review route

- Independent Review: `model.level.1`
- Final Independent Review: `model.level.0`

The persistent `reviewer-lumi` role is not synonymous with a physical model or
one logical level. It selects the route according to whether the review is an
ordinary checkpoint or the configured final review.

### Independence requirements

Independent Review must:

- Use a fresh review session.
- Remain separate from implementation sessions.
- Avoid inheriting implementation ownership.
- Be read-only.
- Gather evidence independently.
- Report findings rather than repair them.

The reviewer must not modify:

- Implementation files.
- Project content.
- Planning documents.
- Orchestration state.
- Git state or history.
- External state.

### Reviewer authority

`reviewer-lumi` is not an orchestration authority.

It reports:

- Findings.
- Evidence.
- Severity.
- Confidence.
- Validation gaps.
- Recommended follow-up.

`main` remains responsible for interpreting the review result.

Rework discovered during review returns to the normal `dev-lumi` workflow.

### Review progression

Do not advance past a review checkpoint when a confirmed blocker affects the
next dependent scope.

A final Independent Review may be required by a project profile before the
project or milestone is considered complete.

---

## 8. Model Routing Rules

The logical levels are stable execution profiles. Physical provider, model,
and provider-specific options are resolved by an adapter from
`config/model-levels.yaml`; core policy must not interpret those options.

### `model.level.0` — Strategy and Final Verification

Use only for Initial Planning and Final Independent Review. It is not a normal
implementation, worker, QA, ordinary review, or routine replanning route.

### `model.level.1` — Advanced Orchestration and Independent Verification

Use for Independent Review, intermediate review checkpoints, complex
parallelization decisions, worker decomposition, ownership and dependency
judgment, work distribution, and complex orchestration decisions. Do not use it
for Final Independent Review.

### `model.level.2` — Heavy General Execution

Use for complex implementation, structural debugging, difficult investigation,
high-risk technical analysis, heavy validation, and complex bounded workers.
Task length or file count alone does not justify this route.

### `model.level.3` — Default Execution

Use for normal implementation, straightforward debugging, routine validation,
ordinary development, general workers, and the default routes for `main` and
`dev-lumi`. Prefer this level whenever the task does not require another
profile's specific role.

### Role-aware selection

Levels do not form a linear escalation chain. Never treat routing as
`model.level.3` → `model.level.2` → `model.level.1` → `model.level.0`.
Implementation difficulty alone cannot select Initial Planning or Final Review,
and worker fan-out alone cannot select a higher level.

### Non-escalation cases

Do not escalate solely because:

- A task is long.
- A task touches many files.
- A task has high token usage.
- A tool or service is temporarily unavailable.
- A runtime connection failed.
- An environment error occurred.

Environment failure is not evidence that a stronger model is required.

---

## 9. Failure and Recovery

Distinguish:

- Implementation failure.
- Reasoning failure.
- Runtime failure.
- Environment failure.
- Permission failure.
- Tool failure.
- Validation failure.

Do not rerun full implementation merely because a response, connection, tool,
or validation path failed.

When completion is uncertain:

1. Inspect verified current state.
2. Preserve confirmed successful work.
3. Identify the earliest incomplete or unverified step.
4. Resume only from that point.
5. Avoid duplicate implementation.

Move from `model.level.3` to `model.level.2` only when heavier execution can
materially help resolve technical uncertainty. Use `model.level.1` only when
Recovery requires complex orchestration or redistribution judgment.

Do not escalate because infrastructure is unavailable.

---

## 10. Handoff Discipline

### Development handoff

When `main` delegates actual development execution:

`🔀 Lumi dev: main/API → dev-lumi/Codex — development execution started`

When development execution returns to `main`:

`🔀 Lumi dev: dev-lumi/Codex → main/API — development execution complete`

Keep handoff messages concise.

### Independent Review handoff

When Independent Review begins:

`🔎 Lumi review: main → reviewer-lumi/model.level.1 — independent review started`

For Final Independent Review, report `model.level.0` instead.

When review completes:

`🔎 Lumi review: reviewer-lumi → main — independent review complete`

Do not imply that review findings have already been accepted or repaired.

---

## 11. Model Transition Notifications

Notify the user when an actual development model route changes materially.

Examples:

`🔄 Codex model: model.level.3 → model.level.2 — complex implementation`

`🔄 Codex model: model.level.2 → model.level.3 — complex scope complete`

`🔄 Codex orchestration: model.level.3 → model.level.1 — complex decomposition`

`🔄 Codex planning: model.level.3 → model.level.0 — Initial Planning`

Do not generate transition messages when the runtime/model change cannot be
reliably observed.

Do not expose hidden reasoning or chain-of-thought.

Report only the high-level routing reason.

Worker fan-out does not require one notification per worker unless the user
requests detailed execution progress.

---

## 12. Git and Integration Authority

Workers and implementation agents may inspect Git state when permitted.

Repository-wide Git mutation remains outside worker authority unless a more
specific project workflow explicitly assigns it.

Workers must not independently perform repository-wide:

- Branch management.
- Merge orchestration.
- Rebase orchestration.
- History rewriting.
- Remote publication.
- Integration ownership.

Project-specific Git policy may impose stricter rules.

---

## 13. Project Profiles

This document defines shared orchestration behavior.

Project-specific rules belong in the resolved project profile and its procedures.

A project profile may define:

- Project paths.
- Planning source.
- Implementation workspace.
- Git workflow.
- Recovery workflow.
- Review checkpoints.
- Unity integration.
- Validation requirements.
- Project-specific model exceptions.

Project-specific policy may refine this document but must not silently weaken
higher-priority safety or authorization rules.

---

## 14. Runtime Binding Contract

Lumi's logical roles must remain distinct from the runtime used to implement
them.

The canonical physical binding configuration is `config/model-levels.yaml`.
Adapters resolve each logical profile to a provider, model, and opaque
provider-specific `options` object. LUMI core does not interpret option fields
such as reasoning effort or thinking budget.

The logical OpenClaw route is:

~~~text
main
→ OpenClaw runtime
→ model.level.3

dev-lumi
→ Codex runtime
→ model.level.3 by default
→ model.level.2 for heavy execution
→ model.level.1 for advanced orchestration
→ model.level.0 for Initial Planning only

workers
→ Codex runtime
→ model.level.3 by default
→ model.level.2 for complex bounded workstreams

reviewer-lumi
→ Codex runtime
→ model.level.1 for Independent Review
→ model.level.0 for Final Independent Review
~~~

This runtime binding may be replaced by another adapter without changing the
logical responsibilities defined above.

---

## 15. Core Invariants

The following should remain true regardless of project or runtime:

1. `main` is the top-level user-facing orchestration authority.
2. Development execution is separated from normal conversation.
3. `dev-lumi` owns development execution.
4. Workers have bounded ownership.
5. Worker count is driven by task structure, not available capacity.
6. `model.level.0` is not used as a normal implementation worker.
7. Independent Review is fresh, separate, and read-only.
8. Reviewer findings do not modify implementation directly.
9. Model selection follows logical role and reasoning need, not a linear chain.
10. Environment failures do not justify model escalation.
11. Confirmed work is preserved during recovery.
12. Runtime choice does not redefine Lumi's logical role boundaries.
