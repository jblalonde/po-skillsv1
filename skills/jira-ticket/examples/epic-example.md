# Example — Epic ticket

A worked example of an epic ticket in the format the skill produces, on a **neutral domain** (B2B SaaS — self-service team management). The same shape and discipline applies to any product domain. Domain-specific names below (`PROJ`, *admin d'équipe*, *organisation*, etc.) are illustrative.

This example shows the **two-block output**: Block A is paste-ready in Jira; Block B stays in the chat as the workspace until questions are closed. The 4-line **Validation epic** check runs silently in Step 6 — only failures appear as Block B questions.

---

## Sources of truth used by the skill (inputs)

When the skill produced this epic, the requester provided these citations.

- **Notion strategy page** *« Q3 product priorities »* (link, dated)
- **Initiative parent** "Reduce time-to-value for new customers" (named in the strategy page; no Jira ticket yet)
- **Related Jira** PROJ-1100 (organisations and roles model — the foundation this epic builds on)
- **PO message** (dated) — confirmed scope, out-of-scope items, child story breakdown axis (by capability)

---

## Block A — TICKET (à coller dans Jira)

### Ticket metadata

- **Summary:** `Self-service team management — invitations, rôles, retraits`
- **Type:** Epic
- **Parent:** Initiative *« Reduce time-to-value for new customers »* (référence Notion, pas de clé Jira encore)
- **Labels:** `Team`, `Self-service`
- **Priority:** High
- **Reporter:** [PO name]
- **Time-window estimate:** Q3 (≈ 8–10 sprints)

### Objectif

Permettre à un admin d'équipe d'inviter, modifier le rôle, et retirer un membre de son organisation sans ouvrir de ticket support, en moins de 2 minutes par opération.

### Contexte

**État du monde.** Aujourd'hui, l'ajout, la modification de rôle, et le retrait d'un membre dans une organisation passent par un ticket support traité manuellement par notre équipe ops (délai moyen 1–3 jours ouvrables, voir les métriques mensuelles support). Le modèle d'organisation et l'enum de rôles existent déjà côté backend (PROJ-1100), mais aucune interface admin n'expose ces opérations.

**Pourquoi maintenant.** L'initiative *« Reduce time-to-value for new customers »* identifie le délai d'intégration des membres comme un des trois principaux freins à l'activation d'un nouveau client B2B. Ce trimestre, l'équipe priorise les flows self-service les plus utilisés en support (gestion de membres = ~40% du volume). Affecte les admins d'équipe d'organisations de tous segments, et l'équipe support en aval (réduction de charge attendue).

### Acceptance criteria

- Un admin d'équipe peut inviter un nouveau membre par courriel et lui assigner un rôle, sans ouvrir de ticket support.
- Un admin peut modifier le rôle d'un membre existant à tout moment, et le membre voit le changement à sa prochaine session.
- Un admin peut retirer un membre de son organisation, et le membre perd ses accès dans les 60 secondes (mesurable côté backend).
- L'historique des changements (invitations, modifications de rôle, retraits) est consultable par tout admin de l'organisation, sur les 12 derniers mois.
- Aucune opération de gestion de membre nécessitant l'intervention de l'équipe support pour les 3 capacités ci-dessus.

### Design général

[Lien Figma vers la maquette de la section « Équipe » — vue d'ensemble du flow, pas les écrans détaillés (ceux-ci vivent dans les child stories)]

### Non-inclus

- **Single Sign-On (SSO) et provisioning automatique** — couverts par une autre initiative, hors-scope ici.
- **Gestion des invitations en bulk depuis CSV** — reportée au trimestre suivant (voir PROJ-1300, à drafter).
- **Personnalisation par l'admin de la copie du courriel d'invitation** — hors-scope (PROJ-1305).
- **Modification du modèle de rôles existant** (ajout de nouveaux rôles, sous-permissions granulaires) — la version actuelle de PROJ-1100 reste en place, cette epic se contente de l'exposer.
- **Notifications aux membres lors d'un changement de rôle** — voir Q2 en Block B ; à confirmer.

### Stories enfants

Découpage par capacité (axe choisi : un admin peut compléter chacune des trois capacités principales indépendamment).

- [ ] **Modal Inviter un membre** — flow d'invitation simple, modal + envoi du courriel + entrée dans la liste avec statut « En attente ». *À drafter en story (new-feature.md).*
- [ ] **Liste des membres avec rôle** — affichage temps-réel des membres et de leur rôle, filtrage par statut. *À drafter.*
- [ ] **Modifier le rôle d'un membre** — flow de changement de rôle avec confirmation + historique. *À drafter.*
- [ ] **Retirer un membre** — flow de retrait avec confirmation + révocation des accès dans les 60s. *À drafter.*
- [ ] **Historique des changements de membre** — vue lecture-seule des 12 derniers mois, filtrable par membre et par type d'action. *À drafter.*

### Details

- **Project:** PROJ
- **Issue type:** Epic
- **Parent:** Initiative *« Reduce time-to-value for new customers »* — référence Notion (pas de clé Jira ; à mettre à jour dès que créée)
- **Labels:** `Team`, `Self-service`
- **Priority:** High
- **Reporter:** [PO name]
- **Time-window estimate:** Q3 (≈ 8–10 sprints, 5 child stories)

---

## Block B — Questions à régler (workspace PO, **ne pas coller dans Jira**)

**Pour la PO**

- **Q1** — *(débloque Acceptance criteria — historique 12 mois)* Pourquoi 12 mois ? Y a-t-il une exigence légale (rétention RGPD, audit) ou une décision produit ? Citer la source pour que les child stories puissent l'invoquer.
- **Q2** — *(débloque Non-inclus — notifications aux membres)* Confirmer : un membre dont le rôle change reçoit-il une notification courriel automatique, ou la communication se fait-elle hors-produit (entre l'admin et le membre directement) ? Si automatique, c'est une 6e child story à drafter.

**Pour l'équipe support / opérations**

- **Q3** — *(débloque Objectif — métrique de réduction de charge)* Confirmer la métrique cible : si self-service couvre les 3 capacités, quelle est la réduction de tickets support attendue (en % ou en valeur absolue) ? Cette métrique sera utilisée pour valider le succès de l'epic.

**Validation epic — flags surfacés**

Quatre lignes vérifiées en Step 6 :

- **Pourquoi maintenant ?** ✅ Driver cité (initiative *Reduce time-to-value*, métrique support 40% du volume).
- **Découpage** ⚠️ 5 child stories identifiées et chacune sprint-shippable indépendamment, **mais** voir Q2 — une 6e story possible selon la décision « notification au membre ».
- **Edges du périmètre** ✅ Hors-scope explicite et discuté (SSO, bulk CSV, personnalisation copie, modification du modèle de rôles).
- **Mesurabilité** ⚠️ Voir Q3 — la métrique exacte de réduction de charge support reste à confirmer.

---

## Why this epic works well

- **Summary action-neutre** — `Self-service team management — invitations, rôles, retraits` nomme le corps de travail sans verbe d'action (les verbes appartiennent aux child stories).
- **Objectif observable et mesurable** — « moins de 2 minutes par opération », vérifiable.
- **Contexte structuré en deux paragraphes** — état du monde + pourquoi maintenant. Driver cité (initiative + métrique support).
- **Personas qualifiés** dans Contexte, pas génériques — admins d'équipe + équipe support en aval.
- **Acceptance criteria au niveau epic** — 5 bullets, observables et mesurables, **aucun GWT** (ceux-ci vivent dans les child stories).
- **Non-inclus explicite** — 5 capacités adjacentes nommées avec leur destination ou leur raison. C'est la section qui prévient les disputes en milieu d'epic.
- **Stories enfants nommées par capacité** — chaque enfant = une tranche verticale de valeur, livrable et démontrable seule. Pas de « Backend pour... » (Rule 13).
- **Axe de découpage explicite** — par capacité, choisi parmi les options de la section *Atlassian hierarchy* dans `SKILL.md`.
- **Design général en lien**, pas en pièce jointe — l'epic montre la vue d'ensemble, les écrans détaillés vivent dans les child stories.
- **Anti-fabrication respectée** — PROJ-1100 (modèle d'organisations et rôles) est cité comme la fondation existante, sans inventer de noms de tables, services, ou enums.
- **Block A est paste-ready, Block B porte les questions ouvertes** — Rule 15.
- **Validation epic dans Block B uniquement** — pas de section parasite dans Block A. Les deux flags ⚠️ (Découpage, Mesurabilité) sont surfacés comme questions, pas masqués.

## Where it could be even stronger

- Q3 (métrique de réduction de charge support) devrait idéalement être fermée avant la création — sans cible chiffrée, l'epic n'a pas de critère de succès clair. La PO peut accepter de la fermer en parallèle si l'équipe support s'engage à fournir la valeur dans la première semaine.
- L'initiative parent (« Reduce time-to-value for new customers ») n'a pas de clé Jira — l'epic devrait être mis à jour dès que l'initiative est tracée formellement.
- Le découpage par capacité est correct ici, mais une alternative valable aurait été par étapes du flow utilisateur (invitation → onboarding du membre → vie du membre dans l'organisation). La skill aurait pu proposer les deux découpages et laisser la PO choisir — pas un défaut, mais une option.
