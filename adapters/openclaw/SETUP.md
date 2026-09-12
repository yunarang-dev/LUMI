# OpenClaw Adapter Setup

This document describes the reference procedure for configuring LUMI on
OpenClaw.

It assumes:

- OpenClaw is already installed and functional.
- The LUMI repository is available locally.
- Required OpenAI API or Codex authentication can be configured by the user.
- Secret values are never stored in the LUMI repository.

Commands may need adjustment when OpenClaw changes its configuration schema.

Always inspect current state before applying configuration changes.

Before applying these commands, read `config/model-levels.yaml`. It is the
canonical binding source. The physical values below are a reference procedure
for applying that file to OpenClaw, not an independent model-routing policy.

---

## 1. Paths

The reference installation keeps the LUMI source repository separate from the
OpenClaw runtime workspace.

Example shell variables:

    LUMI_REPO="$HOME/LUMI"
    OPENCLAW_WORKSPACE="$HOME/.openclaw/workspace"

The source repository is for:

- version-controlled LUMI policy,
- templates,
- runtime adapters,
- documentation.

The OpenClaw workspace is for:

- deployed LUMI harness files,
- local user configuration,
- local memory,
- project routing,
- runtime-specific local state.

Do not use the source repository as OpenClaw mutable runtime state.

---

## 2. Verify Existing OpenClaw State

Before changing configuration, inspect the current installation.

Recommended checks:

    openclaw --version
    openclaw agents list
    openclaw gateway status

Inspect existing model and agent configuration before replacing values.

Do not run automatic repair commands merely as part of setup when the current
installation is already healthy.

---

## 3. Configure the Shared Workspace

Set the default OpenClaw workspace:

    openclaw config set agents.defaults.workspace ~/.openclaw/workspace

Verify the configured value before continuing.

The runtime workspace should remain separate from the LUMI Git repository.

---

## 4. Deploy the Shared LUMI Core

The shared runtime workspace should receive the core LUMI policy files:

- AGENTS.md
- ORCHESTRATION.md
- SOUL.md
- IDENTITY.md

Local files such as the following should be created or maintained separately:

- USER.md
- MEMORY.md
- TOOLS.md
- projects/ROUTING.md
- projects/local/

Do not overwrite existing local context blindly.

Before replacing any deployed file:

1. Inspect the existing file.
2. Preserve local changes.
3. Compare it with the source repository.
4. Apply the intended update deliberately.

Templates in the public repository may be used when creating a new local
installation.

---

## 5. Configure main

`main` is the primary user-facing LUMI agent.

`model.level.3` reference model:

    openai/gpt-5.6-luna

`model.level.3` opaque reasoning option:

    xhigh

Reference runtime:

    openclaw

Set the model:

    openclaw config set agents.entries.main.model openai/gpt-5.6-luna

Set the default thinking level:

    openclaw config set agents.entries.main.thinkingDefault xhigh

Pin the current `model.level.3` model to the OpenClaw runtime:

    openclaw config set 'agents.entries.main.models["openai/gpt-5.6-luna"].agentRuntime.id' openclaw

Optional reference identity:

    openclaw config set agents.entries.main.identity.name lumi
    openclaw config set agents.entries.main.identity.emoji "✨"

The runtime-configured display identity may differ from the portable
`IDENTITY.md` identity without changing LUMI's logical behavior.

---

## 6. Configure main Authentication

The reference configuration uses an OpenAI API-key authentication profile for
`main`.

Secret values must remain outside the repository.

A local secret may be provided through an environment source such as:

    OPENAI_API_KEY

Use OpenClaw's supported secret configuration flow to create a local
SecretRef-backed OpenAI API-key profile.

For example, when supported by the installed OpenClaw version:

    openclaw secrets configure --agent main --skip-provider-setup

Create or select an OpenAI API-key auth profile backed by the environment
secret rather than storing the key as plaintext.

The reference profile ID is:

    openai:api-key

After the profile exists, prefer it for main:

    openclaw models auth --agent main order set --provider openai openai:api-key

Do not paste API-key values into repository files, shell history, documentation,
or command output.

---

## 7. Create dev-lumi

Create the persistent development agent:

    openclaw agents add dev-lumi \
      --workspace ~/.openclaw/workspace \
      --model openai/gpt-5.6-luna \
      --non-interactive

Set its default thinking level:

    openclaw config set agents.entries.dev-lumi.thinkingDefault xhigh

Optional reference identity:

    openclaw config set agents.entries.dev-lumi.identity.name dev-lumi
    openclaw config set agents.entries.dev-lumi.identity.emoji "🛠️"

---

## 8. Configure dev-lumi Runtime Routes

Pin the physical models required by the current canonical levels to the Codex
runtime.

Luna:

    openclaw config set 'agents.entries.dev-lumi.models["openai/gpt-5.6-luna"].agentRuntime.id' codex

Sol:

    openclaw config set 'agents.entries.dev-lumi.models["openai/gpt-5.6-sol"].agentRuntime.id' codex

Astra:

    openclaw config set 'agents.entries.dev-lumi.models["openai/gpt-6-astra"].agentRuntime.id' codex

Their logical roles are defined only in `ORCHESTRATION.md`. Provider-specific
reasoning options come from `config/model-levels.yaml` and must be applied when
the corresponding route is selected.

The existence of a configured model route does not authorize using it outside
its defined role.

---

## 9. Configure dev-lumi Authentication

The reference Codex path uses the user's supported OpenAI/Codex authentication
state.

When the expected OAuth profile is named:

    openai:default

prefer it for dev-lumi:

    openclaw models auth --agent dev-lumi order set --provider openai openai:default

Verify that the Codex runtime reports usable authentication before starting
expensive implementation work.

Do not infer Codex authentication solely from the selected model name.

---

## 10. Configure Development Workers

Workers are ephemeral sub-agents created by dev-lumi.

Do not create persistent worker profiles unless a future workflow specifically
requires them.

Set the current `model.level.3` binding as the default worker model:

    openclaw config set agents.entries.dev-lumi.subagents.model.primary openai/gpt-5.6-luna

Prefer worker delegation when useful:

    openclaw config set agents.entries.dev-lumi.subagents.delegationMode prefer

Set the shared worker concurrency limit:

    openclaw config set agents.defaults.subagents.maxConcurrent 4

Set the maximum spawn depth:

    openclaw config set agents.defaults.subagents.maxSpawnDepth 2

The reference worker archive delay is:

    60 minutes

If the installed OpenClaw version exposes archive delay configuration, verify
that it remains 60 minutes unless intentionally changed.

Worker model overrides must follow `ORCHESTRATION.md`.

A complex bounded worker may use `model.level.2`.

`model.level.0` must not be used as a normal implementation worker.

---

## 11. Create reviewer-lumi

Create the persistent Independent Review profile with the current
`model.level.1` binding:

    openclaw agents add reviewer-lumi \
      --workspace ~/.openclaw/workspace \
      --model openai/gpt-5.6-sol \
      --non-interactive

Set the ordinary Independent Review reasoning option:

    openclaw config set agents.entries.reviewer-lumi.thinkingDefault high

Pin the current `model.level.1` and `model.level.0` models to Codex so the
review type can select the correct logical route:

    openclaw config set 'agents.entries.reviewer-lumi.models["openai/gpt-5.6-sol"].agentRuntime.id' codex
    openclaw config set 'agents.entries.reviewer-lumi.models["openai/gpt-6-astra"].agentRuntime.id' codex

Optional reference identity:

    openclaw config set agents.entries.reviewer-lumi.identity.name reviewer-lumi
    openclaw config set agents.entries.reviewer-lumi.identity.emoji "🔎"

When the expected Codex OAuth profile is named `openai:default`:

    openclaw models auth --agent reviewer-lumi order set --provider openai openai:default

The persistent reviewer profile is only a runtime role configuration.

Every actual Independent Review must still use a fresh review session isolated
from implementation sessions. Ordinary and intermediate Independent Review use
`model.level.1`; only Final Independent Review selects `model.level.0` and its
configured provider options.

---

## 12. Gateway Secret

When OpenClaw Gateway authentication is enabled, keep its token separate from
the OpenAI API key.

A typical environment secret name is:

    OPENCLAW_GATEWAY_TOKEN

The OpenAI API key and Gateway token must never be mapped to the same secret by
accident.

If an explicit environment-secret allowlist is configured, preserve all
required secret names when updating it rather than replacing unrelated entries.

Never store the Gateway token in the LUMI repository.

---

## 13. Verify Agent Configuration

List agents:

    openclaw agents list

Verify model status independently for each persistent agent:

    openclaw models status --agent main
    openclaw models status --agent dev-lumi
    openclaw models status --agent reviewer-lumi

Expected adapter defaults resolved from the canonical configuration:

    main
    → openai/gpt-5.6-luna
    → xhigh
    → OpenClaw runtime

    dev-lumi
    → openai/gpt-5.6-luna
    → xhigh
    → Codex runtime

    reviewer-lumi ordinary review
    → model.level.1
    → openai/gpt-5.6-sol
    → high
    → Codex runtime

    reviewer-lumi final review
    → model.level.0
    → openai/gpt-6-astra
    → xhigh
    → Codex runtime

Verify the dev-lumi worker configuration:

    openclaw config get agents.entries.dev-lumi.subagents

Verify shared sub-agent limits:

    openclaw config get agents.defaults.subagents

Expected reference values include:

    maxConcurrent: 4
    maxSpawnDepth: 2

---

## 14. Verify Secrets

Use OpenClaw's read-only secret audit when available:

    openclaw secrets audit --check

The expected result is no plaintext, unresolved, shadowed, residual, or legacy
secret finding.

Do not print actual secret values as part of verification.

---

## 15. Verify Gateway Health

Verify normal Gateway state using the installed OpenClaw version's supported
status command.

For example:

    openclaw gateway status

A healthy local installation should be verified through current runtime state
rather than inferred from documentation.

Do not run repair or migration operations merely because setup documentation
mentions them.

---

## 16. Runtime Verification

Model configuration alone is insufficient evidence of runtime selection.

When runtime identity matters, verify the active runtime through OpenClaw's
supported status information.

For Codex-backed work, confirm that the runtime is actually Codex before
starting expensive implementation, planning, or review work.

Do not silently fall back from Codex to direct API execution when the configured
workflow explicitly requires Codex.

---

## 17. Post-Setup Invariants

After setup, the following should remain true:

1. The LUMI Git repository and OpenClaw workspace are separate.
2. `main` is the normal user-facing agent.
3. `main` defaults to `model.level.3` through the OpenClaw runtime.
4. `dev-lumi` defaults to `model.level.3` through Codex.
5. `model.level.2` is available for heavy execution.
6. `model.level.1` handles complex orchestration and Independent Review.
7. Workers are ephemeral and default to `model.level.3`.
8. `model.level.0` is not a normal implementation-worker route.
9. `reviewer-lumi` uses `model.level.1` normally and `model.level.0` only for
   Final Independent Review.
10. Independent Review sessions remain fresh and read-only.
11. API authentication and Codex authentication remain conceptually separate.
12. Gateway authentication and OpenAI authentication remain separate.
13. No plaintext secret is committed to the LUMI repository.
14. `config/model-levels.yaml` remains the canonical physical binding source.

---

## 18. Updating the Adapter

When OpenClaw changes:

1. Verify the new supported configuration schema.
2. Update adapter documentation rather than shared LUMI behavioral policy when
   the change is runtime-specific.
3. Preserve the logical roles defined in `ORCHESTRATION.md`.
4. Re-run runtime, model, worker, and secret verification.
5. Document breaking adapter changes clearly.

Do not modify LUMI's core identity merely because a runtime implementation
changes.
