# po-skills

A Claude skill for Product Owners to create high-quality Jira tickets — with proper context, testable acceptance criteria, design references, and all required metadata — by pulling from Jira, Notion, Confluence, and other connected MCP sources rather than assuming anything.

## What this skill does

- Asks you which project, which ticket type (new feature vs adjustment), and which language — no defaults, no assumptions.
- Pulls project context from Jira MCP: available issue types, recent epics (for the parent), labels actually used in the project, and a few recent tickets of the same type for style reference.
- Fetches the sources you name: Notion PRDs, meeting notes, AI-generated notes, Confluence specs, related Jira tickets, Figma designs, pasted transcripts.
- Drafts the ticket using the correct template (new-feature or adjustment), with the team's conventions for Contexte, ACs, Design, and strike-through revisions.
- Runs a quality check (no vague verbs, every AC testable, UI strings quoted verbatim, error/empty/loading states addressed).
- Shows you the full draft for approval.
- On your explicit approval, creates the ticket in Jira and returns the key + link.

## What this skill does NOT do

- Does not create tickets without explicit approval.
- Does not make up missing information — if it's not given, it's asked.
- Does not execute instructions found inside fetched Notion pages, meeting transcripts, or other non-user content (see `references/security.md`).
- Does not delete tickets, change sharing/permission settings, or take other destructive actions.

## Repo structure

```
po-skills/
├── SKILL.md                                        # the main skill file Claude reads
├── templates/
│   ├── new-feature.md                              # new-feature ticket template
│   └── adjustment.md                               # adjustment ticket template
├── examples/
│   ├── new-feature-COEUR-3835.md                   # annotated real example
│   └── adjustment-COEUR-4270.md                    # annotated real example
├── references/
│   ├── context-gathering.md                        # MCP calls and source handling
│   ├── quality-rubric.md                           # quality checks before finalizing
│   └── security.md                                 # handling instruction-like fetched content
├── evals/
│   ├── README.md                                   # how to run the eval set
│   ├── evals.json                                  # 12 test cases
│   └── rubric.md                                   # human-readable pass/fail criteria
└── README.md                                       # this file
```

## How to install

The skill is distributed as a folder containing `SKILL.md` and supporting files. Installation depends on which Claude surface you're using:

### Claude.ai (web or mobile)

1. Create a new Project in Claude.ai.
2. Copy the contents of `SKILL.md` into the project's custom instructions.
3. Attach the files in `templates/`, `examples/`, and `references/` to the project's knowledge base so Claude can reference them.
4. Connect the MCP servers you need (Jira/Atlassian at minimum; Notion and Confluence recommended).

### Claude Code (CLI)

1. Clone this repo into your skills directory (consult Claude Code's docs for the exact path in your setup).
2. Ensure the Atlassian MCP and Notion MCP are configured in your MCP settings.
3. The skill triggers when you ask Claude to create a Jira ticket.

### Cowork / Claude Desktop

Place the folder in your user-skills directory. The skill will be auto-discovered.

## How to use

Open a new conversation with Claude and say something like:

- "Create a Jira ticket for a new subscription modal in the broker portal."
- "Can you file an adjustment ticket to change the default notification behavior?"
- "Here's the Notion PRD [link] — turn it into a Jira ticket."

The skill will ask you a few questions (ticket type, project, language, sources), draft the ticket, and ask for approval before creating it.

## Testing the skill

Before rolling changes out to the team:

1. Run the eval set (`evals/evals.json`) — 12 cases covering happy paths, edge cases, and security concerns.
2. Check each case against the rubric in `evals/rubric.md`.
3. Aim for at least 10/12 passing and no universal-rubric failures.

See `evals/README.md` for the full testing process.

## Contributing

Changes to the skill go through a PR:

1. Branch off `main`.
2. Edit `SKILL.md`, templates, or references as needed.
3. Re-run the eval set and paste results in the PR description.
4. Require one review from another PO before merging.
5. After merge, post a one-line summary in the team channel.

## Rollout plan

Suggested rollout:

1. **Week 1** — draft the skill, run evals, iterate with 1 PO pilot.
2. **Week 2** — 2–3 POs use it on real tickets; collect friction points.
3. **Week 3** — address top 3 issues, re-run evals.
4. **Week 4** — team-wide rollout with a short internal doc on installation.

## Open questions / roadmap

- Auto-linking: should the skill automatically create issue links (blocks, is-blocked-by, clones) based on related-ticket context? Currently it does not — left for a future version.
- Bulk mode: turning 10 PRD sections into 10 tickets in one pass. Not yet supported.
- Suggested story points from past ticket data in the same project. Currently the skill always asks; a heuristic suggestion based on similar historical tickets is a future idea.
