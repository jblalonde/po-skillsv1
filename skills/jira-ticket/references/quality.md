# Reference — Quality (rubric + INVEST)

Two complementary quality systems live in this file: a rubric of 16 rules covering phrasing, structure, GWT, anti-fabrication, and vertical slicing; and the full INVEST checklist for stories. Both run silently in Step 6 of the workflow — failures surface as numbered questions in Block B, never as parasitic sections in Block A.

Section 1 (Rules 1–16) is the rubric. Section 2 is the detailed INVEST checklist that Rule 11 points to.

---

# Section 1 — Quality rubric (Rules 1–16)


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

"The invitation is sent" — by whom? The user clicking? A backend job? A third-party email service?

Rewrite to name the actor:
- "The admin clicks 'Send invitation'" (user action)
- "The backend queues the invitation email" (system action)
- "The email-sending vendor's webhook confirms delivery" (third-party action)

French equivalent: avoid `est + participe passé` without a subject. "L'invitation est créée" → "Le système crée l'invitation".

## Rule 6 — Exactly one ticket's worth of scope

A well-scoped ticket does one identifiable thing. If the description mixes multiple changes (new modal + new notification + new API endpoint), suggest splitting.

Questions to test scope:
- Could two engineers divide this ticket into two independent PRs easily? → probably two tickets.
- Does the AC list include something that would need a separate PR to ship? → probably two tickets.
- Is the Design section covering three different screens? → probably three tickets.

## Rule 7 — Cross-references instead of duplication

If this ticket depends on or is referenced by another, link it rather than duplicating content. Examples:

- "En cas de succès, le modal de confirmation s'affiche (voir PROJ-XXXX)"
- "See parent epic PROJ-YYYY for the overall flow"

## Rule 8 — Language consistency

The ticket is in one language (confirmed in Step 1). Check:
- Summary, all sections, and metadata field names match that language
- UI strings are in their **original** language even if the ticket language is different. An English ticket referencing a French button says: `The "Activer" button triggers...`

## Rule 9 — Template fidelity

Block A section structure must match the template exactly. No extra headers that aren't in the template unless the requester explicitly asked.

- **Epic** — Summary / Objectif / Contexte / Acceptance criteria (3–6 epic-level bullets, NOT GWT) / Design général *(if applicable)* / Non-inclus / Stories enfants / Details.
- **New-feature story** — Summary / Contexte (qualified persona, user-story format) / Acceptance criteria (single bullet list) / Design / Info supp · Non-inclus · Warning *(optional)* / Details.
- **Adjustment story** — Summary (change verb) / Contexte (state of world) / Comportement souhaité (single bullet list) / Design *(only if visual)* / Info supp · Non-inclus · Warning *(optional)* / Details.

The Validation INVEST self-check (stories) and 4-line Validation epic check (epics) are **quality gates that run in Step 6**, not Block A sections. Failing criteria surface as numbered Block B questions tagged `[INVEST — X]` or `[Validation epic — X]`.

## Rule 10 — No silent omissions

Every metadata field in the template's checklist has been explicitly addressed — either filled in or marked "intentionally left blank, confirmed with requester". A story-points field being empty is fine if the requester said so; it's not fine if nobody asked.

## Rule 11 — INVEST compliance

Every ticket must pass the six INVEST criteria: **I**ndependent, **N**egotiable, **V**aluable, **E**stimable, **S**mall, **T**estable. This is load-bearing — it is the main quality signal, not a checkbox.

Run the full checklist in **Section 2 of this file** against the draft. For every criterion that fails:
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
**Scénario — Invitation envoyée à une adresse valide**
- Étant donné qu'un admin d'équipe est sur la liste des membres
- Et qu'il a la permission « inviter un membre »
- Quand il saisit une adresse courriel valide, sélectionne « Membre standard », et clique sur « Envoyer l'invitation »
- Alors le système met le courriel d'invitation en file d'envoi
- Et le bouton « Envoyer l'invitation » affiche un spinner et est désactivé pendant l'appel
- Et au retour du système d'envoi, le modal de confirmation s'affiche (voir PROJ-XXXX)
```

**Example — malformed (flag or fix):**

```
- Quand l'utilisateur clique sur envoyer, ça envoie.
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
| Field / column names | `daily_count`, `status`, `is_active`, `created_at` |
| Table or collection names | `invitations`, `users`, `order_items` |
| Enum values | `status=active`, `state='ON'`, `tier='premium'` |
| Event names / topic names | `invitation.sent`, `UserCreated`, `order-events` |
| Queue / pipeline names | `analytics-pipeline`, `notification-queue` |
| Service / module names | "moteur d'envoi", "user-svc", "billing-worker" |
| Endpoint paths | `POST /api/invitations`, `GET /v2/orders` |
| Library / SDK calls | `mailer.send()`, `prisma.user.update()` |
| Concrete thresholds, prices, SLAs | "100 invitations max / jour", "$49/mois", "99.9% uptime" |
| Verbatim UI copy | button labels, error messages, modal titles, copy paragraphs |

**A "cited source" is one of:**
- A file you have read in this session (give the path).
- A URL you have fetched in this session (give the URL).
- A message from the user in this conversation (quote it).
- A Jira/Notion/Confluence/Slack item retrieved via MCP in this session (give the key/URL).

**Pattern-matching from training data is not a source.** "Most user systems use `status=active`" is not evidence about *this* system. The presence of a vendor or technology name (a payment processor, an email service, a CRM) in the context does not license inventing field names, event names, or endpoints adjacent to that vendor.

### Forbidden behaviours

- **Plausible-default fabrication.** Writing `invitation.sent` because it sounds like an event a typical product would emit. Forbidden.
- **Schema invention from UI labels.** Seeing "Items / order" in the UI and writing `items_per_order` as the corresponding DB field. The DB field could be `line_item_count`, `order_size`, or anything else. Forbidden.
- **Service naming from user-visible features.** Inferring "moteur d'envoi de courriels" because the feature sends emails. Whether such a service exists, and what it's called, is unknown until cited. Forbidden.
- **Enum guessing.** Writing `status='active'` because it's the most common convention. Forbidden — could be a boolean `is_active`, a state machine `state='ENABLED'`, or absent entirely.
- **Verbatim UI copy invention.** Paraphrasing or guessing button text or error messages. Forbidden — UI strings ship in code and screenshots; they must come from there.

### Required protocol when a specific is missing

When a specific would normally go in the ticket but you don't have a cited source for it:

1. Write `*[TODO — <what's missing> à fournir par <who>]*` in place of the specific.
2. State the precise question to be answered. "What table and field name represents the user's invitation quota per day?" — not "TBD".
3. Name the source that would answer it: the engineer responsible, a Confluence page, a code repository, the PO.

**Example — correct:**

```
### Effets de bord système

*[TODO — à compléter avec un ingénieur familier du système. Questions à répondre :*
- *Quels champs sont modifiés à la confirmation de l'action ? (table + colonne + valeur)*
- *Un événement est-il émis vers d'autres services ? Si oui, nom et payload exacts.*
- *Comment l'action déclenche-t-elle des comportements automatiques en aval ?]*
```

**Example — forbidden:**

```
### Effets de bord système

- À la confirmation, un enregistrement `invitation` est créé avec `status=active`
- Un événement `invitation.sent` est émis pour le pipeline analytics
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

- **Name systems, services, queues, pipelines, layers, or flags** that have not been cited. Forbidden: *"confirm the `BetaUsers` flag with engineering"* when `BetaUsers` was never mentioned.
- **List suggested categories or examples** that imply specific architecture exists. Forbidden: *"what events are emitted (analytics, notifications, others)?"* — this names two systems hypothetically.
- **Frame the question as a leading dichotomy** that pre-supposes the design space. Forbidden: *"modal stays open or closes after error?"* when neither option has been confirmed as plausible by the requester.
- **Suggest solutions inside the question.** Forbidden: *"loading state — disabled button? spinner?"* — these are answers I am hinting at without authority to.

**A TODO must:**

- Pose **one open question**, in the user's domain language, naming what is missing and who can answer it.
- Avoid every architectural noun the requester has not used.
- Cite, where applicable, the verbatim phrase from the requester that triggered the question.

**Forbidden vs. compliant — examples:**

| Forbidden | Compliant |
|---|---|
| *[TODO — un événement est-il émis vers d'autres services (analytics, notifications, autres) ?]* | *[TODO — quels effets observables se produisent côté backend à la confirmation de l'action ? Pour chaque effet : où il s'observe et comment le tester.]* |
| *[TODO — état de chargement : bouton désactivé ? spinner ?]* | *[TODO — quel feedback visuel est attendu pendant l'appel au service externe ?]* |
| *[TODO — modal reste ouvert ou se ferme après erreur ?]* | *[TODO — que voit l'utilisateur après l'échec, et que peut-il faire ensuite ?]* |
| *[TODO — flag `BetaUsers` à confirmer]* | *[TODO — quelles conditions déterminent la visibilité de cet écran ?]* |
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
- **Omit entirely** any optional section for which no content has been confirmed. Examples: a Design section when no Figma exists yet, an Info supp / Non-inclus / Warning sub-section when not relevant, the Design général section in an epic when no high-level mockup exists. A section that is 100% TODO does not belong in the paste-ready version — it migrates to Block B as a question.
- Keep verbatim citations like *(verbatim PO)* — they are evidence, not commentary, and add value to the engineer reading the ticket.
- The Validation INVEST self-check (stories) and 4-line Validation epic check (epics) do **not** ship in Block A. Failing criteria appear only as numbered Block B questions, tagged with the criterion that failed.

**Block B (workspace) carries:**

- The numbered open questions, grouped by addressee.
- The full reasoning behind any flagged INVEST failure, if needed.
- Any pre-creation decisions that need explicit PO sign-off (e.g., "absence de plafond sur le montant personnalisé — confirmer ou ajouter un plafond ?").

A reviewer reading only Block A in Jira should see a clean, scoped, coherent ticket. They should not see the drafting process. The drafting process belongs in the conversation, not in the ticket body.

## Rule 16 — Cards, Conversations, Confirmations (Atlassian's 3 C's)

Atlassian's *« 3 C's »* of a user story (see the Atlassian hierarchy section in `SKILL.md`) are a meta-check that the ticket is *workable*, not just well-formatted.

A drafted **story** must satisfy all three Cs:

- **Card** — Summary, Contexte, and the description body are present and clear. The reader knows *what* is being built and *why* without scrolling outside the ticket.
- **Conversation** — Bloc B (workspace) lists the open questions to be answered before sprint commitment. If a brand-new ticket has zero open questions, that's suspicious — re-check whether something was silently assumed (Rule 14).
- **Confirmation** — the AC section contains testable scenarios in GWT (Rule 12). A QA reviewer can write test cases without follow-up questions, *or* the open questions in Bloc B that affect testability are explicitly named and the requester has acknowledged them as known gaps.

If any C is missing, the story is not yet ready for approval — flag in Step 7 and ask the requester to close the gap (or accept it explicitly as a known omission).

**Epics use a different validation path** (see the *Quality gates* section of `templates/epic.md`): Pourquoi maintenant / Découpage / Edges du périmètre / Mesurabilité. The 3 C's apply to stories.

---

## How to present rubric findings to the requester

If issues remain after your silent fix pass, present them like this in Step 7:

> Avant de créer, trois points à régler :
>
> 1. **[T — Testable]** L'AC « doit être réactif » n'est pas testable — je le remplace par « le modal s'ouvre dans les 300ms suivant le clic » ?
> 2. **[INVEST — S]** Le ticket couvre l'activation ET la modification en cours de route. Deux slices distincts — on scinde, ou tu confirmes qu'on garde les deux ici ?
> 3. **[Rule 12 — GWT]** Le scénario d'échec côté service externe n'est pas en Étant donné / Quand / Alors. Je le reformule, ou tu préfères garder la bullet courte ?

Short, specific, each flag tagged with the rule it comes from, each asks for a decision. Not a lecture.

---

# Section 2 — INVEST checklist (referenced from Rule 11)


Every ticket drafted by this skill must pass INVEST before it is presented for approval. This section is loaded during Step 6 of the workflow alongside Section 1 (the rubric) above.

INVEST is not a formality. It is the difference between a ticket an engineer can pick up and ship, and a ticket that will generate five clarification threads, a mid-sprint re-scope, and a half-shipped feature.

Run the six checks **silently** against the draft. For each failure, decide:
- **Fix silently** when the intent is unambiguous and the fix is mechanical (e.g., a behaviour is described in passive voice — rewrite it).
- **Flag for the requester** when the fix requires a product decision (e.g., the ticket mixes two independent slices — ask how to split).

---

## I — Independent

A ticket should be shippable without waiting on another ticket in the same sprint. Dependencies are allowed; entanglement is not.

**Signals of failure:**
- The AC references a sibling ticket that hasn't been drafted yet (`voir autre ticket`, `à faire plus tard`).
- The ticket can only be tested once another unshipped ticket is merged.
- Two tickets edit the same endpoint or component in overlapping ways and will conflict.

**Diagnostic questions:**
- If engineer A picks this up Monday and engineer B picks up the related ticket Wednesday, can A ship without B?
- Does the "success" path of this ticket depend on behaviour that lives in a separate, unshipped ticket?
- Could this ticket be merged to `main` on its own without breaking production?

**How to fix:**
- Link the dependency explicitly in Contexte (`Dépend de PROJ-XXXX`) and require that dependency to be merged first, OR
- Merge the two tickets into one, OR
- Split differently so each slice is independently shippable (often this means slicing **vertically** — see Rule 13 of the rubric).

---

## N — Negotiable

A ticket describes the **outcome**, not the implementation. The engineer owns the "how". The PO owns the "what" and the "why".

**Signals of failure:**
- The AC prescribes a specific data structure, ORM call, queue name, or component library.
- The description reads like a technical design doc.
- Solution-specific language creeps into user-visible behaviour ("uses Redis to cache", "via a webhook").
- **Fabricated technical specifics from pattern-matching** — field names, event names, table names, service names, enum values that "sound right" for the domain but have no cited source. This is the **single most common Negotiable failure** and is covered as a hard stop by Rule 14 in Section 1 above. Catching one means stopping and re-drafting the affected section as TODOs, not patching the wording.

**Exception:** when a specific technical choice is a product constraint (e.g., "must use the existing email-sending vendor under contract — no other vendor allowed"), state it in Contexte as a constraint, not in the AC as an implementation instruction.

**Exception is narrow.** A constraint is something the *requester explicitly said*, not something Claude infers. A vendor name being mentioned by the user is a constraint the user named. An event name like `invitation.sent` is *not* a constraint — it's a fabrication unless the user, a doc, or the code says so.

**Diagnostic questions:**
- Would a senior engineer have meaningful room to choose the approach, or is this a dictation?
- If the AC said the same thing without naming a library / table / service, would it still make sense to a tester?

**How to fix:**
- Move implementation specifics to a "Contraintes techniques" sub-section inside Contexte, if they are true constraints.
- Otherwise, rewrite the AC in terms of user-visible or system-visible behaviour.

---

## V — Valuable

Every ticket ships a slice of value to a real user or a real downstream system. "Refactor module X" is not a user story — it's a technical task, and belongs in its own lane (tech debt / chore), not in a feature ticket.

**Signals of failure:**
- No persona in Contexte, or a persona so generic it tells you nothing ("the user").
- The benefit clause of the user story is circular ("so that they can use the feature") or absent.
- The ticket describes internal plumbing with no user-observable change.

**Diagnostic questions:**
- Who, specifically, is better off when this ships? (role + segment + state)
- What measurable or observable outcome improves?
- If the ticket shipped silently and nobody was told, would anyone notice?

**How to fix:**
- Qualify the persona (see template's Contexte section — role + segment + context).
- Replace a generic benefit with a measurable outcome ("…afin que mon équipe puisse intégrer la personne en quelques minutes plutôt qu'en quelques jours" beats "…afin d'utiliser le système").
- If truly a technical task with no user value, reclassify as a chore/tech-debt ticket with its own template — don't dress it up as a user story.

---

## E — Estimable

The team can give a credible story-point estimate. If the ticket is so fuzzy that the estimate would be a guess, it is not ready.

**Signals of failure:**
- Story points are requested but the AC has more `[TODO]` markers than concrete bullets.
- A critical unknown sits inside the AC ("TBD whether we need a backend change").
- The scope could plausibly be 1 point or 8 points depending on interpretation.

**Diagnostic questions:**
- Would three engineers on this team converge within one Fibonacci step (e.g., all say 3 or 5, not one saying 2 and another saying 13)?
- Are all the unknowns surfaced in Contexte, or are they hiding inside the AC?

**How to fix:**
- Resolve `[TODO]` markers before estimation — don't estimate placeholders.
- For genuine unknowns, spin off a small spike ticket (time-boxed investigation) and block this ticket on its resolution.
- If the scope is just too broad to estimate, that's usually a Small-failure too → split.

---

## S — Small

One ticket = one slice, deliverable inside one sprint by one engineer (or one small pair). A ticket that spans weeks is not a ticket — it's an epic in disguise.

**Signals of failure:**
- The AC list is so long you stop reading.
- The ticket touches three different screens or three different services.
- The summary has an "and" in it (`X and Y`).
- Engineers on review say "I'll take the frontend part, you take the backend part" — that's two tickets.

**Diagnostic questions:**
- Could one engineer ship this within a normal sprint without heroics?
- Can this be split vertically into two or more shippable slices (each still valuable on its own)?
- Are there sub-sections of the AC that describe entirely different flows?

**How to fix:**
- Prefer **vertical splits**: "slice 1 = invite a single member with a pre-set role, slice 2 = bulk invite from CSV, slice 3 = invite with a custom role" — each is a complete tiny slice.
- Avoid **horizontal splits** ("frontend ticket" + "backend ticket") unless the team is explicitly organised that way — they violate Independent and Valuable.
- Move ACs that are business rules, not behaviours of *this* flow, out of this ticket (link them as referenced rules, or their own tickets).

---

## T — Testable

Every acceptance criterion can be verified by a tester with the final build in front of them, using a yes/no answer. Most ACs should follow **Given-When-Then** (see Rule 12 of the rubric).

**Signals of failure:**
- ACs with feeling-words ("intuitive", "smooth", "fluide").
- ACs with vague verbs ("handle", "manage", "support") without specifying the observable behaviour.
- Behaviour described without the triggering condition ("a confirmation appears" — when?).
- UI strings paraphrased rather than quoted verbatim.

**Diagnostic questions:**
- Given a fresh environment and this ticket, can QA write test cases without a follow-up conversation?
- For each AC, what is the observable signal (screen state, log line, API response, DB row) that tells a tester it passed or failed?

**How to fix:**
- Rewrite behavioural ACs as **Étant donné / Quand / Alors** (or Given / When / Then).
- Quote every UI string.
- Specify the observable output for system actions (what does the user see? what does the backend log?).

---

## Presenting INVEST findings

If INVEST checks pass silently, don't mention them — the quality is expected, not announced. If one or more fail and need a product decision, include them in Step 7's flags. Keep it blunt:

> Avant de créer, trois points INVEST à régler :
>
> 1. **[S — Small]** Le ticket couvre l'invitation ET la modification de rôle d'un membre existant — deux slices distincts. Je suggère de scinder. On garde juste l'invitation ici ?
> 2. **[I — Independent]** L'AC de succès pointe vers "voir autre ticket" sans référence Jira. Faut-il créer d'abord le ticket du modal de confirmation et linker ?
> 3. **[T — Testable]** Le message d'erreur n'est pas cité verbatim. Quel texte exact doit s'afficher ?

Never hand-wave a failure as acceptable without the requester explicitly approving it. "We'll figure it out in sprint" is not an answer — either the ticket is ready or it isn't.
