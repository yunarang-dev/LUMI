# ROUTING.md — Lumi Project Routing

This file resolves project identifiers and agent/runtime contexts to project
profiles.

The public LUMI repository provides `ROUTING.example.md` as a template.

A deployed Lumi installation should maintain its actual routing configuration
as `projects/ROUTING.md`.

Do not store secrets or credentials here.

---

## Principles

Project routing must be explicit.

Do not select an active project solely from:

- memory,
- recent conversation history,
- repository-name similarity,
- filesystem proximity,
- another agent's previous session,
- or guessed intent.

A project may be selected from:

1. an explicit project identifier in the user's current request;
2. a configured agent default;
3. another explicit routing mechanism defined in this file.

If routing remains ambiguous, stop project-specific execution and ask for or
report the missing routing information.

---

## Project Registry

Register each known project with a stable project ID.

| Project ID | Project Name | Profile |
| --- | --- | --- |
| example-project | Example Project | projects/local/example-project/PROJECT.md |

Project IDs should be:

- stable,
- unique,
- lowercase when practical,
- independent of local filesystem paths.

The profile path is relative to the LUMI workspace unless explicitly documented
otherwise.

---

## Project Aliases

Aliases may be used when the user commonly refers to a project by another name.

| Alias | Project ID |
| --- | --- |
| example | example-project |

Aliases must resolve uniquely.

Do not create aliases that can reasonably refer to multiple projects.

---

## Agent Defaults

An agent may optionally have a default project.

| Agent ID | Default Project |
| --- | --- |
| example-agent | example-project |

A default project is a routing convenience, not permission to ignore an
explicit project selection in the user's current request.

If an agent is intentionally project-neutral, omit it from this table.

Generic agents such as `main` or `dev-lumi` do not need a default project.

---

## Resolution Order

When project-specific work begins:

1. Check whether the user's current request explicitly identifies a registered
   project or unambiguous alias.
2. Otherwise check whether the current agent has a configured default project.
3. Resolve the corresponding profile from the Project Registry.
4. Verify that the profile exists and is readable.
5. Read that project's `PROJECT.md` when required.
6. Use only the paths, procedures, planning sources, and workflow configuration
   declared by that profile.

If more than one project matches, do not guess.

If no project matches and project-specific execution is required, stop and
request clarification.

---

## Development Delegation

Project selection and development-agent selection are separate decisions.

For example:

- `main` may resolve `example-project`;
- `main` may then delegate development execution to `dev-lumi`;
- `dev-lumi` continues using the same explicitly resolved project profile.

Delegation must not silently change the active project.

When passing work between agents, include the canonical Project ID and resolved
profile path in the task context when practical.

For state-changing execution, also preserve the bounded execution scope
established through trusted LUMI delegation. A child may narrow that scope but
must not expand it or reuse it for another project, task, or session.

---

## Safety

Routing information is configuration, not authorization.

Project routing and execution authorization are separate concerns. Resolving
the same Project ID does not create, renew, or expand mutation authority.

A valid route does not bypass:

- the Lumi Execution Activation Gate,
- project-specific permissions,
- Git safety,
- external-action confirmation,
- destructive-operation restrictions,
- or platform-level security policy.

Do not place secrets, credentials, tokens, or private keys in this file.
