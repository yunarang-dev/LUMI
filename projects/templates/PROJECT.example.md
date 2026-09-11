# PROJECT.md — Lumi Project Profile

This file defines project-specific configuration for one project.

Shared Lumi behavior belongs in `AGENTS.md`.

Shared orchestration and model routing belong in `ORCHESTRATION.md`.

This profile should contain project-specific facts, paths, workflow flags, and
procedure references.

Do not store secrets or credentials here.

---

## Project Identity

- Project ID: example-project
- Project Name: Example Project
- Status:
- Description:

`Project ID` must match the canonical ID registered in `projects/ROUTING.md`.

---

## Local Paths

- Repository:
- Implementation Workspace:
- Additional Workspace:

Use verified paths only.

Do not infer paths from repository names or from another project.

The implementation workspace is the directory that development execution should
treat as the active implementation root.

---

## Planning

- Planning Enabled: false
- Planning System:
- Planning Source:
- Planning Access Method:
- Derived Orchestration State:

When planning is enabled, `Planning Source` is the authoritative design source
unless explicitly documented otherwise.

Do not treat derived orchestration state as a replacement for authoritative
planning.

Leave unused fields blank rather than inventing values.

---

## Development Workflow

- Managed Development: false
- Development Delegation: true
- Implementation Boundary: runtime-default
- Planning Sync: false
- Recovery Workflow: false
- Independent Review: false
- Final Independent Review Required: false

### Planning Sync

When `Planning Sync` is enabled, define the project's synchronization behavior
in a project-specific procedure.

Use `projects/templates/PLANNING_SYNC.example.md` as a starting template when
useful.

Planning Sync remains project-specific and should not be assumed to use the same
change-detection or orchestration-state model as another project.

### Development Delegation

When enabled, actual project development follows the shared delegation policy in
`ORCHESTRATION.md`.

Project-specific rules may refine execution but should not duplicate the entire
shared orchestration policy.

### Implementation Boundary

Typical values may include:

- `codex`
- `runtime-default`
- another explicitly documented project-specific implementation path

Do not silently fall back to another mutation path when the configured
implementation boundary is unavailable.

---

## Model Policy

Shared model routing from `ORCHESTRATION.md` applies by default.

Project-specific exceptions should normally remain empty.

- Default Development Override:
- Complex Development Override:
- Planning Override:
- Independent Review Override:

Use overrides only when the project has a concrete reason to differ from shared
LUMI policy.

A project profile must not silently convert Astra into a normal implementation
worker.

A project-specific override does not change the logical responsibilities of
`main`, `dev-lumi`, workers, or `reviewer-lumi`.

---

## Procedures

Procedure references are optional.

- Git Workflow:
- Planning Sync Procedure:
- Recovery Procedure:
- Independent Review Procedure:
- Build Procedure:
- Deployment Procedure:

Procedure paths may be relative to this profile or use another explicitly
documented location.

Do not substitute a procedure from another project when a configured procedure
is missing.

Read a procedure only when its workflow is actually invoked.

---

## Git

- Lumi-Managed Git: false
- Default Branch:
- Remote Publication Allowed By Workflow: false

When a project-specific Git procedure is configured, follow it together with the
shared Git safety rules.

Never store Git credentials or access tokens here.

---

## Validation

- Validation Enabled: true
- Build Validation:
- Automated Tests:
- Integration Validation:
- Human Validation Required: false

List only validation paths that actually exist for the project.

Do not mark validation complete merely because implementation exists.

When human judgment is required, distinguish human validation from automated or
agent-driven validation.

---

## Unity

- Unity Project: false
- Unity Version:
- Unity Project Path:
- GUI Editor Integration: false
- Pipeline Integration: false
- Player-Path Validation: false

Leave this section disabled for non-Unity projects.

Unity-specific operational procedures should live in dedicated project
procedures or reusable workflows rather than expanding this profile into a
large Unity manual.

---

## Independent Review

When enabled:

- use the shared Independent Review policy from `ORCHESTRATION.md`;
- use a fresh review session;
- keep review separate from implementation;
- keep the reviewer read-only;
- return findings to the orchestration authority;
- handle rework through the normal development workflow.

Project-specific review checkpoints may be documented here or in the configured
review procedure.

### Review Checkpoints

- Final Review:

Add intermediate checkpoints only when they provide meaningful risk reduction.

---

## Project-Specific Constraints

Record durable constraints that materially affect execution.

Examples include:

- supported platforms,
- required engine versions,
- repository restrictions,
- compatibility requirements,
- prohibited dependencies,
- external delivery requirements.

Do not duplicate general LUMI safety or orchestration policy here.

---

## Notes

Keep this profile concise.

If content becomes:

- generally applicable → move it to shared LUMI policy;
- an execution procedure → move it to a procedure file;
- machine-specific → move it to local environment configuration;
- temporary project status → keep it in verified project state or orchestration
  state rather than permanently expanding this profile.
