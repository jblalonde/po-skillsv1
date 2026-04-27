# Reference — Atlassian hierarchy: Theme → Initiative → Epic → Story → Task

This file holds the Atlassian-canonical definitions and sizing rules the skill applies when deciding between drafting an epic, a story, or a task. Source: https://www.atlassian.com/agile/project-management/epics-stories-themes (and linked sub-articles on agile epics and user stories).

## The hierarchy (top to bottom)

- **Theme** — *« An organizational goal that drives the creation of epics and initiatives. »* Multi-quarter to multi-year. **Out of this skill's drafting scope** — we may reference a theme in Contexte but we do not produce theme-level artefacts.

- **Initiative** — *« Initiatives are made up of epics... an initiative compiles epics from multiple teams to achieve a much broader, bigger goal than any of the epics themselves. While an epic is something you might complete in a month or a quarter, initiatives are often completed in multiple quarters to a year. »* **Out of this skill's drafting scope** — we may reference an initiative parent in Contexte.

- **Epic** — *« A large body of work that can be broken down into a number of smaller stories... Epics are almost always delivered over a set of sprints. »* **In scope** — see `templates/epic.md`.

- **Story / User story** — *« The smallest unit of work in an Agile framework. It's an end goal, not a feature, expressed from the software user's perspective. »* Sprint-sized. **In scope** — see `templates/new-feature.md` (and `templates/adjustment.md` for changes to existing).

- **Task** — *« Specific actions or technical work. »* Often a sub-story unit. **In scope only when the requester explicitly asks** — otherwise this skill works at story level.

## User story format (canonical Atlassian template)

> *« As a [persona], I [want to], [so that]. »*

Atlassian's three-part guidance (verbatim):

- **As a [persona]** — *« Not just a job title — the persona of the person... how they work, how they think and what they feel. »*
- **Wants to** — *« Describing their intent — not the features they use... implementation free. »*
- **So that** — *« How does their immediate desire... fit into their bigger picture? What's the overall benefit they're trying to achieve? »*

This is fully compatible with — and stricter than — Atlassian's example phrasings. The qualified-persona rule in `templates/new-feature.md` Section 1 is the operationalisation.

## Sizing rule (story vs epic)

> *« There is no universal definition that draws a line between a big story and an epic. In general, any scope of work that the team estimates at "weeks" (or longer) to complete, rather than "hours" or "days" should be considered an epic and broken down into smaller stories. »*

Practical decision table applied during Step 1 of the skill flow:

| Estimated effort | Format |
|---|---|
| Hours | Task (or sub-task of a story) |
| One sprint or less | Story (`new-feature.md` or `adjustment.md`) |
| Multiple sprints, multiple flows or screens | **Epic** (`epic.md`) — break down into child stories |
| Multiple quarters, cross-team | Likely an initiative — out of scope; draft an epic for the slice the team will own this quarter, link the initiative as parent context |

## Does every story need an epic?

> *« Not every user story needs to be part of an epic; some stories are small enough to stand alone and deliver value independently... a quick bug fix or a minor UI update might be a standalone user story, while a new checkout process would be an epic containing multiple stories. »*

The skill **does not force** an epic parent. If the input is sprint-sized and self-contained, draft a stand-alone story.

## The 3 C's of a user story (Atlassian)

> *« Card, Conversation, and Confirmation. The Card represents the written description of the story, Conversation refers to the discussions that clarify details, and Confirmation is the acceptance criteria that define when the story is complete. »*

Mapping to this skill (operationalised in `quality-rubric.md` Rule 16):

- **Card** — the ticket itself in **Bloc A** (Summary + Contexte + description body).
- **Conversation** — the open questions in **Bloc B** (workspace), which the requester answers before final creation.
- **Confirmation** — the AC section, with GWT scenarios per Rule 12 — testable, no interpretation needed by QA.

A story is not ready until all three Cs are addressed (or open ones are explicitly accepted as known gaps).

## Breaking down an epic — Atlassian's options

> *« User role or persona... Ordered steps... Culture... Time. »*

When drafting an epic, the skill proposes child stories using:

1. **By persona** — different personas trigger different child flows (e.g., new visitor vs. returning customer).
2. **By ordered steps** — the user journey naturally decomposes into stages (each stage = one story).
3. **By value slice** — each child story delivers user-visible value on its own (vertical slice — see `quality-rubric.md` Rule 13).
4. **By time / sprint constraint** — every child story fits in one sprint.

The skill picks the option that produces the cleanest set of independently-shippable stories, then names the breakdown explicitly so the requester can validate.

## Creating an epic — what Atlassian recommends checking

> *« Reporting... Storytelling... Culture... Time. »*

The epic template (`templates/epic.md`) embeds these as required fields: a brief why-it-exists narrative (Reporting + Storytelling), a parent-link slot for theme/initiative (Reporting), an explicit list of child stories with status (Time + Culture), and a coarse time-window estimate.

## Quotes the skill uses verbatim

When the skill needs to justify a sizing decision or breakdown to the requester, it can cite Atlassian directly. The quotes that matter:

- *« A user story is the smallest unit of work in an Agile framework. It's an end goal, not a feature, expressed from the software user's perspective. »*
- *« An epic is a large body of work that can be broken down into a number of smaller stories... almost always delivered over a set of sprints. »*
- *« Any scope of work that the team estimates at "weeks" (or longer) to complete... should be considered an epic and broken down into smaller stories. »*
- *« A story should be sized to complete in one sprint. »*

These citations are stable Atlassian guidance and can be quoted directly when explaining the skill's decisions.
