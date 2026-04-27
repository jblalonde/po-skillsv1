# Reference — INVEST validation

Every ticket drafted by this skill must pass INVEST before it is presented for approval. This file is loaded during Step 6 of the workflow alongside `quality-rubric.md`.

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
- Link the dependency explicitly in Contexte (`Dépend de COEUR-XXXX`) and require that dependency to be merged first, OR
- Merge the two tickets into one, OR
- Split differently so each slice is independently shippable (often this means slicing **vertically** — see Rule 13 of the rubric).

---

## N — Negotiable

A ticket describes the **outcome**, not the implementation. The engineer owns the "how". The PO owns the "what" and the "why".

**Signals of failure:**
- The AC prescribes a specific data structure, ORM call, queue name, or component library.
- The description reads like a technical design doc.
- Solution-specific language creeps into user-visible behaviour ("uses Redis to cache", "via a webhook").
- **Fabricated technical specifics from pattern-matching** — field names, event names, table names, service names, enum values that "sound right" for the domain but have no cited source. This is the **single most common Negotiable failure** and is covered as a hard stop by Rule 14 of `quality-rubric.md`. Catching one means stopping and re-drafting the affected section as TODOs, not patching the wording.

**Exception:** when a specific technical choice is a product constraint (e.g., "must go through Stripe because that's the only processor under contract"), state it in Contexte as a constraint, not in the AC as an implementation instruction.

**Exception is narrow.** A constraint is something the *requester explicitly said*, not something Claude infers. "Stripe" being mentioned by the user is a constraint the user named. "subscription.activated event" is *not* a constraint — it's a fabrication unless the user, a doc, or the code says so.

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
- Replace a generic benefit with a measurable outcome ("…afin de recevoir des leads sans action manuelle" beats "…afin d'utiliser le système").
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
- Prefer **vertical splits**: "slice 1 = activate with pre-defined volumes only, slice 2 = add custom amount, slice 3 = edit in-flight subscription" — each is a complete tiny slice.
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
> 1. **[S — Small]** Le ticket couvre l'activation ET la modification en cours de route — deux slices distincts. Je suggère de scinder. On garde juste l'activation ici ?
> 2. **[I — Independent]** L'AC de succès pointe vers "voir autre ticket" sans référence Jira. Faut-il créer d'abord le ticket du modal de confirmation et linker ?
> 3. **[T — Testable]** Le message d'erreur n'est pas cité verbatim. Quel texte exact doit s'afficher ?

Never hand-wave a failure as acceptable without the requester explicitly approving it. "We'll figure it out in sprint" is not an answer — either the ticket is ready or it isn't.
