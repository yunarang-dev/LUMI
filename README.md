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

## Reference Model Routing

The current OpenClaw reference configuration uses:

- `main` — GPT-5.6 Luna, xHigh, direct API/OpenClaw runtime
- `dev-lumi` default — GPT-5.6 Luna, xHigh, Codex runtime
- complex development — GPT-5.6 Sol, High, Codex runtime
- substantial development planning — GPT-6 Astra, High, Codex runtime
- general workers — GPT-5.6 Luna, xHigh, Codex runtime
- complex single worker — GPT-5.6 Sol, High, Codex runtime
- Independent Review — GPT-6 Astra, xHigh, Codex runtime

These bindings are runtime configuration, not Lumi's identity.

The logical agent roles are intended to remain usable if the underlying
runtime or model family changes.

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
