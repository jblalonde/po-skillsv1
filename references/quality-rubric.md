# Reference — Quality rubric

Run this rubric silently against every draft before showing the final version to the requester (Step 6 of the workflow). For each rule that fails, either fix it silently (if the fix is obvious and the requester's intent is unambiguous) or flag it to the requester (if it needs their input).

The rubric is not a gate. It's a checklist that makes sure nothing sneaks through that would cost an engineer a morning.

---

## Rule 1 — No vague verbs

Ban these (and their French equivalents):

| English | French |
|---|---|
| "should work well" | "devrait bien fonctionner" |
| "should be intuitive" | "devrait être intuitif" |
| "user-friendly" | "convivial" |
| "seamless" | "fluide" (when used without specifics) |
| "handle gracefully" | "gérer correctement" |
| "as appropriate" | "au besoin" |
| "etc." | "etc." |
| "fast" / "quickly" (without a number) | "rapide" / "rapidement" (sans chiffre) |

**Fix:** Replace with measurable or enumerable phrasing. "Fast" becomes "responds within 500ms". "Handle errors gracefully" becomes "on failure, display error message X and leave the form in its pre-submit state". "As appropriate" becomes an explicit list.

## Rule 2 — Every AC must be testable

A tester should be able to answer "yes" or "no" by looking at the final build. If the AC describes a feeling, a vibe, or a quality judgment, it's not testable.

**Not testable:**
- The modal feels responsive.
- The flow is streamlined.
- Users can complete the action without friction.

**Testable:**
- The modal opens within 300ms of the button click.
- The flow has 3 steps (select plan → confirm payment → success modal) with no extra confirmations.
- Clicking "Activer" with a valid selection creates the subscription and dismisses the modal.

## Rule 3 — UI strings are quoted verbatim

Any text that appears on screen — button labels, modal titles, error messages, notification copy, placeholder text, help text — must be in quotes and identical to what ships.

**Bad:**
- There is a button to confirm and another to cancel.
- The error should say something like "try again later".

**Good:**
- Two buttons: "Activer" (primary) and "Annuler" (secondary).
- On failure, display: "Échec de l'activation. Veuillez réessayer." in the modal footer.

## Rule 4 — Error, empty, and loading states when UI is involved

If the ticket affects a screen or component, check that the description addresses:

- **Error state** — what happens when the backend call fails, the form is invalid, the user loses connection?
- **Empty state** — what shows up when there's no data (no leads yet, no transactions yet)?
- **Loading state** — what does the UI do while a request is in flight? Is the button disabled? Does a spinner appear?

If any of these is genuinely not applicable (e.g. the action is instant with no backend call), say so explicitly — don't leave it ambiguous.

## Rule 5 — No passive voice hiding the actor

"The subscription is activated" — by whom? The user clicking? A backend job? A Stripe webhook?

Rewrite to name the actor:
- "The broker clicks 'Activer'" (user action)
- "The backend creates the subscription in Stripe" (system action)
- "Stripe's webhook confirms activation" (third-party action)

French equivalent: avoid `est + participe passé` without a subject. "L'abonnement est créé" → "Le système crée l'abonnement".

## Rule 6 — Exactly one ticket's worth of scope

A well-scoped ticket does one identifiable thing. If the description mixes multiple changes (new modal + new notification + new API endpoint), suggest splitting.

Questions to test scope:
- Could two engineers divide this ticket into two independent PRs easily? → probably two tickets.
- Does the AC list include something that would need a separate PR to ship? → probably two tickets.
- Is the Design section covering three different screens? → probably three tickets.

## Rule 7 — Cross-references instead of duplication

If this ticket depends on or is referenced by another, link it rather than duplicating content. Examples:

- "En cas de succès, le modal de confirmation s'affiche (voir COEUR-XXXX)"
- "See parent epic COEUR-YYYY for the overall flow"

## Rule 8 — Language consistency

The ticket is in one language (confirmed in Step 1). Check:
- Summary, all sections, and metadata field names match that language
- UI strings are in their **original** language even if the ticket language is different. An English ticket referencing a French button says: `The "Activer" button triggers...`

## Rule 9 — Template fidelity

- New-feature ticket has Contexte (user story), Critères d'acceptation, Design. In that order.
- Adjustment ticket has Contexte (state of world), Comportement souhaité. Design only if visual.
- No extra headers that aren't in the template unless the requester explicitly asked.

## Rule 10 — No silent omissions

Every metadata field in the template's checklist has been explicitly addressed — either filled in or marked "intentionally left blank, confirmed with requester". A story-points field being empty is fine if the requester said so; it's not fine if nobody asked.

---

## How to present rubric findings to the requester

If issues remain after your silent fix pass, present them like this in Step 7:

> Before I create this, three things to check:
>
> 1. The AC "must feel responsive" isn't testable — want me to change it to something like "the modal opens within 300ms"?
> 2. No error state is described for when Stripe setup fails — should I add one?
> 3. Story points not set — intentional, or want an estimate?

Short, specific, asks for a decision. Not a lecture.
