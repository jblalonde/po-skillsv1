---
description: Draft a high-quality Jira ticket — epic, user story (new feature), or adjustment — with qualified personas, testable acceptance criteria, verbatim UI copy, and complete metadata. Pulls context from connected Jira/Notion/Confluence/Figma; never fabricates.
argument-hint: [rough idea, paste, or links]
---

Invoke the `jira-ticket` skill to draft a Jira ticket from the input below.

Input: $ARGUMENTS

If the input is empty, ask the user what they want to ticket (rough idea, meeting notes, PRD link, Figma link, or revision request) and which Jira project it belongs to before proceeding.

Follow the skill's standard flow:
1. Determine artefact type (epic / story – new feature / story – adjustment) and run the sizing check at Step 1; if input is epic-sized, apply the breakdown protocol.
2. Pull metadata and context from connected MCP sources (Jira, Notion, Confluence, Figma). Never invent field, event, table, service names or enum values — TODO with the question instead.
3. Interview for whatever is missing.
4. Return Bloc A (clean ticket, paste-ready) + Bloc B (numbered questions grouped by addressee with section pointers).
5. On approval, create the ticket via `Atlassian:createJiraIssue`.
