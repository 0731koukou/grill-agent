# I built a skill that asks the agent to question itself first

[简体中文](../zh/推广介绍.md) | **English**

One frustrating part of working with an AI agent is answering questions that the project already answers. The code, configuration, and documentation are there, yet the agent hands you a questionnaire. Once you finish, another round of confirmation begins. Meanwhile, the assumption that matters most may never get checked.

I built `grill-agent` to try to improve that workflow.

It asks the executing agent to examine its own plan: What does this task actually need to accomplish? Which assumptions does the approach depend on? What do the existing files support? If the conclusion is wrong, what counterexample could reveal that early? The agent investigates and answers those questions itself, bringing back the parts that genuinely need a user decision.

Project: [0731koukou/grill-agent](https://github.com/0731koukou/grill-agent).

## An alarm-counting example

One test provided three things: a CSV of the current alarm batch, a set of counting rules, and a summary left over from the previous batch.

The rules were explicit: `open` and `acknowledged` count as unresolved; `closed` does not. Devices with zero unresolved alarms must still appear. The old summary reported zero for all three devices.

The agent first needed to check whether that all-zero summary represented the current data.

In the test, it read the rules and source records, identified A1, A2, and A5 as unresolved, and recomputed the counts: 2 for P-101, 1 for P-102, and 0 for P-103. It also explained that the old summary belonged to the previous batch and could not be reused.

That gives the reader something concrete to check: which conclusion was challenged, what evidence was found, and how the result changed.

## How follow-up questions work

`grill-agent` separates answers into three categories.

**Confirmed answers support the next action.** Data fields, current callers, and existing counting rules can often be checked directly in the project.

**Reasonable assumptions need an explicit default and verification method.** The default should be low-risk and reversible. It should not silently change the task's goal or a critical business definition.

**Actual user decisions come back as a short group of questions.** A dataset might contain `severity=1` without defining whether 1 is the most or least severe value. If that changes maintenance priority, the agent should not guess.

Each answer determines what to check next. If an assumption fails, revise the approach and recheck the affected parts. Stop when no unresolved question would change the action, then return to the review or execution the user originally requested.

## A check for counterexamples

Self-review can turn into an exercise in defending the first answer. The skill therefore asks the agent to identify a concrete counterexample or failure condition that would overturn a material conclusion, then investigate it.

If the evidence does not support the plan, revise or abandon it. If verification is unavailable, retain an unverified status rather than treating the absence of a discovered problem as a pass.

This does not require a long procedure for every small task. Fixing an obvious typo does not need three invented risks.

## Handle temporary artifacts at closeout

The skill now distinguishes final results, maintenance material, and disposable intermediate artifacts. After verifying deliverables, it cleans up files created by this task whose purpose has ended, within existing deletion authorization. Regression tests, reproduction scripts, and referenced assets remain. The Chinese cleanup rules received one read-only simulation with no real deletion; actions still follow host permissions. See the [README](../../README.en.md#task-closeout-temporary-file-cleanup) for the scope.

## What the user sees

For important decisions, the result includes at most three short records:

> Key question → evidence → decision

For example: Does the old summary represent this batch? The project rules and current CSV show that it does not, so recompute the counts.

This is a checkable explanation of the result. The user does not need to read every intermediate step, and a longer response is not treated as proof of a better check.

## What has been tested

The initial Chinese version passed a format check and three isolated scenarios:

1. With complete data and definitions, calculate directly and detect a stale summary.
2. With missing business definitions, identify the gaps without inventing a final maintenance list.
3. When asked only to prepare announcement materials, deliver a draft and missing items without claiming publication.

These tests used synthetic inputs. Each evaluator received no expected answer; the maintainer checked the output afterward. Inputs and result summaries are available in the repository for others to try with their own models.

Later revisions added rules for limiting change scope, defining completion criteria, delivering the full task, preserving useful protections, and stopping verification when sufficient. Both language versions passed format validation, but those additions have not been separately behavior-tested.

There is no controlled experiment or long-term usage study, so these three runs do not support a percentage improvement in reliability. They show that the agent chose the expected behavior in three bounded scenarios.

The repository now includes Chinese and English skill files and documentation. The English skill is a translation of the Chinese rules, with a format check and a review for consistency. Its behavioral scenarios have not been rerun; the English test report translates the existing Chinese-version results.

## Who might want to try it

If you frequently ask agents to review plans, change code, or analyze data, this skill gives them instructions to check a few critical assumptions before presenting a conclusion.

The core is one `SKILL.md`, with no separate service or dedicated API. The Chinese version has been installed and explicitly used in local Codex. Other hosts that support skill files may need different installation steps; compatibility has not been individually tested.

See the [English README](../../README.en.md) for installation. Choose one language version, then start with a small task that already has supporting material:

```text
$grill-agent
Review this plan first. Identify the assumption most likely to be wrong,
check it against the existing files, and give your conclusion with
at most three key pieces of evidence. Review only for now.
```

Practical counterexamples are welcome: questions it should not have asked, assumptions it still skipped, or evidence that did not actually support the conclusion. Those examples are more useful than adding rules without a demonstrated need.

---

## Short version to share

I built `grill-agent`, a small skill that asks an executing agent to question its own plan before acting: What assumptions does it rely on? What do the available files support? What counterexample would invalidate the conclusion? It investigates what it can and asks the user when critical business information or authorization is missing. Important decisions get at most three short records: question → evidence → decision. Chinese and English versions are available. The Chinese skill has been used in local Codex and checked in three isolated scenarios; the English translation has passed a format check. Instructions, installation details, and test records:

[GitHub: 0731koukou/grill-agent](https://github.com/0731koukou/grill-agent)

## One-line introduction

Have the agent question its plan and check counterexamples before bringing back the decisions that need you.
