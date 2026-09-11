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

### Default model

- Model: `openai/gpt-5.6-luna`
- Thinking: `xhigh`

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

`main` does not normally self-escalate to Sol or Astra.

When development work requires stronger reasoning, route that work through the
appropriate `dev-lumi` model path instead.

---

## 4. dev-lumi — Development Orchestrator

`dev-lumi` owns development execution delegated by `main`.

It remains an orchestrator even when it performs implementation itself.

### Default route

- Model: `openai/gpt-5.6-luna`
- Thinking: `xhigh`

Use this route for:

- Normal implementation.
- Clear feature work.
- Localized refactoring.
- Routine testing.
- Straightforward debugging.
- Ordinary development inspection and validation.

### Complex implementation route

- Model: `openai/gpt-5.6-sol`
- Thinking: `high`

Use Sol High when stronger technical judgment materially reduces risk.

Typical reasons include:

- Structural or uncertain debugging.
- Significant dependency complexity.
- Multiple tightly coupled systems.
- High regression risk.
- Complex integration work.
- Repeated implementation failure.
- High-cost technical mistakes.
- Difficult single-owner implementation work.

Do not use Sol merely because:

- The task is long.
- Many files are involved.
- The task uses many tokens.
- Parallel capacity is available.

Return to Luna xHigh when the high-complexity portion is complete.

### Planning route

- Model: `openai/gpt-6-astra`
- Thinking: `high`
- Scope: planning only

Use Astra High for substantial development planning such as:

- Architecture planning.
- Complex dependency analysis.
- Milestone restructuring.
- Ownership design.
- Parallel execution planning.
- Integration planning.
- Large rework assessment.
- High-risk implementation strategy.

Astra planning is analysis, not implementation authorization.

During an Astra planning pass:

- Do not modify implementation content.
- Do not modify Git state or history.
- Do not silently expand project scope.
- Return the resulting plan to the normal development workflow.

Astra is not a normal implementation worker.

---

## 5. Worker Agents

Workers are temporary sub-agents created by `dev-lumi`.

They are execution units, not independent orchestration authorities.

### General worker

- Model: `openai/gpt-5.6-luna`
- Thinking: `xhigh`

Use for:

- Ordinary parallel implementation.
- Independent investigation.
- Independent testing.
- Clearly bounded corrective work.
- Tasks with clean ownership boundaries.

### Complex single worker

- Model: `openai/gpt-5.6-sol`
- Thinking: `high`

Use only when one bounded workstream is individually complex enough to justify
Sol High.

Do not use Sol workers merely to increase aggregate model strength.

### Astra restriction

Workers must not use Astra for implementation.

Astra is reserved for:

- `dev-lumi` planning.
- `reviewer-lumi` independent review.

---

## 6. Parallel Execution

Parallel execution is an optimization, not a default requirement.

Before spawning multiple workers, `dev-lumi` must evaluate:

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

- Model: `openai/gpt-6-astra`
- Thinking: `xhigh`

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

Model selection is based on reasoning need, not task size.

### Luna xHigh

Prefer Luna xHigh when:

- Requirements are clear.
- Scope is bounded.
- Risk is low or moderate.
- Implementation pattern is known.
- Debugging cause is reasonably localized.
- Parallel workstreams are straightforward.

### Sol High

Prefer Sol High when:

- Technical uncertainty is high.
- Dependencies are tightly coupled.
- Regression risk is significant.
- Architecture-level implementation judgment is required.
- Repeated failures indicate a structural problem.
- A complex single workstream benefits materially from stronger reasoning.

### Astra High

Use Astra High only for substantial development planning.

### Astra xHigh

Use Astra xHigh for Independent Review.

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

Escalate Luna → Sol only when stronger reasoning can materially help resolve the
technical uncertainty.

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

`🔎 Lumi review: main → reviewer-lumi/Astra xHigh — independent review started`

When review completes:

`🔎 Lumi review: reviewer-lumi → main — independent review complete`

Do not imply that review findings have already been accepted or repaired.

---

## 11. Model Transition Notifications

Notify the user when an actual development model route changes materially.

Examples:

`🔄 Codex model: Luna xHigh → Sol High — complex implementation`

`🔄 Codex model: Sol High → Luna xHigh — complex scope complete`

`🔄 Codex planning: Luna xHigh → Astra High — development planning`

`🔄 Codex planning: Astra High → Luna xHigh — planning complete`

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

## 14. Reference Runtime Binding

Lumi's logical roles must remain distinct from the runtime used to implement
them.

The current OpenClaw reference binding is:

~~~text
main
→ OpenClaw runtime
→ OpenAI API
→ GPT-5.6 Luna xHigh

dev-lumi
→ Codex runtime
→ GPT-5.6 Luna xHigh
→ GPT-5.6 Sol High when justified
→ GPT-6 Astra High for planning only

workers
→ Codex runtime
→ GPT-5.6 Luna xHigh
→ GPT-5.6 Sol High for complex single workstreams

reviewer-lumi
→ Codex runtime
→ GPT-6 Astra xHigh
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
6. Astra is not used as a normal implementation worker.
7. Independent Review is fresh, separate, and read-only.
8. Reviewer findings do not modify implementation directly.
9. Model escalation is based on reasoning need and failure risk.
10. Environment failures do not justify model escalation.
11. Confirmed work is preserved during recovery.
12. Runtime choice does not redefine Lumi's logical role boundaries.
