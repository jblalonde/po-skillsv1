# Adjustment ticket template

Use this template when the ticket is **changing** something that already exists — tweaking a notification, updating copy, changing a threshold, adding a filter option to an existing screen, etc. If the ticket is for something entirely new, use `new-feature.md` instead.

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

The description has two sections in this order: Contexte, Comportement souhaité. Do not add a Critères d'acceptation section — the desired-behavior section does that work in this template.

### Section 1 — Contexte

State of the world + what's being changed and why. **Not** a user story. Keep it to 2–4 sentences.

**Structure:**
1. What exists today (1 sentence)
2. What's changing (1 sentence)
3. Why (optional — 1 sentence if the rationale isn't obvious)

**Good:**
> Présentement, il existe une notification qui se déclenche lorsqu'il y a une nouvelle demande web ajoutée à la boutique. Nous allons ajouter une notification lorsqu'un lead arrive dans les opportunités potentielles.

**Bad:**
> En tant que courtier, je veux recevoir plus de notifications...
> (Adjustments don't use user-story format. The change is the change.)

> We should change the notifications.
> (No reference to what exists today. Engineer has no anchor.)

### Section 2 — Comportement souhaité / Desired behavior

Describes the new behavior in concrete terms. Bullets are fine; prose is fine; both together is also fine. Structure the content around:

- What triggers the new behavior (event, user action, state change)
- Who sees/experiences it (all users? a subset? with what permissions?)
- What's displayed or what happens
- Exact UI strings (quoted verbatim — this is non-negotiable)
- Default state (on / off) if it's a toggle or opt-in
- Anything that stays the same (explicit about what is NOT changing, if that's a common source of confusion)

**Good:**
> Mettre en place la notification pour qu'elle se déclenche lorsque l'utilisateur reçoit le lead
> - Opportunité potentielle, nouveau prospect d'abonnement
>   - La section de notification s'affiche seulement aux utilisateurs qui ont accès à la boutique
>   - Par défaut, notification sur portail et SMS activé
>   - Une notification s'envoie lorsqu'un lead d'abonnement est assigné à un courtier et s'affiche dans le tableau des opportunités potentielles
>
> La notification affiche: "Nouveau prospect de la boutique reçu — consultez votre portail."

**Bad:**
> Add a notification for new leads. It should be similar to the existing one.
> (Which existing one? What does "similar" mean? What does the notification say?)

### Section 3 — Design (only if the change is visual)

If the adjustment affects something on screen, include a Design section with:
- Screenshot of the before state (if useful for context)
- Screenshot or Figma link of the after state
- Figma link with node-id

If the change is purely behavioral (a rule, a threshold, a notification trigger with no new UI), skip this section.

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
- [ ] **Story points** — ask; adjustments are often smaller or unestimated, but always ask — don't assume

---

## Common pitfalls specific to adjustment tickets

**Pitfall 1 — Adjustment that's actually a new feature.** If the "change" involves building a new screen, modal, or flow from scratch, it's a new feature. Use `new-feature.md` instead. Ask the requester if it's unclear.

**Pitfall 2 — Missing "what's not changing".** For complex systems, engineers sometimes over-interpret an adjustment and refactor adjacent behavior. If there's ambiguity, add a line: `Le [autre comportement] reste inchangé` / `The [other behavior] stays unchanged`.

**Pitfall 3 — Forgetting the old UI strings.** If you're replacing a string with a new one, quote both: the old one (so engineers can find it in the code) and the new one. Example: *Remplacer "Envoyer la demande" par "Soumettre"*.

**Pitfall 4 — No link back to the original ticket.** If the adjustment is tied to a feature that shipped via another ticket, link that ticket in "Contexte" so engineers have the original spec.

---

## Revisions after review

Same strike-through convention as new features:

```
~~Par défaut, notification sur portail seulement~~ Par défaut, notification sur portail et SMS activé
```
