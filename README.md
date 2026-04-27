# po — Claude Code plugin for Product Owners

A Claude Code plugin that bundles **two skills** for Product Owners:

- **`/po:jira-ticket`** — drafts high-quality Jira tickets (epics, user stories / new features, adjustments) with qualified personas, testable acceptance criteria, verbatim UI copy, and complete metadata. Pulls context from connected Jira, Notion, Confluence, Figma, and other MCP sources rather than assuming anything. Ends in `Atlassian:createJiraIssue` on explicit approval.
- **`/po:check`** — audits an existing Jira ticket against the same quality contract. Reads the ticket via `Atlassian:getJiraIssue`, runs the structural and INVEST/Validation epic checks, returns a pass/fail report with concrete fixes, and optionally applies them via `Atlassian:editJiraIssue` (with explicit per-fix approval).

Project- and domain-agnostic. No defaults baked in — every project gets its own metadata lookup before drafting.

## What the plugin enforces (the contract)

- **Anti-fabrication (hard stop)** — no invented backend specifics (field/event/service/queue/flag/copy). Missing info becomes a TODO with the exact question to ask, naming who can answer.
- **Two-block output** — Block A = the ticket paste-ready in Jira. Block B = open questions, workspace-only.
- **Qualified personas** — role + segment/state + context of need. No "as a user".
- **Testable acceptance criteria** — every behavioural AC in Given-When-Then. No vague verbs.
- **Verbatim UI copy** — every button label, modal title, error message, notification text quoted from a cited source (Figma, Notion, PO message, code).
- **Vertical slicing** — every ticket ships user-visible value, not a technical layer.
- **Explicit approval gate** — never creates or modifies a ticket without the requester saying so.

## Plugin structure

```
po/
├── .claude-plugin/
│   └── plugin.json                                 # plugin manifest (name: po)
├── skills/
│   ├── jira-ticket/                                # /po:jira-ticket
│   │   ├── SKILL.md                                # entrypoint
│   │   ├── templates/
│   │   │   ├── epic.md                             # 8 Block A sections
│   │   │   ├── new-feature.md                      # 6 Block A sections
│   │   │   └── adjustment.md                       # 6 Block A sections
│   │   ├── examples/
│   │   │   ├── epic-example.md                     # neutral domain
│   │   │   ├── new-feature-example.md              # neutral domain
│   │   │   └── adjustment-example.md               # neutral domain
│   │   └── references/
│   │       ├── quality.md                          # Section 1: 16-rule rubric. Section 2: INVEST checklist.
│   │       └── mcp-handling.md                     # Section 1: MCP calls. Section 2: security for fetched content.
│   └── check/                                      # /po:check
│       └── SKILL.md                                # epic + story checklists, audit report format
├── evals/                                          # test artifacts (not part of the runtime plugin)
│   ├── README.md
│   ├── evals.json                                  # 13 test cases
│   └── rubric.md                                   # human pass/fail criteria
└── README.md                                       # this file
```

## How to install

### As a plugin (recommended for sharing)

The plugin can be loaded directly for development or installed via a marketplace.

**Local development / testing:**

```bash
claude --plugin-dir /path/to/po
```

This loads the plugin without installing. Skills become invocable as `/po:jira-ticket` and `/po:check`. Run `/reload-plugins` to pick up edits.

**Marketplace install (for team-wide rollout):**

1. Add the plugin to a marketplace (your team's, or the official Anthropic marketplace).
2. Team members install via `/plugin install po@your-marketplace`.
3. Versioned via `version` field in `plugin.json`.

See [Claude plugin docs](https://code.claude.com/docs/en/plugins) and [marketplace docs](https://code.claude.com/docs/en/plugin-marketplaces) for details.

### Prerequisites for full functionality

- **Atlassian MCP** — required for fetching project context (Step 2 of `/po:jira-ticket`), creating tickets (Step 8), reading tickets for audit (`/po:check`), and editing tickets (`/po:check` apply-fix). Configure via `mcp__claude_ai_Atlassian__*` tools.
- **Notion MCP** *(optional but recommended)* — for fetching PRDs and meeting notes referenced as sources of truth.
- **Confluence MCP** *(optional)* — same idea, for product specs.
- **Figma** — links are kept in the ticket; no MCP fetch (Figma content is not extracted by Claude).

## How to use

### Drafting a new ticket

Open a new conversation and either type the slash command or describe the work in natural language:

- *"Create a Jira ticket for a new modal that lets admins invite team members."*
- *"`/po:jira-ticket` I need an epic for our self-service team management initiative."*
- *"Here's the Notion PRD [link] — turn it into a Jira ticket."*

The skill walks you through five Step 1 questions (subject, sizing, type, project, language), pulls project context from Jira (Step 2), asks for written sources (Step 3), drafts the ticket (Step 4), runs silent quality gates (Step 6), and presents Block A + Block B (Step 7). On explicit approval, calls `Atlassian:createJiraIssue` (Step 8).

### Auditing an existing ticket

Pass a Jira key or paste a ticket draft:

- *"`/po:check` PROJ-1234"*
- *"Is this ticket ready for sprint? [pasted draft]"*
- *"Run a quality check on the epic I just filed."*

The skill fetches the ticket, identifies the type (epic vs story), runs the structural + quality checklists, and returns a Block A audit report (passes / flags / hard fails, each with a suggested fix) plus a Block B of open questions for the PO / Design / Engineer.

Optionally, the skill can apply specific fixes via `Atlassian:editJiraIssue` — one approval per fix, never a blanket "fix everything".

## Testing the plugin

Before rolling changes out:

1. Run the eval set (`evals/evals.json`) — 13 cases covering happy paths (epic, new feature, adjustment), edge cases (ambiguous type, missing Figma, language handling, vague AC), and security concerns (injection in fetched sources).
2. Check each case against the rubric in `evals/rubric.md`.
3. Aim for at least 11/13 passing and no universal-rubric failures.

See `evals/README.md` for the full testing process.

## Contributing

Changes to the plugin go through a PR:

1. Branch off `main`.
2. Edit `skills/<skill>/SKILL.md`, templates, references, or examples as needed.
3. Re-run the eval set and paste results in the PR description.
4. Bump `version` in `.claude-plugin/plugin.json` if you want users to receive an update.
5. Require one review from another PO before merging.
6. After merge, post a one-line summary in the team channel.

## Open questions / roadmap

- Reproducibility harness — a structural-fingerprint + N-run + judge protocol to verify each skill produces consistent output across runs of the same prompt. Sketched in `evals/` but not yet built.
- Auto-linking: should `/po:jira-ticket` automatically create issue links (`Blocks`, `Blocked by`, `Clones`) based on related-ticket context?
- Bulk mode: turning N PRD sections into N tickets in one pass.
- Suggested story points from past ticket data in the same project.
- Additional shortcuts: `/po:from-prd <url>`, `/po:epic` — only added if usage shows clear repeating patterns.
