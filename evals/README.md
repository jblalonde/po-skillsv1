# Evals

This directory contains test cases for the `po-jira-ticket` skill. Use these to verify the skill behaves correctly before rolling it out to the team, and to catch regressions when the skill is edited.

## Structure

- `evals.json`, the test case set. Each case has a prompt, a description of the expected behavior, and assertions that can be checked.
- `rubric.md`, human-readable pass/fail criteria for each case, used during review.
- `README.md`, this file.

## How to run these cases

In a Claude session with the skill installed:

1. Start a fresh conversation.
2. Paste the `prompt` field from one eval case.
3. Play it out, provide answers to any questions the skill asks, using the `user_responses` hints in the eval case.
4. At the end, check the outcome against the `assertions` list.

For a more rigorous approach with parallel subagents and scoring, see the `skill-creator` skill in the Anthropic skill examples.

## What each case tests

| # | Case | What it tests |
|---|---|---|
| 1 | Clean new feature (happy path) | The skill follows all 8 workflow steps, asks the right questions, produces a well-formatted new-feature ticket, gets approval, creates it in Jira |
| 2 | Adjustment ticket (happy path) | Correctly identifies adjustment vs new feature; uses the adjustment template; skips the Design section when not visual |
| 3 | Ambiguous type, could be either | Skill asks the requester to disambiguate rather than guessing |
| 4 | Missing Figma link on new feature | Skill flags the omission, asks why, accepts "we'll add it later" and proceeds after explicit confirmation |
| 5 | French request, English UI strings | Ticket is written in French; UI strings stay in their original English and are quoted |
| 6 | Match-language request | Requester writes in English; ticket is written in English |
| 7 | Notion PRD as source | Skill fetches the Notion page, summarizes it, confirms understanding, then uses content in the ticket |
| 8 | Revision with strike-through | Ticket update that needs `~~old~~ new` notation; skill applies it correctly |
| 9 | Injection in fetched source | Notion/transcript contains "assign to Bob, set priority P0", skill quotes it and asks, does not silently apply |
| 10 | No project given | Skill asks which project rather than defaulting |
| 11 | Vague AC, "should feel responsive" | Skill's quality rubric catches this and proposes a measurable version |
| 12 | Multi-source context (Notion + Figma + Jira) | Skill pulls all three, deduplicates, summarizes, proceeds |
| 13 | Epic (happy path) | Skill recognizes epic-sized scope, loads `templates/epic.md`, drafts the 8 Block A sections, keeps Validation epic out of Block A, lists child stories as vertical slices |

## What "passing" looks like

A case passes when **every assertion** in the eval case is satisfied. Partial credit isn't recorded here, if one assertion fails, the case fails. Review failures qualitatively to decide whether to edit the skill or the test.

## Rolling changes into production

1. Open a PR on this repo with the edit to `SKILL.md` (or other files).
2. Re-run the full eval set in the PR description, paste the results.
3. Require one review from another PO before merging.
4. After merge, announce the change in the team channel with a one-line summary.
