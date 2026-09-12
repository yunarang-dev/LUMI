# LUMI — Long-term Unified Machine Intelligence

LUMI is a modular AI orchestration harness designed to preserve a consistent
identity, working style, execution policy, and development workflow across
different models, runtimes, projects, and sessions.

The project is currently under active development.

## Goals

LUMI aims to separate the agent itself from the runtime that hosts it.

Its core principles are:

- persistent identity independent of the underlying model;
- explicit separation between conversation, implementation, and review;
- reusable project routing and project profiles;
- model routing based on reasoning needs rather than task size;
- safe handling of local state, Git, external actions, and mutable systems;
- modular configuration that can be adapted to different runtimes.

OpenClaw is currently the primary runtime used to develop and test LUMI, but
the shared harness is designed so that its logical behavior is not inherently
tied to OpenClaw.

## Architecture

The current core consists of:

- `AGENTS.md` — shared execution, safety, memory, project, and Git policy.
- `ORCHESTRATION.md` — agent roles, delegation, worker coordination, model
  routing, recovery, and Independent Review.
- `config/model-levels.yaml` — canonical provider and runtime-model bindings for
  logical model levels.
- `SOUL.md` — personality, communication style, and working character.
- `IDENTITY.md` — Lumi's runtime-independent identity.
- `TOOLS.example.md` — template for local machine and runtime notes.
- `USER.example.md` — template for private user-specific context.
- `MEMORY.example.md` — template for private long-term memory.
- `projects/ROUTING.example.md` — project-routing configuration template.
- `projects/templates/PROJECT.example.md` — generic project-profile template.

## Agent Model

The current reference topology is:

    main
    ├── dev-lumi
    │   └── temporary workers
    └── reviewer-lumi

### main

The primary user-facing Lumi agent.

It handles conversation, requirements, project routing, read-only inspection,
coordination, and final reporting.

### dev-lumi

The development execution agent.

It handles implementation orchestration, debugging, validation, and temporary
worker coordination.

### workers

Temporary bounded execution agents created for independent implementation,
investigation, or validation workstreams.

Workers do not become repository-wide orchestration authorities.

### reviewer-lumi

A separate read-only Independent Review role.

Review is intentionally isolated from implementation and reports findings back
to the orchestration authority instead of modifying the project directly.

## Logical Model Routing

LUMI core routes work through four provider-independent execution profiles:

| Profile | Stable responsibility |
| --- | --- |
| `model.level.0` | Initial Planning and Final Independent Review |
| `model.level.1` | Advanced orchestration, decomposition, and Independent Review |
| `model.level.2` | Heavy implementation, investigation, debugging, and validation |
| `model.level.3` | Default implementation, routine validation, and general workers |

These levels are role-aware profiles, not a linear difficulty ladder. LUMI's
shared orchestration policy depends only on this interface, so a deployment may
bind different levels to OpenAI, Anthropic, Google, local, or mixed providers
without changing core workflow semantics.

### Current OpenAI Reference Binding

The canonical binding in `config/model-levels.yaml` currently resolves:

| Profile | Reference provider/model | Opaque provider options |
| --- | --- | --- |
| `model.level.0` | OpenAI GPT-6 Astra | `reasoning_effort: xhigh` |
| `model.level.1` | OpenAI GPT-5.6 Sol | `reasoning_effort: high` |
| `model.level.2` | OpenAI GPT-5.6 Sol | `reasoning_effort: medium` |
| `model.level.3` | OpenAI GPT-5.6 Luna | `reasoning_effort: xhigh` |

This mapping is a reference configuration, not LUMI policy. Provider-specific
options are opaque to LUMI core and are interpreted only by the runtime adapter.

## Public and Local Configuration

The public repository intentionally does not contain personal runtime state.

Files such as the following are expected to remain local:

- `USER.md`
- `MEMORY.md`
- `memory/`
- `TOOLS.md`
- `projects/ROUTING.md`
- `projects/local/`
- runtime credentials and authentication state
- local recovery archives
- generated state and caches

Use the provided `.example.md` files as starting points.

Never commit API keys, OAuth tokens, passwords, private keys, or other
credentials.

## Project Routing

LUMI supports multiple projects through explicit routing.

A typical installation creates:

    projects/ROUTING.md
    projects/local/<project-id>/PROJECT.md

`ROUTING.md` resolves the active project.

Each `PROJECT.md` contains project-specific paths, planning configuration,
workflow flags, validation requirements, and procedure references.

The shared harness must not infer the active project solely from memory or
repository names.

## Runtime Integration

Runtime-specific adapters and installation automation are still being designed.

The current development environment uses OpenClaw, but OpenClaw-specific
configuration should remain separate from Lumi's shared behavioral policy
where practical.

## Status

LUMI is currently being refactored from a personal OpenClaw harness into a more
general and reusable system.

Some older workflows, including asset tagging and Unity Asset Store tooling,
are intentionally excluded from the public core until their interfaces and
behavior are stable enough to generalize.

Expect breaking changes while this refactor is in progress.

## License

Licensed under the Apache License 2.0.

See `LICENSE` for details.
