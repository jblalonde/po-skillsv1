# Adjustment ticket template

Use this template when the ticket is **changing** something that already exists, tweaking a notification, updating copy, changing a threshold, adding a filter option to an existing screen, etc. If the ticket is for something entirely new, use `new-feature.md` instead.

Adjustments are smaller than new features but not less rigorous. The same INVEST criteria apply, they just tend to pass more easily because the scope is narrower. Block A (the ticket as it lands in Jira) is light and matches what the team writes today; the quality bar (anti-fabrication, GWT, INVEST, "what stays unchanged") is enforced silently in Step 6 and surfaces as Block B questions when checks fail.

---

## Block A: sections that ship in Jira

```
1. Summary            : change verb + what's changing
2. Contexte           : état du monde + ce qui change + pourquoi + segment affecté
3. Comportement souhaité, bullets, testable; GWT for behavioural changes; verbatim UI copy
4. Design             : only if the change is visual (before/after + Figma)
5. Info supp / Non-inclus / Warning , optional, only when relevant
6. Details            : project, parent epic, labels, priority, assignee, sprint, story points
```

The **Validation INVEST** self-check is **not** a Block A section. It runs in Step 6, failures surface as Block B questions.

---

## Section guidance

### 1: Summary

Starts with an action verb of change.

**French verbs:** `Changer`, `Ajouter`, `Retirer`, `Modifier`, `Mettre à jour`, `Désactiver`, `Corriger`
**English verbs:** `Change`, `Add`, `Remove`, `Update`, `Disable`, `Fix`

**Good:**
- `Ajouter une notification d'expédition au courriel de confirmation de commande`
- `Ajouter un filtre par statut sur le tableau des commandes`
- `Retirer l'option "Lecture seule" du modal d'invitation`
- `Update the password reset email copy for clarity`

**Bad:**
- `Fix the notifications` (which notifications? fix what?)
- `Improve the dashboard UX` (not an adjustment, too broad)
- `Notification tweak` (what kind of tweak?)

### 2: Contexte

State of the world + what's being changed and why. **Not** a user story. Keep it to 2–5 sentences.

**Structure:**
1. What exists today (1–2 sentences, specific enough that an engineer can find it)
2. What's changing (1 sentence)
3. Why (1 sentence, the driver: user complaint, product decision, compliance, metric regression, etc.)
4. **Affected segment** (1 sentence, who experiences the change)

The persona on an adjustment is lighter than on a new feature (no full user-story arc), but the affected segment must still be named. "All customers" is fine if true; "customers who have placed at least one order in the last 30 days" changes how the change is tested.

**If the adjustment is tied to an existing ticket or spec**, link it in the first sentence: `Suite à PROJ-1234, nous ajustons…`. INVEST rule **I** (Independent) prefers explicit links over implicit context.

**Good:**

> Présentement, le courriel de confirmation de commande contient uniquement le récapitulatif des articles et le total (voir PROJ-1234 pour l'implémentation initiale). Nous ajoutons une deuxième section dans le même courriel qui annonce la date d'expédition prévue dès que le centre logistique a programmé l'envoi. Objectif : informer le client au moment où il peut planifier la réception, pas seulement à l'achat. Affecte tous les clients ayant passé une commande livrable.

**Bad:**

> We should change the order emails.

Why this is bad: no reference to what exists, no driver, no audience, and the engineer has no anchor.

> En tant que client, je veux recevoir plus de courriels…

Why this is bad: adjustments don't use user-story format. The change **is** the change, describe it directly.

### 3: Comportement souhaité

A single bullet list (no required sub-headings). Every bullet must be **testable**, a tester answers yes/no by looking at the build.

**Inside every list, address:**

- **The new behaviour**: described as GWT scenarios when behavioural (trigger → reaction).
- **The old behaviour being replaced**: when a behaviour is being retired, write **two** scenarios (old + new) using strike-through `~~old~~` so reviewers see what's being retired.
- **Verbatim UI copy**: both old and new strings when copy is changing: `Remplacer « Envoyer la demande » par « Soumettre »`. Quoted from a cited source, never paraphrased.
- **Default state**: if a toggle is added, what's the default (on/off)?
- **Audience**: which user segment sees the change (if narrower than the whole user base).
- **What explicitly stays unchanged**: for adjacent behaviours that reviewers or engineers might worry about. One bullet: `**Inchangé** : le comportement de [X] reste tel quel.`

#### GWT for behavioural changes

When a bullet describes a **behaviour** (a new trigger, a changed reaction, a state transition), write it in **Étant donné / Quand / Alors** format. See Rule 12 in `references/quality.md`.

**Example, adding a new trigger:**

```
**Scénario, Section d'expédition dans le courriel de confirmation**
- Étant donné qu'une commande livrable a été planifiée par le centre logistique
- Et que la date d'expédition est connue
- Quand le courriel de confirmation de commande est généré
- Alors le courriel contient une section « Expédition prévue le [date] » sous le récapitulatif des articles
- Et la section apparaît dans la même langue que le reste du courriel
```

**Example, replacing an existing behaviour (with strike-through):**

```
**Scénario, Notification de réinitialisation de mot de passe**
- Étant donné qu'un utilisateur demande la réinitialisation de son mot de passe
- Quand le système envoie le courriel
- ~~Alors le courriel contient le sujet « Reset your password »~~
- Alors le courriel contient le sujet « Réinitialiser votre mot de passe » dans la langue de l'utilisateur
```

If the change is purely a trigger condition (no user-visible output change), still write it as GWT, the precondition shift is the whole point.

#### Anti-fabrication

Field names, table names, event names, service names, and verbatim UI copy must come from a cited source (code, Figma, Notion, PO message). Never paraphrase a UI string from memory and never invent a backend specifier because it sounds plausible. If a source is missing, ask the requester an open question first (architecture-neutral); the answer becomes citable content, or the gap surfaces in Block B for an engineer. See Rule 14 / 14b in `references/quality.md`.

**Example, clean Comportement souhaité:**

- **Scénario, Section d'expédition dans le courriel de confirmation** *(GWT, voir ci-dessus)*
- Copie de la section : `Expédition prévue le [date]` *(verbatim, source : Notion PRD §3.2)*
- Format de la date : `JJ/MM/AAAA` en français, `MM/DD/YYYY` en anglais. *(règle)*
- Position dans le courriel : sous le récapitulatif des articles, au-dessus du total.
- Audience : tous les clients recevant le courriel de confirmation pour une commande livrable (conditions précises à confirmer en Q2). *(segment)*
- **Inchangé** : le récapitulatif des articles, le total, et le pied de page du courriel restent identiques à aujourd'hui.
- **Inchangé** : les courriels pour les commandes non-livrables (téléchargement numérique, billet d'événement) n'incluent pas la nouvelle section.

### 4: Design *(only if the change is visual)*

If the adjustment affects something on screen, include a Design section with:
- Screenshot of the **before** state (context for the engineer and QA)
- Screenshot or Figma link of the **after** state
- Figma link with `node-id`

If the change is purely behavioural (a rule, a threshold, a notification trigger with no new UI), **omit the section entirely**, do not add an empty heading.

### 5: Info supplémentaire / Non-inclus / Warning *(optional)*

Use these short subsections only when the content meaningfully helps the engineer.

- **Info supp**: business context that doesn't fit Contexte (e.g., a downstream impact, a related metric being tracked).
- **Non-inclus**: explicit out-of-scope items when over-interpretation is a real risk. Use the **Inchangé** bullets in Section 3 for narrow technical "stays the same" notes; reserve Non-inclus for adjacent capabilities.
- **Warning**: known risks, blocked dependencies, or constraints the engineer must know upfront.

### 6: Details

Metadata checklist. Confirm every field with the requester. Defaults are suggestions, never apply without asking.

- [ ] **Project**: no default, ask
- [ ] **Issue type**: usually Task; confirm based on project's available types
- [ ] **Parent epic**: ask; adjustments are often linked to an epic but sometimes stand alone
- [ ] **Labels**: suggest from labels actually used in the project
- [ ] **Priority**: ask
- [ ] **Assignee**: ask
- [ ] **Reporter**: defaults to whoever is running the skill; confirm
- [ ] **Sprint**: ask
- [ ] **Due date**: ask
- [ ] **Story points**: ask. Adjustments are often 1–3 points on a Fibonacci scale, sometimes unestimated by team convention. Always ask, never assume.
- [ ] **Linked tickets**: `Relates to` the original ticket being adjusted, at minimum

---

## Quality gates (run silently in Step 6)

These do not appear as sections in Block A. They run during drafting and any failure surfaces as a numbered question in Block B.

### INVEST self-check

- **I, Independent**: can this ticket ship without waiting on another? If blocked, is there an explicit `Blocked by` link?
- **N, Negotiable**: does the Comportement souhaité describe outcomes, not implementation?
- **V, Valuable**: who specifically gains what observable benefit from this adjustment?
- **E, Estimable**: no blocking unknowns?
- **S, Small**: scope of change circumscribed to one identifiable behaviour?
- **T, Testable**: every behavioural change in GWT, every UI copy quoted verbatim?

If a criterion fails, the failure becomes a Block B question, never a fabricated pass-line.

### Other gates checked silently

- **Anti-fabrication (Rule 14, 14b)**: no invented service/event/table/flag/copy.
- **Affected segment named**: Contexte must name who experiences the change.
- **What stays unchanged is explicit**: for adjacent behaviours at risk of misinterpretation.
- **Linked back to original**: adjustments tied to an existing feature must `Relates to` the original ticket.

---

## Common pitfalls specific to adjustment tickets

**Pitfall 1, Adjustment that's actually a new feature.** If the "change" involves building a new screen, modal, or flow from scratch, it's a new feature. Use `new-feature.md` instead. Ask the requester if it's unclear.

**Pitfall 2, Missing "what's not changing".** For complex systems, engineers sometimes over-interpret an adjustment and refactor adjacent behaviour. When there's any risk of ambiguity, use the **Inchangé** bullets in Section 3 aggressively.

**Pitfall 3, Forgetting the old UI strings.** If you're replacing a string with a new one, quote both: the old one (so engineers can find it in the code) and the new one. Example: `Remplacer « Envoyer la demande » par « Soumettre »`.

**Pitfall 4, No link back to the original ticket.** If the adjustment is tied to a feature that shipped via another ticket, link that ticket in Contexte so engineers have the original spec. Unlinked adjustments fail INVEST rule **I** (Independent) by default.

**Pitfall 5, The "while we're at it" adjustment.** If during drafting the requester says "oh and can we also change X", that's a second ticket. One adjustment = one identifiable change. Bundling leads to larger estimates, riskier reviews, and blocked releases when one half isn't ready.

---

## Revisions after review

Same strike-through convention as new features:

```
~~Par défaut, courriel envoyé au seul propriétaire du compte~~ Par défaut, courriel envoyé au propriétaire et aux admins de l'organisation
```
