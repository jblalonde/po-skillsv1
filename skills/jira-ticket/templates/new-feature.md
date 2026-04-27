# New feature ticket template

Use this template when the ticket is for something **new** — a new screen, a new modal, a new flow, a new capability. If the ticket is modifying something that already exists, use `adjustment.md` instead.

This template is opinionated on the *quality bar*, not on the section count. Block A (the ticket as it lands in Jira) is light and matches what the team writes today. The expert-grade discipline (qualified persona, GWT, INVEST, vertical slice, anti-fabrication) is enforced silently in Step 6 and surfaces as numbered questions in Block B when checks fail.

---

## Block A — sections that ship in Jira

```
1. Summary             — noun phrase, names the UI/capability
2. Contexte            — En tant que [rôle qualifié], je souhaite [objectif], afin de [bénéfice]
3. Acceptance criteria — bullet list, each testable; UI copy verbatim; address error/empty/loading
4. Design              — embedded screenshot + Figma URL with node-id
5. Info supp / Non-inclus / Warning  — optional, only when relevant
6. Details             — project, parent epic, labels, priority, assignee, sprint, story points
```

The **Validation INVEST** self-check is **not** a Block A section. It runs in Step 6 — failures surface as Block B questions.

---

## Section guidance

### 1 — Summary

Noun-based. Names the UI component or capability being built. One noun phrase, no verbs, no colons, no ticket references.

**Good:**
- `Modal Inviter un membre`
- `Onboarding profile setup screen`
- `Tableau des commandes en cours`
- `Bulk export of orders to CSV`

**Bad:**
- `Build invite thing` (vague, verb-led for a new feature)
- `Make it easier to invite teammates` (describes a goal, not the artifact)
- `Backend pour le modal d'invitation` (layer, not capability — Rule 13 violation)
- `PROJ-XXXX` (references another ticket in the summary)

### 2 — Contexte

One paragraph, user-story format, with a **qualified persona**. The persona is the difference between a feature built for "users" (meaningless) and a feature built for a specific person doing a specific job in a specific state.

**Persona qualification — three parts, always:**

1. **Role** — the job title or system role (admin d'équipe, client, nouveau visiteur, agent support, gestionnaire de produit, etc.).
2. **Segment or state** — what narrows the role to the specific audience for this ticket (nouveau / déjà inscrit / avec une commande en cours / sans paiement enregistré / gestionnaire d'une équipe de 10+).
3. **Context of the need** — the moment or situation in which the user hits this feature.

**Format — French:**

> En tant que [rôle qualifié avec segment/état], [contexte du besoin], je souhaite [objectif observable] afin de [bénéfice mesurable].

**Format — English:**

> As a [qualified role with segment/state], [context of need], I want [observable goal] so that [measurable benefit].

**Good:**

> En tant qu'admin d'une équipe de 10+ membres, lorsque je dois ajouter un nouveau collaborateur sans passer par le support, je souhaite l'inviter par courriel avec un rôle pré-sélectionné afin que mon équipe puisse intégrer la personne en quelques minutes plutôt qu'en quelques jours.

Why this is good: *admin d'une équipe de 10+ membres* (role + segment), *lorsque je dois ajouter un nouveau collaborateur sans passer par le support* (context), *l'inviter par courriel avec un rôle pré-sélectionné* (observable goal), *en quelques minutes plutôt qu'en quelques jours* (measurable benefit — before/after is verifiable).

**Bad:**

> En tant qu'utilisateur, je souhaite inviter quelqu'un afin de l'ajouter.

Why this is bad: generic role, no segment, circular benefit, no context. The persona-qualification gate catches this in Step 6 and surfaces it in Block B.

**If business context is needed**, add a single sub-heading `**Contexte métier**` under the user story with 2–3 sentences of framing. Anything longer belongs in the linked epic or a Confluence page.

**If there are hard technical constraints** that drive the product shape (e.g., "must use the existing email-sending vendor under contract — no new vendor allowed"), add a `**Contraintes**` sub-heading listing them in bullets.

### 3 — Acceptance criteria

A single bullet list (no required sub-headings). Every bullet must be **testable** — a tester answers yes/no by looking at the build.

**Inside every AC list, address:**

- **Préconditions** — the state needed for any scenario to apply (auth, flag, prior state).
- **Happy path** — the main success flow, including each branching decision (one bullet or one GWT scenario per branch).
- **Error paths** — backend failure, validation failure, third-party failure.
- **Edge cases** — empty state, loading state, race conditions, dismissal/cancel path.
- **Règles & contraintes** — enumerated values, mutual exclusion rules, bounds, business rules.
- **Verbatim UI copy** — every button label, modal title, error message, description paragraph quoted from a cited source. **Never paraphrase or invent.**
- **Effets de bord système** — observable backend changes (data modified, message sent, behaviour triggered) cited from a real source. If no source, see Rule 14b protocol below.

The list is one block — but completeness across these categories is what makes the AC testable. The skill checks all of them silently in Step 6.

#### GWT for behavioural ACs

When an AC bullet describes a **behaviour** (trigger → reaction, state transition, error path), use **Étant donné / Quand / Alors** format inline. See Rule 12 in `references/quality.md`.

```
**Scénario — Invitation envoyée à une adresse valide**
- Étant donné qu'un admin d'équipe est sur la liste des membres
- Et qu'il a la permission « inviter un membre »
- Quand il saisit une adresse courriel valide, sélectionne le rôle « Membre standard », et clique sur « Envoyer l'invitation »
- Alors le bouton « Envoyer l'invitation » affiche un spinner et est désactivé jusqu'au retour du système d'envoi
- Et le modal de confirmation s'affiche dès que le courriel est mis en file d'envoi
```

Static rules (enumerated values, UI copy inventory, mutual exclusion, bounds) stay as plain bullets — don't force GWT where it adds noise.

**Example mix — clean AC list:**

- L'admin est authentifié dans le produit. *(précondition)*
- L'admin a la permission « inviter un membre » sur l'organisation courante (voir conditions à confirmer en Q4). *(précondition)*
- Titre du modal : « Inviter un membre ». *(verbatim UI)*
- Rôles offerts : « Admin », « Membre standard », « Lecture seule ». *(règle — sourcée Notion PRD §2.1)*
- Boutons : « Annuler » (secondaire), « Envoyer l'invitation » (primaire). *(verbatim UI)*
- Une seule adresse courriel par invitation (pas de saisie multiple dans ce ticket — voir Non-inclus). *(règle)*
- **Scénario — Invitation envoyée à une adresse valide** *(GWT — voir exemple ci-dessus)*
- **Scénario — Adresse courriel invalide** *(GWT — error path)*
- **Scénario — Annulation par l'admin** *(GWT — cancel path)*

#### Anti-fabrication on Effets de bord système

This is the highest-risk area for Rule 14 violations. Tempting to write plausible field/event/service/queue names that match the domain. **Do not.**

**Forbidden — fabricated specifics:**
- *Un enregistrement `<table>` est créé avec `<champ>=<valeur>`* when the table/field/value is not cited.
- *Un événement `<name>` est émis vers `<service>`* when the event name and service are not cited.
- *Le moteur de `<X>`*, *le pipeline `<Y>`* when the existence and naming are not cited.

**Required protocol when sources are missing:** do **not** write a TODO listing categories of systems (analytics, queue, distribution engine, etc.) — that is itself a fabrication (Rule 14b). Ask the requester an open architecture-neutral question first:

> *« Quels effets observables côté backend se produisent à la confirmation de l'action ? Cela peut être des changements de données, des messages envoyés à d'autres parties du système, ou des comportements automatiques qui démarrent. Si vous ne savez pas, dites-le et nous laisserons la section ouverte pour qu'un ingénieur réponde. »*

If the requester answers with specifics → cite them in the AC. If they say "I don't know" → the AC bullet stays open in the requester's words, with no invented categories, and the gap surfaces in Block B as a question for an engineer.

### 4 — Design

Two elements, in this order:

1. An embedded screenshot of the current design (attach the image to the ticket; Jira will render it inline).
2. The Figma link with the `node-id` preserved so engineers land on the exact frame.

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

If no Figma exists yet, flag it in Step 5 and confirm explicit override before proceeding (do not fabricate a link).

### 5 — Info supplémentaire / Non-inclus / Warning *(optional)*

Use these short subsections only when the content meaningfully helps the engineer. Omit the section entirely when there's nothing to say — empty headers are noise.

- **Info supplémentaire** — business context that doesn't fit Contexte (e.g., a referenced internal initiative, a downstream impact worth flagging).
- **Non-inclus** — explicit out-of-scope items when there's a real risk of over-interpretation. *« L'historique des invitations envoyées n'est pas dans ce ticket — voir PROJ-XXXX. »*
- **Warning** — known risks, blocked dependencies, or constraints the engineer must know upfront. *« Cette feature dépend de la migration PROJ-YYYY ; ne pas merger avant. »*

### 6 — Details

Metadata checklist. Confirm every field with the requester. Defaults are suggestions — never apply without asking.

- [ ] **Project** — no default, ask
- [ ] **Issue type** — usually Task; confirm based on project's available types
- [ ] **Parent epic** — required; suggest from recent epics pulled via MCP
- [ ] **Labels** — suggest from labels actually used in the project (see `references/mcp-handling.md` Section 1)
- [ ] **Priority** — ask
- [ ] **Assignee** — ask
- [ ] **Reporter** — defaults to whoever is running the skill; confirm
- [ ] **Sprint** — ask
- [ ] **Due date** — ask
- [ ] **Story points** — ask; new features almost always estimated. Fibonacci (1, 2, 3, 5, 8, 13) is the default scale unless the project uses a different convention.
- [ ] **Attachments** — design screenshot
- [ ] **Linked tickets** — `Blocks`, `Blocked by`, `Relates to` as required by Rule 13 (vertical slice) and INVEST **I** (Independent)

---

## Quality gates (run silently in Step 6)

These do not appear as sections in Block A. They run during drafting and any failure surfaces as a numbered question in Block B.

### INVEST self-check

The skill verifies each criterion against the draft. Each criterion that fails surfaces as a Block B question with the tag (e.g. `[INVEST — S]`).

- **I — Independent** — can this ticket ship without waiting on another? If blocked, is there an explicit `Blocked by` link?
- **N — Negotiable** — does the AC describe outcomes, not implementation?
- **V — Valuable** — who specifically gains what observable benefit?
- **E — Estimable** — are unresolved gaps small enough that an engineer can size this in one sprint?
- **S — Small** — one engineer, one sprint, scope of one slice?
- **T — Testable** — can QA write test cases from the AC without a follow-up conversation?

If a criterion fails, the failure becomes a Block B question — never a fabricated pass-line.

### Other gates checked silently

- **Anti-fabrication (Rule 14, 14b)** — no invented service/event/table/flag/copy.
- **Persona qualification** — Contexte must name role + segment/state + context of need (no "utilisateur").
- **Vertical-slice check (Rule 13)** — ticket ships user-visible value alone, not a layer.
- **Verbatim UI copy** — every UI string in AC quoted from a cited source.
- **Error/empty/loading addressed** — when a UI is involved, all three states named or explicitly marked non-applicable.

---

## Revisions after review

When a ticket is being updated after design or product review, use strike-through for changes:

```
L'admin peut choisir parmi 3 rôles ~~4 rôles~~ : « Admin », « Membre standard », ~~« Modérateur »,~~ « Lecture seule »
```

This keeps the history readable for engineers and QA who already had context on the earlier version. After a revision has been acknowledged and the sprint moves on, the strike-through can be cleaned up on the next revision — but while the ticket is in-flight, keep it.
