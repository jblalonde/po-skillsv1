# Example — Adjustment ticket (COEUR-4270)

This is a cleaned, annotated version of a real adjustment ticket from the Groupe Jolicoeur (COEUR) project. Use it as a reference for style and structure.

---

## Ticket metadata (as filed)

- **Key:** COEUR-4270
- **Summary:** `Changer les notifications de nouvelles demandes web dans la boutique`
- **Type:** Task
- **Parent:** COEUR-3447 (Epic: WallStreet 2.0)
- **Labels:** `BrokerPortal`, `Features`
- **Priority:** Medium
- **Assignee:** guillaume baud
- **Reporter:** Justine Crépeau-Viau
- **Sprint:** COUCOU À MON TOUR
- **Due date:** 2026-04-27
- **Story points:** (not set)
- **Attachments:** none

---

## Description (as filed)

### Contexte

Présentement, il existe une notification qui se déclenche lorsqu'il y a une nouvelle demande web ajoutée à la boutique. Nous allons ajouter une notification lorsqu'un lead arrive dans les opportunités potentielles.

### Comportement souhaité

Mettre en place la notification pour qu'elle se déclenche lorsque l'utilisateur reçoit le lead

- Opportunité potentielle, nouveau prospect d'abonnement
  - La section de notification s'affiche seulement aux utilisateurs qui ont accès à la boutique
  - Par défaut, notification sur portail et sms activé
  - Une notification s'envoie lorsqu'un lead d'abonnement est assigné à un courtier et s'affiche dans le tableau des opportunités potentielles

La notification affiche: "Nouveau prospect de la boutique reçu — consultez votre portail."

---

## Why this ticket works well

- **Summary starts with a verb of change** (`Changer`) — immediately signals an adjustment, not a new feature.
- **Contexte is a state-of-the-world paragraph**, not a user story. It anchors the change against what exists today (the "nouvelle demande web" notification) and states what's being added (the "opportunités potentielles" notification). Two sentences, all the engineer needs.
- **Comportement souhaité is concrete**: trigger ("lorsque l'utilisateur reçoit le lead"), audience ("utilisateurs qui ont accès à la boutique"), default state ("portail et sms activé"), and the exact notification string in quotes.
- **No Design section** because the change is behavioral (a new notification trigger), not visual. Correctly omitted.
- **Story points not set** — typical for small behavioral adjustments, and the team is OK with this. A drafting skill should still ask ("no story points — is that intentional?") and accept the explicit answer.

## Where it could be even stronger

- The Contexte says "nous allons ajouter une notification" but the summary says "Changer les notifications" — is this an **add**, a **change**, or both? Worth clarifying in a real drafting session.
- **No link to the parent notification system** — the engineer has to go find where the existing web-request notification lives. A reference to the original ticket or a Confluence spec would help.
- **No error / fallback** — what happens if the SMS fails? Silently? Logged? Retried? Worth a question.
- The notification string is in French but the sub-bullets mix tense and casing. A small polish pass would help consistency.
