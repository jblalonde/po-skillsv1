# New feature ticket template

Use this template when the ticket is for something **new** — a new screen, a new modal, a new flow, a new capability. If the ticket is modifying something that already exists, use `adjustment.md` instead.

This template is opinionated on purpose. Every section exists because its absence has caused a real problem on a real ticket. Don't delete sections; if a section genuinely doesn't apply, mark it `Non applicable — [raison]` so reviewers see you considered it.

---

## Summary

Noun-based. Names the UI component or capability being built. One noun phrase, no verbs, no colons, no ticket references.

**Good examples:**
- `Modal Activer l'abonnement`
- `Subscription confirmation modal`
- `Tableau des opportunités potentielles`
- `Broker lead assignment flow`

**Bad examples:**
- `Build subscription thing` (vague, verb-led for a new feature)
- `Make it easier to activate subscriptions` (describes a goal, not the artifact)
- `Backend pour le modal d'abonnement` (layer, not capability — violates Rule 13)
- `COEUR-XXXX` (references another ticket in the summary)

---

## Description structure

The description has three sections in this order: Contexte, Critères d'acceptation, Design. Do not rename, reorder, or skip them for new-feature tickets. A fourth section — **Validation INVEST** — is appended at the bottom as a self-check before the ticket is presented for approval.

### Section 1 — Contexte

One paragraph, user-story format, with a **qualified persona**. The persona is the difference between a feature built for "users" (meaningless) and a feature built for a specific person doing a specific job in a specific state.

**Persona qualification — three parts, always:**

1. **Role** — the job title or system role (courtier, franchisé, admin, courtier abonné, etc.).
2. **Segment or state** — what narrows the role to the specific audience for this ticket (nouveau / abonné actif / sans carte enregistrée / gestionnaire d'équipe).
3. **Context of the need** — the moment or situation in which the user hits this feature.

**Format — French:**

> En tant que [rôle qualifié avec segment/état], [contexte du besoin], je veux [objectif observable] afin de [bénéfice mesurable].

**Format — English:**

> As a [qualified role with segment/state], [context of need], I want [observable goal] so that [measurable benefit].

**Good:**

> En tant que courtier abonné à la boutique de leads, lorsque je veux recevoir automatiquement des demandes entrantes selon ma capacité journalière, je veux activer un abonnement avec un volume choisi afin de ne plus avoir à réclamer manuellement chaque opportunité.

Why this is good: *courtier abonné à la boutique* (role + segment), *lorsque je veux recevoir automatiquement* (context), *activer un abonnement avec un volume choisi* (observable goal), *ne plus avoir à réclamer manuellement* (measurable benefit — before/after is verifiable).

**Bad:**

> En tant qu'utilisateur, je veux activer un abonnement afin de l'utiliser.

Why this is bad: generic role, no segment, circular benefit ("use the feature so I can use the feature"), no context. Fails **V** (Valuable) in INVEST.

**If business context is needed**, add a single sub-heading `**Contexte métier**` under the user story with 2–3 sentences of framing. Do not exceed that. Anything longer belongs in the linked epic or a Confluence page.

**If there are hard technical constraints** that drive the product shape (e.g., "must use Stripe — no other processor under contract"), add a `**Contraintes**` sub-heading listing them in bullets. This keeps INVEST rule **N** (Negotiable) clean: the AC stays about outcomes, the Contexte carries the constraints.

### Section 2 — Critères d'acceptation

Four sub-sections, in this order. Do not collapse them — the structure is what makes the ticket testable and INVEST-compliant.

#### 2.1 — Préconditions

Bullets. The state of the system and the user required before any scenario in this ticket applies. Engineers and QA start here to set up their test environments.

**Examples:**
- Le courtier est authentifié dans le portail
- Le courtier a accès à la boutique de leads (flag `BrokerShop` activé sur son compte)
- La tuile abonnement est visible dans l'accueil du portail

If there are no preconditions, write `Aucune précondition — accessible à tout utilisateur authentifié` explicitly. Do not omit the section.

#### 2.2 — Scénarios

The behavioural core of the ticket. Each scenario follows **Étant donné / Quand / Alors** (see `references/quality-rubric.md` Rule 12). Cover, at minimum:

1. **Happy path** — the main success flow.
2. **Alternate happy paths** — each branching decision (e.g., pre-defined volume vs. custom amount) gets its own scenario.
3. **Error paths** — backend failure, validation failure, third-party failure (Stripe, etc.).
4. **Edge cases** — loading state, empty state, race conditions, dismissal.
5. **Cancel / dismiss path** — explicit, even if trivial.

**Format:**

```
**Scénario — [nom court]**
- Étant donné [état initial]
- Et [précondition additionnelle]
- Quand [action / événement]
- Alors [résultat observable]
- Et [résultat observable additionnel]
```

**Example — happy path:**

```
**Scénario — Activation avec volume pré-défini et carte enregistrée**
- Étant donné qu'un courtier est sur la tuile abonnement
- Et qu'une carte de crédit est enregistrée dans son compte Stripe
- Quand il sélectionne « 20 demandes / jour » et clique sur « Activer »
- Alors le bouton « Activer » affiche un spinner et est désactivé jusqu'au retour de Stripe
- Et le système envoie la requête d'activation à Stripe
- Et le modal de confirmation s'affiche dès que Stripe confirme l'activation (voir COEUR-XXXX)
```

**Example — error path:**

```
**Scénario — Échec de l'activation côté Stripe**
- Étant donné qu'un courtier a sélectionné un volume et cliqué sur « Activer »
- Quand Stripe retourne une erreur
- Alors le modal reste ouvert
- Et le message d'erreur « Échec de l'activation. Veuillez réessayer. » s'affiche sous le bouton « Activer »
- Et aucun abonnement n'est créé côté portail
- Et le bouton « Activer » redevient cliquable
```

#### 2.3 — Règles & contraintes

Non-behavioural acceptance criteria — bullets are the right format here, not GWT. This is where the static rules live.

**⚠️ Verbatim UI copy must come from a cited source** (Figma, Notion, the PO's exact words). Never paraphrase, never invent a "natural-sounding" version of a button label, modal title, error message, or description paragraph. If the copy isn't available, write `*[TODO — copie verbatim à fournir par la PO]*` and ask. See Rule 14 in `quality-rubric.md`.

**What goes here:**
- Enumerated values (list of allowed volumes, list of payment types) — every value cited from a source
- Verbatim UI copy (modal title, button labels, description paragraph, error messages) — from Figma / PO / Notion only
- Mutual exclusion rules ("une seule option sélectionnable à la fois")
- Bounds (min / max / none) — with the deliberate decision stated if bounds are absent
- Business rules that govern the feature but aren't scenario-based — cited from the PO or a spec

**Example:**
- Titre du modal : « Activer l'abonnement »
- Volumes pré-définis offerts : 5, 10, 20, 40, 100 demandes / jour
- Le champ « Montant personnalisé » accepte tout entier ≥ 1 (pas de borne supérieure — décision produit du [date])
- Les options pré-définies et le champ personnalisé sont mutuellement exclusifs (sélectionner l'un désélectionne l'autre)
- Boutons : « Annuler » (secondaire), « Activer » (primaire)
- Copie descriptive : « [texte verbatim à fournir par le PO] »

#### 2.4 — Effets de bord système

What changes in the system when the happy path succeeds — the side-effects that aren't directly visible in the UI but matter for downstream services, reporting, analytics, or state.

**⚠️ This is the highest-risk section for Rule 14 (anti-fabrication) violations.** It is tempting to write plausible-sounding field names, event names, service names, and queue names that match the domain. **Do not.** Every specific in this section must come from a cited source — code you have read, a Notion/Confluence spec, a sentence from the requester or an engineer, an existing Jira ticket. If you don't have a source, write a TODO with the precise question instead.

**Forbidden — fabricated specifics:**
- `Un enregistrement <table> est créé avec <champ>=<valeur>` when the table/field/value is not cited.
- `Un événement <name> est émis vers <service>` when the event name and service are not cited.
- "Le moteur de <X>", "Le pipeline <Y>" when the existence and naming are not cited.

**Required protocol when sources are missing:**

Do **not** write a TODO that lists categories of systems (analytics, queue, distribution engine, etc.) — that is itself a fabrication (see Rule 14b). Instead, **ask the requester first** with an open, architecture-neutral question. Only after their answer write the TODO.

Open question to ask the requester (no architectural assumptions embedded):

> *« Quels effets observables côté backend se produisent à la confirmation de l'action ? Cela peut être des changements de données, des messages envoyés à d'autres parties du système, ou des comportements automatiques qui démarrent. Si vous ne savez pas, dites-le et nous laisserons la section ouverte pour qu'un ingénieur réponde. »*

If the requester answers with specifics → cite them. If the requester says "I don't know, leave it open" → write the TODO this way (no system names, no categories):

```
### Effets de bord système

*[TODO — effets observables côté backend à la confirmation de l'action,
à compléter avec un ingénieur familier du système.
Pour chaque effet : sa nature, où il s'observe (donnée modifiée, message
émis, comportement déclenché), et comment le vérifier. Citer depuis le
code ou la doc. Ne rien lister par hypothèse.]*
```

**Acceptable — when sources exist:**
- Cite the source in line: `Le service de notification (voir COEUR-XXXX, fichier services/notifications.ts) reçoit l'événement et envoie un SMS.`
- Quote the requester directly: `Selon Justine (PO) en réunion 2026-04-22 : "il faut que le moteur d'assignation soit notifié au moment de la confirmation Stripe, pas avant".`

If there are no side effects, write `Aucun effet de bord — action purement UI`. Do not omit the section.

### Section 3 — Design

Two elements, in this order:

1. An embedded screenshot of the current design (attach the image to the ticket; Jira will render it inline).
2. The Figma link with the node-id preserved so engineers land on the exact frame.

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

### Section 4 — Validation INVEST (self-check)

Before presenting the draft for approval, fill in one line per criterion. This is what the skill flags at Step 7 — the requester sees the self-check and knows nothing was skipped. Be concise: a short sentence naming the evidence.

**Format:**

```
**Validation INVEST**
- **I — Independent** : [pourquoi ce ticket peut être livré sans attendre un autre ticket, ou quel ticket le bloque]
- **N — Negotiable** : [confirmation que l'AC décrit les outcomes, pas l'implémentation]
- **V — Valuable** : [qui spécifiquement gagne quoi de mesurable]
- **E — Estimable** : [aucun TODO bloquant, les inconnues sont dans Contexte]
- **S — Small** : [un engineer, un sprint, scope du slice]
- **T — Testable** : [tous les scénarios en GWT, toutes les copies UI verbatim]
```

If any criterion fails, **do not fill it in with a lie**. Mark it `[FAIL — motif]` and raise it in the Step 7 flags for the requester to decide.

---

## Metadata checklist

Before creating, confirm every field below with the requester. Defaults are suggestions — do not apply them without asking.

- [ ] **Project** — no default, ask
- [ ] **Issue type** — usually Task; confirm based on project's available types
- [ ] **Parent epic** — required; suggest from recent epics pulled via MCP
- [ ] **Labels** — suggest from labels actually used in the project (see `references/context-gathering.md`)
- [ ] **Priority** — ask
- [ ] **Assignee** — ask
- [ ] **Reporter** — defaults to whoever is running the skill; confirm
- [ ] **Sprint** — ask
- [ ] **Due date** — ask
- [ ] **Story points** — ask; new features almost always estimated. Fibonacci (1, 2, 3, 5, 8, 13) is the default scale unless the project uses a different convention (check Step 2 lookup).
- [ ] **Attachments** — design screenshot
- [ ] **Linked tickets** — `Blocks`, `Blocked by`, `Relates to` as required by Rule 13 (vertical slice) and INVEST **I** (Independent)

---

## Revisions after review

When a ticket is being updated after design or product review, use strike-through for changes:

```
Le courtier peut choisir parmi 4 options de volume journalier ~~hebdomadaire~~ : 5, 10, 20, 40 ~~ou 100~~ demandes par jour ~~par mois~~
```

This keeps the history readable for engineers and QA who already had context on the earlier version. After a revision has been acknowledged and the sprint moves on, the strike-through can be cleaned up on the next revision — but while the ticket is in-flight, keep it.
