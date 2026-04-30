---
name: check
description: Audit an existing Jira ticket, epic or user story, against the team's quality contract. Verifies structural sections (Objectif, Contexte, Acceptance criteria, Design, Non-inclus, child links, Details), anti-fabrication discipline (no invented backend specifics or UI copy), qualified personas, GWT for behavioural ACs, vertical slicing, and INVEST or 4-line Validation epic. Returns a structured report of passes, gaps, hard fails, and concrete fix suggestions. Optionally applies fixes via Atlassian:editJiraIssue with explicit per-edit approval.
when_to_use: Use when the user wants to audit, review, validate, or quality-check an existing Jira ticket. Trigger phrases include "/po:check", "audit this ticket", "check ticket PROJ-1234", "is this ticket ready for sprint?", "review my user story", "is this epic complete?", "quality check on...", "review the ticket I just drafted". Accepts a Jira ticket key (e.g. PROJ-1234) or a pasted ticket draft as input.
---

# /po:check: Quality audit for existing Jira tickets

Audits an existing Jira ticket against the same contract that the `jira-ticket` skill enforces during drafting. Returns a clean pass/fail report with concrete suggestions, surfaces gaps as numbered Block B questions, and optionally applies fixes back to Jira (with explicit per-fix approval).

This skill is **read-first, write-only-with-approval**. It will never silently modify a ticket.

## When to invoke

- The user passes a Jira ticket key (`/po:check PROJ-1234`).
- The user pastes a ticket draft inline and asks for a review.
- The user asks "is this ready for sprint?" about a specific ticket.

## Workflow

### Step 1: Fetch or read the ticket

**If the input is a Jira ticket key:**

1. Get cloudId via `Atlassian:getAccessibleAtlassianResources` if not already known.
2. Fetch with `Atlassian:getJiraIssue(cloudId, issueIdOrKey, responseContentFormat="markdown")`.
3. Read: summary, description, issue type (Epic / Task / Story), status, parent, labels, priority, assignee, story points, linked issues, attachments.

**If the input is pasted text:**

- Parse the structure inline.
- If the type is ambiguous (could be epic or story), **ask the requester before running checks**. Do not guess.

### Step 2: Identify ticket type

The checklist depends on the type:

- **Epic** → epic checklist (Section A below).
- **Story / new feature** or **story / adjustment** → story checklist (Section B below).

### Step 3: Run the appropriate checklist silently

Apply every check. Each check produces one of three outcomes:
- ✅ **Pass**, section is present and meets the standard.
- ⚠️ **Flag**, section is present but has a fixable gap, or section is missing where it should exist.
- ❌ **Hard fail**, must be fixed before sprint (anti-fabrication violations, no AC, etc.).

### Step 4: Produce the audit report (Block A + Block B)

Same two-block structure as the drafting skill. Block A is the paste-ready audit summary; Block B is the open questions for the PO / Design / Engineer.

### Step 5: Optional: apply fixes

After the requester reviews the report, the skill can:
- Suggest concrete edits (rewrites, additions) for each flag.
- On explicit per-fix approval, call `Atlassian:editJiraIssue` to apply.

**Never modify a ticket without per-fix approval.** "Fix all" is not enough, the requester confirms each change.

---

## Section A: EPIC checklist

For an epic, every check below runs against the ticket. Pass criteria match the contract enforced by `templates/epic.md` in the `jira-ticket` skill.

### Structural sections (Block A of the source ticket)

| # | Check | Pass criteria |
|---|---|---|
| A0 | **Issuetype matches structure** | Le ticket est filé avec `issuetype=Epic`. Mismatch ⚠️ si filé `Story` / `Task` / `Bug` alors que le ticket a 3+ child stories liées et un AC niveau-epic (3-6 outcomes mesurables, pas de GWT) — la structure dit *epic* mais le type Jira dit autre chose. Confirmer la liste des types disponibles via `Atlassian:getJiraProjectIssueTypesMetadata` avant de proposer un fix. |
| A1 | **Summary** | Noun phrase, action-neutral. No verb (e.g. "Activer X" is a child story, not an epic summary). |
| A2 | **Objectif** | 1–2 sentences naming the observable outcome the epic delivers. NOT a vague aspiration ("améliorer l'expérience"). |
| A3 | **Contexte** | État du monde (what exists today) + pourquoi maintenant (driver) + affected segments. Each segment qualified: role + segment/state + context of need. No "utilisateurs". |
| A4 | **Acceptance criteria** | French canonical header: `### Critères d'acceptabilité` (not *« Critères d'acceptation »*). English: `### Acceptance criteria`. 3–6 epic-level bullets, observable & measurable. NOT GWT (those belong in child stories). If the AC section is sparse but the Contexte covers the same ground, flag as a structural improvement (move outcome bullets into AC). |
| A5 | **Design général linked** | Figma or high-level mockup link if applicable. Omitted only if there's truly no high-level design (note that detailed per-screen designs live in child stories). |
| A6 | **Non-inclus** | Explicit list of adjacent capabilities that are NOT in scope. **This is the most common gap in real epics, flag aggressively when missing or empty.** |
| A7 | **Stories enfants (linked child work items)** | Checklist of children, each named as a vertical slice. No layer-named children ("Backend pour X" = Rule 13 violation). Each child has a status (À drafter / Drafted as KEY / In sprint / Done). |
| A8 | **Details** | Project, parent (theme/initiative), labels, priority, reporter, time-window estimate. Each addressed. |

### Quality gates (silent in the source ticket: surface failures)

| # | Check | Pass criteria |
|---|---|---|
| A9 | **Anti-fabrication (Rule 14)** | No invented service, event, table, field, queue, flag, or UI copy specifics. Every backend reference is traceable to a cited source (a related Jira ticket, code path, doc, or a quoted PO message). |
| A10 | **Vertical slicing of children (Rule 13)** | Each child story is a complete slice of user-visible value, shippable independently. |
| A11 | **Validation epic (4-line check)** | Pourquoi maintenant ✅ · Découpage ✅ · Edges du périmètre ✅ · Mesurabilité ✅. Each line either passes or surfaces as a Block B question. |

---

## Section B: USER STORY checklist (new feature OR adjustment)

For a story (either type), the structural sections differ slightly between new features and adjustments. The skill adapts based on the ticket's framing, but the underlying quality bar is the same.

### Structural sections (Block A of the source ticket)

| # | Check | Pass criteria |
|---|---|---|
| B0 | **Issuetype matches structure** | Le ticket n'est PAS filé avec `issuetype=Epic`. Mismatch ⚠️ si filé `Epic` mais structuré comme une story (un seul flow, AC en GWT, aucune story enfant liée) — la structure dit *story* mais le type Jira dit *epic*. Confirmer la liste des types disponibles via `Atlassian:getJiraProjectIssueTypesMetadata` avant de proposer un fix. |
| B1 | **Summary** | New feature: noun phrase naming the UI/capability. Adjustment: change verb (Changer, Ajouter, Retirer, Modifier, Mettre à jour). No vague summaries ("Fix X"). |
| B2 | **Contexte** | New feature: *« En tant que [rôle qualifié, role + segment/state + context of need], je souhaite [objectif observable], afin de [bénéfice mesurable] »*. Adjustment: état du monde + ce qui change + pourquoi + segment affecté (no user-story format). NO "en tant qu'utilisateur" (Rule 1, INVEST V). |
| B3 | **Acceptance criteria** | French canonical header: `### Critères d'acceptabilité` (not *« Critères d'acceptation »*). English: `### Acceptance criteria`. A set of pre-defined, specific, verifiable conditions. Bullets, each testable (a tester answers yes/no looking at the build). Addresses preconditions, happy path, error paths, edge cases (empty/loading), cancel/dismiss. Behavioural items in GWT (Rule 12). Verbatim UI copy from a cited source (Rule 3). No vague verbs (Rule 1: "feel responsive", "user-friendly", etc.). |
| B4 | **Design linked** | New feature: Figma URL with `node-id` + screenshot embedded. Adjustment: Design section only if visual change. |
| B5 | **Optional sections (info supp / non-inclus / warning)** | Present when relevant, flag if there's an obvious need (known dependency = warning; over-interpretation risk = non-inclus; business context = info supp). Optional, but not silently omitted when clearly needed. |
| B6 | **Details** | Project, issue type, parent epic, labels, priority, assignee, reporter, sprint, due date, story points, linked tickets, attachments. Each addressed (filled or explicitly N/A). Tag included where needed (e.g., `BlockedBy`, `Blocks`, `Relates to`). |

### Quality gates (silent in the source ticket: surface failures)

| # | Check | Pass criteria |
|---|---|---|
| B7 | **Anti-fabrication (Rule 14, 14b)** | No invented backend specifics; UI copy verbatim from cited source. TODOs do not name systems/services/flags the ticket has not cited. |
| B8 | **INVEST** | I (Independent) · N (Negotiable, outcomes not implementation) · V (Valuable, qualified persona + measurable benefit) · E (Estimable, no blocking TODOs) · S (Small, one engineer, one sprint) · T (Testable, every AC has an observable signal). |
| B9 | **Vertical slicing (Rule 13)** | Story ships user-visible value, not a layer. Summary is not "Backend pour Y" or "API endpoint for Z". |
| B10 | **3 C's (Rule 16)** | Card (description body present) · Conversation (open questions exist or are explicitly closed) · Confirmation (AC is testable end-to-end). |

---

## Step 4 detail: the audit report

The output structure mirrors the drafting skill's two-block contract.

### Block A: AUDIT REPORT (paste-ready summary)

```
## Audit: [TICKET-KEY]: [Summary as filed]

**Type detected:** Epic | Story (new feature) | Story (adjustment)
**Overall status:** Ready for sprint ✅  |  Needs work ⚠️  |  Major rework ❌

### Passes
- ✅ [check ID + name], [one-line evidence: which section, what's good]
- ✅ ...

### Flags (fixable, recommended before sprint)
- ⚠️ [check ID + name], [what's missing or weak] → **Suggested fix:** [concrete one-line proposal]
- ⚠️ ...

### Hard fails (must fix before sprint)
- ❌ [check ID + name], [what's broken, anti-fabrication violation, missing AC, etc.] → **Required fix:** [concrete one-line proposal]
- ❌ ...
```

The summary block at top gives an at-a-glance verdict. The three lists below let the requester triage quickly.

### Block B: Questions à régler (workspace, never pasted into Jira)

Numbered questions grouped by addressee (PO / Design / Ingénieur / PM), each pointing to the check ID it unblocks. Same shape as the drafting skill.

```
**Pour la PO**
- Q1, (unblocks A6 Non-inclus) Quelles capacités adjacentes voulez-vous explicitement exclure ?
- Q2, (unblocks B3 Acceptance criteria) Quel est le texte exact du message d'erreur ?

**Pour Design**
- Q3, (unblocks B4 Design) Le lien Figma actuel n'a pas de node-id. Pouvez-vous partager le lien avec le frame sélectionné ?

**Pour Ingénieur**
- Q4, (unblocks A9 / B7 Anti-fabrication) ...
```

---

## Step 5 detail: applying fixes

After the requester reviews the report, they can:

- **Take the report and edit Jira themselves**: the report is paste-ready as a comment on the ticket.
- **Ask the skill to apply specific fixes**: for each flag/fail, the skill proposes the exact rewrite, and on explicit per-fix approval calls `Atlassian:editJiraIssue` to update the ticket.

**Approval discipline:**
- One approval per fix. Not "approve all".
- Show the proposed before/after diff before each edit.
- After each edit, confirm the change took effect (by re-fetching the ticket section).

**Never apply fixes that the audit found as Hard Fails without explicit user approval per fix.** Those are usually Anti-fabrication violations, the skill cannot invent the missing content; the requester must provide it.

### Step 5b: Fixing an issuetype mismatch (A0 / B0)

A mismatch surfaced by A0 or B0 follows its own protocol because Jira treats issuetype changes specially. Do not bundle this fix with the others.

1. **Récupérer la liste des types valides** dans le projet via `Atlassian:getJiraProjectIssueTypesMetadata` avant de proposer quoi que ce soit. Le skill ne suggère qu'un type qui existe réellement dans le projet.
2. **Proposer le bon type** au PO (basé sur la structure observée) et attendre une approbation explicite. Format : *« Ce ticket est structuré comme un epic mais filé en `Story`. Le projet expose `Epic` comme type. Veux-tu que je tente le changement via `editJiraIssue` ? »*
3. **Tenter** `Atlassian:editJiraIssue(cloudId, issueIdOrKey, fields={"issuetype": {"name": "<NewType>"}})`.
4. **Si Jira accepte**, confirmer le changement en re-fetchant le ticket et re-rouler la checklist correspondante (epic ou story) pour valider que la nouvelle structure passe.
5. **Si Jira refuse**, afficher l'**erreur Jira verbatim** au PO, puis diagnostiquer la cause probable et proposer une sortie contextuelle. Ne rien faire automatiquement.

**Causes typiques de refus + sortie proposée :**

| Erreur Jira | Cause probable | Sortie proposée |
|---|---|---|
| *« Field X is required for issue type Y »* | Le nouveau type a un champ obligatoire que l'ancien n'avait pas (Bug exige souvent « Steps to reproduce », « Severity »). | Lister les champs requis, demander les valeurs au PO, **retenter `editJiraIssue` avec les nouveaux champs**. |
| *« Cannot change issue type to Epic »* / *« Cannot change issue type from Epic »* | La hiérarchie Epic est spéciale dans Jira ; `editJiraIssue` ne supporte pas la conversion. | Proposer le **« Move » manuel via Jira UI** (`...` > `Move` sur le ticket), pas exposé par le MCP. Donner le chemin précis. |
| *« Issue type X is not available in this project »* | Le type retiré ou pas dans le scheme du projet. | Re-surfacer le menu des types valides et demander au PO de choisir. |
| Erreur sur le champ `issuetype` lui-même (champ non-éditable, MCP qui rejette) | Limite du tool MCP `editJiraIssue`. | Proposer la **recréation** : drafter un nouveau ticket avec le bon type via `/po:jira-ticket` (le contenu actuel sert de source), créer le nouveau, lier l'ancien via `relates to`, fermer l'ancien. **Chaque action approuvée séparément**, jamais de fermeture automatique. |

**Discipline d'approbation pour la recréation :**
- Le draft du nouveau ticket suit le flow normal de `/po:jira-ticket` (Step 7 : Block A + Block B, approbation explicite avant `createJiraIssue`).
- La fermeture de l'ancien ticket est une action **séparée** qui demande son propre « oui » du PO.
- Jamais de `delete` ou de `close` silencieux. Si le PO préfère garder les deux ouverts (audit, lien historique), c'est son choix.

---

## What this skill will NOT do

- Will not modify a Jira ticket without explicit per-fix approval.
- Will not invent missing content to "fix" gaps, surfaces them as flags or Block B questions.
- Will not blanket-approve a ticket "because it looks good", every check is concrete and traceable.
- Will not delete tickets, change permissions, or take other destructive actions on Jira.
- Will not run on a ticket type it can't classify, asks the requester first.

---

## Common patterns this skill catches

1. **Empty Non-inclus on an epic**: almost always a gap in real tickets. Flagged as ⚠️ even when nothing else is wrong.
2. **AC at epic level written as GWT**: those belong in child stories, not the epic. ⚠️
3. **AC at story level as plain bullets without GWT for behavioural items**: Rule 12 violation. ⚠️
4. **"En tant qu'utilisateur"**: generic persona, INVEST V failure. ⚠️
5. **Layer-named children on an epic** ("Backend pour Y"), Rule 13 violation. ❌
6. **Invented backend specifics** (table names, event names, service names not traceable to a source), Rule 14 hard stop. ❌
7. **Story points filled with major TODOs in AC**: Estimable failure. ⚠️
8. **No `Blocked by` link when the AC depends on another unshipped ticket**: INVEST I failure. ⚠️
9. **Verbatim UI copy paraphrased instead of quoted**: Rule 3 violation. ⚠️
10. **Acceptance criteria section literally empty or "TBD"**: Hard fail. ❌
