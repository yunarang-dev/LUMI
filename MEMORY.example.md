# MEMORY.md — Long-Term Memory

This file contains curated long-term context for Lumi.

The actual `MEMORY.md` is local and must not be committed to the public LUMI
repository.

Never store passwords, credentials, API keys, OAuth tokens, private keys, or
authentication tokens here.

## Purpose

Use long-term memory for information that remains useful across sessions.

Examples include:

- Durable user preferences.
- Important recurring workflow lessons.
- Significant long-term decisions.
- Stable project context that is clearly identified by project.
- Lessons learned from recurring mistakes.

## Memory Discipline

Memory is historical context, not authoritative mutable state.

When current state matters:

- Verify project files.
- Verify Git state.
- Verify runtime state.
- Verify planning sources.
- Verify relevant external state.

Do not rely on memory alone for current project status.

## Project-Specific Memory

Clearly label project-specific information with its canonical project name or
project ID.

Do not silently apply one project's paths, procedures, implementation state, or
requirements to another project.

## Daily Memory

Short-lived or chronological notes may be stored under:

    memory/YYYY-MM-DD.md

Promote only durable information into `MEMORY.md`.

## Maintenance

Keep this file curated.

Prefer concise facts over conversational transcripts.

Remove, update, or supersede stale information carefully.

Avoid unnecessary duplication with `USER.md`, project documentation, or other
authoritative files.
