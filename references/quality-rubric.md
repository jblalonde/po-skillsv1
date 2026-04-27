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

## Rule 11 — INVEST compliance

Every ticket must pass the six INVEST criteria: **I**ndependent, **N**egotiable, **V**aluable, **E**stimable, **S**mall, **T**estable. This is load-bearing — it is the main quality signal, not a checkbox.

Run the full checklist in `references/invest-criteria.md` against the draft. For every criterion that fails:
- **Mechanical failure** (passive voice, missing quote, implementation leakage): fix silently.
- **Product decision** (scope needs split, dependency needs linking, persona is ambiguous): flag to the requester in Step 7 with a tagged bullet (`[S — Small]`, `[I — Independent]`, etc.).

Never ship a ticket with an unacknowledged INVEST failure. "The engineer will figure it out" is not an answer.

**Quick diagnostic:**
- **I** — could this ship on its own this sprint without a sibling ticket?
- **N** — does the AC describe outcomes or implementation?
- **V** — who specifically is better off when this ships?
- **E** — would three engineers converge within one Fibonacci step?
- **S** — one engineer, one sprint, no heroics?
- **T** — can QA write test cases from the AC without a follow-up conversation?

## Rule 12 — Given-When-Then for behavioural ACs

Any AC that describes a **behaviour** (a reaction to a user action, a system event, or a state transition) must be written in **Étant donné / Quand / Alors** (French) or **Given / When / Then** (English) format. Non-behavioural ACs (content inventory, enum of allowed values, verbatim UI copy, static rules) stay as bullets — don't force GWT where it adds noise.

**When GWT is required:**
- Trigger → reaction scenarios (user clicks X → Y happens)
- Happy paths
- Error paths
- Edge cases (empty state, loading state, race conditions)
- Conditional flows ("if card registered… / if not…")

**When bullets are fine:**
- Lists of allowed values, options, or enum members
- Verbatim UI string inventory (button labels, modal titles, error copy)
- Static invariants ("only one option selectable at a time")
- Cross-references to linked tickets

**Format (French):**

```
**Scénario — [nom court du scénario]**
- Étant donné [état initial du système / préconditions]
- Et [précondition additionnelle si nécessaire]
- Quand [action de l'utilisateur ou événement système]
- Alors [résultat observable #1]
- Et [résultat observable #2]
```

**Example — well-formed:**

```
**Scénario — Activation réussie avec carte enregistrée**
- Étant donné qu'un courtier est sur la tuile abonnement
- Et qu'une carte de crédit est enregistrée dans Stripe
- Quand il sélectionne « 20 demandes / jour » et clique sur « Activer »
- Alors le système envoie la requête d'activation à Stripe
- Et le bouton « Activer » affiche un spinner et est désactivé pendant l'appel
- Et au retour de Stripe, le modal de confirmation s'affiche (voir COEUR-XXXX)
```

**Example — malformed (flag or fix):**

```
- Quand l'utilisateur clique sur activer, ça active.
```
(No Étant donné, vague Alors, no observable outcome.)

**Fix when missing:** rewrite in GWT form. If the requester resists ("c'est évident"), explain that GWT is what lets QA write cases without a follow-up — it is the contract between product and engineering, not a formality.

## Rule 13 — Vertical slice, not horizontal silo

A ticket must deliver a **functional slice across the stack** — a thin piece of user-visible value traversing UI, API, and data where relevant — not a layered chunk (e.g., "build the frontend, backend comes later").

**Signals of a horizontal silo (flag and fix):**
- Summary names a layer: `Backend pour X`, `API endpoint for Y`, `Frontend du modal Z`.
- The ticket says the user-facing feature won't work until another ticket ships ("l'UI est là mais l'endpoint arrive au prochain sprint").
- The AC only describes things a user can't see (DB rows, queue messages, API responses) without tying them back to a user-visible outcome.
- Two tickets in the same epic read like a pair: one frontend, one backend, neither shippable alone.

**Signals of a healthy vertical slice:**
- A real user can complete a real action end-to-end when the ticket ships, even if the feature is still narrow.
- The ticket touches every layer the action needs (UI event → API call → persistence → response → UI update), scoped tightly.
- If the ticket is part of a series, each slice is individually releasable: "slice 1 = one volume option, slice 2 = all five options, slice 3 = custom amount" is fine; "slice 1 = UI, slice 2 = backend" is not.

**Diagnostic questions:**
- When this ships alone, can a real user benefit from it without waiting for a sibling ticket?
- Does the summary describe a user-facing capability or a technical layer?
- If an engineer merges this ticket's PR and nothing else, is there a demo to show?

**How to fix:**
- Rename the ticket in terms of the user-visible capability.
- Absorb the sibling layer into this ticket (if the combined scope stays Small), OR
- Re-slice the work vertically: each resulting ticket must independently ship a narrower but complete slice of user value.

Horizontal splits are sometimes requested by teams organised by specialty (dedicated frontend / backend squads). If that's the team's model, confirm with the requester once — then document the exception in the ticket and link the paired tickets to each other with a `Blocks / Blocked by` relationship.

## Rule 14 — Anti-fabrication (HARD STOP)

This rule overrides every other rule. **A fabricated specific is worse than an obvious gap.** A reader can act on a TODO; they can't act on a confidently-written hallucination, and once they catch one, they have to verify everything else in the ticket.

**Never write the following without a cited source:**

| Category | Examples (illustrative — do not copy as defaults) |
|---|---|
| Field / column names | `daily_volume`, `status`, `is_active`, `created_at` |
| Table or collection names | `subscriptions`, `users`, `lead_assignments` |
| Enum values | `status=active`, `state='ON'`, `tier='premium'` |
| Event names / topic names | `subscription.activated`, `UserCreated`, `lead-events` |
| Queue / pipeline names | `analytics-pipeline`, `lead-distribution-queue` |
| Service / module names | "moteur de distribution", "broker-svc", "billing-worker" |
| Endpoint paths | `POST /api/subscriptions`, `GET /v2/leads` |
| Library / SDK calls | `stripe.subscriptions.create()`, `prisma.user.update()` |
| Concrete thresholds, prices, SLAs | "100 demandes max", "$49/mois", "99.9% uptime" |
| Verbatim UI copy | button labels, error messages, modal titles, copy paragraphs |

**A "cited source" is one of:**
- A file you have read in this session (give the path).
- A URL you have fetched in this session (give the URL).
- A message from the user in this conversation (quote it).
- A Jira/Notion/Confluence/Slack item retrieved via MCP in this session (give the key/URL).

**Pattern-matching from training data is not a source.** "Most subscription systems use `status=active`" is not evidence about *this* system. The presence of "Stripe" in the context does not license inventing Stripe-adjacent field names.

### Forbidden behaviours

- **Plausible-default fabrication.** Writing `subscription.activated` because it sounds like an event a Stripe-integrated system would emit. Forbidden.
- **Schema invention from UI labels.** Seeing "Volume / jour" in the UI and writing `daily_volume` as the corresponding DB field. The DB field could be `requests_per_day`, `lead_quota_daily`, or anything else. Forbidden.
- **Service naming from user-visible features.** Inferring "moteur de distribution des leads" because the feature distributes leads. Whether such a service exists, and what it's called, is unknown until cited. Forbidden.
- **Enum guessing.** Writing `status='active'` because it's the most common convention. Forbidden — could be a boolean `is_active`, a state machine `state='ENABLED'`, or absent entirely.
- **Verbatim UI copy invention.** Paraphrasing or guessing button text or error messages. Forbidden — UI strings ship in code and screenshots; they must come from there.

### Required protocol when a specific is missing

When a specific would normally go in the ticket but you don't have a cited source for it:

1. Write `*[TODO — <what's missing> à fournir par <who>]*` in place of the specific.
2. State the precise question to be answered. "What table and field name is used for the broker's daily lead quota?" — not "TBD".
3. Name the source that would answer it: the engineer responsible, a Confluence page, a code repository, the PO.

**Example — correct:**

```
### Effets de bord système

*[TODO — à compléter avec un ingénieur familier du système. Questions à répondre :*
- *Quels champs sont modifiés à la confirmation Stripe ? (table + colonne + valeur)*
- *Un événement est-il émis vers d'autres services ? Si oui, nom et payload exacts.*
- *Comment l'activation déclenche-t-elle l'assignation automatique des leads ?]*
```

**Example — forbidden:**

```
### Effets de bord système

- À la confirmation Stripe, un enregistrement `subscription` est créé avec `status=active`
- Un événement `subscription.activated` est émis pour le pipeline analytics
```

(All five specifics in the forbidden example — the table name, the field name, the value, the event name, the pipeline name — are fabricated unless cited.)

### How this rule is enforced

- During Step 4 (drafting), every specific that would land in the ticket is mentally checked against "do I have a cited source for this?". If no, write a TODO instead.
- During Step 6 (rubric run), Rule 14 is the **first** check. Catching a fabrication here means stopping the draft and re-drafting the affected section as TODOs, not patching it.
- During Step 7 (presenting the draft), if any TODOs remain, surface them in the flags. Do not present a ticket where a fabricated specific has slipped through.

A fabricated ticket loses the engineer's trust on first read. Rebuilding that trust costs more than every minute saved by skipping the citation discipline.

## Rule 14b — TODOs are subject to Rule 14

A TODO is not a free pass. Writing `*[TODO — confirm the analytics pipeline event name]*` is itself a fabrication if no analytics pipeline has been mentioned by the requester or cited from a source. Soft fabrications inside TODOs leak the same false signal as hard fabrications inside ACs.

**A TODO must not:**

- **Name systems, services, queues, pipelines, layers, or flags** that have not been cited. Forbidden: *"confirm the `BrokerShop` flag with engineering"* when `BrokerShop` was never mentioned.
- **List suggested categories or examples** that imply specific architecture exists. Forbidden: *"what events are emitted (analytics, lead distribution, others)?"* — this names two systems hypothetically.
- **Frame the question as a leading dichotomy** that pre-supposes the design space. Forbidden: *"modal stays open or closes after error?"* when neither option has been confirmed as plausible by the requester.
- **Suggest solutions inside the question.** Forbidden: *"loading state — disabled button? spinner?"* — these are answers I am hinting at without authority to.

**A TODO must:**

- Pose **one open question**, in the user's domain language, naming what is missing and who can answer it.
- Avoid every architectural noun the requester has not used.
- Cite, where applicable, the verbatim phrase from the requester that triggered the question.

**Forbidden vs. compliant — examples:**

| Forbidden | Compliant |
|---|---|
| *[TODO — un événement est-il émis vers d'autres services (analytics, assignation de leads, autres) ?]* | *[TODO — quels effets observables se produisent côté backend à la confirmation Stripe ? Pour chaque effet : où il s'observe et comment le tester.]* |
| *[TODO — état de chargement : bouton désactivé ? spinner ?]* | *[TODO — quel feedback visuel est attendu pendant l'appel à Stripe ?]* |
| *[TODO — modal reste ouvert ou se ferme après erreur ?]* | *[TODO — que voit le courtier après l'échec, et que peut-il faire ensuite ?]* |
| *[TODO — flag `BrokerShop` à confirmer]* | *[TODO — quelles conditions déterminent la visibilité de la tuile « Abonnement » ?]* |
| *[TODO — hiérarchie primaire / secondaire des boutons ?]* | *[TODO — quel style et quelle prominence pour chacun des deux boutons ?]* |

### Required protocol when architecture context is missing

When drafting reaches a section that requires architectural specifics not provided by the requester:

1. **Stop drafting that section.**
2. **Ask the requester an open question first** — before writing any TODO. Phrasing should not name systems, services, or solutions. The requester's answer (or their explicit "I don't know, leave it open") is what licences the eventual TODO.
3. **Only after the answer**, write a TODO — and write it as openly as possible, citing what the requester said.

This protocol is part of Step 5 (Interview for gaps) and Step 4 (Drafting). The skill does not get to "fill in" architecture from imagination, even via TODOs.

## Rule 15 — The pasted ticket must be clean

Open questions are not part of the ticket. They live in a separate workspace block (see Step 7 of `SKILL.md`). The ticket as it appears in Jira must be readable as a finished artifact, not a half-filled form.

**The Block A (paste-ready) version must:**

- Contain no `[TODO …]` blocks longer than a short placeholder (`[copie à venir — voir Q3]`) that points to a numbered question in Block B.
- **Omit entirely** any section for which no content has been confirmed. A section that is 100% TODO (e.g., Effets de bord système with no input from PO or engineer) does not belong in the paste-ready version — it migrates to Block B as a question.
- Keep verbatim citations like *(verbatim PO)* — they are evidence, not commentary, and add value to the engineer reading the ticket.
- Render the Validation INVEST table, if kept, with the failing criteria's "Note" cells pointing to question numbers in Block B (e.g., "voir Q5, Q12") rather than restating the failure inline.

**Block B (workspace) carries:**

- The numbered open questions, grouped by addressee.
- The full reasoning behind any flagged INVEST failure, if needed.
- Any pre-creation decisions that need explicit PO sign-off (e.g., "absence de plafond sur le montant personnalisé — confirmer ou ajouter un plafond ?").

A reviewer reading only Block A in Jira should see a clean, scoped, coherent ticket. They should not see the drafting process. The drafting process belongs in the conversation, not in the ticket body.

## Rule 16 — Cards, Conversations, Confirmations (Atlassian's 3 C's)

Atlassian's *« 3 C's »* of a user story (see `references/atlassian-hierarchy.md`) are a meta-check that the ticket is *workable*, not just well-formatted.

A drafted **story** must satisfy all three Cs:

- **Card** — Summary, Contexte, and the description body are present and clear. The reader knows *what* is being built and *why* without scrolling outside the ticket.
- **Conversation** — Bloc B (workspace) lists the open questions to be answered before sprint commitment. If a brand-new ticket has zero open questions, that's suspicious — re-check whether something was silently assumed (Rule 14).
- **Confirmation** — the AC section contains testable scenarios in GWT (Rule 12). A QA reviewer can write test cases without follow-up questions, *or* the open questions in Bloc B that affect testability are explicitly named and the requester has acknowledged them as known gaps.

If any C is missing, the story is not yet ready for approval — flag in Step 7 and ask the requester to close the gap (or accept it explicitly as a known omission).

**Epics use a different validation path** (see `templates/epic.md` Section 6): Pourquoi maintenant / Découpage / Edges du périmètre / Mesurabilité. The 3 C's apply to stories.

---

## How to present rubric findings to the requester

If issues remain after your silent fix pass, present them like this in Step 7:

> Avant de créer, trois points à régler :
>
> 1. **[T — Testable]** L'AC « doit être réactif » n'est pas testable — je le remplace par « le modal s'ouvre dans les 300ms suivant le clic » ?
> 2. **[INVEST — S]** Le ticket couvre l'activation ET la modification en cours de route. Deux slices distincts — on scinde, ou tu confirmes qu'on garde les deux ici ?
> 3. **[Rule 12 — GWT]** Le scénario d'échec Stripe n'est pas en Étant donné / Quand / Alors. Je le reformule, ou tu préfères garder la bullet courte ?

Short, specific, each flag tagged with the rule it comes from, each asks for a decision. Not a lecture.
