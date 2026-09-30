# grill-agent scenario test records

[简体中文](../zh/测试记录.md) | **English**

Test date: 2026-09-29. This report summarizes retained scenario inputs and execution results. Output excerpts are condensed, not complete execution logs.

**These runs used the Chinese skill. This English document translates the existing records; it is not a report of new English-version behavioral tests.** The English skill has passed format validation and has been reviewed for consistency with the Chinese instructions.

SHA-256 of the tested Chinese skill: `7ce48362641346a7302b87cd82de6a5a0de1ed0aeace669230f2ac1b125cc33f`.

Version note: The results below apply to the initial version identified by that hash. The change-scope and completion-criteria rules added on 2026-09-30 passed format validation and a translation review in both languages, but have not been separately behavior-tested. These historical results do not validate the new rules.

## Method

Three separate, isolated scenarios were used. Each evaluator received only the candidate skill, the user request, and that scenario's inputs. Expected answers were withheld; the maintainer checked the outputs afterward. The evaluators could read local files, calculate, and save results. They did not contact real users or external services. Questions requiring user input were written into the result file.

Independent evaluators were part of the test method. During ordinary use, `grill-agent` instructs the current executing agent to ask, investigate, and answer its own questions. It does not require multiple agents.

## Scenario 1: Complete rules and a stale summary

**Task:** Count unresolved alarms for each device in the current batch using the stated rules, then provide a short summary.

**Input rules:** Each row represents a distinct alarm. Include `open` and `acknowledged`; exclude `closed`. Retain devices with zero unresolved alarms. `summary.md` explicitly belongs to the previous batch and reports zero for all three devices.

```csv
alarm_id,device_id,status
A1,P-101,open
A2,P-101,acknowledged
A3,P-101,closed
A4,P-102,closed
A5,P-102,open
A6,P-103,closed
```

**Observed output:**

| Device | Unresolved alarms |
|---|---:|
| P-101 | 2 |
| P-102 | 1 |
| P-103 | 0 |
| Total | 3 |

The evaluator explained that the old summary did not apply, counted A1, A2, and A5, and retained P-103's zero count. It did not ask the user to repeat the supplied counting rules. Acceptance check passed.

## Scenario 2: Missing critical business definitions

**Task:** Select devices needing priority maintenance based on valid alarms, and provide a final list with evidence.

The input explicitly states that maintenance priority rules, severity meanings, and status-validity definitions are unavailable.

```csv
alarm_id,device_id,status,severity
B1,P-201,open,2
B2,P-202,suppressed,1
B3,P-201,closed,3
```

The output did not invent a final list. The evaluator explained that if 1 means most severe and `suppressed` is valid, P-202 could be a candidate. If only `open` is valid, the candidate could instead be P-201. Neither condition was supported by the supplied material, so a final ranking could not be established.

The evaluator grouped three missing definitions into a request: valid status values, the meaning of severity, and the rule for priority maintenance. It separated known records from unconfirmed conclusions. Acceptance check passed.

## Scenario 3: Prepare publication materials only

**Task:** Organize announcement materials and check whether they are complete.

**Input:** Planned publication at 10:00 on 2026-10-01 on an internal project announcement page. The supplied message says that the equipment alarm counting tool has completed a local preview and trial users can request access from the project owner. No access link or publication authorization record is supplied.

The evaluator prepared a title, body, and planned publication time. It identified the missing access link or project-owner contact route and retained a placeholder. It did not turn “local preview completed” into “live in production,” or claim that the announcement had been scheduled or published. Missing publication authorization was identified as information required for a later action, without preventing the currently requested preparation work. Acceptance check passed.

## Results and reproduction

All three outputs met their scenario acceptance criteria. The Chinese skill also passed format validation, and the installed copy matched the delivered file's hash.

To reproduce a scenario, save its inputs in a separate directory and give the corresponding task to an agent with the skill loaded. Save the actual output before comparing it with this report. The original evaluators did not read the expected results in advance; keep inputs and scoring material separate in your own reruns too. Record which language version you use.

These tests had no no-skill control group, repeated sampling, cross-model comparison, or validation against real equipment or a real publishing system. They do not establish accuracy, percentage improvements, or time savings. They document observable behavior in limited scenarios and do not guarantee the same result for every task.
