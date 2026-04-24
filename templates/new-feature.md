# New feature ticket template

Use this template when the ticket is for something **new** — a new screen, a new modal, a new flow, a new capability. If the ticket is modifying something that already exists, use `adjustment.md` instead.

---

## Summary

Noun-based. Names the UI component or capability being built.

**Good examples:**
- `Modal Activer l'abonnement`
- `Subscription confirmation modal`
- `Tableau des opportunités potentielles`
- `Broker lead assignment flow`

**Bad examples:**
- `Build subscription thing` (vague, verb-led for a new feature)
- `Make it easier to activate subscriptions` (describes a goal, not the artifact)
- `COEUR-XXXX` (references another ticket in the summary)

---

## Description structure

The description has three sections in this order: Contexte, Critères d'acceptation, Design. Do not rename, reorder, or skip them for new-feature tickets.

### Section 1 — Contexte

One paragraph, in user-story format.

**French:**
> En tant que [rôle], je veux [objectif] afin de [bénéfice].

**English:**
> As a [role], I want [goal] so that [benefit].

Keep it short (1–2 sentences max). If the business context needs more, add a second paragraph with a single "Contexte métier" / "Business context" header — but try to avoid it. The user story is the heart.

**Good:**
> En tant que courtier, je veux pouvoir activer un abonnement afin de recevoir automatiquement des opportunités selon le volume journalier choisi.

**Bad:**
> We need to let brokers activate subscriptions because the current flow doesn't work and it would be good for retention.
> (No clear role, no clear goal, no clear benefit, and it's about the old system instead of the new one.)

### Section 2 — Critères d'acceptation / Acceptance criteria

Flat bullet list. Each bullet is one testable statement. Sub-bullets handle edge cases, error paths, and alternate flows.

**Structure expectations:**
- Trigger / entry point (how does the user get here?)
- Content on display (what shows up?)
- User interactions (buttons, inputs, toggles — include exact labels in quotes)
- Success path
- Cancel / dismiss path
- Error path
- Empty / loading states
- Side effects (what changes in the system when the action succeeds?)

Every bullet should be answerable with "yes" or "no" by a tester with the final build in front of them. "Works well" is not testable. "Displays X within 2 seconds" is.

**Good:**
- Le modal s'ouvre via le bouton "Activer l'abonnement" sur la tuile abonnement
- Le modal contient un bouton "Annuler" et un bouton "Activer"
  - "Annuler" ferme la modale sans effet
- En cas d'échec lors de l'activation, un message d'erreur s'affiche et l'abonnement n'est pas créé
- En cas de succès, le modal de confirmation s'affiche (voir ticket [LIEN])

**Bad:**
- The modal should work properly and feel good (untestable)
- Handle errors gracefully (handle how? show what?)
- Activer ferme la modale (which button? unclear)

### Section 3 — Design

Two elements, in this order:

1. An embedded screenshot of the current design (attach the image to the ticket; Jira will render it inline).
2. The Figma link with the node-id preserved so engineers land on the exact frame.

**Format:**

```
Design (updaté)

[embedded screenshot]

https://www.figma.com/design/<fileId>/<fileName>?m=auto&node-id=<nodeId>&t=<token>
```

If the design was updated after review, title the section "Design (updaté)" / "Design (updated)" so readers know to check for changes.

**Do not:**
- Reference a Figma file without a node-id (forces engineers to hunt for the frame)
- Paste a screenshot without the Figma link (screenshots go stale)
- Paste a Figma link without a screenshot (harder to preview in Jira)

---

## Metadata checklist

Before creating, confirm every field below with the requester. Defaults are suggestions — do not apply them without asking.

- [ ] **Project** — no default, ask
- [ ] **Issue type** — usually Task; confirm based on project's available types
- [ ] **Parent epic** — required; suggest from recent epics pulled via MCP
- [ ] **Labels** — suggest from labels actually used in the project (see `references/context-gathering.md`)
- [ ] **Priority** — ask
- [ ] **Assignee** — ask
- [ ] **Reporter** — defaults to whoever is running the skill; confirm
- [ ] **Sprint** — ask
- [ ] **Due date** — ask
- [ ] **Story points** — ask; new features almost always estimated
- [ ] **Attachments** — design screenshot

---

## Revisions after review

When a ticket is being updated after design or product review, use strike-through for changes:

```
Le courtier peut choisir parmi 4 options de volume journalier ~~hebdomadaire~~ : 5, 10, 20, 40 ~~ou 100~~ demandes par jour ~~par mois~~
```

This keeps the history readable for engineers and QA who already had context on the earlier version. After a revision has been acknowledged and the sprint moves on, the strike-through can be cleaned up on the next revision — but while the ticket is in-flight, keep it.
