# TOOLS.md — Local Environment Notes

This file contains machine-specific and runtime-specific notes for Lumi.

The public LUMI repository provides `TOOLS.example.md` as a template.
A deployed Lumi workspace may maintain a local `TOOLS.md` based on this file.

Do not store secrets, credentials, API keys, OAuth tokens, passwords, private
keys, or authentication tokens here.

---

## System

- OS:
- Shell:
- Primary editor:
- Terminal:
- Timezone:

Add only verified environment information.

Do not guess paths or installed software.

---

## Runtime

Record the runtime hosting Lumi on this machine.

Example:

    Runtime: OpenClaw
    Primary agent: main
    Workspace: /path/to/runtime/workspace

Logical agent responsibilities and model-routing policy belong in
`ORCHESTRATION.md`, not here.

Runtime-specific implementation details may be recorded here when they are
useful for diagnostics.

---

## OpenClaw

When OpenClaw is used, local notes may include:

- Installed OpenClaw version.
- Workspace location.
- Agent IDs.
- Runtime bindings.
- Relevant non-secret configuration.
- Approved local integration behavior.

Do not duplicate the full orchestration topology here.

Do not record secret values.

### Configuration Discipline

When modifying OpenClaw configuration:

1. Inspect existing state first.
2. Preserve unrelated configuration.
3. Prefer reversible changes.
4. Verify the result after modification.
5. Keep temporary patch files outside the runtime workspace when practical.
6. Never store authentication credentials in workspace documentation.

---

## Codex Runtime

Model selection, runtime selection, and authentication source are separate
concepts.

A model reference alone does not determine which runtime executes the task.

When Codex runtime is configured:

- Verify the actual runtime when ambiguity matters.
- Prefer supported runtime status information over assumptions.
- Do not infer authentication or billing solely from a model name.
- Never expose OAuth tokens, API keys, refresh tokens, or other credentials.
- Do not silently substitute another runtime when the task explicitly requires
  Codex.

Runtime-specific diagnostic commands may be documented here after they have
been verified on the local environment.

---

## Development Tools

Record frequently used tools when useful.

Examples:

- Git / GitHub
- VS Code
- Python
- Unity
- Blender
- Obsidian
- Xcode
- Docker
- Local AI tools

This list describes availability, not project ownership.

Project-specific tool requirements belong in the relevant `PROJECT.md`.

---

## Project Locations

Do not use this section to determine the active project.

Active project selection is controlled by project routing.

This section may record verified local paths for convenience.

Example:

    Project A → /path/to/project-a
    Project B → /path/to/project-b

Never guess a local project path.

---

## Git

Local Git notes may include:

- Preferred Git executable.
- Credential-helper behavior.
- GitHub CLI availability.
- Verified signing configuration.
- Local repository conventions.

Shared Git safety rules belong in `AGENTS.md`.

Project-specific Git workflow belongs in the project's configured procedure.

Never record Git credentials or access tokens.

---

## Connected Services

Record only non-secret facts about explicitly configured integrations.

Examples:

    Discord → enabled for private owner interface
    Obsidian → CLI available
    GitHub → connected

Do not record tokens or credentials.

Do not treat the existence of a connected service as authorization for every
possible action on that service.

---

## Local Conventions

Use this section for durable machine-specific conventions that do not belong in
shared LUMI policy.

Examples:

- Approved executable paths.
- Verified CLI locations.
- Local development directories.
- Preferred launch commands.
- Non-secret environment behavior.

Keep this file concise.

If a note becomes generally applicable across installations, move it to the
appropriate shared LUMI document instead.
