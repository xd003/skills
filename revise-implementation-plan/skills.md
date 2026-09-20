---
name: revise-implementation-plan
description: Revise an existing implementation plan after a discovered implementation problem. Determine whether the problem was caused by an execution failure, plan omission, or plan defect, then strengthen validation, append missing coverage, or correct the affected plan guidance so future executions do not repeat the problem. Invoke manually only.
license: MIT
metadata:
  version: "1.0"
---

# Revise Implementation Plan

Revise an existing implementation plan after a problem is discovered during or after implementation.

The goal is not merely to document the fix. Determine why the original plan failed to prevent the problem, then improve the plan so a fresh implementation session following the revised plan would avoid the same failure.

The plan remains the canonical implementation contract, not a changelog or postmortem.

## Input

The user must identify the existing `.PLAN.md` and describe or reference the discovered problem.

Use available evidence to understand what happened. This may include:

- the implementation that followed the plan;
- the commit or diff that fixed the problem;
- relevant tests;
- repository history;
- the user's description of the failure.

If the user references a fix commit, inspect it and its parent state. If the problem was fixed in the current working tree, inspect the relevant diff.

Never ask the user for something the repository can answer.

## Classify the failure

Before changing the plan, determine which of these categories applies.

### 1. Execution failure

The plan already specified the correct behavior with enough detail, but the implementation failed to follow it correctly.

Do not rewrite correct guidance merely to make it more forceful.

Instead, strengthen validation or acceptance criteria so the incorrect implementation would be detected before the work is considered complete.

The validation must make clear:

- what scenario is exercised;
- what result is expected;
- what constitutes failure.

Prefer an automated regression test when practical. Otherwise add a deterministic verification.

### 2. Plan omission

The plan did not account for the discovered requirement, edge case, interaction, failure mode, or necessary implementation work.

Preserve the existing correct plan and append a new implementation step covering the missing work.

The new step must explain:

- the previously uncovered case;
- the required behavior;
- where the implementation belongs;
- how it integrates with the existing implementation;
- how it is verified.

Add or update acceptance criteria as needed.

### 3. Plan defect

The plan explicitly specified behavior or an implementation approach that is incorrect, incomplete, or incompatible with the actual system.

Correct the affected section in place.

Remove or replace incorrect guidance rather than leaving contradictory historical instructions in the executable plan.

Update any dependent steps, snippets, assumptions, validation, and acceptance criteria affected by the correction.

## Workflow

1. Read the complete implementation plan.

2. Inspect the discovered problem and available implementation evidence. Trace the relevant code far enough to understand both the intended behavior and why the problem occurred.

3. Compare the evidence against the original plan. Determine whether this is an execution failure, plan omission, or plan defect. Classify based on evidence, not merely on what the eventual code fix changed.

4. Before editing, briefly report:
   - the discovered problem;
   - the root cause;
   - the classification;
   - the evidence supporting that classification;
   - which parts of the plan need revision.

5. Revise the existing `.PLAN.md` in place according to the classification rules above.

6. Ensure the plan contains a concrete validation that would detect the discovered problem if it were introduced again.

7. Review the complete revised plan for consistency.

## Revision rules

Preserve all correct existing guidance.

Make the smallest revision that fully captures what was learned.

Do not add speculative requirements or unrelated edge cases.

Do not weaken requirements merely to match the current implementation.

Do not turn the plan into a history of what went wrong. The plan describes how the feature should be implemented correctly from scratch.

Do not add wording such as "this was previously broken", "the original implementation forgot", or references to bug-fix commits unless that historical information is genuinely required to implement the feature.

For an execution failure, prefer stronger verification over stronger prose. If the plan already says exactly what must happen, repeating it with "must", "strictly", or similar wording does not solve the failure.

For a plan omission, append the missing implementation work rather than unnecessarily rewriting unrelated existing steps.

For a plan defect, correct the original instruction where it appears so a fresh execution session never encounters the obsolete guidance.

Keep the existing plan structure and conventions established by `create-implementation-plan`.

## Final verification

Before finishing, verify all of the following:

1. A fresh agent reading only the revised plan would know the correct intended behavior.
2. Correctly following the revised plan would prevent the discovered problem.
3. The plan contains validation capable of detecting a regression of the problem.
4. No correct original requirements were accidentally changed.
5. No contradictory old and new instructions remain.
6. The revision introduces no unrelated scope or unnecessary complexity.
7. The plan remains self-contained and executable without knowledge of the incident that caused the revision.

If any of these checks fail, revise the plan again before finishing.

## Boundaries

- Modify only the referenced implementation plan.
- Do not modify source code.
- Do not implement or re-fix the bug.
- Do not commit or push changes.
- Do not create a second version of the plan unless explicitly requested.
- Do not guess when the codebase can answer the question.
- If classification or the correct revised behavior cannot be determined from the plan, code, history, tests, or fix, ask the user rather than inventing an answer.

## Completion

State:

- the classification;
- what was changed in the plan;
- what validation was added or strengthened;
- the project-relative path of the revised plan.

The revised `.PLAN.md` is now the canonical implementation contract for future implementation and review sessions.
