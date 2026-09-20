---
name: rebase-upstream
description: Rebase the current branch onto the latest upstream base branch while preserving branch intent and upstream changes. Inspect remotes, fetch upstream, resolve conflicts based on intent, detect functionality already implemented upstream, and validate the complete rebased result. Invoke manually only.
license: MIT
metadata:
  version: "1.0"
---

# Rebase Upstream

Rebase the current branch onto the latest upstream base branch while preserving both the intent of the existing work and the latest upstream behavior.

The final branch should contain the minimum changes necessary to express the branch's intended behavior cleanly on top of current upstream.

Do not preserve obsolete implementation details merely because they existed before the rebase. Do not silently discard intended behavior or valid upstream changes.

## 1. Establish the rebase target

Before modifying anything:

1. Inspect the current branch and working tree.
2. Inspect configured Git remotes.
3. Identify the upstream repository.
4. Identify the appropriate upstream base branch, normally `main`.
5. Fetch the latest upstream state.

Do not add, remove, rename, or modify Git remotes.

If the upstream repository or base branch is missing or genuinely ambiguous, stop and ask the user.

If the working tree contains changes that could interfere with the rebase, stop and explain them rather than modifying, committing, stashing, or discarding them automatically.

## 2. Establish branch intent

Before rebasing, understand what the current branch is intended to accomplish.

Inspect:

- commits unique to the current branch;
- the complete branch diff against its original base where determinable;
- relevant implementation and tests;
- commit messages;
- available PR descriptions, issues, or other repository context when accessible.

Build a concise understanding of the behaviors and requirements that must survive the rebase.

Treat the existing PR or branch as the source of truth for intended behavior.

Do not confuse implementation with intent. Exact code may become obsolete because of upstream changes while the behavior it was intended to provide remains required.

## 3. Inspect relevant upstream changes

Inspect relevant changes that landed upstream since the branch diverged.

Pay particular attention to upstream changes affecting the same:

- files;
- functions;
- components;
- APIs;
- schemas;
- tests;
- behaviors;
- architectural areas.

Look for semantic overlap, not only textual overlap.

A clean Git rebase does not prove that the resulting implementation is semantically correct.

## 4. Rebase onto upstream

Rebase the current branch onto the fetched upstream base branch.

Preserve the existing commit structure unless changing it is necessary for a correct rebase.

Resolve straightforward conflicts using the conflict-resolution procedure below.

Make only the minimum changes necessary for the rebase.

Do not introduce unrelated refactors, cleanup, or new behavior.

## 5. Conflict resolution

For every conflict:

1. Inspect the current rebase state, relevant history, conflicting files, and conflicting hunks.

2. Find the primary sources for both sides of the conflict. Understand why each change exists and what behavior it was intended to provide. Use commit messages, PR context, issues, tests, and surrounding code where available.

3. Resolve each hunk according to intent rather than mechanically choosing ours or theirs.

4. Preserve both intents when they are compatible.

5. When upstream has changed the surrounding architecture, adapt the branch implementation to the current upstream structure while preserving its intended behavior.

6. Do not invent new behavior merely to resolve a conflict.

7. Stage resolved files and continue the rebase.

Repeat this process until all commits have been rebased.

If a conflict exposes a genuine product, design, or architectural decision that cannot be resolved from existing intent and repository evidence, do not guess. Leave the rebase paused and ask the user.

## 6. Detect upstream overlap

Upstream may have independently implemented functionality that overlaps with the current branch.

This can occur with or without a Git conflict.

When overlap is found, compare the implementations semantically.

Determine:

- which branch requirements the upstream implementation satisfies;
- whether their observable behavior is equivalent;
- whether either implementation supports cases the other does not;
- whether existing tests demonstrate equivalent behavior;
- how each implementation fits the current upstream architecture;
- whether retaining both would create duplication, conflicting behavior, or unnecessary complexity.

Do not assume code is equivalent merely because it appears to solve the same problem.

### Upstream fully supersedes branch functionality

If upstream now provides the same intended behavior and its implementation appears preferable because it is more complete, better integrated with current upstream, simpler, or otherwise makes the branch implementation redundant, stop before dropping the branch implementation.

Explain to the user:

- what functionality overlaps;
- what the branch implementation provides;
- what upstream now provides;
- why upstream appears to supersede the branch implementation;
- whether any behavior or edge cases differ;
- what branch code or commits would become unnecessary.

Ask for confirmation before allowing the upstream implementation to supersede the branch implementation.

If approved, prefer upstream and remove or adapt only the redundant branch portion.

Do not retain duplicate functionality merely to preserve the original code.

### Upstream partially supersedes branch functionality

If upstream implements only part of the intended behavior, preserve the upstream implementation and adapt the branch to provide only the remaining required behavior where this can be done without changing intent.

Avoid maintaining a parallel implementation of functionality upstream already provides.

If deciding how responsibilities should be divided requires a genuine design or architectural decision, leave the rebase paused and ask the user.

### Upstream does not supersede branch functionality

If upstream changes the surrounding implementation but does not provide the branch's intended behavior, adapt the branch implementation to current upstream while preserving its intent.

## 7. Decision boundary

Proceed autonomously when the correct resolution follows clearly from:

- existing branch or PR intent;
- upstream behavior;
- tests;
- commit history;
- established architecture;
- straightforward compatibility requirements.

Stop and ask the user when resolution requires choosing between legitimate alternatives involving:

- architecture;
- product behavior;
- public API semantics;
- data model changes;
- scope changes;
- intentionally dropping intended branch behavior;
- replacing branch functionality with an upstream implementation.

Do not disguise a design decision as conflict resolution.

## 8. Validate the rebased result

After the rebase completes, do not consider the task finished merely because Git reports success.

Review the complete resulting diff against current upstream.

Compare the resulting branch with the original branch intent and, where useful, the original branch diff.

Verify that:

1. Every intended behavior is still present or was explicitly approved as superseded.
2. No valid upstream behavior was accidentally reverted.
3. No conflict resolution silently dropped functionality.
4. No duplicate implementation remains where upstream and the branch now solve the same problem.
5. The branch does not restore code, assumptions, or behavior intentionally removed upstream.
6. The resulting implementation follows the current upstream architecture.
7. The resulting diff contains no unnecessary rebase-only changes.
8. Each rebased commit remains coherent relative to its intended purpose.

Pay particular attention to semantic conflicts that Git could not detect.

## 9. Run project validation

Discover and run the relevant automated checks supported by the repository.

Use the project's established validation workflow where available. Depending on the project, this may include:

- type checking;
- targeted tests;
- regression tests;
- broader test suites;
- linting;
- formatting checks;
- builds.

Fix issues introduced by the rebase when the correct fix follows clearly from existing intent.

Do not use validation failures as an opportunity for unrelated cleanup.

If a failure exposes an architectural or behavioral decision, stop and ask the user.

If validation cannot be run, state exactly what could not be verified and why.

Never claim validation succeeded unless it was actually run.

## 10. Final review

Before finishing:

1. Inspect the final commit history.
2. Inspect the complete diff against current upstream.
3. Compare the result against the original branch intent.
4. Review all conflict resolutions and upstream-driven adaptations.
5. Check for accidental behavior loss, duplicated functionality, stale assumptions, and conflict-resolution artifacts.
6. Confirm that no unrelated changes were introduced.

The branch should be ready for review when this process finishes.

## Boundaries

- Do not push or force-push unless explicitly requested.
- Do not modify Git remotes.
- Do not silently drop commits or intended behavior.
- Do not automatically replace branch functionality with an upstream implementation without user confirmation.
- Do not introduce unrelated refactors or cleanup.
- Do not treat a conflict-free or successful Git rebase as proof of semantic correctness.
- Do not make product, design, or architectural decisions on the user's behalf.
- Do not abort an in-progress rebase merely because a difficult conflict is encountered. If user input is required, leave it paused and ask.

## Completion

Report:

- the upstream repository and base branch used;
- whether conflicts occurred;
- important conflict resolutions;
- adaptations made because of upstream changes;
- any upstream functionality that superseded branch functionality with user approval;
- validation performed and its results;
- anything that could not be verified.

The final branch should represent the original work's intended behavior cleanly on top of the latest upstream state.
