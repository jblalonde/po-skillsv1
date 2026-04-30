# Epic ticket template

Use this template when the work is too large for a single story, Atlassian's rule: *« any scope of work that the team estimates at "weeks" (or longer) to complete... should be considered an epic and broken down into smaller stories »* (see *Atlassian hierarchy* section in `SKILL.md`).

An epic is **not** an inflated story. It does **not** carry full GWT acceptance criteria, those live in the child stories. The epic carries the *why*, the *scope edges*, the *list of children*, and the *epic-level success criteria*.

All anti-fabrication rules (Rule 14, Rule 14b) apply at this level, exactly as for stories. Do not invent service names, table names, integration names, or any specific the requester has not cited.

---

## Block A: sections that ship in Jira

The epic, as it lands in the project, has eight sections in this order. Sections marked *(if applicable)* are omitted entirely when there's no content, do not leave empty headers.

```
1. Summary            : noun phrase, action-neutral
2. Objectif           : 1–2 sentences: what this epic achieves
3. Contexte           : état du monde + pourquoi maintenant + segments affectés
4. Acceptance criteria: 3–6 epic-level bullets, observable & measurable (NOT GWT)
5. Design général     : Figma / mockup link (if applicable)
6. Non-inclus         : explicit list of what is intentionally out of scope
7. Stories enfants    : checklist, each = one vertical slice
8. Details            : project, parent (theme/initiative), labels, priority, reporter
```

The four-line **Validation epic** self-check (Pourquoi maintenant / Découpage / Edges / Mesurabilité) is **not** a Block A section. It runs silently in Step 6, failures surface as numbered questions in Block B.

---

## Section guidance

### 1: Summary

Noun phrase naming the body of work. Action-neutral; the action verbs live in child stories.

**Good:**
- `Self-service team management, invitations, roles, removals`
- `Onboarding mobile redesign, first-run experience`
- `Order returns end-to-end, refund, restock, customer notification`

**Bad:**
- `Inviter un membre` (that's a child story, not an epic)
- `Tout refaire` (no scope edge, vague aspiration)
- `Backend pour la gestion d'équipe` (layer, not capability, Rule 13 violation)

### 2: Objectif

One or two sentences naming the outcome the epic delivers. Specific and observable. The "what" of the epic, not the "why" (the why goes in Contexte).

**Good:**
- *Permettre à un admin d'équipe d'inviter, modifier les rôles, et retirer des membres sans ticket support.*
- *Remplacer le flow d'onboarding mobile actuel par une expérience guidée en 3 étapes.*
- *Permettre à un client de retourner une commande de bout en bout (initiation, remboursement, notification) sans intervention de l'équipe support.*

**Bad:**
- *Améliorer l'expérience utilisateur.* (not observable)
- *Refondre tout le produit.* (no edge)

### 3: Contexte

Two short paragraphs.

**Paragraph 1, État du monde.** What exists today, what's about to change. Cite the requester verbatim where possible.

**Paragraph 2, Pourquoi maintenant.** The business outcome or organizational driver. If a theme or initiative is known, link it explicitly: `Theme : [nom]` or `Initiative : [clé Jira ou nom]`. If not, leave a TODO asking, do not invent.

Name the affected user segments inline (e.g. *« admins d'équipe d'organisations de plus de 10 membres »*, *« clients ayant déjà passé au moins une commande »*, *« nouveaux utilisateurs à leur première session »*). Each segment must be qualified the same way as in `new-feature.md` (role + segment/state + context of need). If a downstream operator (support, admin, analyst) is affected, name them too.

### 4: Acceptance criteria

**Section header by language**, render the header in the ticket's language:
- French → **`### Critères d'acceptabilité`** (canonical for this team; do not use *« Critères d'acceptation »*)
- English → **`### Acceptance criteria`**

Three to six bullets. Epic-level success is **observable and measurable**, but coarser than story-level GWT.

**Acceptable:**
- *Un admin d'équipe peut inviter, modifier le rôle, et retirer un membre sans ouvrir de ticket support.*
- *Le système notifie un nouveau membre dans les 60 secondes suivant l'invitation, par un canal mesurable.*
- *L'historique des changements de rôle est consultable par l'admin sur les 12 derniers mois.*

**Not acceptable here (push to child stories or remove):**
- Implementation specifics, no field names, no service names, no event names. Rule 14 applies at every level.
- Vague aspirations, *« meilleure expérience admin »* is not measurable.
- Sprint-level GWT, those go in child stories, not in the epic.

If acceptance criteria can't be written without naming systems the requester hasn't cited, write the bullet openly (in user-domain language) or surface the gap as a Block B question.

### 5: Design général *(if applicable)*

Link to a high-level mockup, design flow, or Figma file showing the epic shape (not individual screens, those live in child stories' Design sections). Optional. Omit the section entirely if no high-level design exists.

If included:
- Figma URL with `node-id` if linking to a specific frame
- One-line caption naming what the link shows

Do not paste detailed screen-level mockups here, those belong in the child story.

### 6: Non-inclus

Explicit bullet list of adjacent capabilities that are **intentionally not** part of this epic. This is the section reviewers complain about most when missing, engineers and stakeholders will assume otherwise without an explicit list.

Examples of what to call out:
- Legacy flows being replaced (named, with a "stays as-is until X" note if relevant)
- Related screens that stay unchanged
- Follow-on epics that pick up the rest
- Edge personas this epic does not serve

If there's truly nothing out of scope (rare), write `Aucun hors-scope identifié à ce stade, à confirmer avec [stakeholder].` Do not omit the section.

### 7: Stories enfants

Checklist. Each child story is one slice of vertical user-visible value (see Rule 13). For each:

- A noun-phrase summary (matches the format of `new-feature.md`)
- A short one-line scope hint
- Status: `À drafter` / `Drafted as [KEY]` / `In sprint` / `Done`

**Example:**

```
- [ ] **Modal Inviter un membre**: flow d'invitation, modal + envoi du courriel + validation. *À drafter en story (new-feature.md).*
- [ ] **Liste des membres avec rôle**: affichage temps-réel des membres et de leur rôle dans l'organisation. *À drafter.*
- [x] **Retirer un membre actif**: flow de retrait avec confirmation. *Drafted as XXX-1234.*
```

The skill **does not auto-create** child stories during epic drafting. It produces the epic + the list of stories to draft next, and the requester decides which to draft and when.

If a child story can't be named without naming a system or a layer (e.g., "Backend pour Y"), that's a Rule 13 violation, re-slice vertically.

### 8: Details

Metadata checklist. Confirm every field with the requester before creating.

- [ ] **Project**: no default, ask
- [ ] **Issue type**: choisi au Step 2 dans la liste retournée par `Atlassian:getJiraProjectIssueTypesMetadata` (recommandation par défaut pour cet artefact : `Epic`). Verrouillé après le Step 2, plus ré-interviewé au Step 5.
- [ ] **Parent**: theme, initiative, or roadmap item if applicable (ask, do not assume)
- [ ] **Labels**: suggest from project scan (Step 2 of skill flow)
- [ ] **Priority**: ask
- [ ] **Reporter / owner**: ask
- [ ] **Time-window estimate**: Atlassian: *« a couple weeks... not too long and not too short »*. Quarter-sized is typical.
- [ ] **Linked tickets**: every child story to come gets `Belongs to epic: [this key]`

---

## Quality gates (run silently in Step 6)

These do not appear as sections in Block A. They run as a self-check during drafting and surface as numbered questions in Block B when they fail.

### Validation epic (4-line check)

- **Pourquoi maintenant ?** *(driver business / contractuel / compliance, cité du requester ou de l'initiative)*
- **Découpage** *(toutes les stories enfants sont identifiées, et chacune est sprint-shippable indépendamment)*
- **Edges du périmètre** *(le hors-périmètre est explicite et discuté)*
- **Mesurabilité** *(les success criteria peuvent être vérifiés à la fin de l'epic, sans nouvelle interprétation)*

For each line, the skill writes a one-sentence evidence statement. If a line fails, the failure becomes a Block B question (not a Block A section). Never fabricate a pass-line.

### Other gates checked silently

- **Anti-fabrication (Rule 14, 14b)**: no invented service/event/table/flag/copy.
- **Persona qualification**: every affected segment is named with role + state/context (no "utilisateurs").
- **Vertical-slice check on each child**: every child story ships user-visible value alone.
- **One coherent objective**: if Acceptance criteria fan out across two unrelated business outcomes, that's two epics.

---

## Anti-patterns to flag during drafting

1. **Epic that's actually a story.** If the requester estimates 1 sprint or less, redirect to `new-feature.md`.
2. **Epic that's actually an initiative.** If the work spans multiple quarters AND multiple teams AND multiple goals, the input is at the initiative level, the skill drafts the *slice that fits in 1–2 quarters* as the epic, and notes the initiative parent.
3. **Epic without identified child stories.** Section 7 is mandatory. If unknown, the requester must either decompose first or accept that the epic is not yet ready to file.
4. **Epic with full GWT inline.** GWT belongs in child stories. An epic with 30 GWT scenarios is a story stuffed full of unrelated cases.
5. **Epic that mixes business outcomes.** An epic should serve one coherent objective. If you find yourself writing two distinct success-criteria pillars (e.g., "subscriptions" + "tile cleanup"), that's two epics.
