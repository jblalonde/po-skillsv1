# Epic ticket template

Use this template when the work is too large for a single story — Atlassian's rule: *« any scope of work that the team estimates at "weeks" (or longer) to complete... should be considered an epic and broken down into smaller stories »* (see `references/atlassian-hierarchy.md`).

An epic is **not** an inflated story. It does **not** carry full GWT acceptance criteria — those live in the child stories. The epic carries the *why*, the *scope edges*, the *list of children*, and the *epic-level success criteria*.

All anti-fabrication rules (Rule 14, Rule 14b) apply at this level, exactly as for stories. Do not invent service names, table names, integration names, or any specific the requester has not cited.

---

## Summary

Noun phrase naming the body of work being undertaken. Action-neutral; the action verbs live in child stories.

**Good examples:**
- `Abonnements aux opportunités — flow de bout en bout`
- `Refonte du parcours d'onboarding courtier`
- `Distribution automatique des leads par rotation`

**Bad examples:**
- `Activer l'abonnement` (that's a child story, not an epic)
- `Tout refaire` (no scope edge — vague aspiration)
- `Backend pour les abonnements` (layer, not capability — same Rule 13 violation as for stories)

---

## Description structure

Six sections in this order: Contexte, Personas, Périmètre, Stories enfants, Success criteria, Validation.

### Section 1 — Contexte

Two paragraphs.

**Paragraph 1 — Pourquoi cet epic existe.** The business outcome or organizational driver. If a theme or initiative is known, link it explicitly: `Theme : [nom]` or `Initiative : [clé Jira ou nom]`. If not, leave a TODO asking — do not invent.

**Paragraph 2 — État du monde.** What exists today, what's about to change, who is affected at a high level. Cite the requester verbatim where possible.

### Section 2 — Personas affectés

A bullet list, with one bullet per persona. Each persona is qualified the same way as in `new-feature.md` Section 1 (role + segment/state + context of need). An epic typically affects **multiple** personas — list all of them. If a persona is a downstream operator (support, admin, analyst), name it.

### Section 3 — Périmètre

Two sub-sections — both required.

#### 3.1 — Dans le périmètre de l'epic

Bullet list of capabilities, flows, or screens included. Each bullet should map to one or more child stories named below in Section 4.

#### 3.2 — Hors du périmètre (explicit)

Bullet list of adjacent capabilities that are **intentionally not** part of this epic. This is critical — engineers and stakeholders will assume otherwise without an explicit list. Examples of what to call out: legacy flows being replaced, related screens that stay unchanged, follow-on epics that pick up the rest.

### Section 4 — Stories enfants

A checklist. Each child story is one slice of vertical user-visible value (see Rule 13). For each:

- A noun-phrase summary (matches the format of `new-feature.md`)
- A short one-line scope hint
- Status: `À drafter` / `Drafted as [KEY]` / `In sprint` / `Done`

Example:

```
- [ ] **Modal Activer l'abonnement** — flow d'activation, modal + intégration de paiement + validation. *À drafter en story (new-feature.md).*
- [ ] **Tableau de bord d'abonnement** — état temps-réel : leads reçus aujourd'hui, semaine, total. *À drafter.*
- [x] **Désactiver un abonnement actif** — flow d'arrêt sans pénalité. *Drafted as XXX-1234.*
```

The skill **does not auto-create** child stories during epic drafting. It produces the epic + the list of stories to draft next, and the requester decides which to draft and when.

If a child story can't be named without naming a system or a layer (e.g., "Backend pour Y"), that's a Rule 13 violation — re-slice vertically.

### Section 5 — Success criteria au niveau epic

Three to six bullets. Epic-level success is **observable and measurable**, but coarser than story-level GWT.

**Acceptable:**
- *Un courtier peut activer, monitorer, et désactiver un abonnement sans intervention support.*
- *Le système distribue les leads aux courtiers abonnés selon une règle convenue, mesurable côté backend.*
- *L'historique des transactions est consultable par le courtier.*

**Not acceptable here (push to child stories or remove):**
- Implementation specifics — no field names, no service names, no event names. Rule 14 applies at every level.
- Vague aspirations — *« meilleure expérience courtier »* is not measurable.
- Sprint-level details — those go in child stories, not in the epic.

### Section 6 — Validation epic

A 4-line check (epic-specific — INVEST applies to stories, not directly to epics):

- **Pourquoi maintenant ?** *(driver business / contractuel / compliance — cité du requester ou de l'initiative)*
- **Découpage** *(toutes les stories enfants sont identifiées, et chacune est sprint-shippable indépendamment)*
- **Edges du périmètre** *(le hors-périmètre est explicite et discuté)*
- **Mesurabilité** *(les success criteria peuvent être vérifiés à la fin de l'epic, sans nouvelle interprétation)*

For each line, write a single sentence with the evidence. If a line fails, mark `[FAIL — motif]` and surface in Bloc B as a question.

---

## Metadata checklist

- [ ] **Project** — no default, ask
- [ ] **Issue type** — typically `Epic`; confirm based on the project's available types
- [ ] **Parent** — theme, initiative, or roadmap item if applicable (ask, do not assume)
- [ ] **Labels** — suggest from project scan (Step 2 of skill flow)
- [ ] **Priority** — ask
- [ ] **Reporter / owner** — ask
- [ ] **Time-window estimate** — Atlassian: *« a couple weeks... not too long and not too short »*. Quarter-sized is typical.
- [ ] **Linked tickets** — every child story to come gets `Belongs to epic: [this key]`

---

## Anti-patterns to flag during drafting

1. **Epic that's actually a story.** If the requester estimates 1 sprint or less, redirect to `new-feature.md`.
2. **Epic that's actually an initiative.** If the work spans multiple quarters AND multiple teams AND multiple goals, the input is at the initiative level — the skill drafts the *slice that fits in 1-2 quarters* as the epic, and notes the initiative parent.
3. **Epic without identified child stories.** Section 4 is mandatory. If unknown, the requester must either decompose first or accept that the epic is not yet ready to file.
4. **Epic with full GWT inline.** GWT belongs in child stories. An epic with 30 GWT scenarios is a story stuffed full of unrelated cases.
5. **Epic that mixes business outcomes.** An epic should serve one coherent objective. If you find yourself writing two distinct success criteria pillars (e.g., "subscriptions" + "tile cleanup"), that's two epics.
