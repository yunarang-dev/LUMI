# GIT_WORKFLOW.md — Shared Development Git Workflow

## Purpose

This document defines the shared LUMI Git and remote-repository workflow for
managed development projects.

The goals are to:

- preserve existing user work;
- keep Git history clear and traceable;
- separate implementation from Git orchestration;
- prevent development workers from independently mutating repository history;
- verify local and remote state before destructive or publishing operations;
- maintain consistent branch, commit, merge, and push behavior.

Project-specific policy may refine this workflow when explicitly configured.

---

## 1. Git Operation Authority

LUMI owns repository-wide Git orchestration.

Implementation agents and workers may perform development work such as:

- writing or modifying code;
- refactoring;
- debugging;
- tests;
- documentation;
- project-content changes;
- implementation-level conflict resolution.

They may use read-only Git inspection when useful.

Examples:

    git status
    git diff
    git log
    git show

Implementation agents and workers must not independently perform Git operations
that modify repository state, history, branches, tags, or remotes.

Git mutation reserved for LUMI includes:

- branch creation, deletion, and switching;
- staging;
- commit;
- merge;
- rebase;
- revert;
- reset;
- restore;
- stash creation, application, or deletion;
- tag creation or deletion;
- git clean;
- push;
- remote configuration;
- other repository-wide history or state mutation.

After implementation completes, LUMI inspects the result and performs the
applicable Git workflow.

---

## 2. Commit Message Format

The shared default format is:

    <type>: <Description>

The Description must:

- be written in English;
- describe the resulting change concisely;
- prefer imperative wording;
- capitalize the first letter of major words;
- avoid unnecessary detail.

Examples:

    feat: Add Movement System
    fix: Fix Player Collision
    set: Update Project Settings
    docs: Update Character Design
    refactor: Refactor Player Controller
    test: Add Movement Tests
    perf: Optimize Enemy Detection
    build: Update Package Dependencies
    revert: Revert Movement Refactor
    asset: Add Character Illustrations

Commit messages describe the change.

They do not encode the identity of the person or agent that executed the Git
command.

Projects requiring explicit contributor prefixes or another commit convention
must define that as a project-specific override.

---

## 3. Change Types

Each commit should use exactly one primary change type.

Supported shared types:

- `feat:` — feature addition or meaningful feature update;
- `fix:` — bug or issue fix;
- `set:` — project, environment, or base configuration change;
- `docs:` — documentation or planning-document change;
- `refactor:` — structural improvement without intended behavior change;
- `test:` — test addition or modification;
- `perf:` — performance optimization;
- `build:` — build system, package, dependency, or packaging change;
- `revert:` — intentional reversal of a previous change;
- `asset:` — project asset addition or modification.

When a change could fit multiple types, choose the type that best describes its
primary resulting purpose.

Examples:

- cleanup that adds functionality → `feat:`
- structural work primarily for performance → `perf:`
- structural cleanup performed as part of a bug fix → `fix:`
- configuration-only change → `set:`
- dependency-only change → `build:`
- resource-only change → `asset:`
- behavior-preserving code restructuring → `refactor:`
- tests only → `test:`
- documentation only → `docs:`

---

## 4. One Type per Commit

Each commit uses one primary type.

When independent changes have different purposes, split them into separate
commits whenever practical.

For example:

    asset: Add Character Model
    feat: Add Character Switching
    docs: Update Character Switching Design

A small inseparable support change may remain with the primary implementation.

Examples include:

- required generated metadata;
- minimal configuration required by the feature;
- a small support file required for associated tests.

If a change has independent meaning, prefer a separate commit.

---

## 5. Branch Naming

Managed development should not normally be implemented directly on the
project's protected or configured default branch.

The default branch is supplied by the active project profile.

If none is configured, use:

    main

Work branches use:

    <type>/<short-description>

The description uses lowercase English kebab-case.

Examples:

    feat/add-movement-system
    fix/player-collision
    set/update-project-settings
    docs/update-character-design
    refactor/player-controller
    asset/add-character-illustrations

The branch type represents the primary purpose of the overall workstream.

Individual commits inside that branch may use different types when appropriate.

---

## 6. Protect Existing Work

Before repository-changing Git operations, inspect the current Working Tree.

If staged or unstaged changes already exist:

- do not delete them;
- do not overwrite them;
- inspect `git status`;
- inspect relevant diffs;
- determine their origin and purpose when reasonably possible;
- preserve current state by default;
- do not arbitrarily commit, stash, reset, restore, or move unfinished work;
- isolate new work only when doing so is actually safe and useful;
- do not modify or remove changes whose purpose is unclear.

Commands requiring particular care include:

- `git pull`;
- `git rebase`;
- `git reset`;
- `git restore`;
- `git checkout`;
- `git stash`;
- `git clean`.

Preserving existing work takes priority over simplifying the Git workflow.

---

## 7. Synchronizing the Default Branch

Before creating or integrating work, inspect local and remote state.

Recommended procedure:

1. Inspect the current branch and Working Tree.
2. Run `git fetch`.
3. Compare the local default branch with its remote tracking branch.
4. Determine whether a safe fast-forward is possible.
5. Fast-forward only when current state makes it safe.
6. If histories diverged unexpectedly, do not automatically merge or rebase.

Do not run `git pull` blindly.

`git pull` behavior depends on local configuration and may perform an
unintended merge or rebase.

When local and remote history diverge unexpectedly:

- preserve current state;
- inspect the commits involved;
- do not choose a reconciliation strategy arbitrarily;
- report or resolve the situation according to the active project policy.

---

## 8. Default Branch Workflow

The normal managed workflow is:

1. Inspect current branch and repository state.
2. Inspect staged and unstaged changes.
3. Preserve pre-existing work.
4. Inspect remote state when applicable.
5. Fetch remote references when applicable.
6. Safely synchronize the configured default branch.
7. Determine the primary work type.
8. Create a work branch.
9. Perform implementation through the normal development workflow.
10. Validate the result.
11. Inspect `git status` and relevant diffs.
12. Split independent changes into separate commits when useful.
13. Commit using the shared or configured project convention.
14. Return to the configured default branch.
15. Reinspect remote state when remote integration is intended.
16. Confirm that the default branch remains safe for integration.
17. Merge the work branch.
18. Confirm that no unresolved conflict remains.
19. Perform final validation when required.
20. Publish remote state only when the requested or configured workflow includes
    remote publication.
21. Verify the expected remote result when publication occurred.
22. Remove the local work branch when appropriate.

If pre-existing changes make this sequence unsafe, preserving those changes
takes priority and LUMI may adjust the sequence.

---

## 9. Remote Work Branches

Work branches should normally remain local unless remote publication provides a
specific benefit.

Typical local flow:

    Local Work Branch
    → Local Default Branch Merge
    → Remote Default Branch Push

Publish a work branch when:

- a Pull Request is required;
- another collaborator requires the branch;
- CI or another workflow requires a remote branch;
- remote preservation is intentionally desired;
- the user explicitly requests it.

Do not publish branches merely because they exist.

---

## 10. Merge Strategy

The shared default is a non-fast-forward merge for managed work branches:

    git merge --no-ff

This preserves the workstream boundary in history.

A project may override the merge strategy explicitly.

Merge commit messages follow the normal commit-message convention.

Format:

    <type>: Merge <Description>

Examples:

    feat: Merge Movement System
    fix: Merge Player Collision Fix
    asset: Merge Character Illustrations

Use the primary work-branch type for the merge commit unless project policy
defines otherwise.

Do not keep an uninformative automatic message when the configured workflow
requires a descriptive merge commit.

---

## 11. Merge Conflict Handling

If a merge conflict occurs, LUMI coordinates the resolution.

Implementation agents may:

- inspect conflicted code;
- analyze the cause;
- recommend a resolution;
- modify project files when implementation-level correction is needed.

Implementation agents must not independently:

- stage the final resolution;
- complete the Git merge;
- rewrite repository history.

LUMI verifies the resolved state and completes Git integration.

Do not discard one side of a conflict merely to make the merge succeed.

---

## 12. Pre-Commit Verification

Before every commit, inspect:

- `git status`;
- staged changes;
- unstaged changes;
- relevant diffs;
- newly added files;
- deleted files;
- unrelated changes.

Do not include unrelated work in the same commit.

Pay special attention to unintended files such as:

- temporary files;
- logs;
- caches;
- unnecessary generated files;
- local environment files;
- test artifacts;
- personal configuration;
- credentials or secrets;
- files whose purpose is unclear.

Whenever practical, stage only files related to the current commit.

If a broad staging command such as `git add .` is used, inspect the staged result
before committing.

---

## 13. Mixed Contributors

Commit messages describe the resulting change rather than encoding contributor
identity.

Preserve actual Git authorship and repository attribution policy where
applicable.

When multiple contributors create separable logical changes, prefer separate
commits when that improves traceability.

Do not claim that LUMI created user-authored content merely because LUMI
performed the commit.

Projects that require explicit contributor attribution inside commit messages
may define a project-specific convention.

---

## 14. Dangerous Git Operations

The following operations may destroy work or rewrite history and require
special caution:

- `git reset --hard`;
- `git clean -f`;
- `git clean -fd`;
- force push;
- forced remote-branch deletion;
- rewriting existing commit history;
- large or destructive rebases;
- forced restoration of tracked files;
- discarding uncommitted changes;
- arbitrary application or deletion of existing stashes.

These operations are not part of the normal workflow.

Before any such operation:

1. inspect current repository state;
2. understand what will be affected;
3. preserve unrelated work;
4. obtain explicit user authorization when the action can destroy or rewrite
   existing work.

Never automatically delete:

- user-created work;
- uncommitted work;
- work whose origin or purpose is unclear.

---

## 15. Remote Publication

Local implementation does not automatically imply remote publication.

Push only when:

- the user requested publication;
- the active project workflow explicitly includes publication;
- or another authorized workflow requires it.

Before pushing:

- inspect the target branch;
- inspect the remote;
- verify the commits to be published;
- verify that unintended files or history are not included.

After pushing:

- verify that the expected remote branch advanced;
- verify that the expected commits are present;
- do not report remote completion solely because `git push` returned without an
  obvious local error.

---

## 16. Git Task Completion Criteria

A Git task is complete only when all applicable conditions are satisfied.

Depending on scope:

- requested implementation exists;
- required validation succeeded or its limitation is recorded;
- existing work remains intact;
- no unintended files were committed;
- commit messages follow the configured convention;
- required branch integration completed;
- no unresolved merge conflict remains;
- the configured default branch is valid;
- requested remote publication completed;
- the expected remote result was verified.

A task that does not require remote publication does not require a push merely
to satisfy this workflow.

If an error occurs, report:

- actual Git state;
- the failed step;
- what completed successfully;
- what remains unresolved.

Never report Git or remote completion when the relevant final state has not
actually been verified.

---

## 17. Project Overrides

The active project profile may override details such as:

- default branch;
- whether work branches are mandatory;
- branch naming;
- commit types;
- commit-message format;
- contributor attribution;
- merge strategy;
- remote branch policy;
- publication requirements.

Project-specific policy should override only the necessary details.

Do not duplicate the entire shared Git workflow when a small project override is
sufficient.

Shared safety rules for preserving user work and destructive operations remain
applicable unless a higher-priority instruction explicitly requires otherwise.
