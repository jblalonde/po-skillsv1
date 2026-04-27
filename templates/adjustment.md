# Adjustment ticket template

Use this template when the ticket is **changing** something that already exists — tweaking a notification, updating copy, changing a threshold, adding a filter option to an existing screen, etc. If the ticket is for something entirely new, use `new-feature.md` instead.

Adjustments are smaller than new features but not less rigorous. The same INVEST criteria apply — they just tend to pass more easily because the scope is narrower.

---

## Summary

Starts with an action verb of change.

**French verbs to use:** `Changer`, `Ajouter`, `Retirer`, `Modifier`, `Mettre à jour`, `Désactiver`, `Corriger`
**English verbs to use:** `Change`, `Add`, `Remove`, `Update`, `Disable`, `Fix`

**Good examples:**
- `Changer les notifications de nouvelles demandes web dans la boutique`
- `Ajouter un filtre par date sur le tableau des leads`
- `Retirer l'option "100 demandes par mois" du modal d'abonnement`
- `Update the lead assignment rules for high-volume brokers`

**Bad examples:**
- `Fix the notifications` (which notifications? fix what?)
- `Improve the store UX` (not an adjustment, too broad)
- `Notification tweak` (what kind of tweak?)

---

## Description structure

The description has three sections in this order: Contexte, Comportement souhaité, Validation INVEST. A Design section is added only if the change is visual. **Do not** add a `Critères d'acceptation` header — in an adjustment, the Comportement souhaité section does that work with GWT scenarios.

### Section 1 — Contexte

State of the world + what's being changed and why. **Not** a user story. Keep it to 2–5 sentences.

**Structure:**
1. What exists today (1–2 sentences — specific enough that an engineer can find it)
2. What's changing (1 sentence)
3. Why (1 sentence — the driver: user complaint, product decision, compliance, metric regression, etc.)
4. **Qualified persona or affected segment** (1 sentence — who experiences the change)

The persona on an adjustment is lighter than on a new feature (no full user-story arc), but the affected segment must still be named. "All brokers" is fine if true; "brokers who have already activated a subscription" changes how the change is tested.

**If the adjustment is tied to an existing ticket or spec**, link it in the first sentence: `Suite à COEUR-3835, nous ajustons…`. INVEST rule **I** (Independent) prefers explicit links over implicit context.

**Good:**

> Présentement, la notification « Nouveau prospect » se déclenche lorsqu'une nouvelle demande web est ajoutée à la boutique (voir COEUR-3202 pour l'implémentation initiale). Nous ajoutons une deuxième notification qui se déclenche lorsqu'un lead est assigné dans les opportunités potentielles. Objectif : informer le courtier au moment où il peut agir, pas seulement à l'arrivée de la demande côté boutique. Affecte tous les courtiers ayant accès à la boutique de leads.

**Bad:**

> We should change the notifications.

Why this is bad: no reference to what exists, no driver, no audience, and the engineer has no anchor.

> En tant que courtier, je veux recevoir plus de notifications…

Why this is bad: adjustments don't use user-story format. The change **is** the change — describe it directly.

### Section 2 — Comportement souhaité

The behavioural core of an adjustment. Two sub-sections, in this order.

#### 2.1 — Scénarios de changement

Every behavioural change is expressed as **Étant donné / Quand / Alors** (see `references/quality-rubric.md` Rule 12). When an adjustment replaces an existing behaviour, write **two** scenarios — the old and the new — using strike-through on the old one so reviewers see what's being retired.

**Format:**

```
**Scénario — [nom court]**
- Étant donné [état initial]
- Quand [action / événement]
- Alors [résultat observable — nouveau]
```

**Example — adding a new trigger:**

```
**Scénario — Notification au courtier lorsqu'un lead est assigné**
- Étant donné qu'un courtier a accès à la boutique de leads
- Et que les notifications portail et SMS sont activées pour ce courtier (valeur par défaut)
- Quand un lead d'abonnement lui est assigné dans les opportunités potentielles
- Alors une notification « Nouveau prospect de la boutique reçu — consultez votre portail. » s'affiche dans le portail
- Et un SMS avec le même texte est envoyé au numéro enregistré
```

**Example — replacing an existing behaviour (with strike-through):**

```
**Scénario — Notification sur nouvelle demande web**
- Étant donné qu'une nouvelle demande web arrive dans la boutique
- Quand elle est ajoutée au backlog
- ~~Alors une notification « Nouvelle demande web ajoutée » est envoyée à tous les courtiers de la franchise~~
- Alors une notification « Nouvelle demande web ajoutée » est envoyée uniquement au courtier désigné responsable de la boutique
```

If the change is purely a trigger condition (no user-visible output change), still write it as GWT — the precondition shift is the whole point.

#### 2.2 — Règles, copie et ce qui ne change pas

Bullets. This sub-section carries:

- **Enumerated changes** — if multiple options or values are being added/removed, list them.
- **Verbatim UI copy** — if strings are changing, quote both old and new: `Remplacer « Envoyer la demande » par « Soumettre »`.
- **Default state** — if a toggle is added, what's the default (on/off)?
- **Audience** — which user segment sees the change (if narrower than the whole user base).
- **What explicitly stays unchanged** — for adjacent behaviours that reviewers or engineers might worry about. One line: `Le comportement de [X] reste inchangé.`

**⚠️ Anti-fabrication — applies here too.** Field names, table names, event names, service names, and verbatim UI copy must come from a cited source (code, Figma, Notion, PO message). Never paraphrase a UI string from memory and never invent a backend specifier because it sounds plausible. If a source is missing, write a TODO naming the question and the source that would answer it. See Rule 14 in `quality-rubric.md`.

**Example:**
- Copie de la notification portail : `Nouveau prospect de la boutique reçu — consultez votre portail.`
- Copie SMS : identique à la copie portail
- Par défaut : notification portail **et** SMS activés (peut être désactivée par le courtier dans ses préférences)
- Audience : courtiers avec le flag `BrokerShop` activé uniquement
- **Inchangé** : la notification « Nouvelle demande web » existante continue de fonctionner comme avant
- **Inchangé** : les règles d'assignation de leads ne sont pas modifiées — ce ticket porte uniquement sur la notification

### Section 3 — Design (only if the change is visual)

If the adjustment affects something on screen, include a Design section with:
- Screenshot of the **before** state (context for the engineer and QA)
- Screenshot or Figma link of the **after** state
- Figma link with node-id

If the change is purely behavioural (a rule, a threshold, a notification trigger with no new UI), skip this section entirely — do not add an empty heading.

### Section 4 — Validation INVEST (self-check)

Same structure as the new-feature template. One line per criterion.

```
**Validation INVEST**
- **I — Independent** : [ce ticket peut être livré sans attendre un autre ticket, ou ticket bloquant explicite]
- **N — Negotiable** : [le Comportement souhaité décrit l'outcome, pas l'implémentation]
- **V — Valuable** : [qui gagne quoi de mesurable grâce à l'ajustement]
- **E — Estimable** : [aucun TODO bloquant]
- **S — Small** : [le scope de changement est circonscrit à un comportement]
- **T — Testable** : [les changements sont en GWT, les copies en verbatim]
```

If any criterion fails, mark it `[FAIL — motif]` and raise it in Step 7.

---

## Metadata checklist

Before creating, confirm every field below with the requester. Defaults are suggestions — do not apply them without asking.

- [ ] **Project** — no default, ask
- [ ] **Issue type** — usually Task; confirm based on project's available types
- [ ] **Parent epic** — ask; adjustments are often linked to an epic but sometimes stand alone
- [ ] **Labels** — suggest from labels actually used in the project
- [ ] **Priority** — ask
- [ ] **Assignee** — ask
- [ ] **Reporter** — defaults to whoever is running the skill; confirm
- [ ] **Sprint** — ask
- [ ] **Due date** — ask
- [ ] **Story points** — ask. Adjustments are often 1–3 points on a Fibonacci scale, sometimes unestimated by team convention. Always ask — never assume.
- [ ] **Linked tickets** — `Relates to` the original ticket being adjusted, at minimum

---

## Common pitfalls specific to adjustment tickets

**Pitfall 1 — Adjustment that's actually a new feature.** If the "change" involves building a new screen, modal, or flow from scratch, it's a new feature. Use `new-feature.md` instead. Ask the requester if it's unclear.

**Pitfall 2 — Missing "what's not changing".** For complex systems, engineers sometimes over-interpret an adjustment and refactor adjacent behaviour. When there's any risk of ambiguity, use Section 2.2's **Inchangé** bullets aggressively.

**Pitfall 3 — Forgetting the old UI strings.** If you're replacing a string with a new one, quote both: the old one (so engineers can find it in the code) and the new one. Example: `Remplacer « Envoyer la demande » par « Soumettre »`.

**Pitfall 4 — No link back to the original ticket.** If the adjustment is tied to a feature that shipped via another ticket, link that ticket in Contexte so engineers have the original spec. Unlinked adjustments fail INVEST rule **I** (Independent) by default — the engineer has to go archaeology on the git history.

**Pitfall 5 — The "while we're at it" adjustment.** If during drafting the requester says "oh and can we also change X", that's a second ticket. One adjustment = one identifiable change. Bundling leads to larger estimates, riskier reviews, and blocked releases when one half isn't ready.

---

## Revisions after review

Same strike-through convention as new features:

```
~~Par défaut, notification sur portail seulement~~ Par défaut, notification sur portail et SMS activés
```
