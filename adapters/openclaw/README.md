# OpenClaw Adapter

This adapter describes how to run LUMI on OpenClaw.

The shared LUMI core remains runtime-independent.

OpenClaw-specific agent definitions, runtime bindings, authentication setup,
workspace deployment, and operational notes belong here rather than in the
shared behavioral policy.

---

## Scope

This adapter covers:

- OpenClaw agent topology.
- Runtime and model bindings.
- Workspace deployment.
- OpenAI API and Codex authentication separation.
- Development-worker configuration.
- Independent-review configuration.
- OpenClaw-specific verification.

It must not contain:

- API keys.
- OAuth tokens.
- Gateway tokens.
- Passwords.
- User-specific absolute paths.
- Private project configuration.
- Runtime databases or session state.

---

## Reference Topology

The current reference topology is:

    main
    ├── dev-lumi
    │   └── temporary sub-agents
    └── reviewer-lumi

### main

Purpose:

- User-facing Lumi.
- General orchestration.
- Conversation.
- Read-only inspection and research.
- Project routing.
- Development delegation.

Reference binding:

- Model: openai/gpt-5.6-luna
- Thinking: xhigh
- Runtime: OpenClaw
- Authentication: OpenAI API key

### dev-lumi

Purpose:

- Development execution.
- Development orchestration.
- Implementation.
- Debugging.
- Validation.
- Worker coordination.

Reference bindings:

- Default: openai/gpt-5.6-luna / xhigh / Codex
- Complex implementation: openai/gpt-5.6-sol / high / Codex
- Development planning: openai/gpt-6-astra / high / Codex

### Workers

Workers are temporary OpenClaw sub-agents created by dev-lumi.

Reference policy:

- General worker: openai/gpt-5.6-luna / xhigh / Codex
- Complex single worker: openai/gpt-5.6-sol / high / Codex
- Astra must not be used for normal implementation workers.
- Maximum concurrent workers: 4
- Maximum spawn depth: 2
- Worker archive delay: 60 minutes

### reviewer-lumi

Purpose:

- Fresh Independent Review.
- Read-only verification.
- Evidence gathering.
- Findings and recommendations.

Reference binding:

- Model: openai/gpt-6-astra
- Thinking: xhigh
- Runtime: Codex

Each Independent Review should use a fresh review session separate from
implementation sessions.

---

## Workspace

The recommended OpenClaw workspace is separate from the LUMI source repository.

Conceptually:

    LUMI repository
    → source and distribution

    OpenClaw workspace
    → deployed runtime copy

Do not use the Git repository itself as mutable OpenClaw runtime state.

The deployment process should copy or synchronize the required shared harness
files into the configured OpenClaw workspace.

Typical deployed files include:

- AGENTS.md
- ORCHESTRATION.md
- SOUL.md
- IDENTITY.md
- local USER.md
- local MEMORY.md
- local TOOLS.md
- local projects/ROUTING.md
- required project profiles and procedures

Private local files must remain outside public Git history.

---

## Authentication Separation

Model reference, runtime, and authentication are separate concerns.

The reference setup uses:

### main

    Runtime: OpenClaw
    Provider: OpenAI
    Authentication: API key

### dev-lumi and reviewer-lumi

    Runtime: Codex
    Provider: OpenAI
    Authentication: Codex-compatible OpenAI account state

Do not assume that selecting an OpenAI model automatically selects either the
direct API runtime or Codex runtime.

Do not store credential values inside this repository.

---

## Secret Handling

Secret values belong in the runtime's supported secret storage or environment.

Examples of secret categories include:

- OpenAI API keys.
- OpenClaw Gateway tokens.
- OAuth access tokens.
- OAuth refresh tokens.
- Private keys.

The adapter may document secret names or expected secret sources when useful,
but must never include actual secret values.

---

## Agent Configuration

The reference OpenClaw installation uses three persistent agents:

- main
- dev-lumi
- reviewer-lumi

Workers remain ephemeral sub-agents rather than persistent worker profiles.

The logical behavior of these agents is defined by `ORCHESTRATION.md`.

This adapter only binds those roles to OpenClaw.

---

## main Configuration

Reference settings:

    Agent ID: main
    Model: openai/gpt-5.6-luna
    Thinking: xhigh
    Agent runtime: openclaw

main should not normally use Sol or Astra directly.

Development work should be delegated according to `ORCHESTRATION.md`.

---

## dev-lumi Configuration

Reference settings:

    Agent ID: dev-lumi
    Default model: openai/gpt-5.6-luna
    Thinking: xhigh
    Agent runtime: codex

Additional available development routes:

    openai/gpt-5.6-sol
    openai/gpt-6-astra

Their use is governed by `ORCHESTRATION.md`.

Sub-agent defaults:

    Primary worker model: openai/gpt-5.6-luna
    Delegation mode: prefer
    Maximum concurrent workers: 4
    Maximum spawn depth: 2
    Archive delay: 60 minutes

---

## reviewer-lumi Configuration

Reference settings:

    Agent ID: reviewer-lumi
    Model: openai/gpt-6-astra
    Thinking: xhigh
    Agent runtime: codex

The persistent agent profile does not replace the requirement for fresh review
sessions.

Each Independent Review must still be isolated from implementation state as
defined by `ORCHESTRATION.md`.

---

## Verification

After configuring OpenClaw, verify:

1. The expected agents exist.
2. Each agent uses the expected default model.
3. main is bound to the OpenClaw runtime.
4. dev-lumi is bound to the Codex runtime.
5. reviewer-lumi is bound to the Codex runtime.
6. main uses the intended API authentication path.
7. Codex runtime authentication is usable.
8. dev-lumi worker defaults are correct.
9. Worker concurrency and spawn depth match the reference policy.
10. No plaintext secrets are present in workspace or repository files.

Verification should inspect actual current runtime state rather than relying on
documentation alone.

---

## Runtime Independence

OpenClaw is an adapter, not LUMI itself.

The following belong to the shared LUMI core:

- identity,
- personality,
- safety policy,
- project-routing concepts,
- orchestration roles,
- worker ownership rules,
- review independence,
- model-routing semantics.

The following belong to this adapter:

- OpenClaw agent configuration.
- OpenClaw runtime bindings.
- OpenClaw workspace deployment.
- OpenClaw authentication integration.
- OpenClaw-specific diagnostics and lifecycle behavior.

This separation allows another runtime adapter to reproduce the same logical
LUMI behavior without copying OpenClaw-specific implementation details.
