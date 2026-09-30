---
name: grill-agent
description: When the user asks an agent to question itself, challenge a plan, or check its work before acting, have the executing agent ask, investigate, and answer its own questions instead of defaulting to a user interview.
---

# Agent self-questioning

Treat the current task as a proposal under review. Raise questions that could change your approach or conclusion, investigate them, and answer them yourself. Do not call other agents by default or hand the user a list of questions.

Explore only the critical branches relevant to this task: the actual goal, what existing files or runtime state support, the assumption most likely to be wrong, the smallest workable approach, and how to verify the result. Leave questions that depend on an earlier answer for the next round. When you find a problem, revise your approach and recheck the affected branches. Stop when no unresolved question would change the action.

For a proposal with material consequences, first identify a concrete counterexample or failure condition that would overturn a key conclusion. Then check it against actual files, tool results, or a minimal verification. Revise or abandon a proposal that the evidence does not support; do not invent reasons to defend it. Failing to find a counterexample does not mean validation has passed. If verification is unavailable, keep the claim unverified. Do not fabricate counterexamples or pad simple tasks with them.

Choose a direct approach that fully satisfies the current task, and identify and remove unnecessary steps, abstractions, or configuration from the plan. Every change should trace back to the user's request or a necessary supporting adjustment. Complete the required adjustments to affected callers, data, tests, and documentation. You may point out unrelated issues, but do not fix them along the way. Preserve checks and protections that serve a real purpose. If their role is unclear, inspect the relevant path first; missing evidence does not establish that a protection is redundant.

Before execution, define observable completion criteria; afterward, check the actual results against them and satisfy the project's required checks. When fixing a problem, prefer a minimal reproduction to confirm it, then verify the fix. Explicitly leave anything that cannot be verified as unverified. Reuse existing evidence while it remains valid for the final state. Finish when the requested result is delivered, acceptance requirements are met, temporary-file cleanup within the authorized scope is complete, and no known in-scope blocker remains. Do not add another verification loop just for self-review.

Before creating files, distinguish final deliverables, files needed for maintenance or reproduction, and disposable intermediate artifacts. Follow the project's directory conventions. If none exist, keep this task's temporary artifacts in a separate `work/<task-name>/` directory and track their purpose and ownership in task context, without creating another manifest file for this purpose. Do not scatter temporary artifacts in the project root or include pre-existing files or another task's files in cleanup.

After verifying deliverables, use existing deletion authorization to remove temporary files created by this task whose purpose has ended and which are no longer needed by deliverables, reproduction workflows, or running tasks. Retain necessary regression tests, generation scripts, diagnostic material, and recovery backups. Do not infer disposability from filenames or untracked Git status. Before deletion, verify resolved absolute paths and the allowed scope; do not follow symbolic links or directory junctions into out-of-scope locations. Keep files whose ownership, purpose, or deletion permission is unclear and report them together. When confirmation is required, present candidates and reasons under the host's rules; do not request authorization again for the same already-authorized scope. After cleanup, check that deliverables and their references still work, and briefly report removed and pending items. Never claim that unauthorized cleanup was completed.

Classify each answer:

- **Confirmed**: Supported by user statements, files, code, tool results, or other checkable evidence.
- **Reasonable assumption**: Evidence is incomplete, but a low-risk, reversible default is available. State the assumption and how to verify it.
- **Requires a user decision**: Business information is missing and cannot be obtained independently, or different choices would materially change the outcome, permissions, cost, or external impact.

Investigate facts yourself first. Never treat guesses as evidence or user authorization. Only bring the third category to the user: group the necessary questions, keep them brief, and suggest options. While waiting, continue work that does not depend on the answer. After the self-check, proceed with work already authorized by the current request without adding another start-work confirmation. External publication, spending, production changes, and similar actions still follow the existing approval boundaries.

For decisions with material consequences, include at most three concise records of “key question → evidence → decision” with the result. Prioritize findings that overturned an assumption or changed the approach. Point to actual file locations, tool results, or verification results; explicitly label checks that were not performed as unverified. Do not add a fixed table to simple tasks. If the user wants more explanation, provide checkable supporting evidence and a summary of the conclusions.
