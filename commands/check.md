---
description: Audit an existing Jira ticket (epic or story) against the team's quality contract — structural sections, anti-fabrication, qualified personas, GWT, INVEST. Returns a pass/fail report with concrete fixes; can apply edits via Atlassian MCP on approval.
argument-hint: [ticket key or pasted draft]
---

Invoke the `check` skill to audit the ticket below against the team's quality contract.

Input: $ARGUMENTS

If the input is empty, ask the user for a Jira ticket key (e.g. `PROJ-1234`) or a pasted ticket draft before proceeding.

Follow the skill's standard flow:
1. Fetch the ticket (or parse the pasted draft).
2. Run the audit across structural sections, anti-fabrication, persona qualification, GWT criteria, vertical slicing, INVEST / 4-line Validation epic.
3. Return Bloc A (clean report: passes, gaps, hard fails, fix suggestions) + Bloc B (numbered questions grouped by addressee with section pointers).
4. If the user approves fixes, apply them via `Atlassian:editJiraIssue` with explicit per-edit confirmation.
